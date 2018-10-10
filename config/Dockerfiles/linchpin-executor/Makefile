IMAGE_NAME = linchpin-executor
CONTAINER_NAME = $(IMAGE_NAME)-build
REPO_URL ?= https://github.com/herlo/linchpin-executor
REPO_REF ?= master
CONTAINER_VERSION ?= latest

.PHONY: buildcontainer
buildcontainer:
	sed -i "s/CONTAINER_VERSION/$(CONTAINER_VERSION)/g" ./Dockerfile
	cat ./Dockerfile
	buildah bud -t ${IMAGE_NAME} .

.PHONY: testcontainer
testcontainer:
	sed -i "s/CONTAINER_VERSION/$(CONTAINER_VERSION)/g" ./Dockerfile
	cat ./Dockerfile
	buildah bud -t $(IMAGE_NAME)-candidate .
	IMAGE_NAME=$(IMAGE_NAME)-candidate CONTAINER_NAME=$(CONTAINER_NAME) ./test/run

