# Territory Dispute Runbook

Steps for channel teams handling overlap claims, cross-territory stock requests and retailer transfers in a voucher distribution network. Replace every figure with your own policy values.

## When two nodes claim the same retailer

1. Open a `dispute_id` and record both claimants, the retailer id and the date of the claim.
2. Move commission earned by that retailer from the claim date into suspense.
3. Pull onboarding GPS, shop photo and onboarding node.
4. Measure the GPS point against the boundary. If it is clearly inside one territory, that territory wins.
5. If it is inside the ambiguity band (**illustrative**: 200 m), first valid onboarding wins.
6. If onboarding GPS is missing or obviously wrong (office location), send a field check before deciding.
7. Record the decision, release suspense to the winner, and notify both nodes in writing.

## Cross-territory stock request

- [ ] Confirm the requesting node has used most of its own allocation (**illustrative**: over 85%)
- [ ] Confirm the lending node has headroom for the rest of the period
- [ ] Get operator channel team approval and record `approved_by`
- [ ] Set an end date; one-off transfers should not roll into next period by default

## Retailer transfer between sub-dealers

- [ ] Written request from the retailer
- [ ] Outstanding credit with the current sub-dealer settled or formally carried over
- [ ] Decision by the parent distributor (same region) or operator (cross-region)
- [ ] Effective date set to the start of the next commission cycle
- [ ] Retailer's credit limit re-set by the new sub-dealer

## Monthly review

- [ ] Open disputes older than 30 days
- [ ] Rebalances and cross-region transfers by reason
- [ ] Retailers with onboarding GPS outside their assigned territory
- [ ] Zones with high allocation but low sell-through
