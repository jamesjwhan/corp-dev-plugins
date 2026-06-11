# Module — Technical & Product Diligence

DRI: **Engineering lead** (under **clean-team protocol**). In scope whenever a codebase/product
transfers. Depth scales: team-and-quality read for acqui-hires; full architecture + security review
for platform deals.

## Clean-team protocol (read first)
Code reviewers should **not** be the people who would build the same feature if the deal dies. Put
intermediation between the clean team and the sponsoring team to de-risk IP-infringement exposure.
Pre-term-sheet technical review is an **abbreviated** form of this; confirmatory is the full review.

## Request list (trim to the deal)

**Code & architecture** *(P0 when product is the thesis)*
- Repo access (clean team); architecture overview; service map; tech-stack inventory
- Code-quality read: test coverage, tech debt, documentation, key-person dependencies in the code
- Scalability/performance posture; infra and cloud spend
- **Integration assessment**: keep / rebuild / discard per component; fit with Toast architecture;
  migration criticality; milestones at 2/4/6 months (feeds the integration plan)

**License & IP** *(P0 when "clean IP" is an assertion — coordinate with legal-dd)*
- Open-source inventory + license obligations (copyleft exposure); SBOM if available
- Third-party dependencies and their licenses

**Security**
- Static/dynamic scanning results; penetration-test history; vuln management
- Secrets management; access controls; incident/breach history
- Data handling and privacy-by-design (coordinate with legal-dd privacy)

**Team (R&D quality)** *(P0 for acqui-hires)*
- Org chart, tenure, who-owns-what; key-person concentration
- Engineering practices (CI/CD, review culture, on-call)

## Priorities
- **P0:** code ownership/license contamination, key-person risk, integration feasibility, critical security findings
- **P1:** tech debt, scalability, infra cost
- **P2:** documentation, tooling

## Out / light when
Light when there's no product to assume (pure talent acqui-hire → focus on team + code quality as a
talent signal, skip deep architecture/integration). Full for platform deals.
