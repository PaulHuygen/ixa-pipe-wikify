# ixa-pipe-wikify

This repository contains the Wikification tool based on [DBpedia
Spotlight](https://github.com/dbpedia-spotlight/dbpedia-spotlight/wiki). *ixa-pipe-wikify*
is part of IXA pipes, a [multilingual NLP
pipeline](http://ixa2.si.ehu.es/ixa-pipes) developed by the [IXA NLP
Group](http://ixa.si.ehu.es/Ixa).

Providing that a DBpedia Spotlight Rest server for a given language is
running, the *ixa-pipe-wikify* module takes a [NAF
document](http://wordpress.let.vupr.nl/naf/) containing *wf* and
*term* elements as input, performs Wikification for your language
of choice, and outputs a NAF document with references to DBpedia on *markables*
element (`<markables source="DBpedia">`)


### Module contents

The contents of the module are the following:

    + src/   	    java source code of the module
    + pom.xml 	    maven pom file wich deals with everything related to compilation and execution of the module
    + README.md	    this README file
    + Furthermore, the installation process, as described in the README.md, will generate another directory:
    target/	    it contains binary executable and other directories


## INSTALLATION

Installing the *ixa-pipe-wikify* requires the following steps:

*If you already have installed in your machine the Java 1.7+ and MAVEN
3, please go to [step
3](#3-download-and-install-statistical-backend---dbpedia-spotlight)
directly. Otherwise, follow the detailed steps*

### 1. Install JDK 1.7

If you do not install JDK 1.7 in a default location, you will probably
need to configure the PATH in .bashrc or .bash_profile:

    export JAVA_HOME=/yourpath/local/java17
    export PATH=${JAVA_HOME}/bin:${PATH}


If you use tcsh you will need to specify it in your .login as follows:

    setenv JAVA_HOME /usr/java/java17
    setenv PATH ${JAVA_HOME}/bin:${PATH}


If you re-login into your shell and run the command

    java -version


you should now see that your JDK is 1.7.


### 2. Install MAVEN 3

Download MAVEN 3 from

    wget http://ftp.udc.es/apache/maven/maven-3/3.0.4/binaries/apache-maven-3.0.4-bin.tar.gz

Now you need to configure the PATH. For Bash Shell:

    export MAVEN_HOME=/yourpath/local/apache-maven-3.0.4
    export PATH=${MAVEN_HOME}/bin:${PATH}

For tcsh shell:

    setenv MAVEN3_HOME ~/local/apache-maven-3.0.4
    setenv PATH ${MAVEN3}/bin:{PATH}

If you re-login into your shell and run the command

    mvn -version

You should see reference to the MAVEN version you have just installed plus the JDK 7 that is using.


### 3. Download and install statistical backend - DBpedia Spotlight

*If you already have installed DBpedia Spotlight for [ixa-pipe-ned
module](https://github.com/ixa-ehu/ixa-pipe-ned/) in your machine,
please go to [step 4](#4-download-and-update-the-language-models)
directly. Otherwise, follow the detailed steps*

Download the statistical backend of DBpedia Spotlight:

    wget http://spotlight.sztaki.hu/downloads/dbpedia-spotlight-0.7.jar

 
Go to the directory the dbpedia-spotlight-0.7.jar is located and execute:

    mvn install:install-file -Dfile=dbpedia-spotlight-0.7.jar -DgroupId=ixa -DartifactId=dbpedia-spotlight -Dversion=0.7 -Dpackaging=jar -DgeneratePom=true

This command will install dbpedia-spotlight jar as a local maven repository.


### 4. Download and update the language models

*If you already use DBpedia Spotlight for [ixa-pipe-ned
module](https://github.com/ixa-ehu/ixa-pipe-ned/) in your
machine, you only have to change the parameter as described at the end
of this step*

Download the language models:

    wget http://spotlight.sztaki.hu/downloads/en_2+2.tar.gz  (English model)
    wget http://spotlight.sztaki.hu/downloads/es.tar.gz   (Spanish model)

Untar the language models:

    tar xzvf $lang.tar.gz

*$lang variable refers to "en_2+2" for English and "es" for Spanish*

Change a parameter in the model in order to do wikification: open the
$lang/spotter_thresholds.txt file and change the last number to 1.0
(you should have something like this: 1.0 0.2 -0.2 1.0)


### 5. Start the DBpedia Spotlight server

*If you already use DBpedia Spotlight for [ixa-pipe-ned
module](https://github.com/ixa-ehu/ixa-pipe-ned/) in your machine and
the server is running at the moment, you have to stop the server,
change the model parameter as described in the previous step and
re-start the server as described here*

    java -jar dbpedia-spotlight-0.7.jar $lang http://localhost:$port/rest 

*$port variable refers to 2020 for English and 2030 for Spanish*

### 6. Get module source code

    git clone https://github.com/ixa-ehu/ixa-pipe-wikify


### 7. Compile

    cd ixa-pipe-wikify
    mvn clean package

This step will create a directory called 'target' which contains
various directories and files. Most importantly, there you will find
the module executable:

    ixa-pipe-wikify-1.2.0.jar

This executable contains every dependency the module needs, so it is
completely portable as long as you have a JVM 1.7 installed.


## USAGE

The *ixa-pipe-wikify* requires a NAF document (with *wf* and
*term* elements) as standard input and outputs NAF through standard
output. You can get the necessary input for *ixa-pipe-wikify* by
piping *[ixa-pipe-tok](https://github.com/ixa-ehu/ixa-pipe-tok)* and
*[ixa-pipe-pos](https://github.com/ixa-ehu/ixa-pipe-pos)* as shown in
the example below.

It also requires the port number as argument. The port numbers
assigned to each language are the following:

   - en: 2020
   - es: 2030

Once you have a DBpedia Spotlight Rest server running you can send
queries to it via the *ixa-pipe-wikify* module as follows:

    cat text.txt | ixa-pipe-tok | ixa-pipe-pos | java -jar ixa-pipe-wikify-1.1.0.jar -p $PORT_NUMBER


When the language is Spanish, the module offers an
additional feature. It is possible to set the corresponding English
entry also. To execute this option, first download the required files
and place them where the executable jar is located, under 'resources'
folder, following these steps:

    cd target
    mkdir resources
    cd resources
    wget http://ixa2.si.ehu.es/ixa-pipes/models/wikipedia-db.tar.gz
    tar xzvf wikipedia-db.tar.gz
    
Execute the module as follows:

    cat text.txt | ixa-pipe-tok | ixa-pipe-pos | java -jar ixa-pipe-wikify-1.1.0.jar -p $PORT_NUMBER -c



For more options running *ixa-pipe-wikify*:

    java -jar ixa-pipe-wikify-1.1.0.jar -h


#### Contact information

    Rodrigo Agerri and Arantxa Otegi
    {rodrigo.agerri,arantza.otegi}@ehu.es
    IXA NLP Group
    University of the Basque Country (UPV/EHU)
    E-20018 Donostia-San Sebastián


