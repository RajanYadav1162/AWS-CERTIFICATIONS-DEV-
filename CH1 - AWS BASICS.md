# AWS Global Infrastructure: Key Concepts

## 1. Regions & Availability Zones (AZs)

- **Region**:
  - Geographically separate area (e.g., `us-east-1`, `ap-south-1`)
  - Contains multiple **AZs** (isolated data centers)
  - Choose regions based on:
    - Latency requirements
    - Compliance (data residency laws)
    - Service availability

- **Availability Zone (AZ)**:
  - Physically isolated data center within a region (e.g., `us-east-1a`, `us-east-1b`)
  - Designed for fault tolerance (power, networking separate from other AZs)
  - Use case: Deploy redundant microservices across AZs for high availability

## 2. Edge Locations vs. PoPs

| Feature               | Edge Locations                          | Points of Presence (PoPs)               |
|-----------------------|------------------------------------------|-----------------------------------------|
| **Purpose**           | Cache content (CloudFront CDN)           | Optimize traffic routing (DNS/network)  |
| **Data Stored**       | Cached files/API responses               | None (pure traffic routing)             |
| **Services Using It** | Only CloudFront                          | Route 53, Lambda@Edge, AWS Shield       |
| **User Interaction**  | Directly serves cached content           | Invisible infrastructure layer          |

### Key Analogies

- **Edge Location** = Fast-food drive-thru (instant cached responses)
- **PoP** = Highway interchange (optimizes path to origin)

## 3. How Traffic Routes Through AWS

| Step | Direction | Node              | Type                     |
|------|-----------|-------------------|--------------------------|
| 1    | →         | User (London)     | Makes DNS Request        |
| 2    | →         | Route 53 PoP      | Local Cache Check        |
| 3a   | →         | Edge Location     | Cached – Return IP       |
| 3b   | →         | Auth DNS Server   | Not Cached – Get IP      |
| 4    | →         | Edge Location     | Serve or Forward Content |
| 5    | ←         | Edge Location     | Response to User         |
