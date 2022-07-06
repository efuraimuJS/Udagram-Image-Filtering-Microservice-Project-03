my-cluster = udagram-project-03-cluster
cluster-region = us-east-1
arn=arn:aws:iam::744555430390:user/com.etbf.project3root
username=project3root
nodename=linux-workers
nodefamily=AmazonLinux2

create_cluster:
	eksctl create cluster --name $(my-cluster) \
	--nodegroup-name $(nodename) \
	--region=$(cluster-region) \
	--nodes-min=2 \
	--nodes-max=3 \
	--asg-access \
	--node-ami-family=$(nodefamily)

delete_cluster:
	eksctl delete cluster --name $(my-cluster) \
	--region=$(cluster-region) \
	--wait


set_context:
	eksctl utils write-kubeconfig \
	--cluster=$(my-cluster) \
	--set-kubeconfig-context=true

create_iamidentitymapping:
	eksctl create iamidentitymapping \
	--username $(username) \
    --cluster $(my-cluster) \
    --region=$(cluster-region) \
    --arn $(arn) \
	--group system:masters \
    --no-duplicate-arns

update_kubeconfig:
	aws eks --region=$(cluster-region) update-kubeconfig \
	--name $(my-cluster)

edit_aws-auth:
	kubectl edit configmap aws-auth -n kube-system

describe_aws-auth:
	kubectl describe -n kube-system configmap/aws-auth

describe_stacks:
	eksctl utils describe-stacks --region=us-east-1 \
	--cluster=$(my-cluster)

deploy_all:
	kubectl apply -f aws-secret.yaml \
	-f env-configmap.yaml \
	-f env-secret.yaml \
	-f backend-feed-deployment.yaml \
	-f backend-feed-service.yaml \
	-f backend-user-deployment.yaml \
	-f backend-user-service.yaml \
	-f reverseproxy-deployment.yaml \
	-f reverseproxy-service.yaml \
	-f frontend-deployment.yaml \
	-f frontend-service.yaml

pods:
	kubectl get pods

compose_down:
	sudo aa-remove-unknown

	docker stop $(docker ps -a -q)
	docker rm $(docker ps -a -q)

	sudo apparmor_parser -r /etc/apparmor.d/*snap-confine*
	sudo apparmor_parser -r /var/lib/snapd/apparmor/profiles/snap-confine*

	sudo apparmor_parser -r /var/lib/snapd/apparmor/profiles/*
	sudo systemctl enable --now apparmor.service
	
destroy_all:
	kubectl delete -f aws-secret.yaml \
	-f env-configmap.yaml \
	-f env-secret.yaml \
	-f backend-feed-deployment.yaml \
	-f backend-feed-service.yaml \
	-f backend-user-deployment.yaml \
	-f backend-user-service.yaml \
	-f reverseproxy-deployment.yaml \
	-f reverseproxy-service.yaml \
	-f frontend-deployment.yaml \
	-f frontend-service.yaml
