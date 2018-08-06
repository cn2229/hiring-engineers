Carmen Ng
carmen@columbia.edu

Exercise:
1. Prerequisites - Setup the environment
	-Successfully set a Vagrant Ubuntu VM (v. 16.04, vagrant@ubuntu-xenial) with Datadog Agent metric reporting. 

2. Collecting Metrics:
Add tags in the Agent config file and show us a screenshot of your host and its tags on the Host Map page in Datadog.
	-Inserted the following line for the tags in the datadog.yaml file. Edited the file using $ sudo vi datadog.yaml 
		
		tags: tagkey_1:value_1, tagkey_2:value_2, tagkey_3:value_3

Install a database on your machine (MongoDB, MySQL, or PostgreSQL) and then install the respective Datadog integration for that database.
	-Installed MongoDB v. 3.6

Create a custom Agent check that submits a metric named my_metric with a random value between 0 and 1000.
	vagrant@ubuntu-xenial:/etc/datadog-agent/checks.d$ cat my_check.py

	from checks import AgentCheck
	from random import randint

	class My_Check(AgentCheck):
	    def check(self, instance):
		    self.gauge('my_metric',randint(1,1000))

Change your check's collection interval so that it only submits the metric once every 45 seconds.
	vagrant@ubuntu-xenial:/etc/datadog-agent/conf.d$ cat my_check.yaml 

	init_config:

	instances:
	    -  min_collection_interval: 45

Bonus Question Can you change the collection interval without modifying the Python check file you created?
	- Yes, reviewing the documentation it is noted that you can change the collection interval in seconds by editing the my_check.yaml file in conf.d folder. You can simply add the following line under instances "-  min_collection_interval: 45"
	
	
