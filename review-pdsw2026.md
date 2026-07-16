# Review Notes — "Benchmarking DAOS for Scientific Streaming" (PDSW 2026)

Reviewed 2026-07-16. Scope: full read of `main.tex` + all files under `secs/`, plus a
compile check and a citation/figure consistency check. Typos and grammar issues found
during the pass were corrected in place (list at the bottom); everything else here is
left for you to decide on.

## Compile & page-limit check

- Paper compiles cleanly with `latexmk -pdf` (IEEEtran, conference/double-column mode).
  Only cosmetic `Underfull \hbox` warnings, no undefined references, no missing figures.
- All 10 referenced figures in `figures/` exist; all `\cite{}` keys resolve against
  `references.bib` (30 used, 37 defined — 7 unused entries, harmless).
- Body content (Introduction → Conclusions) ends on **page 6**; the `\newpage` before
  `\bibliographystyle` pushes references to page 7. **You are within the 6-page limit
  (excluding references)** as currently written — confirmed via the compiled PDF's
  page anchors, not just a page count.

## Critical: double-blind anonymization is incomplete

You confirmed the two stacked `\author{...}` blocks are intentional — the second one
(`Anonymous Authors`) overrides the real byline, so the title page renders anonymously.
I added a one-line comment above each block clarifying which one is camera-ready-only,
but I did **not** touch the self-citation issue below, since it changes bibliography
content rather than being a typo:

- `secs/introduction.tex` cites `mei2024sc-poster` and `e2sar-github` in the same
  sentence that frames your own streaming/burst-buffer motivation. Both bib entries
  list **Xinxin Mei** as an author (`references.bib:213`, `references.bib:249`), i.e.
  co-author of *this* submission. A reviewer who checks the bibliography (most do) can
  identify the author team directly from this, even though the byline itself says
  "Anonymous Authors." If PDSW enforces double-blind review, you likely need to either
  cite these anonymously (e.g., "removed for double-blind review" placeholder) or
  rephrase to avoid citing your own poster/repo by name. Worth checking PDSW's exact
  self-citation policy before submitting.

## Numeric consistency to double-check

- `secs/results.tex`, Multi-Node Scaling: "DAOS scales near-linearly with $n$ before
  saturating at $\mathrm{PPN} \times n = 4096$ processes." Later in the same
  subsection, the PPN=48/$n$=128 configuration is explicitly given as **6144**
  processes (48 × 128), and the tested PPN values are {8,16,32,48}. 4096 doesn't
  correspond to any PPN×n combination in that grid (32×128=4096 is the closest, but
  that's the *2 MiB* crossover point mentioned two paragraphs later, not obviously
  what this sentence means). I didn't change this since I can't verify against your
  raw data, but please double check that the "4096" saturation point is the number you
  intend — it may be a stale figure from an earlier draft of the sweep.

## Content strengths (worth keeping as-is)

- The tail-latency and access-order angle is a genuine gap versus the peak-throughput
  focus of prior DAOS studies you cite — this is a clear, well-motivated contribution
  for a systems-benchmarking workshop like PDSW.
- Dual-platform (Aurora + local testbed) evaluation is a nice touch: findings that hold
  on both let you claim they're DAOS-inherent rather than platform artifacts, and you
  say this explicitly in Background — good framing.
- Each results subsection ends with a bolded takeaway; this is very reviewer-friendly
  and makes the paper skimmable, which matters for a 6-page workshop paper.
- Numbers in the text are largely internally consistent — I cross-checked ~15 of the
  ratio/percentage claims (e.g., "10×", "26×", "3–5×") against the raw figures quoted
  nearby and they check out arithmetically.

## Suggestions (not applied — your call)

1. **Redundancy factor set to 0 on Sys-A** (`secs/methods.tex:77`): you note the
   default `rd_fac=3` was overridden to 0 "to maximize throughput" for the multi-node
   IOR runs. A reviewer may push back that this reports best-case, non-fault-tolerant
   numbers that don't reflect production DAOS pools (which typically run with
   replication/erasure coding). A one-sentence justification or a caveat in the
   takeaway would preempt that question.
2. **"Near-linear" scaling claim** (`secs/results.tex:147`): 73→457 GiB/s from 2→16
   nodes is an 8× node increase for a 6.3× throughput increase (~78% scaling
   efficiency). That's good scaling but calling it "near-linear" is a little generous;
   consider "scales favorably" or quoting the efficiency number directly.
3. **Missing limitations/threats-to-validity discussion**: the paper has no explicit
   limitations paragraph (common ask from PDSW reviewers) — e.g., single storage
   engine version (v2.6), no comparison against Lustre/GPFS/Ceph directly (only NFS),
   no repeated cross-validation of the same experiment on both Sys-A and Sys-B. Even
   two sentences in the Conclusion would help.
4. Seven bib entries (`clas12-report2021`, `ejfat2023ieee-trans-np`,
   `esnet2024doe-report`, `esnetnews2024`, `globus-flow2023`, `io500-github`,
   `kunkel2019io500`) are defined but never cited — harmless, but easy to prune if you
   want a tidy `references.bib`.
5. `secs/claude.tex` (the orange "Interesting Questions" appendix) is excluded from
   the build (`% \input{secs/claude.tex}` in `main.tex`) — confirmed it has no effect
   on the compiled paper, just flagging in case that was accidental.

## Typos / grammar fixed in place

- `secs/background.tex`: "tail latency, **or** access-order sensitivity" → "tail
  latency, **and** access-order sensitivity" (the sentence lists things you jointly
  examine, not alternatives).
- `secs/background.tex`: normalized "et al." spacing to use LaTeX's control space
  (`et al.\ `) consistently across all five occurrences — without it, LaTeX's
  sentence-spacing heuristic inserts inconsistent extra space after some instances but
  not others in the typeset PDF.
- `secs/methods.tex`: `$fio$` (stray math-mode) → `\textit{fio}`, matching every other
  reference to the tool in the paper.
- `secs/introduction.tex`: "compared **to the checkpoint-based systems**" → "compared
  to **those in** checkpoint-based systems" (fixes a category mismatch — events were
  being compared to systems rather than to other events).
- `main.tex`: added clarifying comments above both `\author` blocks (no visual/content
  change, just documents which block is active for double-blind vs. camera-ready).
