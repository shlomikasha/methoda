pipeline {
    agent any

    stages {
        stage('Merge and Jira Actions') {
            steps {
                // Merge your branch into the master branch

                // Extract the Jira issue key from the branch name
                script {
                    def branchName = env.BRANCH_NAME
                    def jiraIssueKey = branchName =~ /[A-Z]+-\d+/
                    if (jiraIssueKey) {
                        jiraIssueKey = jiraIssueKey[0]
                        echo "Jira Issue Key: ${jiraIssueKey}"

                        // Move the Jira issue to "Done" using REST API
                        def jiraUrl = "http://localhost:8080"
                        def jiraIssueId = getJiraIssueId(jiraUrl, jiraIssueKey)
                        moveJiraIssue(jiraUrl, jiraIssueId)
                    } else {
                        error "Unable to extract Jira issue key from branch name."
                    }
                }
            }
        }
    }
}

// Function to get the Jira issue ID using REST API
def getJiraIssueId(jiraUrl, jiraIssueKey) {
    def apiUrl = "${jiraUrl}/rest/api/2/issue/${jiraIssueKey}"
    def response = httpRequest(url: apiUrl, authentication: 'basicAuth', username: 'shlomikasha', password: 'As124870')
    def json = readJSON text: response.content
    return json.id
}

// Function to move the Jira issue to "Done" using REST API
def moveJiraIssue(jiraUrl, jiraIssueId) {
    def apiUrl = "${jiraUrl}/rest/api/2/issue/${jiraIssueId}/transitions"
    def requestBody = '''
    {
        "transition": {
            "id": "31" // ID of the "Done" transition in your Jira workflow
        }
    }
    '''
    httpRequest(url: apiUrl, authentication: 'basicAuth', username: 'shlomikasha', password: 'As124870', contentType: 'application/json', requestBody: requestBody, httpMode: 'POST')
}
