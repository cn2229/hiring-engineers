Carmen Ng
carmen@columbia.edu

Solution Engineer Technical Exercise:
1. Prerequisites - Setup the environment
	-Successfully set a Vagrant Ubuntu VM (v. 16.04, vagrant@ubuntu-xenial) with Datadog Agent metric reporting. 

2. Collecting Metrics:
Add tags in the Agent config file and show us a screenshot of your host and its tags on the Host Map page in Datadog.
	-Inserted the following line for the tags in the datadog.yaml file. Edited the file using $ sudo vi datadog.yaml 
		
		tags: tagkey_1:value_1, tagkey_2:value_2, tagkey_3:value_3
	
	-screenshot of datadog.yaml file update: https://i.imgur.com/LXidHU6.png
	-screenshot of Host Map in Datadog: https://i.imgur.com/U0V51bM.png

Install a database on your machine (MongoDB, MySQL, or PostgreSQL) and then install the respective Datadog integration for that database.
	-Installed MongoDB v. 3.6 (https://i.imgur.com/tgx7o1B.png & https://i.imgur.com/DPpI9kT.png)

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
	
	
3. Visualizing Data:
Utilize the Datadog API to create a Timeboard that contains:
Your custom metric scoped over your host.
	-Obtained API and APP keys from Datadog API section and set customed graphs (https://i.imgur.com/Esxuvy1.png)
	
Any metric from the Integration on your Database with the anomaly function applied.
	-screenshot of json code: https://i.imgur.com/T7vK5HK.png
	
Your custom metric with the rollup function applied to sum up all the points for the past hour into one bucket
Please be sure, when submitting your hiring challenge, to include the script that you've used to create this Timeboard.
	-screenshot of Timeboard python script: https://i.imgur.com/SRTWTc9.png
	vagrant@ubuntu-xenial:/etc/datadog-agent$ cat timeboard.py
	
	from datadog import initialize, api

	options = {
	    'api_key': '<df6b2b309c3676dcb6b924227781a5c3>',
	    'app_key': '<12288d6b45a04139e1c494e062a60d2e0cd7e34d>'
	}

	initialize(**options)

	title = "My Timeboard"
	description = "Host Metric Scope"
	graphs = [{
	    "definition": {
		"events": [],
		"requests": [
			    {"q": "anomalies(avg:my_metric{*}, 'basic', 2)"},
			    {"q": "avg:my_metric{*}.rollup(sum, 3600)"} #60s*60m=3600=1hr
		 ],
		"viz": "timeseries"
	    },
	    "title":"Host Metric Timeboard"
	}]

	template_variables = [{
	    "name": "host1",
	    "prefix": "host",
	    "default": "host:my-host"
	}]

	read_only = True
	api.Timeboard.create(title=title,
			     description=description,
			     graphs=graphs,
			     template_variables=template_variables,
			     read_only=read_only)


Once this is created, access the Dashboard from your Dashboard List in the UI:
	-screenshot of metric explorer view: https://i.imgur.com/8fRD3Up.png & https://i.imgur.com/zrGsO36.png

Set the Timeboard's timeframe to the past 5 minutes
Take a snapshot of this graph and use the @ notation to send it to yourself.
	-screenshot of snapshot in Datadog: https://i.imgur.com/lF4F8Yt.png
	-screenshot of email: https://i.imgur.com/NxfI9zY.png
	
Bonus Question: What is the Anomaly graph displaying?
	-The anomaly graph is displaying and capturing the peaks and drops deviating from metrics data overtime.

4. Monitoring Data
Since you’ve already caught your test metric going above 800 once, you don’t want to have to continually watch this dashboard to be alerted when it goes above 800 again. So let’s make life easier by creating a monitor.
Create a new Metric Monitor that watches the average of your custom metric (my_metric) and will alert if it’s above the following values over the past 5 minutes:
Warning threshold of 500
Alerting threshold of 800
And also ensure that it will notify you if there is No Data for this query over the past 10m.
Please configure the monitor’s message so that it will:
Send you an email whenever the monitor triggers.
Create different messages based on whether the monitor is in an Alert, Warning, or No Data state.
Include the metric value that caused the monitor to trigger and host ip when the Monitor triggers an Alert state.
When this monitor sends you an email notification, take a screenshot of the email that it sends you.
	-new Metric Monitor set in Datadog (https://i.imgur.com/QJwK2VU.png)
	-screenshot of inputting warning and alert, part 1: https://i.imgur.com/RgzQdma.png
	-screenshot of inputting warning and alert, part 2: https://i.imgur.com/1s7dxCK.png
	-screenshot of message setup: https://i.imgur.com/kO2x3Fl.png
	-screenshot of email alert notification: https://i.imgur.com/iOrD4wq.png

Bonus Question: Since this monitor is going to alert pretty often, you don’t want to be alerted when you are out of the office. Set up two scheduled downtimes for this monitor:
One that silences it from 7pm to 9am daily on M-F,
And one that silences it all day on Sat-Sun.
Make sure that your email is notified when you schedule the downtime and take a screenshot of that notification.
	-Added downtimes under "Manage Downtime" in the Monitor Datadog tab.
	-screenshot of downtime, 7pm-9pm daily on M-F setup: https://i.imgur.com/DQPGhyT.png
	-screenshot of email notification: https://i.imgur.com/RHN88K4.png
	-screenshot of downtime, weekend setup: https://i.imgur.com/7mAXDGW.png
	-screenshot of email notification: https://i.imgur.com/W8Hnhdb.png
	
5. Collecting APM Data:
Note: Using both ddtrace-run and manually inserting the Middleware has been known to cause issues. Please only use one or the other.
Bonus Question: What is the difference between a Service and a Resource?
Provide a link and a screenshot of a Dashboard with both APM and Infrastructure Metrics.
Please include your fully instrumented app in your submission, as well.
	- Unable to complete this section. Encountered various issues and error messages with the setup.

6. Final Question:
Datadog has been used in a lot of creative ways in the past. We’ve written some blog posts about using Datadog to monitor the NYC Subway System, Pokemon Go, and even office restroom availability!
Is there anything creative you would use Datadog for?
	-Given the services that Datadog is offering, I would look into using Datadog to monitor the real estate property and prices for either sell or rent. NYC real estate is pricey, and Datadog can be a resource to help gather various data points from sites to one central location about homes/apartments sells, price decrease/increase, and comparsions of similar properties by type and/or by location.

	
	
