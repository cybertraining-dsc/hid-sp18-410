SWAGGERCODEGEN=java -jar swagger-codegen-cli-2.3.1.jar
YAMLFILE=time.yaml
DESTDIR=server/time/flaskConnexion
CREATED=server
TIMEDIR=time

all: service start 
	@echo "DONE"

docker-all: docker-build docker-start
	@echo "DONE"

docker-build:
	@echo "building the image from docker file..."
	docker build -t krkamatg/cloudmesh-timestamp .
	@echo "image DONE"

docker-pull:
	@echo "pulling the image from dockerhub..."
	docker pull krkamatg/cloudmesh-timestamp
	@echo "image DONE"

docker-start:
	@echo "starting the service in container..."
	docker run -v /home/:/home/ -p 8080:8080 krkamatg/cloudmesh-timestamp

docker-stop:
	@echo "stoping the service..."
	docker stop $$(docker ps -alq)
	@echo "service stopped"

docker-remove:
	@echo "removing the image..."
	docker rmi -f krkamatg/cloudmesh-timestamp
	@echo "image removed"

docker-clean: docker-stop docker-remove
	@echo "DONE"

dest:
	mkdir -p $(DESTDIR)
	 
service: dest
	@echo "creating the service..."
	wget http://central.maven.org/maven2/io/swagger/swagger-codegen-cli/2.3.1/swagger-codegen-cli-2.3.1.jar
	$(SWAGGERCODEGEN) generate \
	-i $(YAMLFILE) \
	-l python-flask \
	-o $(DESTDIR) \
	-D supportPython3=true
	
	rm *.jar
	pip install --upgrade pip

	cp -r *.py $(DESTDIR)/swagger_server/controllers
	cp timestamp.py $(DESTDIR)/swagger_server/models
	cp base_model_.py $(DESTDIR)/swagger_server/models

	cd $(TIMEDIR) && pip install -r requirements.txt && python setup.py install
	
	cd $(DESTDIR) && pip install -r requirements.txt && \
	python setup.py install 
	
	

start:  
	@echo "starting the service..."
	#cp timestamp.pyc $(DESTDIR)/swagger_server/models
	cd $(DESTDIR) && python -m swagger_server

test:
	curl http://localhost:8080/api/timestamp?path=/home

	curl http://localhost:8080/api/timestamp?path=/home/algo2.py
            
stop:
	@echo "stopping the service"
	ps -ef | grep swagger_server | grep -v "grep" | awk '{print $$2}' | xargs kill -9
	@echo "service stopped"

clean:
	@echo "removing service files created"
	rm -rf $(CREATED)

