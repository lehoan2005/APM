# Disclaimer

**Disclaimer: The information presented in this article is for educational/general information purposes only. Any scripts or examples are presented where-is, as-is, and are unsupported by Oracle Support and Development. Always refer to the official oracle documentation and other necessary documentation for offical steps. Steps here are not actively maintain, refer to commit dates in git**

# OCI Apm for Weblogic

## High Level Steps

1. Proision apm domain with the private abnd puiblic data key and url
https://docs.oracle.com/en-us/iaas/application-performance-monitoring/doc/create-apm-domain.html

2. Download the agent and provision the agent in the linux machine of wls (weblogic)
https://docs.oracle.com/en-us/iaas/application-performance-monitoring/doc/provision-apm-java-agent.html

3. Add the jar into the class path and restart
https://docs.oracle.com/en-us/iaas/application-performance-monitoring/doc/deploy-apm-java-agent.html#GUID-081D2588-8A46-46CA-9297-3654002702D0

4. Configure sampling. Defayult sampling is 250ms. Meaning it capture the snapshot of ongoing program at 250ms interval. Meaning anything slower than 250ms will be capture. Anything faster than 250 ms will not.
https://docs.oracle.com/en-us/iaas/application-performance-monitoring/doc/configure-apm-sampling-apm-agent.html

5. Enable thread snapshot default disable
https://docs.oracle.com/en-us/iaas/application-performance-monitoring/doc/configure-thread-snapshots-apm-agent.html#GUID-F0D28A2F-8F34-433A-9F1A-AD1959DA374A__GUID-C2994BC7-3AA6-45AE-BB49-E1AED09123D7

6. out of the box wls metrics
https://docs.oracle.com/en-us/iaas/application-performance-monitoring/doc/application-performance-monitoring-metrics.html

7. Additonal jmx metrics can also be added
https://docs.oracle.com/en-us/iaas/application-performance-monitoring/doc/metrics-collection.html#GUID-9EBB6AFA-109F-4FFE-9FAC-17ADD6FD15C2


## Provision Apm

1. Go to Observability & Management -> Application Performance Monitoring-> Administration

![example](https://github.com/wenjian80/wlsapm/blob/main/navigateapm.JPG)

2. Click create apm domain and name as whatever name you want.

![example](https://github.com/wenjian80/wlsapm/blob/main/createapmdomain.JPG)

3. Jot down the endpoint and private public key.

![example](https://github.com/wenjian80/wlsapm/blob/main/apmkey.JPG)

4. Download the agent and transfer to your weblogic server

![example](https://github.com/wenjian80/wlsapm/blob/main/downloadagent.JPG)

## Install apm-agent

1. I am install on /u01/apm folder and below is the command

```
[opc@wlstestinstance u01]$ /u01/jdk1.8.0_202/bin/j

APM Agent Installer log file can be found at /u01/ApmAgentInstall.log

Action [provision-agent] completed successfully
********************************************
APM Agent has been provisioned successfully. To complete the setup, you need to
modify your destination's appserver startup script so that the APM Agent you
have just deployed can be started. Here is the general information on the
changes.

1. Make a backup copy of your appserver startup script.
2. Edit appserver startup script to add -javaagent argument of APM Agent
bootstrap library to java executable. For example:
    java -javaagent:<destination>/oracle-apm-agent/bootstrap/ApmAgent.jar ${JAVA_OPTIONS} -cp <class_path> ... ...
3. Stop and restart your appserver.

Additional configuration such as proxy settings, can be set by editing
<destination>/oracle-apm-agent/config/<version>/AgentConfig.properties

For more information, see
    https://docs.oracle.com/iaas/application-performance-monitoring/doc/deploy-apm-java-agent.html
```

2. Enabled thread sampling. Open /u01/apm/oracle-apm-agent/config/1.9.20.29/AgentConfig.properties amd enabled the thread snapshot.

![example](https://github.com/wenjian80/wlsapm/blob/main/threadsnapshot.JPG)

## Set weblogic path

1. There is various way to do that. if you are using node manager you can set in weblogic console server start or manualy in start weblogic start script. We are adding the below to include for managed server 1 which is named as ms1. We ibnclude the apmagent jar, put where the agent logs supposedt to be and we give a display name call ms1.

```
if [ "${SERVER_NAME}" == "ms1" ] ; then
        JAVA_OPTIONS="${JAVA_OPTIONS} -javaagent:/u01/apm/oracle-apm-agent/bootstrap/ApmAgent.jar -Dcom.oracle.apm.agent.log.dir=/u01/apm/oracle-apm-agent/log -Dcom.oracle.apm.agent.resource.display.name=ms1"
fi

```
![example](https://github.com/wenjian80/wlsapm/blob/main/addpath.JPG)


## MOnitoring

1. wls server need to be restart to take effect. We are deploying a health check app which you can download in the git to ms1. 
2. From trace explorer, we include the display name as there may be many managed server so we can see the display name shown in trace explorer.

![example](https://github.com/wenjian80/wlsapm/blob/main/apmspan.JPG)

3. You could also montir the service metrics from metric explorer. You could then create various dashbaord and alerts/notifcation based on these metrics

![example](https://github.com/wenjian80/wlsapm/blob/main/metricexplorer1.JPG)

![example](https://github.com/wenjian80/wlsapm/blob/main/metrics.JPG)





