## Multi-tenancy (when applicable)

If the product is multi-tenant, plans should consider:

- Tenant isolation requirements.
- Tenant scoping on identifiers (e.g. `tenant_id` in queries and APIs).
- Row-level security or equivalent enforcement, if used.
- Test scenarios that prove isolation.

## Security

Plans should address where relevant:

- Credential storage and rotation.
- API and transport security.
- Tenant or customer data boundaries.
- Input validation and safe defaults.
- Errors and logs that **do not** leak sensitive data.

## Performance

Plans should consider where relevant:

- Database indexes and query shape.
- Caching and invalidation.
- Background or async processing.
- Rate limits and backoff for external services.
