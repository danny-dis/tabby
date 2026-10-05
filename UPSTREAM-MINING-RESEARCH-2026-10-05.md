# TABBY — Mining Open Research, OR-Agent and HEC Open Research

The most valuable transfer is empirical context selection.

## Adopt

Compare retrieval strategies around current cursor, symbols, definitions, references, recently modified code, related tests, repository documentation and issue/MR context.

Use deterministic symbol/reference structure first, then lexical/semantic relevance. Keep retrieved context explainable.

Benchmark competing context policies:
~~~text
cursor-only
symbol-neighborhood
dependency-neighborhood
recent-change
test-aware
hybrid
~~~

Measure completion correctness, acceptance rate, latency, context size and failure rate.

Persist scenario/version/model/context-strategy metadata so improvements are reproducible.

## Do not import

Do not turn Tabby into a general research orchestrator. Keep the research/benchmark layer focused on coding-context quality.

## Implementation target

Add a context-selection benchmark suite and StrategyCandidate/EvaluationVector records so Ghost Research or DMR-X can run external tournaments against Tabby context strategies.