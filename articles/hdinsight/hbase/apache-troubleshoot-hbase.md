---
title: Felsöka HBase med Azure HDInsight
description: Få svar på vanliga frågor om att arbeta med HBase och Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 13a4831d946eb7e25e586cafae4cae51b49fd8a7
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780762"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Felsöka Apache HBase med hjälp av Azure HDInsight

Lär dig mer om de vanligaste problemen och deras lösningar när du arbetar med Apache HBase-nyttolaster i Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Hur gör jag för att köra kommando rapporter för hbck med flera otilldelade regioner?

Ett vanligt fel meddelande som kan visas när du kör `hbase hbck` kommandot är "flera regioner som inte är tilldelade eller hål i kedjan av regioner".

I HBase Master användar gränssnitt kan du se antalet regioner som inte är balanserade över alla region servrar. Sedan kan du köra `hbase hbck` kommandot för att se hålen i region kedjan.

Hålen kan ha orsakats av offline-regionerna, så åtgärda tilldelningarna först. 

Utför följande steg för att ta tillbaka de otilldelade regionerna till ett normalt tillstånd:

1. Logga in på HDInsight HBase-klustret med hjälp av SSH.
2. Kör `hbase zkcli` kommandot för att ansluta till Apache ZooKeeper Shell.
3. `rmr /hbase/regions-in-transition` Kör kommandot`rmr /hbase-unsecure/regions-in-transition` eller kommandot.
4. Om du vill avsluta `hbase zkcli` gränssnittet använder du `exit` kommandot.
5. Öppna Apache Ambari-ANVÄNDARGRÄNSSNITTET och starta om tjänsten Active HBase Master.
6. `hbase hbck` Kör kommandot igen (utan några alternativ). Kontrol lera utdata för kommandot för att säkerställa att alla regioner tilldelas.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Hur gör jag för att åtgärda timeout-problem när du använder hbck-kommandon för områdes tilldelningar?

### <a name="issue"></a>Problem

En möjlig orsak till timeout-problem när du använder `hbck` kommandot kan vara att flera regioner är i läget "i över gång" under en längre tid. Du kan se dessa regioner som offline i HBase Master gränssnittet. Eftersom ett stort antal regioner försöker över gången kan HBase Master tids gräns och inte kunna ta tillbaka dessa regioner online igen.

### <a name="resolution-steps"></a>Lösningsanvisningar

1. Logga in på HDInsight HBase-klustret med hjälp av SSH.
2. Kör `hbase zkcli` kommandot för att ansluta till Apache ZooKeeper Shell.
3. `rmr /hbase/regions-in-transition` Kör kommandot`rmr /hbase-unsecure/regions-in-transition` eller.
4. Om du vill `hbase zkcli` avsluta gränssnittet använder du `exit` kommandot.
5. Starta om tjänsten Active HBase Master i Ambari-ANVÄNDARGRÄNSSNITTET.
6. `hbase hbck -fixAssignments` Kör kommandot igen.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Hur gör jag för att tvinga-inaktivera HDFS fel säkert läge i ett kluster?

### <a name="issue"></a>Problem

Det lokala Apache Hadoop Distributed File System (HDFS) är fastnat i fel säkert läge i HDInsight-klustret.

### <a name="detailed-description"></a>Detaljerad beskrivning

Felet kan bero på ett fel när du kör följande HDFS-kommando:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

Felet som du kan se när du försöker köra kommandot ser ut så här:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
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

HDInsight-klustret har skalats ned till ett par noder. Antalet noder är under eller nära den HDFS-replikeringslänken.

### <a name="resolution-steps"></a>Lösningsanvisningar 

1. Hämta status för HDFS i HDInsight-klustret genom att köra följande kommandon:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
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
2. Du kan också kontrol lera integriteten hos HDFS i HDInsight-klustret med hjälp av följande kommandon:

   ```apache
   hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
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

3. Om du anser att det inte finns några saknade, skadade eller replikerade block, eller om dessa block kan ignoreras, kör du följande kommando för att ta bort namn-noden från fel säkert läge:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Hur gör jag för att åtgärda problem med JDBC eller SQLLine-anslutningen med Apache Phoenix?

### <a name="resolution-steps"></a>Lösningsanvisningar

Om du vill ansluta till Apache Phoenix måste du ange IP-adressen för en aktiv Apache ZooKeeper-nod. Se till att ZooKeeper-tjänsten som sqlline.py försöker ansluta till är igång.
1. Logga in på HDInsight-klustret med hjälp av SSH.
2. Ange följande kommando:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Du kan hämta IP-adressen för den aktiva ZooKeeper-noden från Ambari-ANVÄNDARGRÄNSSNITTET. Gå till **HBase** > **Quick Links** > **ZK\* (Active)**  > **Zookeeper info**. 

3. Om sqlline.py ansluter till Phoenix och inte tids gränsen kör du följande kommando för att verifiera tillgänglighet och hälsa för Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Om kommandot fungerar finns det inga problem. Den IP-adress som användaren angav kan vara felaktig. Men om kommandot pausar en längre tid och sedan visar följande fel, fortsätter du till steg 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Kör följande kommandon från Head-noden (hn0) för att diagnosticera tillståndet för Phoenix-systemet. Katalog tabell:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   Kommandot ska returnera ett fel som liknar följande: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. I Apache Ambari-ANVÄNDARGRÄNSSNITTET utför du följande steg för att starta om HMaster-tjänsten på alla ZooKeeper-noder:

    1. I avsnittet **Sammanfattning** i HBase går du till **HBase** > **Active HBase Master**. 
    2. I avsnittet **komponenter** startar du om HBase master tjänsten.
    3. Upprepa de här stegen för alla återstående **vänte läges HBase Master** tjänster. 

Det kan ta upp till fem minuter innan den HBase Master tjänsten stabiliseras och slutför återställnings processen. Efter några minuter upprepar du sqlline.py-kommandona för att bekräfta att systemet. Katalog tabellen är upp och kan frågas. 

När systemet. Katalog tabellen är tillbaka till normal, anslutnings problem till Phoenix bör lösas automatiskt.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>Vad gör det inte att starta en huvud server?

### <a name="error"></a>Fel 

Ett Atom namnbytes fel inträffar.

### <a name="detailed-description"></a>Detaljerad beskrivning

Under start processen slutför HMaster många initierings steg. Dessa inkluderar att flytta data från mappen Scratch (. tmp) till mappen data. HMaster tittar också på mappen Write-Ahead-loggar (WALs) för att se om det finns några icke-tillgängliga region servrar och så vidare. 

Under starten gör HMaster ett grundläggande `list` kommando i dessa mappar. När som helst ser HMaster en oväntad fil i någon av dessa mappar, utlöses ett undantag och startar inte.  

### <a name="probable-cause"></a>Möjlig orsak

I region Server loggarna försöker du identifiera tids linjen för att skapa filen och kontrollerar sedan om det fanns en process krasch runt den tidpunkt då filen skapades. (Kontakta HBase-supporten för att hjälpa dig med detta.) Detta hjälper oss att tillhandahålla mer robusta mekanismer så att du kan undvika att ta del av den här buggen och se till att processerna går smidigt.

### <a name="resolution-steps"></a>Lösningsanvisningar

Kontrol lera anrops stacken och försök att avgöra vilken mapp som kan orsaka problemet (till exempel kan det vara mappen WALs eller mappen. tmp). Försök sedan att hitta problem filen i Cloud Explorer eller med HDFS-kommandon. Detta är vanligt vis en \*-renamePending. JSON-fil. (RenamePending. JSON -filenärenjournalfilsomanvändsförattimplementeraAtomicRename-åtgärdeniWASB-drivrutinen.\* På grund av buggar i den här implementeringen kan de här filerna lämnas över efter att process kraschar och så vidare.) Framtvinga-ta bort den här filen antingen i Cloud Explorer eller med HDFS-kommandon. 

Ibland kan det också finnas en temporär fil som heter *$ $ $. $ $ $* på den här platsen. Du måste använda HDFS `ls` -kommandot för att se den här filen. du kan inte se filen i Cloud Explorer. Om du vill ta bort den här filen använder `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`du kommandot HDFS.  

När du har kört dessa kommandon ska HMaster starta omedelbart. 

### <a name="error"></a>Fel

Ingen server adress visas i *HBase: meta* för region xxx.

### <a name="detailed-description"></a>Detaljerad beskrivning

Du kan se ett meddelande i Linux-klustret som anger att *HBase: meta* -tabellen inte är online. Om `hbck` du kör kan du rapportera att "HBase: meta Table replicaId 0 finns inte i någon region." Problemet kan vara att HMaster inte kunde initieras efter att du har startat om HBase. I HMaster-loggarna kan du se meddelandet: "Ingen server adress visas i HBase: meta för region HBase: namn \<\>på säkerhets kopia av region".  

### <a name="resolution-steps"></a>Lösningsanvisningar

1. I HBase-gränssnittet anger du följande kommandon (ändra de faktiska värdena efter vad som är tillämpligt):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Ta bort posten *HBase: namespace* . Posten kan vara samma fel som rapporteras när *HBase: namespace* -tabellen genomsöks.

3. Om du vill ta HBase i ett körnings tillstånd startar du om den aktiva HMaster-tjänsten i användar gränssnittet för Ambari.  

4. Kör följande kommando i HBase-gränssnittet för att öppna alla offline-tabeller:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Ytterligare resurser

[Det gick inte att bearbeta HBase-tabellen](https://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Fel

HMaster orsakar ett allvarligt undantag som liknar "Java. io. IOException: Stängningsåtgärd 300000ms väntar på att namn områdes tabellen ska tilldelas. "

### <a name="detailed-description"></a>Detaljerad beskrivning

Det här problemet kan uppstå om du har många tabeller och regioner som inte har tömts när du startar om dina HMaster-tjänster. Det kan hända att starta om, och du ser föregående fel meddelande.  

### <a name="probable-cause"></a>Möjlig orsak

Detta är ett känt problem med HMaster-tjänsten. Allmänna start åtgärder för klustret kan ta lång tid. HMaster stängs av eftersom namn områdes tabellen ännu inte har tilldelats. Detta inträffar endast i scenarier där det finns stora mängder data som inte har rensats och en tids gräns på fem minuter inte räcker.
  
### <a name="resolution-steps"></a>Lösningsanvisningar

1. I Apache Ambari-användargränssnittet går du till **HBase** > -**konfigurationer**. I den anpassade HBase-site. XML-filen lägger du till följande inställning: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Starta om de tjänster som krävs (HMaster och eventuellt andra HBase-tjänster).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>Vad orsakar ett omstarts fel på en region Server?

### <a name="issue"></a>Problem

Ett omstarts problem på en region Server kan förhindras genom att följa bästa praxis. Vi rekommenderar att du pausar tung arbets belastnings aktivitet när du planerar att starta om HBase region servrar. Om ett program fortsätter att ansluta till region servrar när avstängningen pågår, kommer den omstarts åtgärden för region servern att gå långsammare med flera minuter. Det är också en bra idé att först tömma alla tabeller. En referens för hur du tömmer tabeller finns i [HDInsight HBase: Så här förbättrar du Start tiden för kluster för Apache-HBase genom](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)att tömma tabeller.

Om du startar om omstarten på HBase regions servrar från Apache Ambari-ANVÄNDARGRÄNSSNITTET, ser du omedelbart att region servrarna gick ned, men inte startar om direkt. 

Följande what's händer i bakgrunden: 

1. Ambari-agenten skickar en stoppbegäran till region servern.
2. Ambari-agenten väntar i 30 sekunder på att region servern ska stängas av på ett smidigt sätt. 
3. Om programmet fortsätter att ansluta till region servern stängs servern inte av direkt. Tids gränsen på 30 sekunder upphör att gälla innan avstängning sker. 
4. Efter 30 sekunder skickar Ambari-agenten ett Force-Kill (`kill -9`)-kommando till region servern. Du kan se detta i Ambari-agentens logg (i katalogen/var/log/för respektive arbetsnoden):

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
   På grund av den plötsligt avstängningen kanske den port som är associerad med processen inte frigörs, även om region Server processen har stoppats. Den här situationen kan leda till en AddressBindException när region servern startas, som du ser i följande loggar. Du kan kontrol lera detta i region-Server. log i katalogen/var/log/HBase på arbetsnoder där region servern inte kan starta. 

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

1. Försök att minska belastningen på HBase regions servrar innan du startar en omstart. 
2. Alternativt kan du (om steg 1 inte hjälper) försöka att starta om region servrar manuellt på arbetsnoderna med hjälp av följande kommandon:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Se även
[Felsöka med Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
