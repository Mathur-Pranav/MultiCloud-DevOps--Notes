## Key Outcomes

The session covered GitHub repository management, continuous integration (CI) concepts, and hands-on Jenkins installation on cloud VMs. Students explored GitHub features including branch protection rules, webhooks, and POLLSCM, then connected these concepts to a CI pipeline workflow using Jenkins. A job opportunity for a Senior SRE role (50–65 LPA, 100% remote) was also announced at the start of the session.

---

## Decisions Made

- **Jenkins** selected as the primary CI/CD tool for upcoming sessions due to its open-source nature, 2,300+ plugins, cross-platform UI consistency, and strong community support 
- **Next full week** dedicated entirely to Jenkins — pipeline setup, job configuration, CI/CD implementation 
- **Tomorrow's session** designated as Linux revision + Reverse KT (students must bring 2–3 new commands or concepts to share) 
- **Friday** scheduled for AWS revision + Reverse KT 
- Students advised to use **GCP** as the primary cloud platform to start with, with AWS as an alternative 
- Students instructed to commit notes and learning content to personal GitHub repos daily to build green contribution activity 

---

## Job Opportunity Announced

- **Role:** Senior SRE (Site Reliability Engineer) 
- **Budget:** 50–65 LPA, **100% remote** 
- **Client:** Undisclosed at this stage; name to be revealed after first interview round 
- **Minimum experience required:** ~10 years genuine experience (background verification will be conducted) 
- **Key skills required:**
    - Monitoring management: **Grafana, Prometheus, Loki, OpenTelemetry** 
    - **AWS:** CloudWatch, IAM, VPC, EKS, ECS 
    - **Terraform**, CICD ownership, production environment experience 
    - Observability/monitoring setup — at least 1–2 tools sufficient 
- **Apply by:** 28 October, 4 PM — send resume directly to the provided Skills Capital email 
- Eligible candidates (Batch 44, 45, 46) should share email ID with Vikas after applying 
- Candidates with 6 years of experience confirmed **not eligible** — 10 years minimum strictly required 

---

## GitHub Features Covered

### Repository Types & Visibility

- **Public repos** are accessible by anyone; **private repos** restrict access 
- Visibility can be toggled via Settings → Change Repository Visibility; MFA verification required to make private 
- Demo performed live: repo made private → collaborator (Nitesh) added → repo made public again 

### Collaborators & Access Control

- Adding collaborators: Settings → Collaborators → Add People → send invitation via email 
- Collaborator receives email invite; access granted upon acceptance 
- Teams can also be created and users added in bulk; AD integration also possible 
- **Level of access** for collaborators is configurable based on rules — not all collaborators get full settings access 

### Repository Settings & Features

- **Features toggleable:** Issues, Wiki, Projects, Sponsorship, Social Preview — enabling/disabling changes what tabs appear in the repo UI 
- **Issues tab:** Functions as an internal Jira — bug tracking, enhancement requests, support tickets, labels, milestones, assignees 
- **Pull Requests:** Mechanism to merge feature branch changes into main/master branch after approval 
- **Wiki:** Documentation similar to Confluence; supports multiple pages but has more limitations than dedicated tools 
- **Projects tab:** Workspace/project-related information and reporting 
- **Security tab:** Code scanning, vulnerability policies, [security.md](https://security.md) file, advisory management — third-party tools like SonarQube preferred in practice 
- **Insights tab:** Activity metrics — PR counts, open/closed issues, top contributors, merge stats, commit history 
- **Webhooks:** Connect GitHub to external CI/CD tools (e.g., Jenkins) to trigger pipelines on code push 
- **Transfer Ownership / Archive / Delete:** Available in Danger Zone settings; deletion is irreversible 

### Branch Protection Rules

- Accessible via Settings → Branches → Add Classic Rules 
- **Key rules demonstrated:**
    - **Required pull request before merging** — code cannot go to main without PR approval 
    - **Required status checks** — automated checks must pass before merge 
    - **Branch must be up-to-date** before merging — prevents merge conflicts 
    - **Required conversation resolution** — all review comments must be resolved 
    - **Required signed commits** — verified signatures enforced 
    - **Required deployment to succeed** before merging — build/test must pass first 
    - **Lock branch** — makes branch read-only 
    - **Allow force push** — enables `git push -f` to forcefully override; rarely recommended 
    - **Allow deletion** — controls whether branch can be deleted 
- In real companies, **not everyone can push directly to main** — branch protection rules enforce this 
- Rule sets are a one-time setup activity per project 

### Git Commands Referenced

- `git revert <commit-id>` — reverts to a previous version 
- `git push -f` (force push) — forcefully pushes even when errors occur; use with caution 
- `git reset --hard` — resets everything, ignores errors; workaround for stubborn revert failures 
- `git pull` before pushing — recommended to avoid push rejection errors 
- `.gitignore` — used on local machine to exclude files (credentials, API keys, build folders, binary dependencies) from being tracked or pushed to GitHub 

### GitHub Agents / Actions

- GitHub has built-in **agents/runners** for CI automation but they are **not free beyond a limit** — companies rarely use them due to heavy charges 
- **Jenkins** (open-source, free) used instead for build/compile/test automation 

### GitHub Contribution Activity

- Green squares on GitHub profile = commit activity — visible to recruiters and background verifiers 
- **Recommendation:** Minimum **10–15 commits per month** — students advised to fork Vikas's notes repo and make daily edits to build green activity 

---

## Continuous Integration (CI) — Concepts

### Definition

CI is the practice of automatically triggering **build, compilation, and unit testing** every time a developer pushes code to a shared repository (GitHub), with success/failure feedback sent directly to the developer via email. 

### How GitHub Connects to Jenkins

Two primary integration mechanisms: 

- **Webhooks** (~20–30% of companies) — GitHub notifies Jenkins on code push to trigger pipeline
- **POLLSCM** (~70% of companies) — Jenkins periodically polls the GitHub repository for changes; preferred in most real-world setups 

### CI vs. Agile

- In **Agile**, integration was the boundary — testing was separate
- In **DevOps CI**, unit testing is also automated within the pipeline itself — no manual build engineer needed 

### Traditional vs. CI Approach

- **Traditional:** A dedicated person (e.g., "Arthav") manually ran builds at fixed times (e.g., midnight) — developers waited 12–24 hours for feedback 
- **CI:** As soon as code is pushed, build + test runs automatically within minutes 

### Benefits of CI

- **Improved quality** — code tested 10–20 times per day automatically 
- **Increased productivity** — no waiting for manual builds; feedback in 5–15 minutes 
- **Reduced risk** — automated unit tests catch errors consistently; human error eliminated from build process 

### Popular CI Tools

Jenkins, GitLab CI, GitHub Actions, Azure Pipelines, Bamboo, TeamCity, Travis CI, AWS CodePipeline, Bitbucket Pipelines, CodeMagic 

- **Jenkins** used by ~50%+ of companies in job descriptions 
- Concepts (pipelines, steps, stages) are **the same across all tools** — Jenkins chosen for its ubiquity 

---

## Jenkins — Why It's Popular

Key reasons (interview-ready answer): 

- **Open-source and free** — no licensing cost; preferred for open-source project requirements
- **2,300+ community plugins** — integrates with virtually any tool (Git, Maven, Gradle, Selenium, SonarQube, Docker, Kubernetes, Jira, etc.) 
- **Framework, not just a tool** — fully customizable; custom plugins can be written 
- **Cross-platform** — runs on Windows, Linux, macOS with identical UI 
- **Lightweight and reliable** — fast, trustworthy for production use
- **Strong community support** — thousands of contributors maintaining plugins
- **Scripted builds** — supports both UI-based and code-based pipeline definitions 
- Can function as a **cron server replacement** 

---

## Jenkins Installation — Hands-On

### Prerequisites

- Ubuntu VM (24.04 LTS recommended) with **30 GB disk** 
- Minimum hardware per official docs: sufficient CPU/RAM for team size; Java 21+ required 
- VM created on GCP (Compute Engine → VM Instances → Create Instance) 

### Installation Steps (Official Jenkins Docs)

1. `sudo apt update` — update packages 
2. Install Java: `sudo apt install openjdk-21-jre` — Jenkins is Java-based 
3. Add Jenkins GPG key to keyring 
4. Add Jenkins repository to `/etc/apt/sources.list` so Ubuntu knows where to download Jenkins 
5. `sudo apt update` again 
6. `sudo apt install jenkins` — installs Jenkins (~54 MB) 
7. Verify: `jenkins --version` 

### Accessing Jenkins UI

- Default port: **8080** 
- Navigate to `http://<VM-public-IP>:8080` 
- First-time setup requires **initial admin password** from: `cat /var/lib/jenkins/secrets/initialAdminPassword` 
- Select **"Install Suggested Plugins"** (~80 plugins installed automatically) 
- Set admin credentials → Jenkins dashboard accessible 

### Common Issues & Fixes

|                     Issue                      |                                                Fix                                                 |
|------------------------------------------------|----------------------------------------------------------------------------------------------------|
| UI not loading                                 | Ensure `http://` not `https://`; check firewall/port 8080                                          |
| Port 8080 blocked                              | GCP: Firewall → Edit HTTP rule → Allow all ports; AWS: Security Group → Add inbound rule TCP 8080  |
| Jenkins service not running                    | Run `sudo systemctl start jenkins`; check with `systemctl status jenkins`                          |
| Permission denied on cat command               | Run `sudo -i` to become root user first                                                            |
| VM stopped → Jenkins stopped                   | Manually restart with `sudo systemctl start jenkins` after VM restart                              |
| Organization GCP account firewall restrictions | Use individual account instead                                                                     |

### Verifying Port Listening (Troubleshooting Tip)

- Use `ss` (networking command) to check if port 8080 is listening on the server before investigating firewall — best practice before jumping to VM settings 

### VM Management

- Stop VM when not in use to avoid charges; Jenkins will stop with VM 
- Delete VM after session; recreate fresh for next class using saved Cloud Shell commands 
- GCP Cloud Shell command saved for quick VM recreation each day 

---

## Pending Confirmation

- Client name for the Senior SRE job opportunity — to be disclosed after first interview round 
- Mock interview schedule — confirmed for upcoming session (Wednesday mentioned as prep day) 

---

## Action Items

- **All eligible students (10+ years exp):** Apply to Senior SRE role at Skills Capital email by **28 October, 4 PM**; share email ID with Vikas after applying 
- **All students:** Install Jenkins on a cloud VM and explore the dashboard before next class 
- **All students:** Create/maintain a GitHub repo and make daily commits to build green contribution activity; minimum 10–15 commits/month 
- **All students:** Prepare 2–3 Linux commands or new concepts for **tomorrow's Reverse KT session** 
- **Students without GCP account:** Watch GCP account creation video in the LMS (available in course materials) and create account; 90-day free trial available 
- **Vikas:** Send WhatsApp message with job details, classroom link, and session reminder 
- **Students on LinkedIn leaderboard:** Update profile name and LinkedIn URL in the leaderboard form; post daily on LinkedIn, sync every Friday 

---

## Open Questions

- How to design multi-branch deployment pipelines in GitHub (feature → UAT → main → production) with branch protection rules preventing direct publishing from feature branches — discussed conceptually; full implementation to be covered in Jenkins sessions 
- Kubernetes architecture deep-dive (API server flow when applying deployment YAML, multi-region deployments using node affinity/selectors) — raised by a student post-interview; flagged for upcoming Kubernetes class 
- DevSecOps vs. DevOps pipeline differences — briefly addressed: security tools added on top of existing DevOps pipeline; transition happens after DevOps is stable

# Day-3 DevOps Interview Preparation
## Git & GitHub + Jenkins Overview
**Based on:** Batch-45 session notes — 28 October 2026

This document contains 20 technical interview questions with answers and 20 scenario-based questions with practical answers. Topics are based on the session notes.

---

# Part 1: 20 Technical Interview Questions and Answers

### 1. What is the difference between a public and a private GitHub repository?
**Answer:** A public repository can be viewed by anyone. A private repository restricts access to authorized users. Repository visibility can be changed from the repository settings, and GitHub may require verification for sensitive changes.

### 2. How do you give another developer access to a GitHub repository?
**Answer:** Open the repository's **Settings → Collaborators**, choose **Add people**, and invite the developer using their GitHub account or email. They must accept the invitation. Grant only the access level they need.

### 3. What is a Pull Request (PR)?
**Answer:** A Pull Request proposes changes from one branch to another, such as a feature branch into `main`. It gives teammates a place to review code, discuss changes, run checks, and approve the merge.

### 4. What are branch protection rules?
**Answer:** Branch protection rules apply safeguards to important branches. Examples include requiring a pull request, approvals, successful status checks, resolved review conversations, or signed commits before changes can be merged.

### 5. Why should teams prevent direct pushes to `main`?
**Answer:** Preventing direct pushes encourages code review and automated checks before changes enter the shared stable branch. It reduces the risk of unreviewed or broken code reaching the main branch.

### 6. What are required status checks?
**Answer:** Required status checks are automated checks—such as a build or test job—that must pass before a protected branch can accept a merge. Jenkins can provide these checks when integrated with GitHub.

### 7. What is a GitHub webhook?
**Answer:** A webhook sends an event notification from GitHub to another system when an event occurs, such as a code push. Jenkins can receive that notification and trigger a pipeline.

### 8. What is Poll SCM in Jenkins?
**Answer:** Poll SCM makes Jenkins check a source repository at configured intervals to see whether changes have occurred. If it detects changes, the configured job can run. Unlike a webhook, it relies on periodic checks.

### 9. What is Continuous Integration (CI)?
**Answer:** CI is the practice of automatically building and testing code when developers integrate changes into a shared repository. It gives developers faster feedback when a change causes a build or test failure.

### 10. How does CI improve on a traditional manual build process?
**Answer:** In a manual process, a person may run builds at fixed times, delaying feedback. CI automates builds and tests after changes are pushed, helping teams discover problems sooner and reducing manual effort.

### 11. Why was Jenkins selected for the sessions?
**Answer:** The session notes highlight that Jenkins is open-source, supports many community plugins, works across operating systems, supports scripted pipelines, and has strong community support.

### 12. Is Jenkins only a graphical user interface tool?
**Answer:** No. Jenkins provides a web interface, but build and delivery workflows can also be defined as code using pipeline scripts. This helps teams version, review, and reproduce pipeline changes.

### 13. What are Jenkins plugins?
**Answer:** Plugins extend Jenkins and help it integrate with tools and services such as Git, build tools, testing tools, code-quality tools, containers, and deployment platforms.

### 14. Why does Jenkins need Java?
**Answer:** Jenkins is Java-based, so a compatible Java runtime is required to run it. The session notes use Java 21 or later as the installation prerequisite for the demonstrated setup.

### 15. What are the main steps to install Jenkins on an Ubuntu VM?
**Answer:** Update package lists, install the required Java runtime, add the official Jenkins repository and signing key, update package lists again, install Jenkins, and verify the installation with `jenkins --version`.

### 16. How do you access Jenkins after installation?
**Answer:** Open `http://<VM-public-IP>:8080` after ensuring the service is running and the VM firewall permits inbound TCP traffic on port 8080. Complete the initial setup using the initial admin password.

### 17. Where is the initial Jenkins administrator password stored?
**Answer:** On the installation described in the notes, it is stored at `/var/lib/jenkins/secrets/initialAdminPassword`. Read it with appropriate privileges, for example:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### 18. How do you check whether Jenkins is running on Linux?
**Answer:** Use:
```bash
sudo systemctl status jenkins
```
To start it if needed:
```bash
sudo systemctl start jenkins
```

### 19. How can you check whether port 8080 is listening?
**Answer:** Run:
```bash
sudo ss -lntp | grep ':8080'
```
This checks whether a process is listening on port 8080. If Jenkins is listening but the UI is unreachable, check the VM firewall, cloud firewall rules, and the URL.

### 20. What is `.gitignore`, and why is it important?
**Answer:** `.gitignore` lists files and directories Git should normally ignore when tracking untracked files. It is useful for excluding build output, generated files, and local dependencies. Do not rely on it to remove files already tracked, and never commit secrets or API keys.

---

# Part 2: 20 Scenario-Based Interview Questions and Answers

### 1. A developer pushes directly to `main`, but your team requires code review. How would you prevent this?
**Answer:** Configure branch protection for `main` and require a pull request before merging. Add required approvals and status checks where appropriate. Test the rules with the team's repository permissions.

### 2. A Pull Request cannot be merged because a required check is failing. What would you do?
**Answer:** Open the PR checks and identify the failed build or test. Review the logs, reproduce the failure locally if needed, fix the code or pipeline, push the correction, and wait for the check to pass before merging.

### 3. A developer says their push was rejected because the remote branch contains work they do not have locally. What is a sensible first step?
**Answer:** Fetch or pull the latest changes, review and resolve any conflicts, run tests, and then push again. Avoid using `git push -f` as a quick fix because it can overwrite remote history.

### 4. A commit introduced a bug and you need to undo it while preserving shared history. What would you use?
**Answer:** Use `git revert <commit-id>` to create a new commit that reverses the target commit's changes. This is generally safer for shared branches than rewriting history.

### 5. A developer accidentally committed a local build directory. How should the team prevent it from happening again?
**Answer:** Add the directory pattern to `.gitignore`. If the directory is already tracked, remove it from Git's index while keeping the local files, for example with `git rm -r --cached <directory>`, then commit the change. Check the repository for any accidentally committed secrets separately.

### 6. A repository should be private while a contractor works on it, but only that contractor should have access. What would you do?
**Answer:** Set the repository visibility to private, invite the contractor as a collaborator, grant the minimum required permissions, and verify that the invitation is accepted. Review access when the work ends.

### 7. A team member can view a repository but cannot change settings. Is this necessarily a problem?
**Answer:** No. Repository permissions can be configured so collaborators have different access levels. Check their assigned role and grant additional permissions only if their responsibilities require them.

### 8. You want a Jenkins job to start shortly after a developer pushes code to GitHub. What integration could you configure?
**Answer:** Configure a GitHub webhook to notify Jenkins of push events, and configure the Jenkins job or pipeline to respond to those events. Verify endpoint reachability, webhook delivery status, and Jenkins job configuration.

### 9. A Jenkins job is configured with Poll SCM but does not run immediately after a push. Why?
**Answer:** Poll SCM checks at configured intervals rather than reacting instantly to a push. Confirm the polling schedule, repository URL, credentials, and branch configuration. If near-immediate triggering is needed, consider a webhook.

### 10. A team wants Jenkins to build and test every change before merge. How would you approach it?
**Answer:** Configure a Jenkins pipeline that checks out the relevant code, runs the build and unit tests, and reports the result back to GitHub. Configure the corresponding status check as required by the protected branch before allowing merges.

### 11. Jenkins is installed, but the browser cannot open `http://<VM-public-IP>:8080`. What should you check first?
**Answer:** Check in a logical order:
1. Confirm Jenkins is running with `sudo systemctl status jenkins`.
2. Check whether port 8080 is listening using `sudo ss -lntp | grep ':8080'`.
3. Confirm the URL uses `http://` for the demonstrated setup.
4. Check the cloud firewall or security-group rule for inbound TCP 8080 and any host firewall.
5. Review Jenkins logs if the service is failing.

### 12. Jenkins stopped after a cloud VM was restarted. How do you troubleshoot it?
**Answer:** Check the service with `sudo systemctl status jenkins`. If it is stopped, run `sudo systemctl start jenkins`, then verify the service and port. Review service logs and startup configuration if it repeatedly fails.

### 13. Running `cat /var/lib/jenkins/secrets/initialAdminPassword` returns “Permission denied.” What would you do?
**Answer:** Read the file with appropriate privileges:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Avoid changing the file's permissions broadly just to read it.

### 14. The Jenkins service is active and port 8080 is listening, but the UI is still unreachable from your laptop. What next?
**Answer:** Check whether the VM has a reachable public IP, verify cloud firewall rules allow inbound TCP 8080 from the intended source, and inspect any host firewall or network restrictions. Keep access restricted to trusted IPs rather than opening the port to everyone.

### 15. A company wants to avoid exposing Jenkins to the entire internet during a class or demo. What is a safer setup?
**Answer:** Restrict inbound TCP 8080 to the instructor's or team's trusted IP range, or access Jenkins through a VPN, bastion, or SSH tunnel. Use strong administrator credentials and avoid leaving a demo VM exposed after the session.

### 16. A student installs Jenkins but does not know how to finish the first-time setup. What steps should they follow?
**Answer:** Retrieve the initial admin password from `/var/lib/jenkins/secrets/initialAdminPassword` with suitable privileges, enter it in the Jenkins setup page, install the suggested plugins, create administrator credentials, and confirm that the dashboard loads.

### 17. A build works on one developer's laptop but fails in Jenkins. What should you investigate?
**Answer:** Compare the runtime and tool versions, environment variables, dependencies, working directory, credentials, and commands used locally versus in the pipeline. Read the Jenkins console output and make the build steps reproducible.

### 18. A team is considering force-pushing to fix a shared branch after a bad commit. What should you recommend?
**Answer:** First understand whether the commit is shared and whether history rewriting is allowed. For a shared branch, `git revert <commit-id>` is usually the safer option because it preserves history. Use force push only with a clear plan, authorization, and awareness of its impact on other collaborators.

### 19. A company wants to review bugs and enhancement requests without adopting another tool immediately. Which GitHub feature from the session could help?
**Answer:** GitHub Issues can track bugs, enhancement requests, and support tasks using labels, assignees, and milestones. Teams can use it for basic tracking, while more complex workflows may still need a dedicated project-management tool.

### 20. A team wants to show consistent GitHub activity to recruiters. What is a useful habit, and what should they avoid?
**Answer:** Maintain a real repository of notes, labs, or projects and commit meaningful progress regularly. The session suggested aiming for 10–15 commits per month. Avoid empty or meaningless commits just to create green squares; the quality and clarity of the work matter more.

---

# Quick Revision Checklist

- [ ] Explain public vs. private repositories and collaborator permissions.
- [ ] Explain pull requests and branch protection rules.
- [ ] Compare GitHub webhooks and Jenkins Poll SCM.
- [ ] Explain CI and why automated build/test feedback matters.
- [ ] Describe why Jenkins is used and how plugins extend it.
- [ ] List the main Jenkins installation steps on Ubuntu.
- [ ] Find the initial admin password and complete first-time setup.
- [ ] Troubleshoot Jenkins service, port 8080, and cloud firewall issues.
- [ ] Explain safe use of `git revert`, caution around force push, and `.gitignore`.
- [ ] Describe how Jenkins results can be used as required status checks.

---

**Note:** These questions are based on the supplied session notes. Detailed multi-branch deployment pipelines and deeper Kubernetes architecture were listed as topics for future sessions, so they are not treated here as fully covered implementation material.

