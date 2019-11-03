# AWS CodeCommit

* Fully managed Source Control service that enables companies to host secure and highly scalable private Git repositories
* Git is an industry-standard Open Source distributed source control system:
    * Centralized repo for all your code, binaries, images, and libraries
    * Tracks and manages code changes
    * Maintains version history
    * Manages updates from multiple sources
    * Enables collaboration

* Users create a copy (known as branch) of the master repo, which they can update independently without impacting other users
* Saved code changes which are ready to be applied to a repo are known as a commit
* When the updated code located in a branch is ready to be added to the master repo, the branch is then merged into the master repo.
* AWS CodeCommit provides all the functionality of Git and you can use Git on your local machine to interact with your CodeCommit repo
* Your data is encrypted in transit and at rest

## Exam Tips

* Based on Git
* Centralized repo for all your code, binaries, images and libraries
* Tracks and manages code changes
* Maintains version history
* Manages updates from multiple sources and enables collaboration

You can send notifications on Repo updates by SNS topic/CloudWatch event rule.
