1. Branching Strategy
    I’ve chosen a simplified GitFlow approach to keep environment mapping clear:
    develop branch → QA Environment: Any feature merged here is automatically deployed to the QA VM for testing.
    release/* branches → Staging Environment: When we’re prepping for a release, we cut a branch here. It deploys to Staging for final UAT (User Acceptance Testing).

    main branch → Production Environment: Merges here are ready for the deployment, but they don't go live without a manual approval.

    How I avoid accidental Production deploys: Aside from branch protection rules in GitHub (requiring PR reviews), the Jenkins pipeline includes a Production Gate. Even if code is merged to main, the pipeline pauses and waits for a manual approval before touching the Production servers.
___________________________________________________________________________________________________
2. Jenkins Pipeline
    The pipeline is designed to handle two main scenarios-

    On Pull Request (PR): We only run the Build & Test stages. We want to know if the code is healthy, but we don't want to deploy "work-in-progress" code to any server.

    On Merge: Once code hits a protected branch (develop, release, or main), the deployment stages are unlocked.

    Rollback Strategy if a deployment fails - 
    Immediate Revert: We can use Jenkins to "Replay" the last successful build.
    Symlink Swap: On the GCP VMs, I will be using a symbolic link to point to the current version of the ".jar" file. Rolling back by swapping that link back to the previous version and restarting the service.
____________________________________________________________________________________________________
3. Configuration & Secrets
    Hardcoding database URLs or API keys is not a good practice so i will go for these:

    Environment Configs: I use Spring Profiles (application-qa.yaml, application-prod.yaml) to manage environment-specific settings like logging levels.
    Secrets handling: For sensitive data like MongoDB credentials, I use Jenkins Credentials. These are injected into the environment at runtime, so they never appear in the logs or the source code.
____________________________________________________________________________________________________
4. Deployment Strategy: Rolling Update
For this service, I chose a Rolling Update strategy.

   4.1 Blue/Green is great but max your server costs. Since we are a startup, a rolling update allows us to update one VM at a time.

    4.2 Zero Downtime: By taking one instance out of the Load Balancer, updating it, and putting it back, we ensure that users always have a working instance to connect to.

    CI/CD Stages:
    Checkout: Pulls the latest code using SCM.
    Build & Test: Runs Maven to compile and execute unit tests.
    Deploy (Env Specific): Deploys to QA, Staging, or Prod based on branch.
    Manual Gate: A final safety check for Production.
    Post-Build: Notifies the team on success or failure.