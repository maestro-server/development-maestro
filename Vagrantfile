# -*- mode: ruby -*-
# vi: set ft=ruby :


Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu-docker"
  config.vm.box_url = "https://github.com/jose-lpa/packer-ubuntu_lts/releases/download/v3.1/ubuntu-16.04.box"

  config.vm.provision "shell", inline: "docker network create --driver bridge net_maestro || true" 
  config.vm.provision "shell", inline: "docker volume create mondovol || true" 
  
  config.vm.provision "docker" do |d|
    d.run "client", 
      image: "maestroserver/client-maestro", 
      args: "-p 80:80 -e 'API_URL=http://localhost:8888' --name client",
      auto_assign_name: false
    
	d.run "server", 
      image: "maestroserver/server-maestro", 
      args: "-p 8888:8888  -e 'MAESTRO_MONGO_URI=mongodb/maestro-client' -e 'MAESTRO_DISCOVERY_URI=http://discovery:5000' -e 'MAESTRO_REPORT_URI=http://reports:5000' --net=net_maestro --name server",
      auto_assign_name: false
    
	d.run "discovery", 
      image: "maestroserver/discovery-maestro", 
      args: "-p 5000:5000 --net=net_maestro -e 'MAESTRO_DATA_URI=http://data:5000' -e 'CELERY_BROKER_URL=amqp://rabbitmq:5672' --name discovery",
      auto_assign_name: false
    
	d.run "discovery-worker", 
      image: "maestroserver/discovery-maestro-celery", 
      args: "--net=net_maestro -e 'MAESTRO_DATA_URI=http://data:5000' -e 'CELERY_BROKER_URL=amqp://rabbitmq:5672' --name discovery_worker",
      auto_assign_name: false
    
	d.run "reports", 
      image: "maestroserver/reports-maestro", 
      args: "-p 5005:5005 --net=net_maestro -e 'MAESTRO_DATA_URI=http://data:5000' -e 'CELERY_BROKER_URL=amqp://rabbitmq:5672' --name reports",
      auto_assign_name: false
    
	d.run "reports-worker", 
      image: "maestroserver/reports-maestro-celery", 
      args: "--net=net_maestro -e 'MAESTRO_DATA_URI=http://data:5000' -e 'CELERY_BROKER_URL=amqp://rabbitmq:5672' --name reports_worker",
      auto_assign_name: false
	  
	d.run "scheduler", 
      image: "maestroserver/scheduler-maestro", 
      args: "--net=net_maestro -e 'MAESTRO_MONGO_URI=mongodb' -e 'MAESTRO_MONGO_DATABASE=maestro-client' -e 'MAESTRO_DATA_URI=http://data:5000' -e 'CELERY_BROKER_URL=amqp://rabbitmq:5672' --name scheduler",
      auto_assign_name: false
    
	d.run "scheduler-worker", 
      image: "maestroserver/scheduler-maestro-celery", 
      args: "--net=net_maestro -e 'MAESTRO_DATA_URI=http://data:5000' -e 'CELERY_BROKER_URL=amqp://rabbitmq:5672' --name scheduler_worker",
      auto_assign_name: false
	  
	d.run "data", 
      image: "maestroserver/data-maestro", 
      args: "-p 5010:5010 --net=net_maestro -e 'MAESTRO_MONGO_URI=mongodb' -e 'MAESTRO_MONGO_DATABASE=maestro-client' --name data",
      auto_assign_name: false
	  
	d.run "mongodb", 
      image: "mongo", 
      args: "-p 27017 --net=net_maestro --name mongodb",
      auto_assign_name: false  
	  
	d.run "rabbitmq", 
      image: "rabbitmq:3-management", 
      args: "-p 15672:15672 -p 5672:5672 --name rabbitmq",
      auto_assign_name: false  
  end
  
  
end