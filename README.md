## Manual Trigger

```sh
gh workflow run greet.yml -f name=mona -f greeting=hello -F data=@myfile.txt
echo '{"name":"mona", "greeting":"hello"}' | gh workflow run greet.yml --json
```

## Webhook Event
 
```sh
curl -X POST \
-H "Accept: application/vnd.github+json" \
-H "Authorization: token {PAT} \
-d '{"event_type": "webhook", "client_payload": {"key": "value"} }' \
https://api.github.com/repos/{owner}/{repo}/dispatches
```

workflow = configurable automated process that will run one or more jobs. defined in .github/workflows
actions = reusable tasks that perform specific jobs within a workflow.
jobs = group of steps that execute on the same runner. run in parallel or concurent.
runs = instances of workflow execution triggered by events.
runners = servers that host the env where the jobs are executed, built-in or self-hosted.
marketplace = platform -> find and share reusable actions.
github actions workflow structure: workflow, jobs, steps
github actions jobs are running in paralel by default
needs: build_job -> it's like depends on,used on job level, can be an array if depends on multiple jobs.
Actions : upload a build artifact or download a build artifact -> share artifact from one to job to another. upload the artifact in job1, and than download in job2/3. artifact stored for 90 days by default. can be changed in settings. max size 500mb.
env vars: can be stored at step level, job level, workflow level
secrets can be store at organisation level under profile -> your organisations sau in repo level (can be used by other workflows) (settings -> secret and variables -> actions) sau in env level. 
secrets are reffered in code ${{ secrets.xxx }}
repo level env are reffered in code ${{ vars.xxx }}
triggers:
on:
    push
	schedule: #scheduled time
	  - cron ' 40 5 22 * * ' # minute hour day month dayofweek
	workflow_dispatch -> to run the workflow manually
	repository_dispatch  # trigger the wf via an external HTTP endpoint. 
						 # only trigger a wf run if the wf file is on the default brench.
						 # send a POST request to the rep;s dispatches endpoint
						 # set Accept type for application/vnd.github+json
						 # provide auth token
						 # pass the event type "webhook"

	
If you specify multiple events, only one of those events needs to occur to trigger the worklow.
If multiple triggering events for your workflow occur at the same time, multiple workflow runs will be triggered.

jobs concurency -> ensure only a single job or workflow is running at a time.

confitional keywords for steps:
jobs.<job_id>.if conditional can be used to prevent a job from running unless a condition is met.