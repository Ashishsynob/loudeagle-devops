1. Key Choices & Justifications

Compute Choice: Cloud Run
 For a startup, Cloud Run is superior to GKE or Compute Engine because GKE is complex and need more time and minds to fix issues.

Cost: It scales to zero for no productive and demand timings.
Auto-scaling: It scales based on request concurrency automatically.
It is Serverless. You don't manage nodes, patches, or OS updates, allowing a small team to focus on code.
______________________________________________________________________________________________________
2. MongoDB Hosting: MongoDB Atlas

Self managed Databases are sometimes costlier and issues require manual backups, sharding, and patching nd require DB admin to fix them so that's why going for Atlas.

Atlas provides a "Free Tier" or "Shared Tier" for startups, with built-in security and automated backups. It integrates directly with GCP via VPC Peering.
______________________________________________________________________________________________________
3. Networking: VPC & Ingress

Ingress: Using HTTP(S) Load Balancer. This provides a single IP, SSL , and Cloud Armor to protect against SQL injection or DDoS attacks
Egress: Using Serverless VPC Access Connector. This allows the Cloud Run service to communicate with the database over a private internal IP, ensuring no database traffic ever touches the public internet.
______________________________________________________________________________________________________
4. Secrets & IAM

Secret Manager: Store MongoDB URIs and API Keys here. Cloud Run has a native integration where secrets can be mounted as environment variables.

Least Privilege (IAM): Create a specific Service Account .Grant it only "secretmanager.secretAccessor"
limited access.

Logging & Monitoring: Google Cloud Operations Suite
Reason: It’s native and requires zero configuration.