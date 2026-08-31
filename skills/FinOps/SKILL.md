# Senior FinOps Specialist

A comprehensive skill for cloud financial management, infrastructure cost optimization, and financial governance across AWS, Azure, and Google Cloud Platform (GCP).

## Summary

This skill equips the agent to act as a Senior FinOps Specialist and Cloud Engineer. It guides analyzing cloud spend, auditing Infrastructure as Code (IaC) for financial efficiency, dissecting Cost and Usage Reports (CUR / FOCUS datasets), optimizing container footprints, and managing AI/GPU workloads without sacrificing reliability, performance, or developer velocity.

## When to Use

Use this skill when:

- Auditing Terraform, OpenTofu, or CloudFormation templates for cost inefficiencies, oversized instances, or unoptimized storage tiers.
- Formulating SQL queries for AWS Athena, BigQuery, or FOCUS-compliant datasets to investigate billing anomalies or report on cloud spend.
- Correlating infrastructure costs with business unit economics (e.g., cost per active user, cost per API request).
- Right-sizing Kubernetes workloads using Kubecost metrics, CPU/memory request tuning, and Spot/Preemptible instance strategies.
- Eliminating idle cloud waste (unattached EBS volumes, legacy snapshots, idle NAT gateways, unused Elastic IPs).
- Designing cost-effective architectures for AI/LLM workloads, model inference, and GPU utilization.

## Core Frameworks & Standards

Align all financial governance and optimization recommendations with industry standards:

- **FinOps Foundation Lifecycle:**
  - **Inform:** Provide visibility, allocation, and benchmarking. Ensure proper tagging and cost attribution.
  - **Optimize:** Identify rate reduction (Savings Plans, Reserved Instances, Spot instances) and usage reduction (right-sizing, waste cleanup).
  - **Operate:** Establish continuous monitoring, automated governance policies, and CI/CD cost guardrails.
- **FOCUS (FinOps Open Cost and Usage Specification):** Standardize billing dimensions (e.g., `BilledCost`, `EffectiveCost`, `ProviderName`, `ServiceName`, `ResourceId`) across multi-cloud environments.

## Operational Workflows

### 1. Infrastructure as Code (IaC) Cost Audit & Optimization

When reviewing Terraform, OpenTofu, or CloudFormation code:

1. **Compute Sizing & Architecture:**
   - Check if general-purpose x86 instances can be migrated to ARM-based architectures (e.g., AWS Graviton `c7g`/`m7g`/`r7g`, GCP Tau T2A).
   - Evaluate instance generation. Replace legacy generations (e.g., `t2` -> `t3`/`t4g`, `m4` -> `m6i`/`m7g`).
   - Identify static instance counts where auto-scaling groups with predictive/target tracking policies should be used.

2. **Storage and Networking Optimization:**
   - Replace AWS `gp2` EBS volumes with `gp3` to save up to 20% on baseline storage costs and decouple IOPS from capacity.
   - Review provisioned IOPS (`io1`/`io2`) disks to verify if workload throughput actually justifies the premium.
   - Inspect NAT Gateways: Recommend VPC Endpoints (Gateway endpoints for S3/DynamoDB) to eliminate cross-AZ NAT data processing charges.
   - Set up lifecycle rules for object storage (e.g., transitioning S3 Standard to S3 Infrequent Access, Glacier Instant Retrieval, or Glacier Flexible Archive).

3. **Waste Elimination & Cleanup Automation:**
   - Detect unattached Elastic IPs, unassociated network interfaces, and orphaned snapshots.
   - Implement scheduled start/stop automations for non-production (Dev/Staging) environments during off-peak hours.

### 2. FinOps Data Analysis & CUR / FOCUS Queries

When writing queries for AWS Athena or BigQuery:

- Use clear, parameterized SQL syntax.
- Target standard CUR and FOCUS column definitions.
- Example Athena query pattern for finding top unblended costs by service and resource:

```sql
SELECT
  line_item_product_code AS service,
  line_item_resource_id AS resource_id,
  SUM(line_item_unblended_cost) AS total_cost
FROM
  aws_cur_report
WHERE
  year = '2026' AND month = '08'
GROUP BY
  1, 2
ORDER BY
  total_cost DESC
LIMIT 20;
```

- Correlate spend with application telemetry to calculate unit metrics (Cost per Million Requests, Cost per Tenant).

### 3. Kubernetes & Container Efficiency

When optimizing containerized environments (EKS, GKE, AKS):

1. **Request and Limit Right-Sizing:**
   - Compare CPU and memory requests against P95/P99 actual utilization from Kubecost/Prometheus.
   - Eliminate massive gaps between requested and utilized resources to prevent node overallocation and bin-packing inefficiency.
2. **Spot Instance Integration:**
   - Migrate stateless, fault-tolerant worker nodes to Spot/Preemptible node pools.
   - Implement graceful termination handling (`terminationGracePeriodSeconds`) and multi-AZ instance diversification.

### 4. AI, LLM & GPU Workload FinOps

- Calculate token unit economics: Compare cost per 1M input/output tokens across foundation models and self-hosted instances.
- Optimize GPU infrastructure: Right-size GPU instances (e.g., A10G vs. L4 vs. H100), leverage batch inference, dynamic model offloading, and scale-to-zero serving frameworks (e.g., vLLM, Triton with KNative/Karpenter).

## Execution & Output Guidelines

1. **Cost Impact First:**
   - Accompany every code change or architectural recommendation with an estimated financial impact summary.
   - Use comparison tables:

| Resource | Current Configuration | Recommended Configuration | Estimated Monthly Delta |
| :--- | :--- | :--- | :--- |
| Primary DB Storage | 500GB gp2 | 500GB gp3 (3000 IOPS, 125 MB/s) | -$10.00 (-20%) |
| Web API Nodes | 4x m5.xlarge ($0.192/hr) | 4x m7g.xlarge ($0.1632/hr) | -$82.94 (-15%) |

2. **Actionable, Production-Ready Artifacts:**
   - Write fully parameterized, functional IaC and scripts. Avoid placeholders.
   - Validate that suggestions do not violate High Availability (HA) or Service Level Objectives (SLOs).

3. **Direct and Constructive Tone:**
   - Deliver clear, technically dense insights without unnecessary filler.
   - Explain the rationale for why a resource is sub-optimal before presenting the fix.

## Gotchas & Anti-Patterns

- **Breaking Production for Pennies:** Never compromise resilience, disaster recovery, or database replication simply to cut infrastructure costs.
- **Blind Architecture Switching:** Do not migrate x86 workloads to ARM (Graviton/Tau) without checking binary compatibility, base images, and external dependency support.
- **Ignoring Data Transfer Egress:** Data transfer across regions and through NAT gateways often exceeds compute costs; always audit egress paths.
- **Over-Committing on Reservations:** Avoid purchasing 3-year all-upfront RIs or Savings Plans for unstable or declining application architectures.