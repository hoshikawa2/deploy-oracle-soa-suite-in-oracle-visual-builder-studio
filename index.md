
# Deploy Oracle SOA SUITE in Oracle Visual Builder Studio

## Introduction

**Oracle Visual Builder Studio** is the official **Oracle** tool for automating **DevOps** processes with the most different technologies, thus allowing you to work with:

* Kubernetes/Docker
* functions
* Application Servers like **Oracle Weblogic**, IIS, JBoss, Tomcat
* **Oracle Forms**
* **Oracle SOA SUITE**
* **Oracle Integration (OIC)**
* Among many other technologies

The project presented below shows an example of how to implement the following functionalities in **Oracle SOA SUITE**

* Exposure of a REST Service
* Working with Kafka (or Oracle Cloud Streaming) topics through a Kafka-compatible REST API
* Implement an **embedded** Java call
* Automatically deploy your **SOA SUITE** package to **SOA** server

>**Important Note**: Currently, it is only possible to implement CI/CD with Oracle SOA Suite through Oracle Visual Builder Studio. This occurs because only Visual Builde Studio has plugins already pre-installed in the virtual machine to process and compile (BUILD) the necessary packages. Therefore, it is not yet possible to implement CI/CD with other market tools such as Jenkins.

## Objectives

The objective of this document is to provide the construction of a project in **SOA SUITE** and that it can be automatically deployed through the **DevOps** process with **Oracle Visual Builder Studio**.

## Prerequisites

You will need knowledge in:

- **SOA SUITE** concepts and components
- **Kafka** concepts and uses 
- Development on **JDeveloper** IDE  

## Task 1: Create your Oracle Streaming instance

Oracle Cloud Streaming is an application that aims to serve as a message queue just like the open-source Kafka tool. Oracle Streaming is a managed solution, meaning you create an instance for use and don't have to worry about infrastructure governance. Pay only for what you use. The tool also allows you to use Kafka's REST API, making it compatible with existing Kafka projects with the advantages of "cloud as a service".

So, in this demo, we will execute a SOA service implementation to produce and another service to consume data in a message queue. So our first stop will be provisioning an Oracle Streaming environment.

First, let's create a user group for Streaming and enable users in this group. Once this is done, we can then determine the accesses to this group through the Policies. Below, see an example of how to determine the accesses to use Oracle Cloud Streaming:

![oracle-stream-policies.png](./images/oracle-stream-policies.png?raw=true)

Now let's create the Oracle Cloud Streaming instance. In the main menu, select the option "Analytics & AI" and "Streaming" as shown in the figure below.

![oracle-streaming-menu.png](./images/oracle-streaming-menu.png?raw=true)

Once this is done, you will see the console with the instances of Oracle Cloud Streaming. To create your instance, select the compartment and click "Create Stream"

![oracle-stream-create-1.png](./images/oracle-stream-create-1.png?raw=true)

After this step, fill in your instance data, such as name, compartment. Also, request that a new Stream Pool be created and provide a name for your pool. Click "Create".

![oracle-stream-create-2.png](./images/oracle-stream-create-2.png?raw=true)

Wait for the creation to complete.

![oracle-stream-complete-create.png](./images/oracle-stream-complete-create.png?raw=true)

### Get settings from the Kafka-compatible REST API

Let's now get the Kafka REST API settings by clicking on the "Kafka Connect Configurations" link as below:

![oracle-stream-kafka-connection-1.png](./images/oracle-stream-kafka-connection-1.png?raw=true)

Let's then create the configuration by selecting the compartment and clicking on the option "Create Kafka Connect Configuration"

![oracle-stream-create-kafka-conn.png](./images/oracle-stream-create-kafka-conn.png?raw=true)

Fill in the compartment and name of your configuration as below. Create the configuration by clicking on "Create Kafka Connect Configuration"

![oracle-stream-kafka-config.png](./images/oracle-stream-kafka-config.png?raw=true)

Let's get the configuration parameters to connect to Oracle Cloud Stream. Go to the "Streams" option from the links on the left:

![oracle-stream-link.png](./images/oracle-stream-link.png?raw=true)

And right after, click on your Pool created earlier:

![oracle-stream-default-pool.png](./images/oracle-stream-default-pool.png?raw=true)

And lastly, click on "Kafka Connection Settings"

![oracle-stream-parameters.png](./images/oracle-stream-parameters.png?raw=true)

Ready! Copy the parameters to use in your Java code:

![oracle-stream-parameters-view.png](./images/oracle-stream-parameters-view.png?raw=true)


## Task 2: Create your SOA SUITE project in JDeveloper

In this step, we will create a project with the following objectives:

* Project to produce and consume a Streaming queue like Kafka
* REST service to test both services

---
    We will create an instance of Oracle Cloud Streaming in order to replace Kafka.
    Oracle Cloud Streaming is Oracle's cloud managed solution for data streaming, with REST API compatible with the open Kafka project. Therefore, the implementation here will also be compatible with Kafka if you want to change the pointer to a Kafka queue created in a virtual machine or even in your on-premises structure

The complete project is available with this documentation, so you can simply change the settings so you can compile, package and deploy to your SOA SUITE instance.

This document will not show in detail how to implement REST services. I understand that you already know how to implement REST services and also consume services with SOA SUITE. If you still don't know how to implement it or just want to remember how, watch this video:

[How to Implement and Consume REST Services with Oracle SOA SUITE](https://objectstorage.us-ashburn-1.oraclecloud.com/p/V9cAskUBRamqg4dYeXeJB9FOmoBBpjsYF0_ak6jbTTUJxnx3mQwcdJ_tCkBgSU1q/n/idavixsf5sbx/b/data/o/SOA%20SUITE%20REST%20Tutorial.mov)

Download the **SOAKafkaProducerApp** project and open the folders until you find the jws application file:

    /SOAKafkaProducerApp/SOAKafkaProducerApp/SOAKafkaProducerApp.jws

This is the link for download the JDeveloper Project: [SOAKafkaProducerApp.zip](https://objectstorage.us-ashburn-1.oraclecloud.com/p/4N3k91pua1HGrmD6jpq3gNspvn8xWbTuFMqw85bs32csIdoft7yaoD-uUHxyCbb7/n/idavixsf5sbx/b/data/o/SOAKafkaProducerApp.zip)

So you can view the projects in **JDeveloper**

* SOAKafkaProducerPrj
* SOAKafkaConsumerPrj

Click on the application tree and look for **SOAKakfaConsumerPrj** and double-click as shown in the image:

![jdev-soa-1.png](./images/jdev-soa-1.png?raw=true)

This is the vision of the SOA project for Kafka queue consumption. This is a typical implementation of the **SOA Composite** type. This is a REST service (component laid out in the **Exposed Services** lane) and must be deployed on SOA Server. The component linked to the service is the implementation of the service itself and is arranged in the **Components** swimlane as shown in the previous image. By double-clicking on the KafkaConsumer component you will be directed to the implementation of the service as shown below:

![jdev-bpel-1.png](./images/jdev-bpel-1.png?raw=true)

The first implementation stage starts with a component called Receive, responsible for receiving initial work parameter(s), typical of a REST call. In this example, we will not pass parameters, but it is illustrated here for services that need this resource:

![jdeve-receive-1.png](./images/jdeve-receive-1.png?raw=true)

![jdev-receive-detail.png](./images/jdev-receive-detail.png?raw=true)

![jdev-bpel-code.png](./images/jdev-bpel-code.png?raw=true)

The Java Embedded component is responsible for calling a Java routine that, in this case, will call a class called KafaExample commented later:

![jdev-embedded-code-1.png](./images/jdev-embedded-code-1.png?raw=true)

The Java code called in this Java Embedding is responsible for the Kafka or Oracle Strem queue production and consumption routines (keep in mind that the API for Oracle Streaming is Kakfa compatible):

```java
		package soakafka;

		import org.apache.kafka.clients.consumer.ConsumerRecord;
		import org.apache.kafka.clients.consumer.ConsumerRecords;
		import org.apache.kafka.clients.consumer.KafkaConsumer;
		import org.apache.kafka.clients.producer.KafkaProducer;
		import org.apache.kafka.clients.producer.Producer;
		import org.apache.kafka.clients.producer.ProducerRecord;
		import org.apache.kafka.common.serialization.StringSerializer;
		import org.apache.kafka.common.serialization.StringDeserializer;

		import java.util.Arrays;
		import java.util.Date;
		import java.util.Properties;

		import java.util.concurrent.ExecutionException;

		import org.apache.kafka.clients.producer.Callback;
		import org.apache.kafka.common.errors.WakeupException;

		public class KafkaExample {
		    private final String topic;
		    private final Properties props;

		    public KafkaExample(String brokers, String username, String password) {
		        this.topic = "kafka_like";

		        String jaasTemplate = "org.apache.kafka.common.security.scram.ScramLoginModule required username=\"%s\" password=\"%s\";";
		        String jaasCfg = String.format(jaasTemplate, username, password);

		        String serializer = StringSerializer.class.getName();
		        String deserializer = StringDeserializer.class.getName();
		        //Propriedades
		        props = new Properties();
		        props.put("bootstrap.servers", brokers);
		        props.put("group.id", "kafka-hoshikawa");
		        props.put("enable.auto.commit", "false");
		        props.put("max.poll.records", "10");
		        props.put("auto.offset.reset", "earliest");
		        props.put("key.deserializer", deserializer);
		        props.put("value.deserializer", deserializer);
		        props.put("security.protocol", "SASL_SSL");
		        props.put("sasl.mechanism", "PLAIN");
		        props.put("sasl.jaas.config", jaasCfg);
		        //props.put("ssl.client.auth", "requested");
		    }

		    public String consume() {
		        String ret = "";
		        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
		        consumer.subscribe(Arrays.asList(topic));

		        try {
		          while (true) {
		            ConsumerRecords<String, String> records = consumer.poll(100);
		            for (ConsumerRecord<String, String> record : records)
		            {
		              System.out.println(record.offset() + ": " + record.value());
		              ret = ret + record.value();
		            }
		              if (ret != "")
		                break;
		          }
		        } catch (Exception e) {
		          // ignore for shutdown
		        } finally {
		          consumer.commitAsync();
		          consumer.close();
		        }
		        return ret;
		    };

		    public void produce(String message) {
		        Producer<String, String> producer = new KafkaProducer<String, String>(props);
		        ProducerRecord record = new ProducerRecord<String, String>(topic, "msg", message);

		        Callback callback = (data, ex) -> {
		            if (ex != null) {
		                ex.printStackTrace();
		                return;
		            }
		            System.out.println(
		                "Mensagem enviada com sucesso para: " + data.topic() + " | partition " + data.partition() + "| offset " + data.offset() + "| tempo " + data
		                    .timestamp());
		        };
		        try {
		            producer.send(record, callback).get();
		        } catch (ExecutionException | InterruptedException e) {
		        }
		        finally {
		            producer.close();
		        }
		    }

		    public static void main(String[] args) {
		                /*
				String brokers = System.getenv("CLOUDKARAFKA_BROKERS");
				String username = System.getenv("CLOUDKARAFKA_USERNAME");
				String password = System.getenv("CLOUDKARAFKA_PASSWORD");
		                */
		                String brokers = "cell-1.streaming.us-ashburn-1.oci.oraclecloud.com:9092";
		                String username = "hoshikawaoraclecloud/oracleidentitycloudservice/hoshikawa2@hotmail.com/ocid1.streampool.oc1.iad.amaaaxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxkgztajzakb5a";
		                String password = "Wxxxxxxxxxxxxxxxxxxxxxxk";
				KafkaExample c = new KafkaExample(brokers, username, password);
		        c.consume();
		    }
		}

```
![jdev-assign-1.png](./images/jdev-assign-1.png?raw=true)

![jdev-assign-details.png](./images/jdev-assign-details.png?raw=true)

![jdev-reply-1.png](./images/jdev-reply-1.png?raw=true)

![jdev-reply-details.png](./images/jdev-reply-details.png?raw=true)

### Including Java packages to your code with MAVEN

In this project, we inject Java code to call the Kafka or Oracle Cloud Streaming API. Because it is Java code, package inclusions are often necessary to use frameworks. MAVEN is the most used tool in these cases and for that, you must include it in your project. With the right mouse button, open the option over your project and select "From Gallery" as in the image below:

![soa-create-pom-file.png](./images/soa-create-pom-file.png?raw=true)

And select the option "Maven POM for Project".

![soa-maven-select.png](./images/soa-maven-select.png?raw=true)

With this, you will be able to configure your package with the necessary frameworks.

### Build with Maven

First of all, to access the **pom.xml** file, you need to have the project directory view. For that, you need to activate this view. Click on "Application Windows Options" with the following figure:

![View-maven-pom-xml-file.png](./images/View-maven-pom-xml-file.png?raw=true)

And select the "Directory View" option. This way you will be able to access the **pom.xml** file found in the structure **/SOA/SCA-INF/pom.xml**

![File-pom-xml-file2.png](./images/File-pom-xml-file2.png?raw=true)

Now double-click on the **pom.xml** file to open it in the **JDeveloper** editor and select the "Source" tab to view the code:

![pom-xml-structure-file.png](./images/pom-xml-structure-file.png?raw=true)

This way you will be able to visualize the code of the **pom.xml** file

![pom-xml-original.png](./images/pom-xml-original.png?raw=true)

Here's a very important change to the **DevOps** process. You **MUST** include these lines for automation:


    The project is already configured to support DevOps automation, but in new projects,
    follow this procedure to automate the DevOps process.
    Between the <build> tag and the <plugins> tag you can include this code, which will be responsible for the magic of assembling your software package for later deployment with **Ant**: 

    <!-- For DevOps automation, you need to include the 3 lines below -->
    <directory>target</directory>
    <outputDirectory>classes</outputDirectory>
    <sourceDirectory>src</sourceDirectory>



![devops-pom-xml.png](./images/devops-pom-xml.png?raw=true)


So your complete code would look like this:


```xml
    pom.xml
    
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>

      <groupId>soakafka</groupId>
      <artifactId>kafka</artifactId>
      <version>1.0-SNAPSHOT</version>

      <dependencies>
        <dependency>
          <groupId>org.apache.kafka</groupId>
          <artifactId>kafka-clients</artifactId>
          <version>1.0.0</version>
        </dependency>
      </dependencies>

      <build>
          
        <!-- For DevOps automation, you need to include the 3 lines below - Cristiano Hoshikawa - 2020-11-28 -->        
        <directory>target</directory>
        <outputDirectory>classes</outputDirectory>
        <sourceDirectory>src</sourceDirectory>

        <plugins>
          <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
              <source>1.8</source>
              <target>1.8</target>
            </configuration>
          </plugin>
          <plugin>
            <artifactId>maven-assembly-plugin</artifactId>
            <configuration>
              <archive>
                <manifest>
                  <mainClass>KafkaExample</mainClass>
                </manifest>
              </archive>
              <descriptorRefs>
                <descriptorRef>jar-with-dependencies</descriptorRef>
              </descriptorRefs>
            </configuration>
            <executions>
              <execution>
                <id>make-assembly</id> <!-- this is used for inheritance merges -->
                <phase>package</phase> <!-- bind to the packaging phase -->
                <goals>
                  <goal>single</goal>
                </goals>
              </execution>
            </executions>
          </plugin>
          <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <version>3.0.2</version>
            <configuration>
              <archive>
                <manifest>
                  <mainClass>KafkaExample</mainClass>
                  <addClasspath>true</addClasspath>
                </manifest>
              </archive>
            </configuration>
          </plugin>
        </plugins>
      </build>  
    </project>


```
### Build and Deploy with Ant

**Ant** is a well-known tool in the market, developed by the **Jakarta** project, responsible for automating compilations, package builds and application deployments for projects such as **Java**.

In the **DevOps** process, **Ant** will be fundamental for building the **SOA SUITE** package and also for deploying this package on the remote **Weblogic SOA SUITE** server.

As in creating your file in **MAVEN** (**pom.xml**), to create your **build.xml** file, with the right mouse button, open the option over your project and select "From Gallery" as in the image below:

![soa-create-pom-file.png](./images/soa-create-pom-file.png?raw=true)

And select the "Ant" option on the left side menu and "Buildfile from Project" as below:

![soa-create-ant.png](./images/soa-create-ant.png?raw=true)


The **build.xml** file is a very common configuration file for use with **Ant**. The format of this file is in **XML** format


```xml
    build.xml
    
    <?xml version="1.0" encoding="UTF-8" ?>
    <!--Ant buildfile generated by Oracle JDeveloper-->
    <!--Generated Oct 12, 2020 11:35:33 PM-->
    <project xmlns="antlib:org.apache.tools.ant" name="SOAKafkaProducerPrj" default="all" basedir=".">
      <property environment="env" /> 
      <property file="build.properties"/>
      <path id="library.SOA.Designtime">
        <pathelement location="${install.dir}/soa/plugins/jdeveloper/extensions/oracle.sca.modeler.jar"/>
      </path>
      <path id="library.SOA.Runtime">
        <pathelement location="${install.dir}/soa/soa/modules/oracle.soa.fabric_11.1.1/fabric-runtime.jar"/>
        <pathelement location="${install.dir}/soa/soa/modules/oracle.soa.fabric_11.1.1/tracking-api.jar"/>
        <pathelement location="${install.dir}/soa/soa/modules/oracle.soa.fabric_11.1.1/tracking-core.jar"/>
        <pathelement location="${install.dir}/soa/soa/modules/oracle.soa.fabric_11.1.1/edn.jar"/>
        <pathelement location="${install.dir}/soa/soa/modules/oracle.soa.mgmt_11.1.1/soa-infra-mgmt.jar"/>
        <pathelement location="${oracle.commons}/modules/com.oracle.webservices.fabric-common-api.jar"/>
      </path>
      <path id="library.BPEL.Runtime">
        <pathelement location="${install.dir}/soa/soa/modules/oracle.soa.bpel_11.1.1/orabpel.jar"/>
      </path>
      <path id="library.Mediator.Runtime">
        <pathelement location="${install.dir}/soa/soa/modules/oracle.soa.mediator_11.1.1/mediator_client.jar"/>
      </path>
      <path id="library.MDS.Runtime">
        <pathelement location="${oracle.commons}/modules/oracle.mds/mdsrt.jar"/>
      </path>
      <path id="library.BC4J.Service.Runtime">
        <pathelement location="${oracle.commons}/modules/oracle.adf.model/adfbcsvc.jar"/>
        <pathelement location="${oracle.commons}/modules/oracle.adf.model/adfbcsvc-share.jar"/>
        <pathelement location="${oracle.commons}/modules/commonj.sdo.backward.jar"/>
        <pathelement location="${oracle.commons}/modules/commonj.sdo.jar"/>
        <pathelement location="${oracle.commons}/modules/oracle.toplink/eclipselink.jar"/>
        <pathelement location="${oracle.commons}/modules/com.oracle.webservices.fmw.wsclient-impl.jar"/>
        <pathelement location="${oracle.commons}/modules/com.oracle.webservices.fmw.jrf-ws-api.jar"/>
        <pathelement location="${oracle.commons}/modules/com.oracle.webservices.fmw.web-common-schemas-impl.jar"/>
      </path>
      <path id="classpath">
        <path refid="library.SOA.Designtime"/>
        <path refid="library.SOA.Runtime"/>
        <path refid="library.BPEL.Runtime"/>
        <path refid="library.Mediator.Runtime"/>
        <path refid="library.MDS.Runtime"/>
        <path refid="library.BC4J.Service.Runtime"/>
      </path>
      <target name="init">
        <tstamp/>
        <mkdir dir="${workspace}/SOAKafkaProducerApp/SOAKafkaProducerPrj/SOA/SCA-INF/classes"/>
      </target>
      <target name="all" description="Build the project" depends="deploy,copy"/>
      <target name="clean" description="Clean the project">
        <delete includeemptydirs="true" quiet="true">
          <fileset dir="${output.dir}" includes="**/*"/>
        </delete>
      </target>
      <target name="compile" depends="init">
          <javac 
                   srcdir="${workspace}/SOAKafkaProducerApp/SOAKafkaProducerPrj/SOA/SCA-INF/src/soakafka"
                   destdir="${workspace}/SOAKafkaProducerApp/SOAKafkaProducerPrj/SOA/SCA-INF/classes"
               includeantruntime="false">
               <include name="${workspace}/SOAKafkaProducerApp/SOAKafkaProducerPrj/SOA/SCA-INF/lib/**"/>
        </javac>
      </target>
      <target name="sca-compile" depends="compile">
          <ant antfile="${middleware.home}/soa/bin/ant-sca-compile.xml" inheritAll="false">
               <property name="scac.input" value="${workspace}/SOAKafkaProducerApp/SOAKafkaProducerPrj/SOA/composite.xml"/>
          </ant>
      </target>
     
      <target name="sca-package" depends="sca-compile">
          <ant antfile="/${middleware.home}/soa/bin/ant-sca-package.xml" inheritAll="false">
               <property name="compositeDir" value="${workspace}/SOAKafkaProducerApp/SOAKafkaProducerPrj/SOA"/>
              <property name="compositeName" value="SOAKafkaProducerPrj"/>
              <property name="revision" value="${revision}"/>
              <property name="sca.application.home" value="${workspace}/SOAKafkaProducerApp/SOAKafkaProducerPrj"/>
          </ant>
      </target>
            
      <target name="deploy" description="Deploy JDeveloper profiles" depends="sca-package">
        <taskdef name="ojdeploy" classname="oracle.jdeveloper.deploy.ant.OJDeployAntTask" uri="oraclelib:OJDeployAntTask"
                 classpath="${oracle.jdeveloper.ant.library}"/>
        <ora:ojdeploy xmlns:ora="oraclelib:OJDeployAntTask" executable="${oracle.jdeveloper.ojdeploy.path}"
                      ora:buildscript="${oracle.jdeveloper.deploy.dir}/ojdeploy-build.xml"
                      ora:statuslog="${oracle.jdeveloper.deploy.dir}/ojdeploy-statuslog.xml">
          <ora:deploy>
            <ora:parameter name="workspace" value="${oracle.jdeveloper.workspace.path}"/>
            <ora:parameter name="project" value="${oracle.jdeveloper.project.name}"/>
            <ora:parameter name="profile" value="${oracle.jdeveloper.deploy.profile.name}"/>
            <ora:parameter name="nocompile" value="false"/>
            <ora:parameter name="outputfile" value="${oracle.jdeveloper.deploy.outputfile}"/>
          </ora:deploy>
        </ora:ojdeploy>
     
         <!-- Deployment SOA SUITE Composite -->
        <ant antfile="/${middleware.home}/soa/bin/ant-sca-deploy.xml" target="deploy" inheritall="false">
          <property name="user"      value="${WEBLOGICUSER}"/>
          <property name="password"  value="${WEBLOGICPWD}"/>
          <property name="serverURL"     value="${WEBLOGICURL}"/>
          <property name="sarLocation"   value="${workspace}/SOAKafkaProducerApp/SOAKafkaProducerPrj/SOA/deploy/sca_SOAKafkaProducerPrj_rev${revision}.jar"/>
          <property name="overwrite"     value="true"/>
        </ant>

      </target>
      <target name="copy" description="Copy files to output directory" depends="init">
        <patternset id="copy.patterns">
          <include name="**/*.GIF"/>
          <include name="**/*.JPEG"/>
          <include name="**/*.JPG"/>
          <include name="**/*.PNG"/>
          <include name="**/*.cpx"/>
          <include name="**/*.dcx"/>
          <include name="**/*.ejx"/>
          <include name="**/*.gif"/>
          <include name="**/*.ini"/>
          <include name="**/*.jpeg"/>
          <include name="**/*.jpg"/>
          <include name="**/*.png"/>
          <include name="**/*.properties"/>
          <include name="**/*.sva"/>
          <include name="**/*.tag"/>
          <include name="**/*.tld"/>
          <include name="**/*.wsdl"/>
          <include name="**/*.xcfg"/>
          <include name="**/*.xlf"/>
          <include name="**/*.xml"/>
          <include name="**/*.xsd"/>
          <include name="**/*.xsl"/>
          <include name="**/*.exm"/>
          <include name="**/*.xml"/>
          <exclude name="build.xml"/>
        </patternset>
        <copy todir="${output.dir}">
          <fileset dir="SOA/SCA-INF/src">
            <patternset refid="copy.patterns"/>
          </fileset>
          <fileset dir=".">
            <patternset refid="copy.patterns"/>
          </fileset>
        </copy>
      </target>
    </project>

```

The **build.properties** file determines the properties that will be used in the **build.xml** configuration file



    build.properties
    
    oracle.commons=../../../../oracle_common/
    install.dir=../../../..
    oracle.home=${env.ORACLE_HOME_SOA_12_2_1}
    oracle.jdeveloper.workspace.path=${env.WORKSPACE}/SOAKafkaProducerApp/SOAKafkaProducerApp.jws
    middleware.home=${env.MIDDLEWARE_HOME_SOA_12_2_1}
    workspace=${env.WORKSPACE}
    oracle.jdeveloper.ant.library=${env.ORACLE_HOME_SOA_12_2_1}/jdev/lib/ant-jdeveloper.jar
    oracle.jdeveloper.deploy.dir=${env.WORKSPACE}/SOAKafkaProducerApp/SOAKafkaProducerPrj/deploy
    oracle.jdeveloper.ojdeploy.path=${oracle.home}/jdev/bin/ojdeploy
    javac.nowarn=off
    oracle.jdeveloper.project.name=SOAKafkaProducerPrj
    revision=1.0
    oracle.jdeveloper.deploy.outputfile=${env.WORKSPACE}/SOAKafkaProducerApp/SOAKafkaProducerPrj/deploy/sca_${profile.name}_rev{$revision}
    output.dir=classes
    javac.deprecation=off
    oracle.jdeveloper.deploy.profile.name=*
    javac.debug=on
    WEBLOGICPWD=${env.WEBLOGICPWD}
    WEBLOGICURL=${env.WEBLOGICURL}
    WEBLOGICUSER=${env.WEBLOGICUSER}
    
### Initialize the SOA SUITE environment for test

First open the firewall port of your SOA SUITE environment. The port to be released is 9092, which represents the Oracle Streaming or Kafka port.

### Enable Embedded SOA Server in JDeveloper

JDeveloper has an integrated SOA SUITE server for development and testing. It is a server with practically all the functionalities necessary for you to develop your SOA services. In order to use this server, you must create and activate it.

To activate the server, you first need to view the application servers. For this, go to the "Window" option and select "Application Servers" as below:

![jdeveloper-select-app-server.png](./images/jdeveloper-select-app-server.png?raw=true)

The application servers window will appear on the left side of the screen. Make sure the "integratedWeblogicServer" is already created in your window. If not, you'll need to create it and then activate it for use.

Go to the "Run" option and select "Start Server Instance" as below:

![jdev-start-app-server.gif](./images/jdev-start-app-server.gif?raw=true)

And fill in the required data to create your weblogic instance. Write down the values ​​as you will need them later for testing.

![jdev-create-app-server.gif](./images/jdev-create-app-server.gif?raw=true)

If you created the server or if it was already available, right click on your server and activate it as below:

![jdeveloper-start-app-server.png](./images/jdeveloper-start-app-server.png?raw=true)


### Run a Manual Deployment in SOA SUITE

First, let's perform the deployment of your Kafka consumer service (SOAKafkaConsumerPrj) manually. To do so, locate your "Applications" tab and open the menu options with the right mouse button, as shown in the figure below:

![soa-deploy-1.png](./images/soa-deploy-1.png?raw=true)

Select the "Deploy" option and then create a new deployment profile. You will see a configuration screen and you must choose the type of profile as "SOA-SAR File" as we will generate a file.

![soa-deploy-2.png](./images/soa-deploy-2.png?raw=true)

This is preparation for us to be able to deploy your package in the next step. On the next screen, keep the given options and click "OK". Let's go to the next step.

![soa-deploy-3.png](./images/soa-deploy-3.png?raw=true)

Opening the "Deploy" menu option again you will now find your deployment option created and ready to use. Select it.

![soa-deploy-4.png](./images/soa-deploy-4.png?raw=true)

Now let's choose the option to deploy directly to your SOA server. Then choose the option as shown below:


![soa-deploy-5.png](./images/soa-deploy-5.png?raw=true)

Leave all options provided and check "Overwrite any existing composites with the same revision ID". If this option is unchecked and you try to perform consecutive deployments after your first run, you will be interrupted with an error message.

![soa-deploy-6.png](./images/soa-deploy-6.png?raw=true)

Then select the "IntegratedWeblogicServer" option. This option represents JDeveloper's embedded server explained in the previous section (How to enable Embedded SOA Server in JDeveloper)

![soa-deploy-8.png](./images/soa-deploy-8.png?raw=true)

If your JDeveloper Weblogic server was successfully created and started, you will see the option below and the "RUNNING" status confirming that everything is OK. If you don't see this option, you probably haven't created the server yet or haven't started it. Review the previous steps.

![soa-deploy-9.png](./images/soa-deploy-9.png?raw=true)

Confirm with "Next" until the summary screen and click "Finish".

![soa-deploy-10.png](./images/soa-deploy-10.png?raw=true)

In the bottom center window, you can follow the deployment of your application. Wait until you see "Deploy finished" as success message.

![soa-deploy-11.png](./images/soa-deploy-11.png?raw=true)

### Installing the JKS certificate in Weblogic:

In SSL environments, it will be necessary to create security through keys (public/private) which will be done through JKS certificates, suitable for the WebLogic environment. The key generation process is done by obtaining the certificate of your Oracle Cloud Streaming instance and adding this certificate to a JKS file , which we will call DemoTrust (DemoTrust.jks).

>**Note**:
    The DemoTrust.jks file will be stamped with an ALIAS as well as a password
    which will be used in your Java code

After creating your JKS file, you can move this file into your WebLogic server structure.

1. Getting the Oracle Cloud Streaming Certificate


    https://docs.oracle.com/en/cloud/paas/integration-cloud/stream-service-adapter/prerequisites-creating-connection.html

2. Generating the JKS file
3. Installing the JKS file in Weblogic

#


	echo -n | openssl s_client -connect cell-1.streaming.us-ashburn-1.oci.oraclecloud.com:9092 | sed -ne  '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > ociStreaming.cert
	keytool -keystore Streaming_truststore.jks -alias OSSStream -import -file ociStreaming.cert
	
	sudo keytool -importcert -file ociStreaming.cert -keystore DemoTrust.jks -alias "kafka" -storepass DemoTrustKeyStorePassPhrase

For example:

	echo -n | openssl s_client -connect cell-1.streaming.us-ashburn-1.oci.oraclecloud.com:9092 | sed -ne  '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > ociStreaming.cert
	keytool -keystore Streaming_truststore.jks -alias OSSStream -import -file ociStreaming.cert

	sudo keytool -importcert -file ociStreaming.cert -keystore DemoTrust.jks -alias "kafka" -storepass DemoTrustKeyStorePassPhrase

If you want to generate or delete keystores by alias, follow these steps:


```shell
    sudo keytool -import -v -file streaming.us-ashburn-1.oci.oraclecloud.com.cer -storepass changeit -keystore /Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home/jre/lib/security/cacerts -alias "kafka" 

    sudo keytool -delete -alias kafka -keystore /Library/Java/JavaVirtualMachines/jdk1.8.0_221.jdk/Contents/Home/jre/lib/security/cacerts -storepass changeit

    sudo keytool -import -v -file streaming.us-ashburn-1.oci.oraclecloud.com.cer -keystore DemoTrust.jks -alias "kafka" -storepass DemoTrustKeyStorePassPhrase

    sudo keytool -delete -alias kafka -keystore DemoTrust.jks -storepass DemoTrustKeyStorePassPhrase
```
go to folder

	Find your wlserver folder which usually has this path:
	/wlserver/server/lib
	
	For exemple:
	cd /u01/app/oracle/middleware/wlserver/server/lib
	cd /User/u01/Oracle/Middleware/Oracle_Home/wlserver/server/lib


And replace the DemoTrust.jks file with the one generated in this step.

After copying the DemoTrust.jks file, you must change the directory where you deployed the file. Change the /src/soakafka/KafkaExample.java files of both projects (SOAKafkaProducerprj and SOAKafkaConsumerprj) when creating the class as follows:


![change-truststore-soa-1.png](./images/change-truststore-soa-1.png?raw=true)


### Test the application

After uploading the test environment, your instance will be available at:

    http://localhost:7101/em
    
    Remember your username and password

![soa-test-1.png](./images/soa-test-1.png?raw=true)

![soa-test-2.png](./images/soa-test-2.png?raw=true)

![soa-test-3.png](./images/soa-test-3.png?raw=true)

![soa-test-4.png](./images/soa-test-4.png?raw=true)

![soa-test-5.png](./images/soa-test-5.png?raw=true)

Fill in the message parameter required by the service, as per the implementation. This parameter was called "msg" and the message format is JSON according to the previous steps:

![soa-test-producer-1a.png](./images/soa-test-producer-1a.png?raw=true)

After entering your message in JSON format, click "Test Web Service" to run your Producer service on the message queue:

![soa-test-producer-2.png](./images/soa-test-producer-2.png?raw=true)


## Task 3: Create a SOA SUITE Instance on Oracle Cloud

You must have an active instance of **Oracle SOA SUITE** for your application to be deployed. The instance to be used in this tutorial will be the image available on our **Marketplace** and will have a **SOA SUITE** structure served by a bastion to protect the server.

For this, let's create an instance. In the **Oracle Cloud** console , click on the main menu (in the upper left corner of the screen), option "Marketplace" and "All Applications" as below:

![market-place.png](./images/market-place.png?raw=true)

And in the image search screen, type "soa suite" to find the correct image:

![marketplace-soa-suite.png](./images/marketplace-soa-suite.png?raw=true)


Click on the "Oracle SOA Suite (BYOL)" option to start creating the instance. You will see some mandatory options to answer.


    Confirm SOA SUITE version: 12.2.1.4 or higher (this workshop was developed with version 12.2.1.4)
    Select the compartment in which you want the SOA SUITE instance to be created
    Confirm acceptance of the terms and conditions of the licensing agreement
    
    And click the "Launch Stack" button:

![soa-suite-creation.png](./images/soa-suite-creation.png?raw=true)


More details on the creation will be in this document:


https://docs.oracle.com/en/cloud/paas/soa-cloud/soa-marketplace/soamp-create-and-view-oracle-soa-suite-instances.html

Select the main menu again in the Oracle Cloud console , Compute and Instances:


![compute-select.png](./images/compute-select.png?raw=true)

Select the compartment where you created your SOA SUITE instance and when you select it you will see 2 virtual machines:

* The SOA SUITE Server
* The VM that will serve as a bastion for protecting the SOA SUITE server

The VM bastion will have a public IP, which it will be possible to access via the Internet to access the SOA SUITE server by opening an IP tunnel, explained later.


![instance-soa-suite-compute.png](./images/instance-soa-suite-compute.png?raw=true)


To establish a tunnel between the Bastion Server and the SOA SUITE instance, you can do it through the SSH command as below:


```shell
    ssh -Y -i <Arq.Chave SSH> -L <Port SOA>:<IP Instance SOA SUITE>:<Port External> opc@<IP Public Bastion>
    
    Example:
    ssh -Y -i keyrvbastion.pem -L 7002:10.253.128.9:7002 opc@152.67.55.11

```    
    After establishing the connection via Bastion, you can access the Weblogic Enterprise Manager with http://localhost:7002/em

## Task 4: Automate the Deployment with Oracle Visual Builder Studio

Now let's automate the deployment of SOA implementations to the Weblogic server created in the previous step. For this you will need an instance of Oracle Visual Builder Studio .

**Oracle Visual Builder Studio** works by projects, so you can create a project and include the users that will take part in its execution.

You can find more information about the operation in the REFERENCES section at the end of this document, search for Deploy with **Visual Builder Studio**.

Next, follow the steps to configure an automated build and deployment of your SOA SUITE implementations to the Weblogic server.

### Configuring a virtual machine for BUILD in ORACLE SOA SUITE

Before starting the settings, you will need to configure a virtual machine that meets the needs of compiling an **Oracle SOA SUITE** project , for this, you will need to select the correct tools to be installed in this VM.

Click **Organization** on the left side menu and **Virtual Machine Templates**:

![vbst-create-template.png](./images/vbst-create-template.png?raw=true)

Click on "Create Template" to configure which tools will be available for building your SOA SUITE projects.


Then type a name for your Template and if you want, optionally, you can describe about this template. Don't forget to select which platform this VM will be on. Choose the **Oracle Linux** option (until the date of this document, we had **Oracle Linux 7**, but you can select another more recent option.

![vbst-create-template-2.png](./images/vbst-create-template-2.png?raw=true)

Here, you must select all the important components for building your projects. Choose all the options as below:

![vbst-config-sw.png](./images/vbst-config-sw.png?raw=true)

![vbst-config-details.png](./images/vbst-config-details.png?raw=true)

Ready! Your TEMPLATE is created and your VM will be instantiated as soon as a project asks for a build. What will be done in the next step.

#### BUILD configuration of a project in Oracle Visual Builder Studio

#
First, open a project within **Oracle Visual Builder Studio**, in the example below, click on **CRISTIANO HOSHIKAWA PROJ**:


![main-visual-builder-studio.png](./images/main-visual-builder-studio.png?raw=true)

You will see your project page, along with a left side menu with the available options and on the right side your projects in the source code repository and also the team that is part of this project (project users).
![visual-builder-studio-project.png](./images/visual-builder-studio-project.png?raw=true)

Click on the BUILD option on the left side menu to view all existing settings and then let's configure a new BUILD for your project. Click "Create Job".

![visual-builder-studio-create-job.png](./images/visual-builder-studio-create-job.png?raw=true)

Enter a name for your Job and also select the VM Template (created in the previous session):

![vbst-create-job-details.png](./images/vbst-create-job-details.png?raw=true)

Now let's configure the build of your project step-by-step.

The first part of the configuration will be to establish the repository of the Oracle SOA SUITE project that must be previously created with the source code. In this document, we are working with the SOAKafkaProducerApp project available in the git of this document.

You will be able to clone this project to the git of your **Oracle Visual Builder Studio** instance .

After cloning the SOAKafkaProducerApp project to your Visual Builder Studio, configure your BUILD with the name of the git repository and select the option "Automatically perform build on SCM commit". Also check that the branch name matches your git project.

![vbst-git.png](./images/vbst-git.png?raw=true)

Now select the "Parameters" tab. You will have to create 3 parameters with the Combobox menu "Add Parameters" on the right side. Below, the names that must be created and also in parentheses, the type:


    WEBLOGICUSER: the Weblogic user (String type)
    WEBLOGICPWD: your Weblogic user password (type Secret/Password)
    WEBLOGICURL: the URL of your Weblogic instance (String type)
    
    An important observation is that the URL in WEBLOGICURL must be localhost
    as a tunnel will be established through the Bastion Server

![vbst-parameters.png](./images/vbst-parameters.png?raw=true)

Now click on the "Before Build" tab to configure the SSH tunnel from the Bastion Server to the Weblogic SOA SUITE Server.

Remember to have your SSH key (Private Key) and fill in the corresponding field.

Together, fill in the data for the tunnel assembly.


    Username: opc
    Local Port: 11002
    Remote Host: <Private IP of your SOA SUITE instance>
    Remote Port: 11002
    SSH Server: <Public IP of your Bastion>
    
    An important observation is that port 11002 was previously configured so that it is exactly the access port for deploying your SOA SUITE projects

![vbst-tunnel.png](./images/vbst-tunnel.png?raw=true)

Click on the "Steps" tab to configure BUILD (via Maven) and DEPLOYMENT (via ANT) of your SOA SUITE project.

Fill in as follows:


    MAVEN
    Goals: compile package
    POM File: SOAKafkaProducerApp/SOAKafkaConsumerPrj/SOA/SCA-INF/pom.xml
    This is the Maven pom.xml file for assembling your project package
    
    ANT
    Targets: sca-package deploy
    Build File: SOAKafkaProducerApp/SOAKafkaConsumerPrj/build.xml
    This is the Ant build.xml file for deploying your application after build
    Properties:
        WEBLOGICUSER=${WEBLOGICUSER}
        WEBLOGICPWD=${WEBLOGICPWD}
        WEBLOGICURL=${WEBLOGICURL}    

    Important note, the SOAKafkaProducerApp application has 2 projects:
    SOAKafkaProducerPrj
    SOAKafkaConsumerPrj
    
    We are illustrating the configuration of one of the projects. The same steps must
    be performed for the other project.

![vbst-config-step.png](./images/vbst-config-step.png?raw=true)

Now let's finish the BUILD configuration. Select the "After Build" tab.

This step is optional, however you may want to generate the artifact created by the previous step when running Maven.

For this, configure this step so that the artifact is generated for later download.

![vbst-after-build.png](./images/vbst-after-build.png?raw=true)

### Execute the BUILD in Oracle Visual Builder Studio
Ready! once configured, every time there is a commit in your code repository (Visual Builder Studio git), the BUILD will start automatically.

You can also run the BUILD manually, for this you just have to click on the BUILD option.

Try running BUILD and you will be able to check the generated Logs


![vbst-log-1.png](./images/vbst-log-1.png?raw=true)

![vbst-logs-3.png](./images/vbst-logs-3.png?raw=true)

![vbst-logs-2.png](./images/vbst-logs-2.png?raw=true)

## Related Links

##### SOA SUITE REST Tutorial
https://www.dropbox.com/s/ul2dogyia6sbpjf/SOA%20SUITE%20REST%20Tutorial.mov?dl=0

##### Integrating REST Operations in SOA Composite Applications
https://docs.oracle.com/en/middleware/soa-suite/soa/12.2.1.3/develop/integrating-rest-operations-soa-composite-applications.html#GUID-F11C4900-67D6-4434-83AC-8EB1D52BE238

##### Oracle Streaming Fast Tutorial
https://blogs.oracle.com/developers/getting-started-with-oracle-streaming-service-oss

##### Incorporating Java and Java EE Code in a BPEL Process
https://docs.oracle.com/middleware/1221/soasuite/develop/GUID-7D0BA7EC-65EB-4462-8761-6911D79EFF6A.htm#SOASE500
https://docs.oracle.com/cd/E15586_01/integration.1111/e10224/bp_java.htm#BABDCCED

##### How to Integrate with Java in SOA SUITE
https://redthunder.blog/2016/11/22/teaching-how-to-integrate-with-java-code-in-oracle-soa-composites/

##### Using the Oracle Cloud Infrastructure Streaming Service Adapter with Oracle Integration
https://docs.oracle.com/en/cloud/paas/integration-cloud/stream-service-adapter/prerequisites-creating-connection.html

    cd /u01/app/oracle/middleware/wlserver/server/lib
    
    sudo keytool -importcert -file streaming_us-ashburn-1_oci_oraclecloud_com.crt -keystore DemoTrust.jks -alias “kafka" -storepass DemoTrustKeyStorePassPhrase

##### How to use an SSH Tunnel in Oracle Developer Cloud Service Build Jobs
https://learncodeshare.net/2018/10/31/how-to-use-an-ssh-tunnel-in-oracle-developer-cloud-service-build-jobs/

##### Deploy with Visual Builder Studio
https://docs.oracle.com/en/cloud/paas/developer-cloud/csdcs/deploy-application.html#GUID-5122E3C6-A929-4900-A853-E794A006E52E

    Use this guide to implement infrastructure as code
    
##### Enabling tunnels in Weblogic
https://munzandmore.com/2015/ora/http-instead-t3-weblogic-wlst

##### Weblogic REST API Services
https://docs.oracle.com/middleware/1221/wls/WLRUR/examples.htm#WLRUR193

##### How to Deploy a Single SOA Composite in Oracle JDeveloper
https://docs.oracle.com/middleware/1213/soasuite/develop/GUID-F2B6386E-0F68-4797-96D2-196800394FEF.htm#SOASE158

##### Patching SOA Composite Instances in Oracle 12.2.1
https://blogs.oracle.com/integration/post/patching-soa-composite-instances-in-oracle-1221


    A new Composite Instance Patching feature has been introduced in SOA Suite 12.2.1 which enables you to deliver urgent composite fixes that can be picked up by long running instances.  This feature is part of Oracle Integration Continuous Availability.  Please refer to the Oracle Fusion Middleware Developing SOA Applications with Oracle SOA Suite documentation for additional information.
    
##### How to Configure JDeveloper to deploy into SOACS
https://redthunder.blog/2016/11/22/teaching-how-to-configure-jdveloper-to-deploy-into-soacs/

##### Using the Oracle Cloud Infrastructure Streaming Service Adapter with Oracle Integration
https://docs.oracle.com/en/cloud/paas/integration-cloud/stream-service-adapter/prerequisites-creating-connection.html

##### Weblogic integrated server Demo Identity Keystore and Demo Trust Keystore
http://sanjeev-technology.blogspot.com/2016/07/weblogic-integrated-server-demo.html

## Acknowledgments

* Author - Cristiano Hoshikawa (Oracle LAD A-Team Solution Engineer)
