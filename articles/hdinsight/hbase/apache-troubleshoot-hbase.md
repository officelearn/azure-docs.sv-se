---
title: Felsöka HBase med Azure HDInsight
description: Få svar på vanliga frågor om hur du arbetar med HBase och Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 6ba17a3839390ed5fe503a6fe57b63d8fb119138
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713495"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Felsöka Apache HBase med Azure HDInsight

Läs mer om de viktigaste problemen och sina lösningar när du arbetar med Apache HBase-nyttolaster i Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Hur kör jag hbck kommandot rapporter med flera otilldelade regioner?

Ett vanligt felmeddelande som kan visas när du kör den `hbase hbck` kommandot är ”flera regioner som otilldelade eller tomrum i kedjan med regioner”.

I HBase Master UI ser du antalet regioner som obalanserade på alla regionservrar. Kör sedan `hbase hbck` kommando för att se tomrum i kedjan region.

Hål kanske på grund av regionerna som är offline, så åtgärda tilldelningarna först. 

Utför följande steg för att göra regionerna som inte tilldelats till normalt läge:

1. Logga in på HDInsight HBase-kluster med hjälp av SSH.
2. Om du vill ansluta med Apache ZooKeeper-shell, kör den `hbase zkcli` kommando.
3. Kör den `rmr /hbase/regions-in-transition` kommando eller `rmr /hbase-unsecure/regions-in-transition` kommando.
4. Avslutar den `hbase zkcli` shell, Använd den `exit` kommando.
5. Öppna Apache Ambari UI, och sedan starta om tjänsten aktiv HBase Master.
6. Kör den `hbase hbck` kommandot igen (utan några alternativ). Kontrollera utdata från det här kommandot för att säkerställa att alla regioner som tilldelas.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Hur kan jag åtgärda timeout-problem när du använder hbck kommandon för region tilldelningar?

### <a name="issue"></a>Problem

En möjlig orsak för timeout-problem när du använder den `hbck` kommandot kan vara att flera regioner är i tillståndet ”i ett övergångsstadium” under en längre tid. Du kan se de regionerna som offline i HBase Master UI. Eftersom ett stort antal regioner försöker övergången, HBase Master kan timeout och går inte att ta med dessa regioner online igen.

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Logga in på HDInsight HBase-kluster med hjälp av SSH.
2. Om du vill ansluta med Apache ZooKeeper-shell, kör den `hbase zkcli` kommando.
3. Kör den `rmr /hbase/regions-in-transition` eller `rmr /hbase-unsecure/regions-in-transition` kommando.
4. Avsluta den `hbase zkcli` shell, Använd den `exit` kommando.
5. Starta om tjänsten aktiv HBase Master i Ambari UI.
6. Kör den `hbase hbck -fixAssignments` -kommandot på nytt.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Hur jag force-inaktivera HDFS felsäkert läge i ett kluster?

### <a name="issue"></a>Problem

Den lokala Apache Hadoop Distributed File System (HDFS) har fastnat i felsäkert läge på HDInsight-kluster.

### <a name="detailed-description"></a>Detaljerad beskrivning

Det här felet kan orsakas av ett fel när du kör följande kommando för HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Felet kan uppstå när du försöker köra kommandot ser ut så här:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Möjlig orsak

HDInsight-klustret har skalats ned till en mycket få noder. Antalet noder som är lägre än eller nära replikeringsfaktorn HDFS.

### <a name="resolution-steps"></a>Lösningsanvisningar 

1. Hämta status för i HDFS på HDInsight-kluster genom att köra följande kommandon:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Du kan också kontrollera integriteten för HDFS på HDInsight-kluster med hjälp av följande kommandon:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Om du anser att det finns ingen saknas, skadad, eller under-replikerade block eller att de block kan ignoreras, kör du följande kommando för att ta namn ur noden ur felsäkert läge:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Hur kan jag åtgärda JDBC eller SQLLine anslutning problem med Apache Phoenix?

### <a name="resolution-steps"></a>Lösningsanvisningar

Om du vill ansluta med Apache Phoenix, måste du ange IP-adressen för en aktiv Apache ZooKeeper-nod. Kontrollera som ZooKeeper-tjänsten till vilken sqlline.py försöker ansluta är igång.
1. Logga in på HDInsight-kluster med hjälp av SSH.
2. Ange följande kommando:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Du kan hämta IP-adressen för den aktiva ZooKeeper-noden från Ambari UI. Gå till **HBase** > **snabblänkar** > **ZK\* (aktiv)** > **Zookeeper Info**. 

3. Om sqlline.py ansluter till Phoenix och har inte timeout, kör du följande kommando för att verifiera tillgängligheten och hälsotillståndet för Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Om det här kommandot fungerar, finns det inga problem. IP-adressen som anges av användaren kan vara felaktig. Om kommandot pausar en längre tid och visar sedan följande fel, Fortsätt till steg 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Kör följande kommandon från huvudnod (hn0) för att diagnostisera villkoret för Phoenix-SYSTEM. KATALOGEN tabell:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Kommandot bör returnera ett fel som liknar följande: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Utför följande steg för att starta om tjänsten HMaster på alla ZooKeeper-noder i Apache Ambari UI:

    1. I den **sammanfattning** avsnittet i HBase, gå till **HBase** > **aktiv HBase Master**. 
    2. I den **komponenter** avsnittet, starta om tjänsten HBase Master.
    3. Upprepa dessa steg för alla återstående **vänteläge HBase Master** tjänster. 

Det kan ta upp till fem minuter för tjänsten HBase Master stabiliseras och slutför återställningen. Upprepa sqlline.py-kommandon för att bekräfta att systemet efter ett par minuter. Servergruppen är igång och att den kan efterfrågas. 

När systemet. KATALOGEN tabellen är tillbaka till normal, anslutningsproblem till Phoenix bör lösas automatiskt.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Vad som orsakar en huvudserver inte kunde starta?

### <a name="error"></a>Fel 

Ett atomiskt namnändringsfel inträffar.

### <a name="detailed-description"></a>Detaljerad beskrivning

Under startprocessen Slutför HMaster många initieringsstegen. Dessa inkluderar flyttar data från mappen scratch (.tmp) till datamappen. HMaster tittar också på write-ahead (WALs) loggkatalogen för att se om det finns några svarar inte regionservrar och så vidare. 

Under starten HMaster har en grundläggande `list` på dessa mappar. Om när som helst ser HMaster en oväntad fil i någon av dessa mappar, genereras ett undantag och startar inte.  

### <a name="probable-cause"></a>Möjlig orsak

Försök att identifiera tidsplanen då filen skapades och se om det uppstod ett processkrasch runt den tid då filen skapades i serverloggar region. (Kontakta HBase support att hjälpa dig att göra detta). Det här hjälper oss tillhandahålla mer robust sätt, så att du kan undvika stöter på den här buggen och se till att rätt processen avstängningar.

### <a name="resolution-steps"></a>Lösningsanvisningar

Kontrollera anropsstacken och försök att fastställa vilken mapp som kan vara orsaken till problemet (till exempel kanske inte mappen WALs eller mappen .tmp). I Cloud Explorer eller med hjälp av HDFS-kommandon, försök sedan att leta upp problemfilen. Detta är vanligtvis en \*-renamePending.json-fil. (Den \*-renamePending.json filen är en journalfil som används för att implementera namnbyte igen i WASB-drivrutinen. På grund av fel i den här implementeringen, kan dessa filer lämnas efter krascher, osv.) Force-ta bort den här filen i Cloud Explorer eller med hjälp av HDFS-kommandon. 

Ibland kan det kan också förekomma en temporär fil som heter något som liknar *$$$. $$$* på den här platsen. Du måste använda HDFS `ls` kommando för att se den här filen, du kan inte se filen i Cloud Explorer. Ta bort den här filen genom att använda kommandot HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

När du har kört dessa kommandon, starta HMaster omedelbart. 

### <a name="error"></a>Fel

Ingen serveradress visas i *hbase: meta* för region xxx.

### <a name="detailed-description"></a>Detaljerad beskrivning

Du kan se ett meddelande på din Linux-kluster som indikerar att den *hbase: meta* tabellen är inte online. Kör `hbck` kan rapportera som ”hbase: metadata tabell replicaId 0 finns inte på valfri region”. Problemet kan vara att HMaster inte kunde initieras när du har startat om HBase. Du kan se meddelandet i HMaster-loggar: ”Ingen serveradress visas i hbase: metadata för region hbase: säkerhetskopiering \<Regionsnamn\>”.  

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Ange följande kommandon (ändra faktiska värden som är tillämpligt) i HBase-gränssnittet:  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Ta bort den *hbase: namnområde* posten. Den här posten kan vara samma fel som rapporterats när den *hbase: namnområde* tabell genomsöks.

3. Om du vill ta fram HBase i ett fungerande tillstånd i Ambari UI, starta om tjänsten Active HMaster.  

4. I HBase-gränssnittet för att lägga till alla tabeller som är offline, kör du följande kommando:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Ytterligare resurser

[Det går inte att bearbeta HBase-tabellen](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Fel

HMaster tidsgränsen uppnås med ett allvarligt undantagsfel av typen ”java.io.IOException: Nådde sin tidsgräns 300000ms väntar på att namnområdet tabell som ska tilldelas ”.

### <a name="detailed-description"></a>Detaljerad beskrivning

Det här problemet uppstå om du har många tabeller och regioner som inte har rensats när du startar om dina HMaster-tjänster. Omstart kan misslyckas och du ser det föregående felmeddelandet.  

### <a name="probable-cause"></a>Möjlig orsak

Det här är ett känt problem med tjänsten HMaster. Allmän kluster startåtgärder kan ta lång tid. HMaster stängs av eftersom tabellen namnområde ännu inte är tilldelad. Detta inträffar endast i situationer där stora mängden unflushed data finns och att en tidsgräns på fem minuter räcker inte.
  
### <a name="resolution-steps"></a>Lösningsanvisningar

1. I Apache Ambari UI, går du till **HBase** > **Peeringkonfigurationer**. Lägg till följande inställning i anpassade hbase-site.xml-filen: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Starta om de nödvändiga tjänsterna (HMaster och eventuellt andra HBase-tjänster).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Vad som orsakar en omstart av fel på en regionsserver?

### <a name="issue"></a>Problem

En omstart av fel på en regionsserver kan förhindras genom följande säkerhetsmetoder. Vi rekommenderar att du pausar arbetsbelastning aktivitet när du planerar att starta om HBase regionservrar. Om ett program fortsätter att ansluta med regionservrar när shutdown pågår, blir omstarten region server långsammare med flera minuter. Det är också en bra idé att först tömma alla tabeller. En referens för hur du tömma tabeller finns [HDInsight HBase: Hur vi kan förbättra Apache HBase-kluster omstart tid genom att rensa tabellerna](https://web.archive.org/web/20190112153155/ https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Om du har initierat omstarten på HBase regionservrar från Apache Ambari UI kan se du direkt att regionservrar fungerar korrekt, men de inte startas om direkt. 

Här är vad som händer i bakgrunden: 

1. Ambari-agenten skickar en stoppbegäran till region-servern.
2. Ambari-agent väntar på 30 sekunder för den region-servern att stänga ett smidigt sätt. 
3. Om ditt program fortsätter att ansluta med region-servern, stängs servern inte omedelbart. 30 sekunder tidsgränsen har nåtts innan avstängning inträffar. 
4. Efter 30 sekunder, Ambari-agenten skickar en force-kill (`kill -9`) för att servern region. Du kan se följande i loggen för ambari-agent (i /var/log/directory respektive arbetsnoden):

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
   På grund av avslutats, kan den port som är associerade med processen inte släppas, även om region serverprocessen har stoppats. Den här situationen kan leda till en AddressBindException när region servern startas enligt följande loggar. Du kan kontrollera detta i region-server.log i katalogen /var/log/hbase på arbetsnoderna där region-servern inte kan startas. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Försök att minska belastningen på HBase regionservrar innan du startar en omstart. 
2. Du kan också (om steg 1 inte hjälper), så försök att starta om regionservrar på arbetsnoderna manuellt med hjälp av följande kommandon:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Se även
[Felsöka med Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
