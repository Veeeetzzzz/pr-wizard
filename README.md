# pr-wizard
An theoretical implementation of CI that uses LLM models to review issues, respond with the proposed code fixes, submit a PR to merge it and then closes off the issue.

To build an autonomous GitHub Issue/Continuous Integration (CI) fixer using OpenAI API, you will need a system that can intelligently analyze issues, understand your repository's codebase, generate fixes, and handle GitHub operations such as submitting pull requests (PRs) and closing issues. This involves a combination of GitHub's API for repository interactions, OpenAI's API for understanding and resolving issues, and possibly other tools for integrating these components. Here's a high-level user flow and the technical components required to set up your autonomous fixer:

### 1. Issue Submission

**User Action:** The user submits an issue to your GitHub repository.

### 2. Triggering the Autonomous Fixer

**GitHub Webhook:** Set up a webhook in your GitHub repository to listen for issue-related events. When an issue is created, the webhook triggers a process in your system (via an HTTP POST request). This process could be hosted on a cloud function or a web server.

- **Tools & Services:** AWS Lambda/Azure Functions for cloud functions, or any server capable of receiving HTTP requests.

### 3. Issue and Repository Analysis

**Fetching Issue Details:** Upon receiving a trigger, your system uses the GitHub API to fetch the newly created issue's details.

**Repository Scanning:** Simultaneously, your system utilizes the GitHub API to clone or scan the repository, understanding the file structure and contents relevant to the issue.

- **GitHub API**: For cloning the repository, fetching file structures, and reading contents.

**Analyzing Issue with OpenAI API:** Use the issue details and the relevant parts of your repository's code to generate a query for the OpenAI API. This involves crafting a prompt that succinctly describes the problem and provides necessary context for generating a fix.

- **OpenAI API**: Use suitable models from OpenAI (like Codex) to generate code fixes or suggestions based on the issue description and code context.

### 4. Generating and Testing the Fix

**Autonomous Fix Generation:** The OpenAI API returns a suggested fix or modification. You'll need to implement additional logic to:

- Parse the suggestion into code.
- Create a new branch in your repository for the fix.

**Testing The Fix:** Integrate with a CI tool to automatically test the newly generated fix. Ensure it passes all the necessary checks.

- **Tools & Services:** Github Actions, Jenkins, Travis CI, etc., for automated testing.

### 5. Submitting a Pull Request

**PR Creation:** If the fix passes all tests, use the GitHub API to commit the changes, push the new branch, and create a pull request against the main branch of your repository. 

- **GitHub API**: To handle git operations and PR creation.

### 6. Review & Merge

**Automated/Manual Review:** Depending on your confidence level in the autonomous fixer, this step could be automated or require human oversight. If the PR looks good, it could be merged automatically or after manual review.

### 7. Closing the Issue

**Issue Resolution:** Once the PR is merged, automatically close the issue that was originally submitted, marking it as resolved.

- **GitHub API**: To close the issue upon successful merge of the PR.

### General Considerations

- **Authentication:** Your system will require proper authentication with GitHub. Generating a personal access token or installing a GitHub App with the necessary permissions to read issues, pull repository data, commit changes, and manage PRs and webhooks is essential.
- **Security:** Given that your system will potentially modify code, ensuring only trusted users can trigger this process and implementing stringent code validation to prevent malicious input or undesired outcomes is crucial.
- **Rate Limits:** Be aware of GitHub API and OpenAI API rate limits. Efficient use of API calls and handling rate limit errors gracefully are important.
