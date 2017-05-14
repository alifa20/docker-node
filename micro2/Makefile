.PHONY: local release setenv create-docker-reg docker-build publish-image docker-login publish-image-with-tag docker-compose tt docker-down nodejs docker-build-production publish-image-production

# Filenames
REPOSITORY_NAME := dockernodemicro2
AWS_CONFIG_PROFILE := default
DOCKER_REGISTRY_NAME_ADDRESS := 095122161940.dkr.ecr.ap-southeast-2.amazonaws.com
AWS_REGION := ap-southeast-2
TAG_NAME = latest

# get git branch
BRANCH_TAG_NAME=$(shell git describe --abbrev=4 --dirty --always --tags)
RELEASE_DATE := $(shell date +"%Y-%m-%d-%H-%M")

local:
	cd ../; make local

# Creating docker registry on  amazon ECS
create-docker-reg:
	aws ecr create-repository --repository-name $(REPOSITORY_NAME) --profile $(AWS_CONFIG_PROFILE)

# login to docker repository
docker-login:
	# run the output of: `aws ecr get-login --region ap-southeast-2 --profile default`
	$(shell aws ecr get-login --region $(AWS_REGION))

docker-build: 
	npm run build:prod
	$(shell aws ecr get-login --region $(AWS_REGION) --profile $(AWS_CONFIG_PROFILE))
	docker build -t $(REPOSITORY_NAME):$(TAG_NAME) .
	docker tag $(REPOSITORY_NAME):$(TAG_NAME) $(DOCKER_REGISTRY_NAME_ADDRESS)/$(REPOSITORY_NAME):$(TAG_NAME)

docker-build-production: 
	# npm run-s bundle:client bundle:server
	# npm run dist
	# npm run build:prod
	# npm run build:client
	$(shell aws ecr get-login --region $(AWS_REGION) --profile $(AWS_CONFIG_PROFILE))
	docker build -t $(REPOSITORY_NAME):$(TAG_NAME) .
	docker tag $(REPOSITORY_NAME):$(TAG_NAME) $(DOCKER_REGISTRY_NAME_ADDRESS)/$(REPOSITORY_NAME):production

# Publish docker images to private repository
publish-image: docker-build
publish-image: 
	${INFO} "Pushing docker images to repository"
	docker push $(DOCKER_REGISTRY_NAME_ADDRESS)/$(REPOSITORY_NAME):$(TAG_NAME)

publish-image-production: docker-build-production
publish-image-production:
	${INFO} "Pushing docker images to repository"
	docker push $(DOCKER_REGISTRY_NAME_ADDRESS)/$(REPOSITORY_NAME):production

publish-image-with-tag: T
	cd ../; make publish-image-with-tag

docker-compose:
	cd ../; make docker-compose

setenv:
	cd ../; make setenv

release: 
	cd ../; make release

tt:
	${INFO} "tt..."
	cd ../; make test

docker-down:
	- docker stop $$(docker ps -a -q)
	- docker rm $$(docker ps -aq)
	- docker volume rm $$(docker volume ls -f dangling=true)
	- docker rmi -f $$(docker images -f dangling=true)

eb-deploy:
	- eb deploy --version HumaDockerrun.aws 
 
nodejs: export APP_ENV:=development
nodejs: export NODE_ENV:=development
nodejs: export GRIFFIN_URL:=http://localhost:3002
nodejs: export PORT_5050_TCP_PORT:=5050
nodejs: export QUEUE_PORT_5000_TCP_ADDR:=192.168.99.100
nodejs: export DB_PORT_5432_TCP_ADDR:=ali-griffin.cz9jdkeawybk.ap-southeast-2.rds.amazonaws.com
nodejs:
	${INFO} "Running node..." 
	nodemon

# Cosmetics
YELLOW := "\e[1;33m"
NC := "\e[0m"

# Shell Functions
INFO := @bash -c '\
	printf $(YELLOW); \
	echo "=> $$1"; \
	printf $(NC)' SOME_VALUE
