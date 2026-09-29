Read AGENTS.md and follow every project and security rule in it.

Perform a full architecture and security audit of all COMMITTED StoreTools Box work through Phase 4A.

DO NOT modify any files.
DO NOT install anything.
DO NOT commit or push.
DO NOT implement fixes.

Review the ACTUAL CODEBASE, not previous summaries or assumptions.

For every Shopify-specific architectural conclusion, verify the behavior against CURRENT OFFICIAL SHOPIFY DOCUMENTATION.

Prefer:
- shopify.dev
- official React Router documentation where applicable
- official framework/library documentation

Do not rely on blogs, Stack Overflow, Reddit, AI memory, or undocumented assumptions for architectural decisions.

If the current implementation conflicts with current Shopify documentation, flag the implementation even if tests currently pass.


AUDIT THESE LAYERS TOGETHER

- water intake calculation and validation
- recommendation rules
- recommendation keys
- merchant product mappings
- AppInstallation metafield persistence
- Shopify GraphQL helpers
- exact mapped-product resolution
- merchant Resource Picker UI
- recommendation product display
- variant resolution and selection
- StoreToolsCartIntent
- React Router configuration
- automated tests
- Shopify Admin vs storefront boundaries
- future Theme App Extension readiness
- future cart integration readiness
- future analytics/recovery compatibility
- security boundaries


PERFORM FOUR REVIEW PASSES

PASS 1 — ARCHITECTURE

Check:

1. hidden coupling between calculator logic and Shopify commerce logic
2. hard-coded product or variant assumptions
3. abstractions introduced too early
4. unnecessary duplicated types or models
5. responsibility boundaries between:
   calculation
   recommendation
   product mapping
   product resolution
   variant selection
   cart intent
6. code that would make adding future tools difficult
7. code that could block Theme App Extension/storefront implementation
8. architecture that would make analytics or abandoned-cart recovery difficult
9. development-only functionality leaking into production architecture


PASS 2 — SHOPIFY CORRECTNESS

Verify against current official Shopify documentation:

10. AppInstallation metafield ownership and persistence
11. metafieldsSet compareDigest/concurrency behavior
12. Product GID handling
13. Variant GID handling
14. Resource Picker usage
15. Admin GraphQL usage
16. GraphQL top-level error handling
17. mutation userErrors handling
18. pagination behavior
19. product status assumptions
20. variant availability assumptions
21. Admin API availability being incorrectly treated as storefront truth
22. price and currency assumptions
23. market-specific pricing assumptions
24. storefront publication assumptions
25. Theme App Extension readiness
26. eventual Ajax Cart API / Storefront Cart API compatibility
27. cart-line attribution design compatibility
28. anything deprecated or likely to create migration risk


PASS 3 — SECURITY

Audit against Shopify security guidance and normal web security principles.

Check:

29. authentication boundaries
30. authorization boundaries
31. cross-shop / tenant data leakage
32. client-supplied shop identity being trusted
33. client-supplied Product GIDs being trusted without validation
34. client-supplied Variant GIDs being trusted without validation
35. variant-to-product ownership validation
36. server-only modules entering client bundles
37. secrets or credentials entering client-visible code
38. unsafe logging
39. unnecessary personal/customer/calculator data persistence
40. cart attribution being treated as trusted data
41. unsafe React Router origin configuration
42. development tunnel exceptions affecting production
43. malformed configuration handling
44. injection/XSS risks involving Shopify product or merchant-controlled data
45. race conditions in metafield writes
46. unsafe error responses exposing internal data
47. dependency or configuration security issues visible from the repository

Do not assume that data produced by our own UI is trustworthy.


PASS 4 — FAILURE AND COMMERCE CORRECTNESS

Check realistic failure behavior:

48. deleted mapped product
49. unavailable mapped product
50. mapping cleared while shopper state exists
51. recommendation changes
52. mapped product changes
53. multiple variants
54. zero variants
55. stale variant selection
56. invalid quantities
57. duplicate clicks
58. GraphQL network failures
59. partial Shopify responses
60. malformed persisted metafield data
61. concurrent merchant updates
62. stale Admin availability
63. changing product prices
64. changing inventory
65. product becoming unpublished
66. multi-currency / market differences
67. test mocks that do not resemble real Shopify responses
68. tests that pass without asserting meaningful behavior
69. missing integration tests around critical boundaries
70. EMFILE/test watcher warnings and whether they represent a real project risk


CART INTENT REVIEW

Audit this boundary carefully:

type StoreToolsCartIntent = {
  version: 1;
  toolId: string;
  recommendationKey: string;
  productGid: string;
  variantGid: string;
  quantity: number;
};

Determine whether:

- toolId should remain string or become a stricter registered type
- recommendationKey should remain string or become tool-specific
- product/variant ownership can be sufficiently validated
- quantity validation is sufficient
- intent contains unnecessary information
- intent is missing information genuinely required by future storefront cart integration
- intent creates security or trust problems
- versioning strategy is appropriate

Do NOT redesign it unless there is a concrete architectural reason.


TEST AUDIT

Review tests critically.

Identify:

- false-positive tests
- excessive implementation-detail testing
- unrealistic Shopify fixtures
- missing GraphQL error cases
- missing tenant-isolation cases
- missing stale-state cases
- missing variant ownership cases
- mocked Shopify behavior that differs from documented API behavior
- critical behaviors currently verified only manually
- areas that should eventually receive storefront integration tests


REPORTING FORMAT

Do not praise the implementation.

Classify every meaningful audit item only as:

BLOCKER
HIGH
MEDIUM
LOW
PASS

For EVERY non-PASS finding provide:

- severity
- exact file(s)
- exact code area/function if identifiable
- problem
- why it matters
- official documentation or technical basis
- smallest corrective action
- whether it must be fixed BEFORE Phase 4B


Then provide these final sections:

A. MUST FIX BEFORE PHASE 4B

Only issues that would make storefront/cart implementation unsafe or architecturally incorrect.

B. SAFE TO FIX LATER

Technical debt or polish that does not threaten Phase 4B.

C. VERIFIED SHOPIFY ASSUMPTIONS

List the Shopify assumptions you checked against current official documentation.

D. UNVERIFIED / LIVE-TEST-ONLY ASSUMPTIONS

Anything that cannot be confidently validated from code/tests/docs alone.

E. PHASE 4B GO / NO-GO

Return exactly one:

GO
or
NO-GO

If NO-GO:
list only the blockers that must be corrected first.

If GO:
state any conditions that Phase 4B must preserve.


IMPORTANT

A passing test suite does NOT automatically mean PASS.

Existing committed code may still be architecturally wrong.

Do not weaken validation to eliminate findings.

Do not make speculative findings without code or documentation evidence.

Do not implement fixes.

Do not modify files.

Do not commit or push.

STOP after the audit.