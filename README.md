## Lab 5.3

### Automated Deployment

Configure an automated deploy process to deploy your code using a continuous integration tool. Your documentation should describe each setting selected in your configuration file and a description of trigger(s) that start the automated deploy process.

### The Code

The code that is ran as part of the automated workflow is a simple shell script ([`entrypoint.sh`](https://github.com/Mordyfier/hello-github-actions/blob/main/action-a/entrypoint.sh), provided as part of the GitHub Actions tutorial), which prints a Hello World message to the console. This entrypoint is specified in the [`Dockerfile`](https://github.com/Mordyfier/hello-github-actions/blob/main/action-a/Dockerfile):

```Dockerfile
FROM debian:9.5-slim

ADD entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

The Dockerfile is specified as the container for the action specified in the shell script, and the [`action.yml`](https://github.com/Mordyfier/hello-github-actions/blob/main/action-a/action.yml) configuration file below specifies some parameters for the action:

```yml
name: "Hello Actions" # This section contains metadata - description of the action and the author
description: "Greet someone"
author: "octocat@github.com"

inputs: # This section is concerned with configurations regarding the input. 
  MY_NAME: 
    description: "Who to greet"
    required: true # In particular, this line, and the line below, specify that the MY_NAME parameter is essential
    default: "World" # ...and the default value it will take in the absence of input

runs:  # Configuration with regard to how the automation should be run - in this case, Docker
  using: "docker"
  image: "Dockerfile"

branding:
  icon: "mic"
  color: "purple"
  ```
  
 Now that the action has been specified, we can incorporate it into a workflow, and set a trigger for it. For that, we have the file [`main.yml`](https://github.com/Mordyfier/hello-github-actions/blob/main/.github/workflows/main.yml) in `.github/workflows`. There we have some more configuration:
 
 
 ```yml
name: A workflow for my Hello World file # This is again a description of the config file
on: push # The 'on' field, however, specifies the trigger for running the action and - therefore - the script.

jobs:  # the action is called as part of a 'job', which organizes the steps to be executed by the workflow
  build:   
    name: Hello world action
    runs-on: ubuntu-latest
    steps:  
      - uses: actions/checkout@v1
      - uses: ./action-a
        with: # "Mona" is passed as the MY_NAME parameter for the entrypoint shell script.
          MY_NAME: "Mona"
 ```
 
 Since the sole trigger specified is `on: push`, the action is performed whenever a push is performed.
 A sample log from the workflow execution can be seen in the file [`workflow.log`](https://github.com/Mordyfier/hello-github-actions/blob/main/workflow.log).
In particular, in line 169 we can see that the script was executed and output "Hello world my name is Mona".

## Sources Used

I used the provided [GitHub Actions Tutorial](https://lab.github.com/githubtraining/github-actions:-hello-world). 
