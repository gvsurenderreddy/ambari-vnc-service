#### Developer Quickstart on HDP Sandbox using Ambari Stacks
An Ambari Stack service package for VNC Server with the ability to install developer tools like Eclipse/IntelliJ/Maven as well to 'remote desktop' to the sandbox and quickly start developing on HDP Hadoop.
Also includes the option to install the [Spark 1.2.0 Tech Preview](http://hortonworks.com/hadoop-tutorial/using-apache-spark-hdp/)

##### Contents

- [Setup VNC stack](https://github.com/abajwa-hw/vnc-stack#setup-vnc-stack)
- [Connect to VNC](https://github.com/abajwa-hw/vnc-stack#connect-to-vnc-server)
  - [Connect via VNC client](https://github.com/abajwa-hw/vnc-stack#connect-via-vnc-client)
  - [Connect via browser](https://github.com/abajwa-hw/vnc-stack#connect-via-browser)
  - [Connect via Ambari view](https://github.com/abajwa-hw/vnc-stack#connect-via-ambari-view)  
- [Getting started with Eclipse/IntelliJ](https://github.com/abajwa-hw/vnc-stack#getting-started-with-eclipseintellij)
- [Getting started with Storm and Maven in Eclipse environment on HDP](https://github.com/abajwa-hw/vnc-stack#getting-started-with-storm-and-maven-in-eclipse-environment)
- [Getting started with Spark TP on HDP](https://github.com/abajwa-hw/vnc-stack#getting-started-with-spark-tp-on-hdp)
- [Getting started with Data science with iPython on HDP](https://github.com/abajwa-hw/vnc-stack#getting-started-with-ipython-notebook-on-hdp)

----------------------

##### Setup VNC stack

- Download HDP 2.2 sandbox VM image (Sandbox_HDP_2.2_VMware.ova) from [Hortonworks website](http://hortonworks.com/products/hortonworks-sandbox/)
- Import Sandbox_HDP_2.2_VMware.ova into VMWare and set the VM memory size to 8GB
- Now start the VM
- After it boots up, find the IP address of the VM and add an entry into your machines hosts file e.g.
```
192.168.191.241 sandbox.hortonworks.com sandbox    
```
- Connect to the VM via SSH (password hadoop) and start Ambari server
```
ssh root@sandbox.hortonworks.com
/root/start_ambari.sh
```

- To deploy the VNC stack, run below
```
cd /var/lib/ambari-server/resources/stacks/HDP/2.2/services
git clone https://github.com/abajwa-hw/vnc-stack.git   
```
- Restart Ambari
```
#on HDP 2.2 sandbox
sudo service ambari restart

#on other HDP 2.2 setups
sudo service ambari-server restart
```
- Then you can click on 'Add Service' from the 'Actions' dropdown menu in the bottom left of the Ambari dashboard:

On bottom left -> Actions -> Add service -> check VNC Server -> Next -> Next -> Enter password -> Next -> Deploy
![Image](../master/screenshots/screenshot-vnc-config.png?raw=true)

  - ~~Note that currently you cant change these configurations after installing the stack (this is WIP)~~
  - ~~To change the geometry you can edit this file /etc/sysconfig/vncservers~~
  - You can also remove the stack using the steps below and re-install with correct settings
- On successful deployment you will see the VNC service as part of Ambari stack and will be able to start/stop the service from here:
![Image](../master/screenshots/screenshot-vnc-stack.png?raw=true)

- When you've completed the install process, VNC server will be available at your VM's IP on display 1 with the password you setup.

- One benefit to wrapping the component in Ambari service is that you can now monitor/manage this service remotely via REST API
```
export SERVICE=VNC
export PASSWORD=admin
export AMBARI_HOST=sandbox.hortonworks.com
export CLUSTER=Sandbox

#get service status
curl -u admin:$PASSWORD -i -H 'X-Requested-By: ambari' -X GET http://$AMBARI_HOST:8080/api/v1/clusters/$CLUSTER/services/$SERVICE

#start service
curl -u admin:$PASSWORD -i -H 'X-Requested-By: ambari' -X PUT -d '{"RequestInfo": {"context" :"Start $SERVICE via REST"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' http://$AMBARI_HOST:8080/api/v1/clusters/$CLUSTER/services/$SERVICE

#stop service
curl -u admin:$PASSWORD -i -H 'X-Requested-By: ambari' -X PUT -d '{"RequestInfo": {"context" :"Stop $SERVICE via REST"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' http://$AMBARI_HOST:8080/api/v1/clusters/$CLUSTER/services/$SERVICE
```

----------------------

##### Connect to VNC server

###### Connect via VNC client 

- Option 1: install [Chicken of the VNC](http://sourceforge.net/projects/chicken/) client on your Mac and use it to connect. On windows you can also install [Tight VNC](http://www.tightvnc.com/) or [UltraVNC](http://www.uvnc.com/) clients to do the same.
![Image](../master/screenshots/screenshot-vnc-clientsetup.png?raw=true)
- Note that:
   
  - For VirtualBox users, you will need to forward port 5901 to avoid connection refused errors.
  - You may need to stop your firewall as well:
  ```
  service iptables save
  service iptables stop
  chkconfig iptables off
  ```
    
  - On logging in you will see the CentOS desktop running on the sandbox
  ![Image](../master/screenshots/screenshot-vnc-clientlogin.png?raw=true)

###### Connect via browser 
  
- Option 2: You can also configure using your browser as a VNC client via Java applet
  - Check your [browser supports Java](https://java.com/en/download/help/enable_browser.xml) and [test it](http://java.com/en/download/help/testvm.xml). If not, [fix it](http://java.com/en/download/help/troubleshoot_java.xml) 
  - Open your [Java control panel](https://www.java.com/en/download/help/mac_controlpanel.xml) and add exception for sandbox.hortonworks.com
  - Allow Java applets permissions on your local Mac (you should revert this change after you are done with VNC)
   ```
   sudo vi "/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/lib/security/java.policy"
   #add permission line below the grant
   grant {
           permission java.security.AllPermission;
   ```
  - Restart browser and navigate to http://sandbox.hortonworks.com:5801 
  - If all goes well you should see a Java applet in your browser requesting your VNC password. Enter hadoop
  - ![Image](../master/screenshots/screenshot-java-applet.png?raw=true)
  
###### Connect via Ambari view
   
  - To **remote desktop into your sandbox from within Ambari**, you can also setup an [Ambari iFrame view](https://github.com/abajwa-hw/iframe-view) and point it to http://sandbox.hortonworks.com:5801:
  ![Image](../master/screenshots/screenshot-VNC-view.png?raw=true)

-------------------

#### Getting started with Eclipse/IntelliJ

- To start Eclipse, click the eclipse shortcut 
![Image](../master/screenshots/screenshot-vnc-eclipsestarted.png?raw=true)

- To start IntelliJ, click the intellij shortcut 
![Image](../master/screenshots/screenshot-IntelliJ.png?raw=true)

- To remove the VNC service: 
  - Stop the service via Ambari
  - Delete the service
  
    ```
    curl -u admin:admin -i -H 'X-Requested-By: ambari' -X DELETE http://sandbox.hortonworks.com:8080/api/v1/clusters/Sandbox/services/VNC
    ```
  - Remove artifacts 
  
    ```
    /var/lib/ambari-server/resources/stacks/HDP/2.2/services/vnc-stack/remove.sh
    ```

-----------------

#### Getting started with Storm and Maven in Eclipse environment

- As a next step, try setting up the Twitter storm topology project in Eclipse to become familiar with how it works.
You can get the sample code by running "git clone" from your repo (git already installed on sandbox)
```
cd /root
git clone https://github.com/abajwa-hw/hdp22-hive-streaming.git 
```
- You will need to complete the pre-requisites mentioned (i.e. install mvn, enable Hive transactions, create Hive table) [here](https://github.com/abajwa-hw/hdp22-hive-streaming#step-4-import-tweets-for-users-into-hive-orc-table-via-storm).

- Once you already have your storm code on the VM, just import the dir containing the pom.xml into Eclipse:
File > Import > Existing Maven Projects > navigate to your code (e.g. /root/hdp22-hive-streaming)  > OK

- Check the java compiler is using 1.7:
File > Properties > Java Compiler > uncheck "use compliance from..." > set "Compiler compliance level" to 1.7 > OK

- The eclipse project should build on its own and not show errors (if not, you may need to add jars to the project properties)

- To run maven compile: Run > Run as > Maven Build
  - The first time you do this, it will ask you for the configuration:
    - Under ‘Goals’: clean install
    - Under Maven Runtime, add your existing mvn install on the sandbox (its faster than using the embedded one)
    - Configure > Add > click ‘Directory’ and navigate to the dir where you installed mvn (e.g. /usr/share/maven/latest)
![Image](../master/screenshots/screeshot-eclipse-mvn.png?raw=true)
    
- Eclipse should now be able to run a mvn compile and create the uber jar

- Now from terminal, run your topology:
```
source ~/.bashrc
cd /root/hdp22-hive-streaming
storm jar ./target/storm-test-1.0-SNAPSHOT.jar test.HiveTopology
```    

-----------------

#### Getting started with Spark TP on HDP

- Run the Spark Pi example
```
/usr/spark-1.2/bin/spark-submit --class org.apache.spark.examples.SparkPi    --master yarn-cluster  --num-executors 3 --driver-memory 512m  --executor-memory 512m   --executor-cores 1  /usr/spark-1.2/lib/spark-examples*.jar
```

- Follow the rest of the examples from the Spark 1.2 on Yarn TP: http://hortonworks.com/hadoop-tutorial/using-apache-spark-hdp/



-------------------

#### Getting started with iPython Notebook on HDP

- Install iPython notebook service using instructions [here](https://github.com/randerzander/ipython-stack). 

- Setup the [airline demo](https://github.com/abajwa-hw/hdp-datascience-demo) in iPython using steps below:

- Make few changes to sandbox VM before setting up airline demo. Instructions to do these tasks are available on the same airline demo page above.
  - Make sure the sandbox VM is started with large amount of memory (15 GB) and disk to 65GB in order to run. 
  - Also change Ambari setting to run using Tez. 
   
- Download airline delay and weather data and copy into HDFS

```
export HOME_DIR=/home/ipython
export PROJECT_DIR=/tmp/HDP_DS_setup

sudo -u hdfs hadoop fs -mkdir /user/ipython
sudo -u hdfs hadoop fs -chown ipython:ipython /user/ipython
hadoop fs -mkdir /user/ipython/airline
hadoop fs -mkdir /user/ipython/airline/delay
hadoop fs -mkdir /user/ipython/airline/weather


mkdir $PROJECT_DIR
cd $PROJECT_DIR

wget http://stat-computing.org/dataexpo/2009/2007.csv.bz2
bzip2 -d 2007.csv.bz2
wget http://stat-computing.org/dataexpo/2009/2008.csv.bz2
bzip2 -d 2008.csv.bz2
hadoop fs -put *.csv /user/ipython/airline/delay
#delete copy of data from local FS to save space
rm $PROJECT_DIR/*.csv


wget ftp://ftp.ncdc.noaa.gov/pub/data/ghcn/daily/by_year/2007.csv.gz
gunzip -d 2007.csv.gz
wget ftp://ftp.ncdc.noaa.gov/pub/data/ghcn/daily/by_year/2008.csv.gz
gunzip -d 2008.csv.gz
hadoop fs -put *.csv /user/demo/airline/weather
#delete copy of data from local FS to save space
rm $PROJECT_DIR/*.csv

```

- download the the python version of the airline demo notebook
```
cd /home/ipython/notebooks
wget https://github.com/abajwa-hw/hdp-datascience-demo/blob/master/demo-HDP2.2/airline_python.ipynb
```

- Launch ipython notebook url and open the notebook at http://sandbox.hortonworks.com:9999

- Run the demo by running through the cells of the ipython notebook using Shift+enter
