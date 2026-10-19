# Voucher Distribution Territory Management

How an electronic voucher network assigns territories across a distributor, sub-dealer and retailer hierarchy, allocates stock by territory, settles overlap disputes, and moves retailers between territories without breaking commission. Applies to airtime PINs, eTopup and **gift card voucher distribution** alike. Written for distribution managers and operators evaluating [EVD System](https://evdsystem.com/) by [MoboGage](https://evdsystem.com/about-mobogage/).

---

## What a territory is in EVD

A **territory** is a named boundary, usually geographic and sometimes by channel, that a node in the distribution tree has rights to sell into. Most networks nest them: region, then zone, then cluster. A distributor holds a region, sub-dealers hold zones inside it, and every retailer belongs to exactly one sub-dealer.

The territory attaches to the node, not to the stock. Stock follows the hierarchy. The territory says who is allowed to recruit and serve which retailers, and whose sales count toward which target.

## What happens without them

Anyone who has run field sales for airtime has seen it. Two distributors send reps to the same market street. One offers retailers an extra half-point of commission to switch. The other retaliates. Margins shrink for both, the operator's coverage map shows three distributors in the city centre and none in the outlying districts, and stock piles up where competition is fiercest while rural shops run dry by the 20th of the month.

Voucher distribution territory management is the boring fix. It gives each node a defined patch, a stock allocation tied to that patch, and a written rule for when two claims collide.

## Hierarchy and allocation model

```
Operator / brand
   |
   +-- Region NORTH  -> Distributor D1
   |      +-- Zone N1 -> Sub-dealer S11 -> retailers
   |      +-- Zone N2 -> Sub-dealer S12 -> retailers
   |
   +-- Region SOUTH  -> Distributor D2
          +-- Zone S1 -> Sub-dealer S21 -> retailers

Allocation per period:
  operator -> region (by sell-through + target)
  region   -> zone   (distributor decides, inside own region)
  zone     -> retailer credit limits (sub-dealer decides)
```

**Boundaries.** Admin district codes are simplest and match how operators report coverage. GPS polygons are more precise but put some shops right on a line. Postal codes work where they are reliable, which is not everywhere.

**Retailer location.** Capture GPS at onboarding from the retailer's own shop, with a photo. A location captured at the distributor's office is useless for territory purposes, and it happens more than anyone admits.

**Rebalancing rule.** A distributor may move allocation between its own zones freely, with a log entry. Moving allocation across regions needs operator approval. This single rule prevents most of the friction.

## Worked example (figures are illustrative)

Every figure in this section is **illustrative**.

Distributor D1 holds region NORTH. Its monthly allocation is **1,200,000** units of face value. Over the last three months, zone N1 sold 58% of the region's volume and N2 sold 42%, so D1 splits the allocation 696,000 to N1 and 504,000 to N2.

On day 18 there is a festival in N2's main town. N2 has used 90% of its allocation; N1 has used 45%. D1 moves **150,000** from N1 to N2. Both zones are inside D1's region, so the move posts immediately with `rebalance_id` and reason "festival demand".

D2 in the south also runs short and asks D1 to lend stock. That is cross-region, so it goes to the operator's channel team, who approve a one-off transfer of 80,000 for the rest of the month.

**Overlap dispute.** Retailer R-2207 sits near the N2 / S1 boundary. Sub-dealer S21 (south) onboarded it in March. In August, D1 claims it falls inside the N2 polygon and asks for it to be moved.

| Check | Finding |
|-------|---------|
| Onboarding GPS | 140 m inside S1 boundary |
| Ambiguity band | 200 m either side of boundary |
| First valid onboarding | S21, March |
| Decision | Stays with S21 |

Because the point falls inside the 200 m band, the rule falls back to "first valid onboarding wins". Commission earned during the dispute window was held in suspense and is released to S21.

**Retailer transfer.** A different retailer asks to move from S11 to S12 because S12's rep visits more often. Both sub-dealers sit under D1, so D1 decides. The retailer's outstanding credit with S11 is settled first, and the move takes effect on the first day of the next commission cycle so that neither sub-dealer's monthly figures are split mid-period.

## Data to keep

| Field | Why |
|-------|-----|
| `territory_id`, `parent_territory_id`, `type` | The region / zone / cluster tree |
| `boundary_ref` | District code list or polygon id |
| `owner_node_id`, `effective_from`, `effective_to` | Who holds it and since when |
| `retailer_id`, `onboarding_gps`, `onboarding_node_id` | Basis for disputes |
| `allocation_period`, `allocated`, `used` | Stock by territory |
| `rebalance_id`, `approved_by` | Movements inside and across regions |
| `dispute_id`, `commission_suspense` | Money held while a claim is open |
| `transfer_id`, `transfer_effective_date` | Retailer moves between nodes |

## Things that go wrong

**Duplicate retailers.** The same shop registered twice under two sub-dealers, often with two different MSISDNs. Match on GPS plus shop photo plus owner ID during onboarding.

**Mid-cycle boundary changes.** Redrawing a territory on the 12th forces commission recalculation for half a month. Make territory changes effective-dated and apply them at cycle start.

**Allocation by headcount.** Splitting stock by number of retailers rewards zones that sign up dormant shops. Allocate by sell-through, then adjust for targets.

**Locks that are too hard.** During a network outage or a festival, a strict "no cross-territory stock" rule can leave a whole town without airtime. Keep an emergency transfer path with operator sign-off.

**No suspense account.** If disputed commission is paid out while the claim is open, clawing it back later causes a second dispute.

A runbook for handling overlap disputes and transfers is in [docs/territory-dispute-runbook.md](./docs/territory-dispute-runbook.md).

---

## FAQ

**What is voucher distribution territory management?**
It is the practice of assigning each distributor and sub-dealer a defined area, allocating voucher stock by that area, and using written rules to settle disputes and retailer transfers.

**How should voucher stock be allocated across territories?**
Mainly by recent sell-through per territory, adjusted for targets and known events such as festivals. Distributors can then rebalance between their own zones.

**Who owns a retailer on a territory boundary?**
Set a rule in advance. A common one: the retailer belongs to the territory its onboarding GPS falls in, and if the point is within an ambiguity band, the first valid onboarding wins.

**When should a retailer transfer take effect?**
At the start of the next commission cycle, after any outstanding credit with the old sub-dealer is settled.

**Does EVD System support multi-tier distribution?**
EVD System by MoboGage covers voucher generation, inventory and reseller channels, including multi-tier distributor and retailer hierarchies.

---

## Glossary

| Term | Meaning |
|------|---------|
| Territory | Boundary a distribution node has rights to sell into |
| Sub-dealer | Middle tier between distributor and retailer |
| Allocation | Stock or credit assigned to a territory for a period |
| Rebalance | Moving allocation between territories |
| Ambiguity band | Distance from a boundary where GPS alone does not decide ownership |
| Commission suspense | Holding account for commission under dispute |

## Further reading

- [Multi-tier reseller voucher distribution](https://evdsystem.com/evd-software/multi-tier-reseller-voucher-distribution/) on evdsystem.com
- [Electronic voucher distribution software](https://evdsystem.com/evd-software/electronic-voucher-distribution-software/) on evdsystem.com
- Related repo: [multi-tier-reseller-distribution](https://github.com/iwtglobal/multi-tier-reseller-distribution)

## License

MIT. See [LICENSE](./LICENSE). Educational material only, not legal or regulatory advice.
