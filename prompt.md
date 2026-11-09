# Final Codex prompt: Dana's Bakery order processing

Build a complete Google Colab-compatible Python LangGraph notebook for Dana's Bakery, suitable for an AI Agents course assignment. Create `prompt.md` containing this technical specification and `dana_bakery.ipynb` containing implementation, actual graph visualization, and tests. Update `README.md` with project description and clear Colab run instructions.

1. Use LangGraph `StateGraph`. Use no LLM calls, API keys, or external AI services. All business logic and customer messages must be deterministic.
2. Accept `item`, `quantity`, `day`, and `city`. Empty city means pickup.
3. Prices in NIS: challah 18, babka 45, rugelach 30, sourdough 25. Croissants are discontinued and must be rejected.
4. Start every independent order with stock: challah 10, babka 0, rugelach 3, sourdough 4. Never mutate global stock between tests.
5. Politely reject invalid items and quantities that are not positive integers.
6. When the entire requested quantity is unavailable, attempt at most two substitutions along babka → rugelach → sourdough → challah. Retain the original quantity and use the substitute price. Reject if neither permitted substitute can fill the quantity. Challah has no successor. Never wrap around.
7. Merchandise subtotal equals final product price times quantity. Apply 15% off only when subtotal is strictly greater than 150 NIS and day is not Friday. Ignore the superseded 10% over 200 rule. Never discount delivery.
8. Jerusalem delivery costs 20 NIS. Empty-city pickup is free. Convert all other cities to free pickup and explain the change.
9. Total equals discounted merchandise plus delivery. Rejected orders have total zero and a clear explanation.
10. Use warm, friendly Python message templates explaining substitutions and delivery changes when applicable.
11. Keep an ordered `steps` list of visited nodes/actions for each order.
12. Use meaningful separate nodes and conditional edges for validation, stock, substitutions, discount, delivery, and final response. Do not place the whole workflow in one node.
13. Compile the graph and support `graph.invoke({'item':'babka','quantity':2,'day':'Thursday','city':'Jerusalem'})`.
14. Render the actual graph using `draw_mermaid_png()` and display the PNG in Colab.
15. Before implementation, explain the state, nodes, and conditional routes in Markdown, including a Mermaid sketch.
16. Include at least ten independent orders covering valid orders, invalid item, invalid quantity, out-of-stock substitution, second substitution, failed substitution/cancellation, discount, Friday no-discount, Jerusalem delivery, empty-city pickup, and outside-Jerusalem pickup. Assert expected outcomes and that every result has exactly six fields: `status`, `item`, `delivery`, `total`, `message`, `steps`. Rejections have `item=None` when nothing was sold.
17. Include a pip install cell for LangGraph and visualization dependencies so a fresh Colab runtime can run the notebook without secrets.
18. Keep code readable, well-commented, and appropriate for the course assignment. Run available validation and tests, report limitations, and prepare changes for GitHub review. Do not claim Colab execution unless actually tested there.
19. Save this technical specification as the final Codex prompt; do not include a narrative. Include a short assumptions/revisions note.

## Assumptions and development revisions

Text inputs are trimmed and case-insensitive. Quantity must be a Python integer (booleans, floats, and numeric strings are rejected). Day and city must be strings; any non-Friday day string follows the non-Friday rule. Public status values are `confirmed`/`rejected`; delivery values are `delivery`/`pickup`/`none`. Monetary arithmetic uses Decimal and returns a numeric NIS total rounded to cents. Stock resets on every invocation, including repeated calls to the same compiled graph.

The final discount rule is 15% strictly above 150 NIS, superseding 10% above 200. Explicit output schemas were added to keep internal state out of results. Visualization uses local Pyppeteer/Chromium rather than a remote PNG rendering API. Tests include the strict discount threshold directly because a 150 NIS merchandise subtotal is unreachable with the specified stock and products.
