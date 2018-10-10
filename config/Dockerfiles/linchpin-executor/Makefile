IMAGE_NAME = linchpin-executor
CONTAINER_NAME = $(IMAGE_NAME)-build
REPO_URL ?= https://github.com/herlo/linchpin-executor
REPO_REF ?= master

.PHONY: buildcontainer
buildcontainer:
	buildah bud -t ${IMAGE_NAME} .

.PHONY: testcontainer
testcontainer:
	buildah bud -t $(IMAGE_NAME)-candidate .
	IMAGE_NAME=$(IMAGE_NAME)-candidate CONTAINER_NAME=$(CONTAINER_NAME) ./test/run

