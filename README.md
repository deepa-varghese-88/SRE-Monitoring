# SRE-Monitoring
SRE - Set up monitoring on a python django web app using elasticsearch -> kibana -> filebeats, hosted on GCP

Inside GCP, Created Dedicated VM for monitoring, so as not to overload the main server
- ElasticSearch - Monitoring VM
- Kibana - Monitoring VM http://34.82.254.169:5601/
- Filebeats - Main VM and Postgres_Standby VM

## Elasticsearch ##
In the Monitoring VM, installed and configured ElasticSearch
- Memory Profiling: By default, ElasticSearch uses 1GB heap memory. Set it to use 512M at max config/jvm.options -> -Xmx512m -Xms512m. To prevent overloading of RAM
- Configured ElasticSearch to allow other VMs to access it by changing it’s binding IP address.
- ElasticSearch came with an option to daemonise the process, therefore started it in the background.

## Kibana ##
In the Monitoring VM, installed and configured Kibana
- Set environment variable NODE_OPTIONS = “max-old-space-size=400” (due to issue that by conjecture, was being caused by Kibana’s optimise/compile processes taking up more memory than the default heaph that Kibana specifies, and the low memory setting with which the VM was created. Therefore set a cap on it.) 
- Configured Kibana to allow other VMs to access it by changing its binding IP address.
- Specified the ElasticSearch host: localhost
- Kibana was started as nohup process to allow it to run in the background.

## Filebeats ##
In the Main VM (that hosts Nginx, Web App and Postgres-main server):
- Configured:
1) ElasticSearch url
2) Kibana url
- Enabled: Postgres, Nginx in Filebeat, to enable logging for both ~/modules.d/ -> ./filebeat modules enable nginx postgres

In the Postgres_Standby VM (that hosts the standby Postgres): similar configuration and enabled Postgres logging.
