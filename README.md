# GHEventToTeamsNotifier

Converts GitHub event payloads to customizable cards that post to your Teams channel


## Creating the Incoming WebHook on Teams

[Here are the official docs](https://docs.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/add-incoming-webhook) from Microsoft on how to setup the Incoming WebHook app for your channel.  Copy the url it generates, you'll need it later.


## Setting up the AWS API Gateway

1. Navigate to API Gateway in AWS
2. Click 'Create API'
3. Choose 'Import' from 'REST API'
4. Protocol should be 'REST' and use 'Import from Swagger or Open API 3'.
5. Import the file 'APIGatewaySpec.yaml' from this repo.  (Feel free to edit endpoints you want to add/delete.  You can also make edits later in the console.)
6. Click on the 'POST' method under the 'pull-request' resource.
7. Choose 'HTTP'
8. Paste your URL from the Incoming WebHook setup you did earlier in Teams.
9. Click 'Integration Request'
10. Click 'Mapping Templates'
11. I choose 'Never' here but it's up to you.  There's not really a reason to pass the content through to Teams if it doesn't match the template format.
12. Click 'Add mapping template'.  
13. Type 'application/json' and click the chckmark.
14. Paste the content from the file 'pull-request.vtl' in this repo.
15. Make modifications as needed (see references).  By default it will only post when a PR is opened.
16. Save!
17. Repeat steps 6-15 for each resource you want to utilize.  Delete resources you don't want.  Contribute to this repo if you make additions or new templates!
18. When finished, click 'actions' dropdown, then 'deploy API'.
19. Click 'deployment stage', 'new stage' (assuming this is your first time deploying), give it the name 'Prod' or similar, type optional descriptions.
20. Click 'deploy'. (you must either do steps 6-15 for each resource or delete them or you'll get an error)
21. You should be taken to the 'stages' view where you can now see your 'invoke URL'.  Copy it.  It should look something like this: https://abcde12345.execute-api.us-east.amazonaws.com/Prod


## Setting up the GitHub Webhook

1. Navigate to your repo in GitHub
2. Go to 'Settings'
3. Go to 'Webhooks'
4. Click 'Add Webhook'
5. Paste your AWS API Gateway URL here.  If you set up multiple events in the Gateway, you'll need to add multiple webhooks.
6. Set 'content type' to 'application/json'
7. Setting up additional security is beyond the scope of this quick tutorial.
8. Click 'let me select individual events'
9. Select the event that matches the endpoint you setup.
10. Click 'add webhook'
11. Don't be worried if GitHub shows your notification failed to deliver.  It will likely only be a success if it passes the logic in your template.  For troubleshooting, use the 'test' feature in the API Gateway to paste payloads.


## References

- [Incoming WebHook docs](https://docs.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/add-incoming-webhook)
- [GH Pull Request payload docs](https://developer.github.com/v3/activity/events/types/#pullrequestevent)
- [GH Pull Request Review payload docs](https://developer.github.com/v3/activity/events/types/#pullrequestreviewevent)
- [GH Issues payload docs](https://developer.github.com/v3/activity/events/types/#issuesevent)
- [GH Ping Event payload docs](https://developer.github.com/webhooks/#ping-event)(if you want to know when someone first sets up their webhook)
- [Velocity Template Language Reference](https://velocity.apache.org/engine/1.7/vtl-reference.html)
- [Message card playground](https://messagecardplayground.azurewebsites.net)