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
	workflow_dispatch -> to run the workflow manually. !To trigger the workflow_dispatch event, your workflow must be in the default branch!
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

Functions:
	- contains
	- startsWith
	- endsWith
	- format
	- join
	- toJson
	- fromJson
	- hashFiles

Status check functions:
	success()
	always()
	cancelled()
	failure()
 ex: steps:
 	- name: test
  	  run: npm test
          if: always() # job/step always runs regardeless the status of previous step/job 

Workflow commands:
	- set env vars: echo "ACTION_ENV=production" >> $GITHUB_ENV
	- adding to system path: echo "/path/to/dir" >> $GITHUB_PATH
	- set output parameters:
	steps: 
	- name: set output
		id : example_step
		run: echo "result=output_value" >> $GITHUB_OUTPUT  
	- name: use output
		run: echo "The output was ${{ steps.example_step.outputs.result }}

	- create debbug message: echo " ::debug:: This is a debug message"
	- group log messages
	- masking values in logs: run: echo "::add-mask::${{secrets.SECRET_VALUE}}"

Contexts = a way to access information about workflow runs, variables, runner env, jobs and steps.  ${{secrets.SECRET_VALUE}} ${{env.DAY_OF_WEEK== 'MONDAY }}

Expressions:
run job/step only if branch is production: if: github.ref == 'refs/heads/production' -> contexts: github, env, vars, job, jobs, steps, runner, secrets, matrix, needs, inputs, strategy.
continue-on-error: true/false, decide if the job continue even if a step fails. by default job fails. if at job level, it will apply to all the steps in the job

cache node dependencies: when making a change in package.json (by installing new dependency like npm install nodemon) the cache is invalidated and new cache key is created in Caches.

github packages: 
integrates with several packages managers like npm, maven repo etc
built in features for storing and managing software packages alongside code on github.
secure publish and share code in organization or publicly'
keeps code and packages together
instead of credentials for each registry (npm.pkg.github.com, maven.pkg.github.com etc) uses GITHUB_TOKEN to provide access to github repository where workflow is defined ghcr.io/OWNER/image_name:version
