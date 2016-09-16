# Use NetApp's OnCommand Performance Manager to send stats about your NetApp clusters to InfluxDB and then visualise them in Grafana
Normally, OCPM supports an "external data provider", in the form of Graphite, which is.. kinda outdated. 
Thankfully, InfluxDB, a more modern and optimised time series database provides a Graphite compatibility mode, with the use of templates which "translate" Graphite's format into InfluxDB's.
I have compiled an InfluxDB template, a doc and a simple Grafana dashboard to start using OCPM with InfluxDB with Grafana(the most popular visualisation choice with Graphite, InfluxDB and others).

# Prerequisites 

* NetApp OCPM
* InfluxDB 0.13+
* Grafana 3+
* Network connectivity between Grafana and InfluxDB, and between OCPM and InfluxDB

# Configure InfluxDB to accept and translate OCPM's stats

Before sending data from OCPM, you need to configure InfluxDB how to accept it. In the [influxdb.conf](./influxdb.conf) I have provided an example Graphite configuration to append to your InfluxDB conf file(usually in /etc/influxdb/influxdb.conf).
If you already have an existing graphite configuration, you can only copy the templates part, which is all that matters.
Then restart the InfluxDB service and everything is ready here.

# Configure OCPM to send data to InfluxDB

Follow [this guide](https://library.netapp.com/ecmdocs/ECMP1552962/html/GUID-DD62BEF8-AE57-4382-BF08-D22E94A26A9A.html) from NetApp, inputing the IP/fqdn of your InfluxDB server with the proper port(by default 2003).

# Check data is comming in
Wait a few minutes(at least 5), and then connect to the InfluxShell by running influx on your InfluxDB server. Then choose the graphite database, and select it's measurements:

```
$ # influx
Visit https://enterprise.influxdata.com to register for updates, InfluxDB server management, and monitoring.
Connected to http://localhost:8086 version 0.13.0
InfluxDB shell version: 0.13.0
> use graphite
Using database graphite
> show measurements

```

And here you should get a nice list measurements(aggregates, ports, etc.). If there's nothing, wait some more and retry, and check the InfluxDB logs (by default /var/log/influxdb/influxdb.log) if anything was received. If not, it's probably a network connectity issue.



# Getting started with Grafana
There's an example dashboard in [netapps_overview_ocpm.json](./netapps_overview_ocpm.json). Open it and change every occurence of "InfluxDB graphite" with your datasource name, and then import it to Grafana.


# Advanced
If you want better granularity than OCPM's 5 mins, and more details, you can install NetApp Harvest(downloadable from NetApp's website along with its installation guide), and use [this project](https://github.com/Oxalide/netapp-harvest-influxdb) to achieve similar results(send all the data in InfluxDB instead of Graphite and visualise it with Grafana).


# License
This project is licensed under the MIT License, available in [LICENSE.txt](./LICENSE.txt)


