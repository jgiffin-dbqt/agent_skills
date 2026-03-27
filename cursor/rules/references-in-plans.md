## Referencing code in plans

When implementation plans mention code:

- Use **module / package / namespace** names as your stack expects (e.g. `MyApp.Context.Module`).
- Use **paths relative to the implementation repository root**.
- Include **short code excerpts** when they clarify behavior or contracts.
- Call out **framework or team conventions** and any **intentional deviations**.

## Referencing other documents

Plans should point to:

- **Architecture** — stable URLs or paths (including cross-repository links if docs live separately).
- **Requirements** — same as above.
- **Other implementation plans** — use **relative paths within the implementation repo** when plans sit beside code; otherwise document the canonical location.

Use a consistent style for cross-repo links (e.g. `planning-repo/architecture/...` vs full GitHub URLs) so agents and humans can resolve them.
