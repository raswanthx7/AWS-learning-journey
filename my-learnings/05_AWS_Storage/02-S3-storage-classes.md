# S3 Storage Classes 

## Topics covered
- Why S3 storage classes exist  
- Storage cost vs access (retrieval) cost  
- Cold storage definition  
- Why some classes have high storage cost and low retrieval cost, and vice-versa  
- When to use: Standard, Standard-IA, One Zone-IA, Glacier Flexible Retrieval, Glacier Deep Archive  
- Real-world example: Netflix (how and why they use different classes)  
- Quick summary

---

## 1. Why S3 storage classes exist
Different workloads have different access patterns. Some data is accessed frequently (hot), some occasionally (warm), and some almost never (cold). Storage classes provide different price/performance trade-offs so companies pay appropriately for how they use data.

---

## 2. Storage cost vs access (retrieval) cost
- **Storage cost**: what you pay per GB per month to keep data in a storage class.  
- **Access (retrieval) cost**: what you pay when you read/download data from that storage class.

AWS charges both because some classes optimize for low monthly cost (cold storage) but require more work and resources to retrieve objects, so retrieval is charged higher. Other classes keep data immediately available (hot storage) and therefore charge more for storage but less for retrieval.

---

## 3. What “cold storage” means
Cold storage = data kept long-term and accessed rarely. Cold storage is optimized for minimal monthly cost at the expense of slower and/or more expensive retrieval. Typical cold storage use-cases: legal retention, long-term backups, historical logs, and compliance archives.

---

## 4. Why some classes have high storage cost and low retrieval cost
**S3 Standard** (hot storage): AWS maintains data on fast, redundant infrastructure ready for immediate access. That infrastructure costs more to operate continuously, so storage rates are higher. Retrieval is cheap because the data is already online and readily served.

---

## 5. Why some classes have low storage cost and high retrieval cost
**Glacier tiers (cold storage)**: AWS stores data on lower-cost media and does not keep everything instantly available. Retrieving data requires additional work (restore jobs, staging, deeper storage systems), which is slower and consumes resources. AWS charges less for storage and more for retrieval to reflect that trade-off.

---

## 6. Storage classes and when to use them

### 1. S3 Standard

**What it is**  
Fast, highly available, multi-AZ object storage for frequently accessed data.

**Why it exists**  
To serve hot data with minimal latency and high throughput for user-facing applications.

**Where the data lives**  
Replicated across multiple Availability Zones (minimum 3 AZs).

**Use cases**  
- Website assets (images, CSS, JS)  
- Application uploads and user content  
- Real-time analytics and active datasets  
- Any data accessed frequently

**Cost model**  
- Storage cost: High  
- Retrieval (access) cost: Low

**When NOT to use**  
- Long-term archival data  
- Rarely accessed backups

**Opportunity cost**  
Storing cold or infrequently accessed data here wastes money because you pay premium storage rates for always-ready access.

---

### 2. S3 Standard-Infrequent Access (Standard-IA)

**What it is**  
Multi-AZ storage with the same durability as Standard but lower storage price and retrieval charges.

**Why it exists**  
To reduce monthly storage costs for data that is needed occasionally but must be immediately available when requested.

**Where the data lives**  
Replicated across multiple Availability Zones.

**Use cases**  
- Monthly or quarterly reports  
- Backups that are restored occasionally  
- Warm logs and infrequently accessed application data

**Cost model**  
- Storage cost: Medium (lower than Standard)  
- Retrieval (access) cost: Medium (charged per GB/read)

**When NOT to use**  
- Frequently accessed data (access charges add up)  
- Data that must be extremely cheap to store with rare retrieval needs (use Glacier instead)

**Opportunity cost**  
Using Standard-IA for hot data increases overall cost due to retrieval fees; using Standard for cold data wastes storage cost.

---

### 3. S3 One Zone-Infrequent Access (One Zone-IA)

**What it is**  
Low-cost infrequent access storage that keeps objects in a single Availability Zone.

**Why it exists**  
To provide cheaper-infrequent access storage for non-critical or easily re-creatable data where multi-AZ durability is not required.

**Where the data lives**  
Stored in one Availability Zone only.

**Use cases**  
- Re-creatable data (can be regenerated)  
- Temporary processing outputs and intermediate files  
- Non-critical caches and analytics artifacts  
- Low-value logs that can be recreated

**Cost model**  
- Storage cost: Low (cheapest among IA classes)  
- Retrieval (access) cost: Medium

**When NOT to use**  
- Production-critical data  
- Compliance or regulated data requiring multi-AZ durability

**Opportunity cost**  
You save on storage but accept higher risk: if the single AZ fails, data can be unavailable or lost.

---

### 4. S3 Glacier Flexible Retrieval (Glacier)

**What it is**  
Archive tier for long-term retention with lower monthly cost and retrieval jobs that take minutes to hours.

**Why it exists**  
To provide very low-cost storage for data that is rarely accessed but must be retained and retrievable within a reasonable time window.

**Where the data lives**  
Archived across AWS storage infrastructure (multi-AZ), optimized for cost over instant access.

**Use cases**  
- Old backups and snapshots  
- Audit logs and historical analytics  
- Compliance data accessed rarely but within minutes–hours when needed

**Cost model**  
- Storage cost: Very low  
- Retrieval (access) cost: Medium to High (depends on retrieval speed and amount)

**When NOT to use**  
- Data that requires instant access or is read frequently

**Opportunity cost**  
You save on storage but pay for retrieval time and cost; frequent retrievals make this tier expensive overall.

---

### 5. S3 Glacier Deep Archive (Glacier Deep Archive)

**What it is**  
Coldest, most cost-effective archival storage for data accessed very rarely (years), with long retrieval times.

**Why it exists**  
To minimize ongoing storage expense for long-term retention requirements (e.g., legal, regulatory) where access is extremely rare.

**Where the data lives**  
Deep archival storage (multi-AZ), optimized for minimal monthly cost and long-term retention.

**Use cases**  
- 7–10 year compliance archives (financial, legal, medical)  
- Historical records and forensic data retention  
- Data kept for legal hold or regulatory purposes

**Cost model**  
- Storage cost: Lowest  
- Retrieval (access) cost: High

**Retrieval time**  
- Typical retrievals: many hours (often 10–12 hours minimum, depends on retrieval option)

**When NOT to use**  
- Any active dataset or data required for regular reporting or near-term analysis

**Opportunity cost**  
Lowest monthly cost but highest retrieval latency and expense; retrieving data is slow and costly, so mistakes in classification can cause long delays and high fees.

---

## Quick reference table

| Storage Class               | AZs       | Storage Cost | Retrieval Cost | Retrieval Speed        | Best Use Case                                |
|----------------------------:|-----------|--------------:|---------------:|------------------------|-----------------------------------------------|
| S3 Standard                 | Multi-AZ  | High         | Low            | Immediate              | Hot data, user-facing assets                  |
| S3 Standard-IA              | Multi-AZ  | Medium       | Medium         | Immediate              | Important but infrequently accessed data      |
| S3 One Zone-IA              | One AZ    | Low          | Medium         | Immediate              | Re-creatable, temporary processing files      |
| S3 Glacier Flexible Retrieval| Multi-AZ  | Very Low     | Medium/High    | Minutes to hours       | Archival, older logs, rarely accessed analytics|
| S3 Glacier Deep Archive     | Multi-AZ  | Lowest       | High           | ~10–12 hours           | Long-term compliance archives (7–10 years)    |

---

## 7. Real-world example: Netflix
**What Netflix stores in S3**
- Thumbnails (movie preview images)  
- Subtitles and captions  
- Metadata (titles, descriptions, language, ratings)  
- Playback and usage logs  
- Transcoded assets and intermediate workflow files  
- Long-term analytics and compliance logs

**How Netflix maps to S3 classes**
- **S3 Standard**: user-facing assets (thumbnails, subtitles, metadata) and frequently accessed logs — immediate access required.  
- **Standard-IA**: older thumbnails, older metadata, and backups that are occasionally requested.  
- **Glacier Flexible Retrieval**: older analytics and debug logs that are rarely accessed but sometimes needed for investigations.  
- **Glacier Deep Archive**: long-term compliance logs and records retained for multi-year legal requirements.

**Why**: Netflix balances cost and performance by keeping hot, user-facing assets in Standard while moving rarely-used, long-retention data to Glacier tiers to minimize monthly storage expense.

---


