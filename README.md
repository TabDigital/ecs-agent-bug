
# ecs-bug

Trying to reproduce a bug on `ECS` where the agent becomes slow and times out.

## Initial setup

You will need

- an ECS cluster
- at least 1 EC2 instance in the cluster
  * running *Amazon Linux AMI 2015.09.g x86_64 ECS HVM GP2* (`ami-7d9bbd1e`)
  * whose instance role includes the `AmazonEC2ContainerServiceforEC2Role` policy

## Reproducing the bug

In your terminal:

```bash
export AWS_DEFAULT_PROFILE=digital-tools    # as defined in ~/.aws
export AWS_DEFAULT_REGION=ap-southeast-2    # target AWS region

./loop --cluster ecs-bug
```

This will create all services listed in [services](services),
and cycle through the images as quickly as possible (waiting for each deployment to be stable).

While this is running, hop on to the `EC2` instance

```bash
# at first, most Docker commands only take a few milliseconds
[ec2-user@ip-0-0-0-0 ~]$ time docker ps
real  0m0.033s
user  0m0.024s
sys   0m0.008s

# give it 15min or so
# and some calls will randomly take a long time to execute
[ec2-user@ip-0-0-0-0 ~]$ time docker ps
real  0m38.700s
user  0m0.020s
sys   0m0.012s
```

You might get 1 slow command following by 10 fast ones. Just keep trying to call `docker ps`.
Note that even after stopping all services, the command is still slow.

You can quickly stop all services using:

```bash
# sets desired_count = 0
./stop --cluster ecs-bug
```
