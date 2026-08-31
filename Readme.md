# Skills

A collection of [Claude Agent Skills](https://www.anthropic.com/news/agent-skills) — packaged instructions that give Claude specialized, on-demand expertise for a given domain. Each skill lives in its own folder under [skills/](skills/) as a `SKILL.md` file.

## Available Skills

### [finops](skills/finops/SKILL.md) — Senior FinOps Specialist Agent

Cloud financial management and cost optimization across AWS, Azure, and GCP. Covers the three FinOps Foundation pillars (Inform / Optimize / Operate) and includes:

- A production-ready system prompt for a standalone FinOps agent.
- Cost optimization playbooks per provider (storage, compute, discounts/reservations) and for Kubernetes.
- A function-calling tool catalog (`fetch_cloud_pricing`, `analyze_cost_and_usage`, `detect_idle_resources`, `calculate_reservation_roi`).
- Worked examples (EBS gp2→gp3 migration, EKS cluster rightsizing) and a LangChain configuration template.

Use it for: auditing Terraform/OpenTofu/CloudFormation for cost inefficiencies, evaluating Savings Plans/Reserved Instances/CUDs, right-sizing Kubernetes or GPU/LLM infrastructure, and general "why is our cloud bill so high" investigations.

### [aws_finops](skills/aws_finops/SKILL.md) — AWS FinOps Specialist Agent (CUR Auditor)

A narrower, AWS-specific skill focused on querying the **Cost and Usage Report (CUR)** via **AWS Athena**. Defines three concrete tools with JSON schemas and reference SQL:

1. `check_extended_support_charges` — flags Extended Support fees from obsolete engines (e.g. RDS MySQL 5.7, legacy EKS control planes).
2. `analyze_cost_anomaly_detail` — diffs a service's cost month-over-month down to `UsageType`/`Operation` to isolate the root cause of a spike.
3. `calculate_financial_impact_runrate` — projects month-end spend from the recent daily burn rate, to catch runaway infrastructure before the billing cycle closes.

Use it for: root-causing a specific AWS billing anomaly and producing a hard month-end cost forecast.

## Repository Structure

```
skills/
├── finops/
│   └── SKILL.md
└── aws_finops/
    └── SKILL.md
```

Each `SKILL.md` should carry a YAML frontmatter block (`name`, `description`) so Claude can discover and auto-trigger it; the description is what determines whether a skill gets consulted for a given request.

---
Author: Everton Araujo
