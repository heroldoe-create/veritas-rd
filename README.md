# Veritas-RD — evidence-audit skill for Claude Code, Claude and AI agents

**Verify claims against a corpus you can point to. Every verdict carries its source, a reproducible locator and its limits — never the model's memory.**

Veritas-RD is an [Agent Skill](https://docs.claude.com/en/docs/claude-code/skills) (a `SKILL.md` protocol) that turns an AI assistant into a **claim auditor**: you give it documents, links or numbered sources plus the claims to check, and it returns one verdict per atomic claim, with the passage that justifies it and a closing section that says what it could *not* verify.

*Spanish version below · [Versión en español](#español)*

---

## Why

Language models are fluent, so a plausible sentence reads like a verified one. Veritas-RD separates the two:

- A claim is only **supported** if *every material part* of it is covered by a cited passage.
- **Absence of evidence is never refutation.** If the corpus is silent, the verdict is *insufficient* — not *contradicted*.
- Knowledge from the model's memory is kept **out of the verdict** (it is flagged `[PARAMÉTRICO — no anclado]`).
- Tools it does not have (OCR, NLI, search…) are declared **"not executed"** — it does not invent scores, hashes or calibration numbers.

## The four verdicts

| Verdict (label used by the protocol) | Meaning |
|---|---|
| `soportado` — supported | Every material part of the claim is covered by the cited passage. |
| `contradicho` — contradicted | Positive evidence incompatible with the claim, and no pertinent evidence the other way after comparing scope, method, sample, date and definition. |
| `insuficiente` — insufficient | Always with a cause: `por-vacío` (nothing pertinent found) · `por-cobertura` (only part of the claim covered) · `por-fuente-no-disponible` (a needed source could not be read) · `por-novedad-potencial` (the claim is a stated new hypothesis — novelty stays *unverified*). |
| `ambiguo` — ambiguous | Pertinent evidence both for and against within the same scope, unresolved — or a passage that admits more than one material reading. Both passages are cited. |

> The protocol text and the verdict labels are written in **Spanish**. Translations (English labels, other languages) are welcome — see [Contributing](#contributing).

## What you get

- **Source inventory with frozen IDs** — exact file/URL, read locator, family (peer-reviewed / preprint / technical / regulatory / voluntary framework), parseability. Sources it could not read never count as read.
- **Atomic claims** — compound claims are split; scope, population, period and metric are kept.
- **Falsification pass** — it actively looks for evidence that limits or contradicts each claim and shows both sides when sources disagree.
- **Independence check** — sources from the same family, authors or dataset are not counted as independent confirmations.
- **Novelty, kept separate from correctness** — evaluated only on request, relative to the corpus and a stated cutoff date.
- **A mandatory closing block** — unread sources, claims without anchor, subtopics with insufficient evidence, worst-covered family, unverified novelty.
- **Two modes** — *Rápido* (fast: under 5 claims, 1–2 sources) and *Completo* (full), announced up front; it escalates and says so if the job turns out bigger.

### Illustrative example (fictional sources — shows the format, not real output)

> Corpus: `[1]` *Q3-report.pdf* · `[2]` *customer-survey.csv*

| # | Claim | Verdict | Evidence |
|---|---|---|---|
| C1 | Revenue grew 12 % year over year in Q3 | `soportado` | [1] "revenue rose 12 % year over year" (p. 4) |
| C2 | …and operating margin improved | `insuficiente` · `por-cobertura` | [1] reports revenue only (p. 4); margin does not appear in the corpus |
| C3 | Churn fell in Q3 | `ambiguo` | [1] "churn fell" (p. 7) vs [2] churn column rises in Q3 (row 41–58); the two define churn differently — claim split into C3a/C3b |

## Install

**Claude Code (personal skill)**

```bash
git clone https://github.com/heroldoe-create/veritas-rd ~/.claude/skills/veritas-rd
```

On Windows PowerShell: `git clone https://github.com/heroldoe-create/veritas-rd "$HOME\.claude\skills\veritas-rd"`.
Restart the session; the skill activates from the `name` and `description` in `SKILL.md`.

**Claude (app)** — zip the folder and upload it as a skill.

**ChatGPT / Codex** — upload the folder; `agents/openai.yaml` supplies the name, icon and invocation policy.

**Gemini, DeepSeek, Kimi, Qwen or any model without skills** — paste [`PORTABLE.md`](PORTABLE.md) as the Gem / project / system instructions. It is the same protocol in a single file (with the `references/` annex included). Without skills there is no automatic routing: the protocol applies whenever it is pasted.

## Use

```
Use veritas-rd to audit these claims against the attached sources.
Corpus: report.pdf, https://example.org/paper, notes.md
Task: audit claims  ·  Mode: choose for me
Claims: (1) … (2) …   — or: "extract the claims from report.pdf"
```

Supported tasks: coverage map · structured extraction · side-by-side comparison · counter-evidence search · claim audit · novelty relative to the corpus. If the corpus or the task is missing, it asks for **only** what is missing; it does not invent sources.

## What it will not do

- Judge whether a *skill or prompt* lives or dies (that is a different job; Veritas can serve as the evidence layer for such an audit).
- Say "this is true". The output is *supported by the corpus*, nothing more.
- Present a confidence score, an NLI score or a model consensus as proof.
- Claim it removes hallucinations, guarantees completeness or transfers across domains.
- Prove that a skill or prompt *behaves* as written — it can only show what the text instructs.

## Optional companion skills

The protocol mentions two companion skills, `auditoria-darwinista` (audits whether a skill or prompt lives or dies) and `playbook-fable` (a method layer). **Neither is required**: if they are not installed, ignore those references and Veritas-RD works on its own. Use any skill-audit tool you prefer in their place.

## Acceptance tests (v4)

| | Setup | Expected |
|---|---|---|
| A | 2 files; claims cite papers that are not attached | Inventory has exactly 2 rows with the real file names |
| B | 2 sources in conflict on the same claim | Verdict `ambiguo`; escalation to *Completo* declared |
| C | 1 unreadable source | Listed in the closing block; its claim is `insuficiente · por-fuente-no-disponible` |
| D | "there are 4 sources" but 3 attached | Stops and asks before ruling |

These are checks you can run on any model/setup. They are **not** a benchmark, and no accuracy figure is claimed.

## Repository layout

```
veritas-rd/
├── README.md · LICENSE · CHANGELOG.md
├── SKILL.md                 the protocol (Claude Code / Claude / Codex)
├── PORTABLE.md              same protocol, single file, for models without skills
├── references/
│   └── causal-models-and-novelty.md   loaded only for causal / novelty analyses
├── agents/openai.yaml       ChatGPT / Codex metadata
└── assets/icon.svg
```

## Contributing

Issues and pull requests are welcome — especially: translations of the protocol and labels, new acceptance tests (with the failure they catch), and reports of cases where a model wrongly assigned a verdict. If you change the protocol, change `SKILL.md` and `PORTABLE.md` together (they are two copies of the same text).

## License

[MIT](LICENSE) © 2026 Heroldo Escobedo.

---

<a id="español"></a>

# Español

**Veritas-RD** es una skill (protocolo `SKILL.md`) para Claude Code, Claude, ChatGPT/Codex y otros modelos que **verifica afirmaciones contra un corpus que tú identificas** (archivos, enlaces o fuentes numeradas). Cada veredicto lleva su fuente, un localizador reproducible y sus límites; nunca se completa con la memoria del modelo.

**Veredictos:** `soportado` · `contradicho` · `insuficiente` (con causa: `por-vacío`, `por-cobertura`, `por-fuente-no-disponible`, `por-novedad-potencial`) · `ambiguo`.

**Reglas que la distinguen**
- *Soportado* solo si **todas** las partes materiales del claim están cubiertas por el pasaje citado.
- **La ausencia de evidencia no es refutación**: si el corpus calla, es `insuficiente`, nunca `contradicho`.
- El conocimiento de memoria queda fuera del dictamen (`[PARAMÉTRICO — no anclado]`).
- Si falta una herramienta (OCR, NLI, búsqueda), el módulo se declara **no ejecutado**; no se inventan scores ni calibraciones.
- Las fuentes de la misma familia, autores o dataset no cuentan como confirmaciones independientes.
- Cierre obligatorio: fuentes no leídas, claims sin anclaje, subtemas con evidencia insuficiente, familia peor cubierta y novedad no verificada.

**Instalación en Claude Code**

```bash
git clone https://github.com/heroldoe-create/veritas-rd ~/.claude/skills/veritas-rd
```

En modelos sin skills (Gemini, DeepSeek, Kimi, Qwen…): pega [`PORTABLE.md`](PORTABLE.md) como instrucciones del Gem/proyecto.

**Uso**

```
Usa veritas-rd para auditar estos claims con las fuentes adjuntas.
Corpus: informe.pdf, https://ejemplo.org/paper, notas.md
Tarea: auditar claims · Modo: elige tú
```

**Qué no hace:** dictaminar si una skill o prompt vive o muere; decir "esto es verdad" (solo *soportado por el corpus*); presentar un score de confianza como prueba; prometer que elimina alucinaciones.

**Licencia:** [MIT](LICENSE).
