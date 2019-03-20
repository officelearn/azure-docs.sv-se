---
title: Säker Apache Oozie-arbetsflöden med Enterprise Security Package - Azure HDInsight
description: Säker Apache Oozie arbetsflöden med hjälp av Azure HDInsight Enterprise Security Package. Lär dig hur du definierar ett Oozie-arbetsflöde och skicka en Oozie-jobb.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 15cdc78559a8f299e2bf0f357bbb7c0664881712
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116902"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Kör Apache Oozie i HDInsight Hadoop-kluster med Enterprise Security Package

Apache Oozie är ett arbetsflöde och koordination system som hanterar Apache Hadoop-jobb. Oozie är integrerad med Hadoop-stacken och stöder följande jobb:
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

Du kan också använda Oozie för att schemalägga jobb som är specifika för ett system, t.ex. Java-program eller kommandoskript.

## <a name="prerequisite"></a>Krav

- Ett Azure HDInsight Hadoop-kluster med Enterprise Security Package (ESP). Se [konfigurera HDInsight-kluster med ESP](./apache-domain-joined-configure-using-azure-adds.md).

    > [!NOTE]  
    > Detaljerade anvisningar om hur du använder Oozie på icke-ESP-kluster finns i [Använd Apache Oozie-arbetsflöden i Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Ansluta till ett ESP-kluster

Läs mer på SSH (Secure Shell), [Anslut till HDInsight (Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Anslut till HDInsight-klustret med hjälp av SSH:  
   ```bash
   ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
   ```

2. För att kontrollera lyckad Kerberos-autentisering, använder de `klist` kommando. Om inte, Använd `kinit` att starta Kerberos-autentisering.

3. Logga in på HDInsight-gatewayen att registrera OAuth-token som krävs för att få åtkomst till Azure Data Lake Storage:   
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    Svaret statuskod **200 OK** anger lyckad registrering. Kontrollera användarnamnet och lösenordet om en obehörig svar tas emot, till exempel 401.

## <a name="define-the-workflow"></a>Definiera arbetsflödet
Oozie arbetsflödesdefinitioner skrivs i Apache Hadoop processen Definition Language (hPDL). hPDL är en definitionsspråk för XML-processen. Vidta följande steg för att definiera arbetsflödet:

1. Konfigurera en domänanvändare arbetsyta:
   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```
   Ersätt `DomainUser` med namnet på användaren. 
   Ersätt `DomainUserPath` med sökvägen till arbetskatalogen för domänanvändaren. 
   Ersätt `ClusterVersion` med klusterversionen Hortonworks Data Platform (HDP).

2. Använd följande instruktion för att skapa och redigera en ny fil:
   ```bash
   nano workflow.xml
   ```

3. När nanoredigeraren öppnas anger du följande XML som filinnehållet:
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
4. Ersätt `clustername` med namnet på klustret. 

5. Välj Ctrl + X för att spara filen. Ange `Y`. Välj sedan **RETUR**.

    Arbetsflödet är uppdelad i två delar:
   * **Avsnittet för autentiseringsuppgifter.** Det här avsnittet tar in de autentiseringsuppgifter som används för att autentisera Oozie åtgärder:

     Det här exemplet använder autentisering för Hive-åtgärder. Mer information finns i [åtgärd autentisering](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     Tjänsten credential tillåter Oozie-åtgärder att personifiera användaren för att komma åt Hadoop-tjänster.

   * **Åtgärdsavsnittet.** Det här avsnittet har tre åtgärder: kartminskningssystem, Hive server 2, och Hive 1:

     - Den kartminskningssystem körs ett exempel från ett Oozie-paket för kartminskningssystem som visar aggregerade ordräkning.

     - Köra en fråga på ett exempel på en Hive-tabell som medföljer HDInsight Hive server 2- och Hive-serveråtgärder 1.

     Hive-åtgärder använder de autentiseringsuppgifter som definierats i avsnittet autentiseringsuppgifter för autentisering med hjälp av nyckelordet `cred` i elementet åtgärd.

6. Använd följande kommando för att kopiera den `workflow.xml` filen till `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`:
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

7. Ersätt `domainuser` med ditt användarnamn för domänen.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definiera Egenskapsfilen för Oozie-jobb

1. Använd följande instruktion för att skapa och redigera en ny fil för egenskaper:

   ```bash
   nano job.properties
   ```

2. När nanoredigeraren öppnas, använder du följande XML som innehållet i filen:

   ```bash
       nameNode=adl://home
       jobTracker=headnodehost:8050
       queueName=default
       examplesRoot=examples
       oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
       hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
       hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
       oozie.use.system.libpath=true
       user.name=[domainuser]
       jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
       jdbcURL=[jdbcurlvalue]
       hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
       hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   * Använd den `adl://home` URI för den `nameNode` egenskapen om du har Azure Data Lake Storage Gen1 som din primära klusterlagringen. Om du använder Azure Blob Storage kan sedan ändra detta till `wasb://home`. Om du använder Azure Data Lake Storage Gen2 sedan ändra detta till `abfs://home`.
   * Ersätt `domainuser` med ditt användarnamn för domänen.  
   * Ersätt `ClusterShortName` med det korta namnet för klustret. Exempel: Om klusternamnet är https:// *[exempel länk]* sechadoopcontoso.azurehdisnight.net, den `clustershortname` är de första sex tecknen i klustret: **sechad**.  
   * Ersätt `jdbcurlvalue` med JDBC-Webbadressen från Hive-konfigurationen. Ett exempel är jdbc:hive2: / / headnodehost:10001 /; transportMode = http.      
   * Om du vill spara filen, Välj Ctrl + X, ange `Y`, och välj sedan **RETUR**.

   För Egenskapsfilen måste finnas lokalt när Oozie jobb som körs.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Skapa anpassade Hive-skript för Oozie-jobb

Du kan skapa två Hive-skript för Hive-servern 1 och Hive server 2 som visas i följande avsnitt.

### <a name="hive-server-1-file"></a>Registreringsdatafil för server 1

1.  Skapa och redigera en fil för Hive-serveråtgärder 1:
    ```bash
    nano countrowshive1.hql
    ```

2.  Skapa skriptet:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  Spara filen till Apache Hadoop Distributed File System (HDFS):
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive server 2-fil

1.  Skapa och redigera ett fält för Hive server 2 åtgärder:
    ```bash
    nano countrowshive2.hql
    ```

2.  Skapa skriptet:
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  Spara filen till HDFS:
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Skicka Oozie-jobb

Skicka Oozie-jobb för ESP-kluster är som att skicka Oozie-jobb i icke-ESP-kluster.

Mer information finns i [Använd Apache Oozie med Apache Hadoop för att definiera och köra ett arbetsflöde på Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Resultat från en Oozie jobböverföring
Oozie jobb körs för användaren. Så granska både Apache Hadoop YARN och Apache Ranger loggarna visa jobb som körs som den personifierade användaren. Kommandoradsgränssnittet utdata för ett jobb för Oozie ser ut som följande kod:



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

Ranger granskningsloggarna för Hive server 2 åtgärder visar Oozie körning av åtgärden för användaren. Vyerna Ranger och YARN är bara synliga för kluster-administratör.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurera användarautentisering i Oozie

Oozie ensamt har en konfiguration för auktorisering av användare som kan blockera användare från att stoppa eller ta bort andra användares jobb. Om du vill aktivera den här konfigurationen, ange den `oozie.service.AuthorizationService.security.enabled` till `true`. 

Mer information finns i [Apache Oozie Installation och konfiguration](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

För komponenter som Hive-servern där Ranger plugin-programmet inte är tillgänglig eller så stöds 1 är endast coarse-grained HDFS auktorisering möjligt. Detaljerade auktorisering är bara tillgänglig via Ranger plugin-program.

## <a name="get-the-oozie-web-ui"></a>Hämta Oozie-webbgränssnittet

Oozie webbgränssnittet ger en webbaserad översikt över statusen för Oozie-jobb i klustret. För att få åtkomst till webbgränssnittet, gör du följande i ESP-kluster:

1. Lägg till en [kantnoden](../hdinsight-apps-use-edge-node.md) och aktivera [SSH Kerberos-autentisering](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Följ den [Oozie webbgränssnittet](../hdinsight-use-oozie-linux-mac.md) steg för att aktivera SSH-tunnel till gränsnoden och komma åt webbgränssnittet.

## <a name="next-steps"></a>Nästa steg
* [Använda Apache Oozie med Apache Hadoop för att definiera och köra ett arbetsflöde på Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).
* [Använda tidsbaserad Apache Oozie-koordinator](../hdinsight-use-oozie-coordinator-time.md).
* [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
