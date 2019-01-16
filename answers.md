Your answers to the questions go here.

# Lewis Reeves Solution Engineer Answers

## Host Agent Setup Docker
##### Host Config File
![Alt text](Host_config_file-datadog.yaml.PNG?raw=true "Lews Host config file")
##### Host UI Tags Applied
![Alt text](/dd_host1_withTags.PNG?raw=true "Lews Host with Tags")

> The Hostname and Tags can be altered using docker run command with variables like `-e DD_HOSTNAME=scooby.dooobedoo -e DD_TAGS=puppy-power_env:scrappy_prod` 

## Database Setup PostGres
> Docker configured to run with variables ` -v /opt/datadog-agent-conf.d:/conf.d:ro -v /opt/datadog-agent-checks.d:/checks.d:ro ` allows integrations to be pushed into the dd-agent

> for postgres copied Postgres.yaml>> /opt/datadog-agent-conf.d

##### Postgres Integration yaml conf used

`init_config:
 instances:
    host: postgres1
    port: 5432
    username: datadog
    password: password `

##### Some additional commands executed

> `create user datadog with password 'password';
grant pg_monitor to datadog;`

##### test db connection with datadog user

> `sudo docker run -it --rm --network postgres-network postgres psql -h postgres1 -U datadog postgres -c "select * from pg_stat_database LIMIT(1);" && echo -e "\e[0;32mPostgres connection - OK\e[0m" || echo -e "\e[0;31mCannot connect to Postgres\e[0m"
Postgres connection - OK`

##### Postgres Metrics in UI

![Alt text](/postgres_metrics.PNG?raw=true "Postgres metrics dashboard")

##### Custom Agent Check

> 1. Created and copied mycheck.yaml>> /opt/datadog-agent-conf.d
> 2. Created and copied mycheck.py>> /opt/datadog-agent-checks.d
> 3. Restarted dd-agent

##### mycheck.py
> ` try:
 except ImportError:
     from datadog_checks.checks import AgentCheck
__version__ = "1.0.0" class mycheck(AgentCheck):
    def check(self, instance):
        self.gauge('my_metric', sum)`
        
##### mycheck.yaml
> `init_config:
instances: min_collection_interval: 45`

###### Bonus Question Can you change the collection interval without modifying the Python check file you created?
> The collection interval can be only be changed in the conf.d yaml file

## Visualising Data

##### Timeboard Script Used to create Timeboard via API

``` 
api_key=4e5ff588b984e056b27e117eaceb4e16
app_key=8c59015e3d1cb3da16a129b5838f0c2f2edd42e5

curl  -X POST -H "Content-type: application/json" \
-d '{

      "graphs" : [{
          "title": "Avg of my_metric over scooby.dooobedoo",
          "definition": {
              "events": [],
              "requests": [
                  {"q":"avg:my_metric{host:scooby.dooobedoo}"}
              ],
              "viz": "timeseries"
          }
      },
          {
          "title": "Avg of postgresql.percent_usage_connections by host",
          "definition": {
              "events": [],
              "requests": [
                  {"q": "anomalies(avg:postgresql.percent_usage_connections{*}, 'basic', 2)"}
              ],
              "viz": "timeseries"
          }
      },
          {
          "title": "Avg of my_metric over host:scooby.dooobedoo by puppy-power_env",
          "definition": {
              "events": [],
              "requests": [
                  {"q":"avg:my_metric{host:scooby.dooobedoo} by {puppy-power_env}.rollup(sum, 3600)"}
              ],
              "viz": "query_value"
          }
      }],
      "title" : "Lewis_API Timeboard",
      "description" : "A dashboard made by Lew",
      "template_variables": [{
          "name": "scooby.dooobedoo",
          "prefix": "host",
          "default": "scooby.dooobedoo"
      }],
      "read_only": "True"
}' \
"https://api.datadoghq.com/api/v1/dash?api_key=${api_key}&application_key=${app_key}"
```
##### Timeboard Snapshot 5 mins
![Alt text](/sendsnashot.PNG?raw=true "Snapshopwith@notation")

##### Timeboard Snapshot 5 mins with anomaly
![Alt text](/snap_with_anomaly.PNG?raw=true "Snapshopwitanomaly")

###### Bonus Question: What is the Anomaly graph displaying?
> The anomaly graph shows that the average number of connections to the database
has spiked outside 2 standard deviations and the avg calculated from retrospective
moving avg

## Monitor Data

##### Manage Monitors Config 1
![Alt text](/Monitors_config1.PNG?raw=true "Mad_monitor1")

##### Manage Monitors Config 2
![Alt text](/Monitors_config2.PNG?raw=true "Mad_monitor2")

##### Manage Monitors Schedule Email
![Alt text](/scheduledt.PNG?raw=true "schedule_email")

##### Manage Monitors Schedule Email 2
![Alt text](/scheduledt2.PNG?raw=true "Schedule_email2")

##### Manage Monitors Schedule Alert Recover Email
![Alt text](/Alert_recov.PNG?raw=true "Alert_Recover")

## Flask App

> Please check out the implemted app at my page

[This is the Link to my flask app repo](https://github.com/Reevzee/dd_Flask_app "Reevzee Flask App")

##### Flask App Dash
![Alt text](/Flaskapp.PNG?raw=true "Flask app dash")

##### Flask app Dash
![Alt text](/flaskapp2.PNG?raw=true "Flask Dash")

#### Bonus Question: What is the difference between a Service and a Resource?

> A Service is a Web application or DB and the Resource is the URI or the Select statment being made to that Service

#### Datadog has been used in a lot of creative ways in the past. We’ve written some blog posts about using Datadog to monitor the NYC Subway System, Pokemon Go, and even office restroom availability!  Is there anything creative you would use Datadog for?

> It would be great to use DD for home smart home devices if it hasnt been done already!



      





      
