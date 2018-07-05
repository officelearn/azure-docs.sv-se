---
title: Hadoop Oozie arbetsflöden i HDInsight domän domänanslutna kluster
description: Använd Hadoop Oozie i Linux-baserade HDInsight-domänen ansluten säkerhetspaketet för företag. Lär dig hur du definierar ett Oozie-arbetsflöde och skicka en Oozie-jobb.
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 9e5b7303830f2064f764d2de023b4a3ff9b0ea9f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450431"
---
#<a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>Kör Apache Oozie i domänen ansluten HDInsight Hadoop-kluster
Oozie är ett arbetsflöde och koordination system som hanterar Hadoop-jobb. Oozie är integrerad med Hadoop-stacken och stöder följande jobb:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Du kan också använda Oozie för att schemalägga jobb som är specifika för ett system, t.ex. Java-program eller kommandoskript.

##<a name="prerequisites"></a>Krav:
- Ett domänanslutet HDInsight Hadoop-kluster. Se [konfigurera domänanslutna HDInsight-kluster](./apache-domain-joined-configure-using-azure-adds.md)

    > [!NOTE]
    > Se detaljerade anvisningar för med Oozie på domänen anslutna kluster finns i [detta](../hdinsight-use-oozie-linux-mac.md)

##<a name="connecting-to-domain-joined-cluster"></a>Ansluta till domänen domänanslutna kluster
Mer information om SSH finns i [autentisering: domänanslutet HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
- Anslut till HDInsight-klustret med hjälp av SSH:
     ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```
Om du vill kontrollera om Kerberos-autentisering lyckades genom att använda `klist` kommando. Om inte, Använd `kinit` att initiera Kerberos-autentisering.

- Logga in på HDInsight-gatewayen ska registreras oAuth-token som krävs för att komma åt Azure Data Lake Store (ADLS)
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Svaret statuskod _200 OK_ anger lyckad registrering. Sök efter användarnamn och lösenord om en obehörig svaret är mottagna (401).

## <a name="define-the-workflow"></a>Definiera arbetsflödet
Oozie arbetsflödesdefinitioner skrivs i Hadoop processen Definition Language (hPDL), vilket är en definitionsspråk för XML-processen. Använd följande steg för att definiera arbetsflödet:

-   Att konfigurera domänanvändare arbetsyta:
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
Ersätt `DomainUser` med namnet på användaren. Ersätt `DomainUserPath` med sökvägen till arbetskatalogen för domänanvändaren. Ersätt `ClusterVersion` med HDP klusterversionen.

-   Använd följande instruktion för att skapa och redigera en ny fil:
 ```bash
nano workflow.xml
 ```

- När nanoredigeraren öppnas anger du följande XML som filinnehållet:
 ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
 ```
Ersätt `clustername` med namnet på klustret. 

Om du vill spara filen, Välj Ctrl + X, ange `Y`, och välj sedan **RETUR**.

Arbetsflödet är uppdelad i två delar:
*   Avsnittet för autentiseringsuppgifter: Avsnittet autentiseringsuppgifter tar in de autentiseringsuppgifter som ska användas för att autentisera oozie åtgärder:

    I det här exemplet används autentisering för Hive-åtgärder. Mer information finns i [detta]( https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

    Tjänsten credential tillåter oozie-åtgärder att personifiera användaren för att komma åt Hadoop-tjänster.

*   Åtgärd-avsnitt: Vi har tre åtgärder här map reduce, hive server 2-åtgärd och en hive-servern 1 åtgärd:

    Map-reduce körs ett exempel från oozie-paketet för kartminskningssystem som matar ut den sammanställda ordräkning.

    Hive server 2- och hive serveråtgärder 1 kör en enkel fråga för hivesample tabellen som medföljer HDInsight.

    Hive-åtgärder använder de autentiseringsuppgifter som definierats i avsnittet autentiseringsuppgifter för autentisering med hjälp av nyckelordet `cred` i elementet åtgärd

- Använd följande kommando för att kopiera den `workflow.xml` filen till `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

    Ersätt `domainuser` med ditt användarnamn för domänen.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definiera Egenskapsfilen för Oozie-jobb

1.  Använd följande instruktion för att skapa och redigera en ny fil för egenskaper:
     ```bash
    nano job.properties
     ```

2.   När nanoredigeraren öppnas, använder du följande XML som innehållet i filen:

    ```bash
        nameNode=adl://home
        jobTracker=headnodehost:8050
        queueName=default
        examplesRoot=examples
        oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
        hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
        hiveScript2=${nameNode}/user/${user.name}/countrowshive1.hql
        oozie.use.system.libpath=true
        user.name=[domainuser]
        jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
        jdbcURL=[jdbcurlvalue]
        hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
        hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
    ```
    

   * Ersätt `domainuser` med ditt användarnamn för domänen.
   * Ersätt `ClusterShortName` med kortnamn för klustret. Om klusternamn är https://sechadoopcontoso.azurehdisnight.net, `clustershortname` är de första sex bokstäverna för klustret: sechad
   * Ersätt `jdbcurlvalue` med JDBC-webbadressen från hive konfig. Till exempel jdbc:hive2: / / headnodehost:10001 /; transportMode = http.
    
   * Om du vill spara filen, Välj Ctrl + X, ange `Y`, och välj sedan **RETUR**.

   * För Egenskapsfilen måste finnas lokalt när du kör oozie-jobb

## <a name="creating-custom-hive-scripts-for-the-oozie-job"></a>Skapa anpassade hive-skript för Oozie-jobb
2 hive-skript för hive-servern 1 och hive server 2 kan skapas på följande:
-   Hive Server 1 fil:
1.  Använd följande instruktion för att skapa och redigera en fil för hive server 1-åtgärd:
    ```bash
    nano countrowshive1.xml
    ```

2.  Skapa skriptet
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Spara filen på Hdfs
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

-   Hive Server 2-fil:
1.  Använd följande instruktion för att skapa och redigera ett fält för hive server 2-åtgärd:
    ```bash
    nano countrowshive2.xml
    ```

2.  Skapa skriptet
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Spara filen på Hdfs
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submission-of-oozie-jobs"></a>Överföring av Oozie-jobb
Skicka oozie-jobb för domänanslutna kluster liknar skicka oozie-jobb i domänen domänanslutna kluster.

Mer information finns i [skicka och hantera jobbet](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-oozie-job-submission"></a>Resultat från Oozie jobböverföring
Eftersom oozie jobb körs på uppdrag av användaren, granska både Yarn och Ranger loggarna visa jobb som körs som den personifierade användaren. CLI-utdata för jobbet oozie se ut så här:


```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

*    Ranger granskningsloggarna för hive server 2 åtgärder visar oozie exekveringen användarens räkning. Ranger och Yarn vyn visas endast för kluster-administratör.

## <a name="configuration-of-user-authorization-in-oozie"></a>Konfigurationen av användarautentisering i Oozie
Oozie ensamt har en konfiguration för användarautentisering, som kan blockera användare från att stoppa avslutar andra användares jobb. Detta aktiveras genom att ange den `oozie.service.AuthorizationService.security.enabled` till `true`. 

Mer information om detta finns i Oozie dokumentation avsnittet - [auktorisering Användarkonfiguration]( https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html):

För komponenter som hive-servern där Ranger-plugin-programmet inte är tillgänglig/stöds 1 är endast coarse-grained hdfs auktorisering möjligt. Bra detaljerade auktorisering är bara tillgänglig via ranger-plugin-program.

## <a name="oozie-web-ui"></a>Oozie-Webbgränssnittet
Oozie webbgränssnittet ger en webbaserad översikt över statusen för Oozie-jobb i klustret. Domän ansluten kluster som du behöver:

1. Lägg till en [kantnoden](../hdinsight-apps-use-edge-node.md) och aktivera [SSH Kerberos-autentisering](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. Följ den [Oozie webbgränssnittet](../hdinsight-use-oozie-linux-mac.md) steg för att aktivera SSH-tunnel till gränsnoden och komma åt webbgränssnittet.

## <a name="next-steps"></a>Nästa steg
* [Använda Oozie med Hadoop för att definiera och köra ett arbetsflöde på Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md)
* [Använda tidsbaserad Oozie-koordinator](../hdinsight-use-oozie-coordinator-time.md)
* [Köra Hive-frågor med SSH på domänanslutna HDInsight-kluster](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
