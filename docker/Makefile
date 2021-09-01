
USER = dtltasu

THIS_FILE := $(lastword $(MAKEFILE_LIST))
.PHONY: help build up down stop logs push_ui push_post push_comment
#help:
#        make -pRrq  -f $(THIS_FILE) : 2>/dev/null | awk -v RS= -F: '/^# File/,/^# Finished Make data base/ {if ($$1 !~ "^[#.]") {print $$1}}' | sort | egrep -v -e '^[^[:alnum:]]' -e '^$@$$'
build:
	docker-compose -f docker-compose.yml build $(c)
up:
	docker-compose -f docker-compose.yml up -d $(c)
down:
	docker-compose -f docker-compose.yml down $(c)
stop:
	docker-compose -f docker-compose.yml stop $(c)
logs:
	docker-compose -f docker-compose.yml logs --tail=100 -f $(c)
push_ui:
	docker push $(USER)/ui
push_post:
	docker push $(USER)/post
push_comment:
	docker push $(USER)/comment
build_dockerfile:
	docker build -f example/Dockerfile -t $(USER):prometheus .
