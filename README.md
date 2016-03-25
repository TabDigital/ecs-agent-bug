
# ecs-bug

Trying to reproduce a bug on `ECS` where the agent becomes slow and times out.

### Initial setup

You will need

- an ECS cluster
- at least 1 EC2 instance in the cluster
- the instance role to have the appropriate policies, including access to ECR


Then in your terminal

```bash
# setup aws access
export AWS_DEFAULT_PROFILE=digital-tools
export AWS_DEFAULT_REGION=us-east-1
eval $(aws ecr get-login)
```

The actual test is a 2 step process

- generate lots of different images on ECR
- run many deployments in parallel using the images above

### 1. Creating images

- creates an ECR repository, if required
- pushes many tags to it (busybox, 1Mb each)

```bash
./create-image --name foo --tags 1:10

# if no name specified, creates a random name
# if no tag range specified, creates 10 tags
```

*current limitation: you cannot run multiple `create-image` in parallel*

### 2. Deploying

- create a service based on the image above
- deploy the specified tag range

```bash
./deploy --cluster test --image foo --tags 1:10
```

You can then run several `deploy` commands in parallel on different images.

### 3. Stopping the test

When you're finished, you can run this to stop all services & deployments.

```bash
./stop --cluster test --image foo
```
