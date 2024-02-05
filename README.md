# pr-wizard
An theoretical implementation of CI that uses LLM models to review issues, respond with the proposed code fixes, submit a PR to merge it and then closes off the issue.

To build an autonomous GitHub Issue/Continuous Integration (CI) fixer using OpenAI API, you will need a system that can intelligently analyze issues, understand your repository's codebase, generate fixes, and handle GitHub operations such as submitting pull requests (PRs) and closing issues. This involves a combination of GitHub's API for repository interactions, OpenAI's API for understanding and resolving issues, and possibly other tools for integrating these components. Here's a high-level user flow and the technical components required to set up your autonomous fixer:

### 1. Issue Submission

**User Action:** The user submits an issue to your GitHub repository.

### 2. Configuring GitHub Webhook for Issue Events - Triggering the Autonomous Fixer

GitHub webhooks allow you to build or set up integrations that subscribe to certain events in your GitHub repositories. When one of those events is triggered, GitHub sends an HTTP POST payload to the webhook's configured URL. Here’s how to configure it for issue events:

- Go to your repository settings on GitHub.
- Find the "Webhooks" menu and click "Add webhook."
- In the "Payload URL" field, enter the URL where you will host the service responsible for handling the issue events (this could be an AWS Lambda function URL, an Azure Function URL, or your server's endpoint).
- Choose `application/json` as the content type.
- Select "Let me select individual events" and then choose "Issues." This ensures that the webhook gets triggered only for issue-related activities.
- Ensure the "Active" checkbox is selected and then add the webhook.

### 2.2 Setting Up the Receiver (AWS Lambda/Azure Functions/Server)

#### AWS Lambda

AWS Lambda allows you to run code in response to triggers such as changes in data or system state. For handling GitHub webhook events:

- Create a new Lambda function from the AWS Management Console.
- Select a runtime that supports your programming language of choice (e.g., Node.js, Python).
- Implement the logic to process the issue event within the Lambda function. This might involve parsing the JSON payload to understand the issue context, deciding on a course of action, and potentially executing automatic fixes or notifying relevant parties.
- Use the Amazon API Gateway to expose your Lambda function over HTTPS. Provide the endpoint URL to GitHub when setting up the webhook.

#### Azure Functions

Azure Functions is another serverless compute service that works similarly:

- Create a Function App in the Azure portal.
- Choose a runtime for your function (e.g., .NET, Node.js).
- Develop a function to handle the GitHub webhook's HTTP POST request. The function should parse the payload, analyze the issue, and carry out corresponding actions such as auto-responding, assigning labels, or even autoclosing under specific conditions.
- Azure Functions provides a URL for your function out of the box. Use this URL in the GitHub webhook setup.

#### Server Setup

If you prefer running on a dedicated or shared server:

- Set up an endpoint in your web application capable of receiving POST requests. This could be a specific route in a Node.js Express app, a Django view, or an ASP.NET controller, depending on your stack.
- Write the logic within this endpoint to handle the incoming JSON payload from GitHub, similar to the Lambda or Azure Function setup. This includes parsing the issue data and performing actions based on your criteria.
- Ensure your server is publicly accessible over HTTPS (for security) and provide the endpoint URL to GitHub.

### 2.3 Security Considerations

- Verify the request origin to ensure the POST requests to your endpoint come from GitHub. This can be achieved by validating the payload against the secret you configure in the GitHub webhook settings.
- Use HTTPS to secure the data in transit.
- Consider rate limiting and logging to manage and monitor incoming requests.

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
