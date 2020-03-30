---
title: Apache Oozie-arbetsflöden & Enterprise Security - Azure HDInsight
description: Säkra Apache Oozie-arbetsflöden med hjälp av Azure HDInsight Enterprise Security Package. Läs om hur du definierar ett Oozie-arbetsflöde och skickar in ett Oozie-jobb.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/09/2019
ms.openlocfilehash: 9ef54707f7fac3dd1328e29f6d05f62c1dee2561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194911"
---
# <a name="run-apache-oozie-in-hdinsight-hadoop-clusters-with-enterprise-security-package"></a>Kör Apache Oozie i HDInsight Hadoop-kluster med Enterprise Security Package

Apache Oozie är ett arbetsflöde och samordningssystem som hanterar Apache Hadoop jobb. Oozie är integrerad med Hadoop stacken, och den stöder följande jobb:

- Apache KartaReeduce
- Apache gris
- Apache Hive
- Apache Sqoop

Du kan också använda Oozie för att schemalägga jobb som är specifika för ett system, till exempel Java-program eller skalskript.

## <a name="prerequisite"></a>Krav

Ett Azure HDInsight Hadoop-kluster med Enterprise Security Package (ESP). Se [Konfigurera HDInsight-kluster med ESP](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Detaljerade instruktioner om hur du använder Oozie på kluster som inte är ESP finns [i Använda Apache Oozie-arbetsflöden i Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="connect-to-an-esp-cluster"></a>Ansluta till ett ESP-kluster

Mer information om Secure Shell (SSH) finns i [Anslut till HDInsight (Hadoop) med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Anslut till HDInsight-klustret med hjälp av SSH:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. Använd kommandot om `klist` du vill verifiera en lyckad Kerberos-autentisering. Om inte, `kinit` använd för att starta Kerberos-autentisering.

1. Logga in på HDInsight-gatewayen för att registrera OAuth-token som krävs för att komma åt Azure Data Lake Storage:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    En statussvarskod **på 200 OK** anger lyckad registrering. Kontrollera användarnamnet och lösenordet om ett obehörigt svar tas emot, till exempel 401.

## <a name="define-the-workflow"></a>Definiera arbetsflödet

Oozie arbetsflöde definitioner är skrivna i Apache Hadoop Process Definition Language (hPDL). hPDL är ett XML-processdefinitionsspråk. Gör så här för att definiera arbetsflödet:

1. Konfigurera en domänanvändares arbetsyta:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Ersätt `DomainUser` med domännamnet.
   Ersätt `DomainUserPath` med domänanvändarens sökväg till startkatalogen.
   Ersätt `ClusterVersion` med klusterdataplattformsversionen.

2. Använd följande uttryck för att skapa och redigera en ny fil:

   ```bash
   nano workflow.xml
   ```

3. När nanoredigeraren har öppnats anger du följande XML som filinnehåll:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
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

5. Om du vill spara filen markerar du **Ctrl+X**. Ange **Y**. Välj sedan **Retur**.

    Arbetsflödet är uppdelat i två delar:

   - **Referens.** Det här avsnittet tar in de autentiseringsuppgifter som används för att autentisera Oozie-åtgärder:

     I det här exemplet används autentisering för Hive-åtgärder. Mer information finns i [Åtgärdsautentisering](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     Autentiseringsuppgifter tjänsten tillåter Oozie åtgärder för att personifiera användaren för att komma åt Hadoop tjänster.

   - **Åtgärder.** Det här avsnittet innehåller tre åtgärder: map-reduce, Hive server 2 och Hive server 1:

     - Åtgärden för att minska kartan kör ett exempel från ett Oozie-paket för kartreducering som matar ut det aggregerade ordantalet.

     - Åtgärderna Hive server 2 och Hive server 1 kör en fråga på en exempeltabell för Hive som medföljer HDInsight.

     Hive-åtgärderna använder de autentiseringsuppgifter som definierats `cred` i avsnittet autentiseringsuppgifter för autentisering med nyckelordet i åtgärdselementet.

6. Använd följande kommando för `workflow.xml` att `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`kopiera filen till:

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Ersätt `domainuser` med ditt användarnamn för domänen.

## <a name="define-the-properties-file-for-the-oozie-job"></a>Definiera egenskapsfilen för Oozie-jobbet

1. Använd följande sats för att skapa och redigera en ny fil för jobbegenskaper:

    ```bash
    nano job.properties
    ```

2. När nanoredigeraren har öppnats använder du följande XML som innehållet i filen:

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
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Använd `adl://home` URI för `nameNode` egenskapen om du har Azure Data Lake Storage Gen1 som primär klusterlagring. Om du använder Azure Blob Storage ändrar du detta till `wasb://home`. Om du använder Azure Data Lake Storage Gen2 `abfs://home`ändrar du detta till .
   - Ersätt `domainuser` med ditt användarnamn för domänen.  
   - Ersätt `ClusterShortName` med kortnamnet för klustret. Om klusternamnet till exempel är https:// *[exempellänk]* sechadoopcontoso.azurehdisnight.net `clustershortname` är det de sex första tecknen i klustret: **sechad**.  
   - Ersätt `jdbcurlvalue` med JDBC-URL:en från Hive-konfigurationen. Ett exempel är jdbc:hive2://headnodehost:10001/;transportMode=http.
   - Om du vill spara filen markerar `Y`du Ctrl+X, anger och väljer sedan **Retur**.

   Den här egenskapsfilen måste finnas lokalt när du kör Oozie-jobb.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Skapa anpassade Hive-skript för Oozie-jobb

Du kan skapa de två Hive-skripten för Hive-server 1 och Hive server 2 som visas i följande avsnitt.

### <a name="hive-server-1-file"></a>Hive server 1 fil

1. Skapa och redigera en fil för Hive server 1-åtgärder:

    ```bash
    nano countrowshive1.hql
    ```

2. Skapa skriptet:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Spara filen i Apache Hadoop Distributed File System (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>Hive server 2 fil

1. Skapa och redigera ett fält för Hive-server 2-åtgärder:

    ```bash
    nano countrowshive2.hql
    ```

2. Skapa skriptet:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Spara filen i HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Skicka Oozie jobb

Att skicka in Oozie-jobb för ESP-kluster är som att skicka in Oozie-jobb i kluster som inte är ESP-kluster.

Mer information finns i [Använda Apache Oozie med Apache Hadoop för att definiera och köra ett arbetsflöde på Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Resultat från en Oozie jobb inlämning

Oozie jobb körs för användaren. Så både Apache Hadoop YARN och Apache Ranger granskningsloggar visar de jobb som körs som den personifierade användaren. Kommandoradsgränssnittet för ett Oozie-jobb ser ut som följande kod:

```output
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

Ranger-granskningsloggarna för Hive-server 2-åtgärderna visar att Oozie kör åtgärden för användaren. Vyerna Ranger och YARN är bara synliga för klusteradministratören.

## <a name="configure-user-authorization-in-oozie"></a>Konfigurera användarauktorisering i Oozie

Oozie i sig har en konfiguration av användarauktorisering som kan blockera användare från att stoppa eller ta bort andra användares jobb. Om du vill aktivera `oozie.service.AuthorizationService.security.enabled` `true`den här konfigurationen ställer du in till . 

Mer information finns i [Installation och konfiguration av Apache Oozie](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

För komponenter som Hive server 1 där Ranger-plugin-programmet inte är tillgängligt eller stöds är endast GROVkornig HDFS-auktorisering möjlig. Finkornigt tillstånd är endast tillgängligt via Ranger plug-ins.

## <a name="get-the-oozie-web-ui"></a>Skaffa Oozie webbgränssnittet

Oozie webbgränssnitt ger en webbaserad vy över status för Oozie-jobb i klustret. Så här hämtar du webbgränssnittet i ESP-kluster:

1. Lägg till en [kantnod](../hdinsight-apps-use-edge-node.md) och aktivera [SSH Kerberos-autentisering](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Följ [Oozie-webbgränssnittsstegen](../hdinsight-use-oozie-linux-mac.md) för att aktivera SSH-tunnel till kantnoden och komma åt webbgränssnittet.

## <a name="next-steps"></a>Nästa steg

- [Använd Apache Oozie med Apache Hadoop för att definiera och köra ett arbetsflöde på Linux-baserade Azure HDInsight](../hdinsight-use-oozie-linux-mac.md).
- [Anslut till HDInsight (Apache Hadoop) med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
