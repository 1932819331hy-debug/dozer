# Streaming IN / NOT IN demo for Dozer #1659

[Download or play the MP4](./demo.mp4) · [Readable transcript](./transcript.txt) · [Replayable test-only patch](./trace-test.patch) · [Independent review controls](./review-controls.patch)

This 73.5-second video renders a transcript captured from actual Dozer Rust tests, using only local synthetic records. It is **not** a live-database demonstration or an asynchronous whole-pipeline recording. It includes the genuine historical baseline planner failure and 13 actual candidate membership-processor event assertions: late inner matches, duplicate/retracted inner rows, outer updates/deletion, NULL and empty-set NOT IN behavior. Separate actual-DAG tests cover source wiring and schema behavior.

Production candidate: `b407582b17f8fc9625735eb7483a6d9a33829769`  
Tree: `77facdd1933ce97219310311f6bf0490f75a4d07`  
Video SHA256: `b8f626d90d293e33969b7435f6adca0f8c5de22c1f35cd7ccbc36d4f803c668d`

The original feature branch remains at that exact candidate; these media files live on a separate documentation branch. The closing “private deliverable” caption describes the recording stage before publication. No upstream acceptance or payment is claimed.

## Reproduce

In a separate checkout of the exact candidate, apply `trace-test.patch` and run:

```sh
cargo test --offline --locked -p dozer-sql demo_streaming_membership_event_trace -- --nocapture
```

A compatible Rust/Protoc installation and the project's resolved dependency cache are prerequisites for offline execution. Use a dedicated build target, or clean affected packages before switching between source variants. The patch only adds tracing/assertions; it does not alter production behavior.

## Verification boundaries

Frozen affected-package suites: core 43 passed; SQL 247 passed and 1 pre-existing ignored; expression 30 passed. Independent review's two additional tests produce SQL 249 passed and the same 1 ignored. Those two review-only tests are not part of the frozen commit. The demo itself passes 13 event assertions.

The original all-features CI is **not green**. Candidate and unchanged main both stop in time 0.3.34 compilation with E0282; later fmt/deny steps are skipped:

- [Exact candidate CI](https://github.com/1932819331hy-debug/dozer/actions/runs/39768693301)
- [Exact baseline CI](https://github.com/1932819331hy-debug/dozer/actions/runs/39769203398)

The independent local workspace formatting check also exposes two unchanged baseline formatting differences. No workflow checks were weakened and no dependency upgrades are included.

See `dozer-sql/STREAMING_IN.md` at the candidate for limits: in-memory retained state, right-change full scans, event-driven volatile-expression evaluation, no timer-driven refresh, and explicit unsupported lifetime/window/correlated/scalar-embedded forms.
