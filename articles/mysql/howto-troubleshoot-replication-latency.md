---
title: Felsöka replikeringsfördröjning – Azure Database for MySQL
description: Lär dig hur du felsöker replikeringsfördröjning med Azure Database for MySQL Läs repliker
keywords: MySQL, felsöka, replikeringsfördröjning i sekunder
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877165"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Felsöka replikeringsfördröjning i Azure Database for MySQL

Med funktionen [Läs replik](concepts-read-replicas.md) kan du replikera data från en Azure Database for MySQL-server till en skrivskyddad replik Server. Läs repliker används för att skala ut arbets belastning genom att dirigera Läs-/rapporterings frågor från programmet till replik servrar. Detta minskar belastningen på den primära servern och förbättrar programmets övergripande prestanda och svars tid när den skalas. Replikerna uppdateras asynkront med MySQL-motorns interna replikeringsteknik som utgår från replikernas position i en binär loggfil (binlog). Mer information om BinLog-replikering finns i [Översikt över MySQL BinLog-replikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

Fördröjningen för replikering på sekundär Läs repliker beror på antalet faktorer, inklusive men inte begränsat till 

- Svarstid för nätverk
- Transaktions volym på käll servern
- Beräknings nivå för källa och sekundär Läs replik Server
- Frågor som körs på den primära och den sekundära servern. 

I det här dokumentet får du lära dig hur du felsöker replikeringsfördröjning i Azure Database for MySQL. Dessutom kan du också förstå några av de vanligaste orsakerna till ökad replikeringsfördröjning på replik servrar.

## <a name="replication-concepts"></a>Metoder för replikering

När binär logg har Aktiver ATS skriver käll servern en genomförd transaktion till den binära loggen, som används för replikering. Den binära loggen är aktive rad som standard för alla nyligen etablerade servrar som har stöd för upp till 16 TB lagring. På replik servrar finns det två trådar som körs per replik Server, en som kallas IO-tråden och den andra som kallas för SQL-tråden.

- **IO-tråden** ansluter till käll servern och begär uppdaterade binära loggar. När den här tråden tar emot de binära logg uppdateringarna sparas de på en replik server i en lokal logg som kallas relä loggen.
- **SQL-tråden** läser relä loggen och tillämpar data ändringarna på replik servrarna.

## <a name="monitoring-replication-latency"></a>Övervaka replikeringsfördröjning

Azure Database for MySQL anger måttet för replikeringsfördröjning i sekunder i [Azure Monitor](concepts-monitoring.md). Detta mått är endast tillgängligt på skrivskyddade replik servrar. Det här måttet beräknas med seconds_behind_master måttet som är tillgängligt i MySQL. Om du vill förstå rotor saken med ökad replikeringsfördröjning ansluter du till replik servern med [MySQL Workbench](connect-workbench.md) eller [Azure Cloud Shell](https://shell.azure.com) och kör följande kommando:

 Ersätt värden med namnet på den faktiska replik servern och inloggnings namnet för administratörs användaren. Administratörens användar namn kräver @ \<servername> för Azure Database for mysql:

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Så här ser upplevelsen ut som i Cloud Shell terminalen
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  Kör följande kommando i samma Azure Cloud Shell terminal

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  En typisk utmatning kommer att se ut så här:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Övervaka replikeringsfördröjning&quot;:::


Utdata innehåller mycket information, men vanligt vis är det bara viktigt att fokusera på följande kolumner:

|Mått|Beskrivning|
|---|---|
|Slave_IO_State| Aktuell status för IO-tråden. Normalt är statusen &quot;väntar på att huvud datorn ska skickas&quot; om den synkroniseras. Men om du ser en status som &quot;anslutning till Master&quot;, har repliken förlorat anslutningen till huvud servern. Kontrol lera om huvud servern körs eller om en brand vägg blockerar anslutningen.|
|Master_Log_File| Den binära logg filen som originalet skrivs till.|
|Read_Master_Log_Pos| Representerar positionen i den binära logg filen där originalet skrivs.|
|Relay_Master_Log_File| Angiven representerar den binära logg filen som replik servern läser från huvud servern.|
|Slave_IO_Running| Anger om IO-tråden körs. Det bör vara &quot;Ja&quot;. Om nej, är det troligare att replikeringen är bruten.|
|Slave_SQL_Running| Indikerar om SQL-tråden körs. Det bör vara &quot;Ja&quot;. Om nej, är det troligare att replikeringen är bruten.|
|Exec_Master_Log_Pos| Visar positionen för Relay_Master_Log_File som repliken gäller. Om det finns en fördröjning bör den här positions ordningen vara mindre än Read_Master_Log_Pos.|
|Relay_Log_Space|Visar den övre gränsen för storleken på relä loggen. Du kan kontrol lera storleken genom att fråga Visa globala variabler som relay_log_space_limit.|
|Seconds_Behind_Master| Visar replikeringsfördröjning i sekunder.|
|Last_IO_Errno|Visar fel koden för IO-tråden, om sådan finns. Mer information om dessa koder finns i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_IO_Error| Visar fel meddelandet i IO-tråden, om det finns några.|
|Last_SQL_Errno|Visar SQL-trådens felkod, om sådan finns. Mer information om dessa koder finns i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Visar fel meddelandet om SQL-tråd, om det finns några.|
|Slave_SQL_Running_State| Anger den aktuella SQL-trådens status. Observera att &quot;system lås" som visas i det här läget är ett normalt beteende. Det är normalt att se status som "väntar på att en beroende transaktion ska genomföras". Det anger att repliken väntar på att huvud servern ska uppdatera genomförda transaktioner.|

Om Slave_IO_Running är ja och Slave_SQL_Running är ja fungerar replikeringen bra. 

Sedan måste du kontrol lera Last_IO_Errno, Last_IO_Error, Last_SQL_Errno och Last_SQL_Error.  Dessa fält innehåller fel numret och ett fel meddelande om det senaste felet som orsakade att SQL-tråden stoppades. Felet 0 och det tomma meddelandet betyder att det inte finns något fel. Ett värde som inte är noll i felet måste undersökas ytterligare genom att leta upp fel koden i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Vanliga scenarier för svars tider med hög replikering

### <a name="network-latency-or-high-cpu-on-source-server"></a>Nätverks fördröjning eller hög processor på käll servern

Om du observerar följande värden, är den vanligaste orsaken till fördröjningen för replikering hög nätverks fördröjning eller hög processor förbrukning på käll servern. I det här fallet körs IO-tråden och väntar på huvud servern. Huvud servern (käll servern) har redan skrivits till den binära logg filen #20, medan repliken bara har fått upp till fil #10. De primära bidrags faktorerna för svars tider med hög replikering i det här scenariot är nätverks hastighet eller hög processor användning på käll servern.  I Azure kan nätverks fördröjningen inom en region vanligt vis vara i millisekunder och över region kan gå upp till sekunder. I de flesta fall orsakas fördröjningen i IO-tråden för att ansluta till käll servern på grund av hög processor användning på käll servern, vilket ledde till att IO-trådens bearbetning blir långsam. Detta kan identifieras genom att övervaka CPU-belastningen och att observera antalet samtidiga anslutningar på käll servern med Azure Monitor.

Om du inte ser hög processor användning på käll servern kan möjliga orsaker vara nätverks fördröjning. Om du ser hög nätverks fördröjning onormalt av en plötslig rekommenderar vi att du kontrollerar [sidan Azure-status](https://status.azure.com/status) för att se till att det inte finns några kända problem eller avbrott. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>Tung burst för transaktioner på käll servern

Om du observerar följande värden är den vanligaste orsaken till replikeringsfördröjning är, tung överföring av transaktioner på käll servern. I utdata nedan, även om repliken kan hämta den binära loggen bakom bakgrunden, indikerar repliken i/o att relä logg utrymmet är fullt. Därför orsakar nätverks hastigheten inte fördröjningen eftersom repliken redan har försökt komma igång så snabbt som möjligt. I stället överskrider den uppdaterade binära logg storleken den övre gränsen för relä logg utrymmet. Om du vill felsöka det här problemet ytterligare ska den [långsamma frågans logg](concepts-server-logs.md) vara aktive rad på huvud servern. Med långsamma Query-loggar kan du identifiera tids krävande transaktioner på käll servern. De identifierade frågorna måste vara justerade för att minska svars tiden på servern. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Här följer vanliga orsaker till svars tiden i den här kategorin:

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>Replikeringsfördröjning på grund av data belastning på käll servern
I vissa fall finns det veckovis eller månatlig data belastning på käll servrar. Svars tiden för replikering kan tyvärr undvikas i det här fallet. I det här scenariot fångas replik servrarna slutligen efter att data belastningen på käll servern har slutförts.


### <a name="slowness-on-the-replica-server"></a>Långsamma på replik servern

Om du observerar följande värden kan den vanligaste orsaken vara problem på den replik server som behöver ytterligare undersökning. I det här scenariot, som visas i utdata, körs både IO-och SQL-trådarna och repliken läser samma binära loggfil som huvud skrivningen. En viss fördröjning inträffar dock på replik servern för att avspegla samma transaktion från käll servern. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Här följer vanliga orsaker till svars tiden i den här kategorin:

#### <a name="no-primary-or-unique-key-on-a-table"></a>Ingen primär eller unik nyckel för en tabell

Azure Database for MySQL använder Row-baserad replikering. Med en diskbaserad replikering skriver huvud servern händelser till den binära loggen om enskilda tabell rads ändringar. SQL-tråden i-Turn kör ändringarna till motsvarande tabell rader på replik servern. Ingen primär eller unik nyckel för en tabell är en av de vanligaste orsakerna till replikeringsfördröjning. Brist på primär eller unik nyckel leder till genomsökning av alla rader i mål tabellen efter SQL-tråd för att tillämpa ändringarna.

I MySQL är primär nyckeln ett associerat index som säkerställer snabba prestanda i frågor eftersom det inte får innehålla NULL-värden. Med InnoDB Storage Engine är tabell data fysiskt organiserade för att göra extremt snabba sökningar och sorteras baserat på den primära nyckeln. Vi rekommenderar därför att du lägger till en primär nyckel för tabeller på käll servern innan du skapar replik servern. I det här scenariot måste du lägga till primära nycklar på käll servern och återskapa Läs repliker för att hjälpa till att förbättra replikeringens svars tid.

Du kan använda följande fråga för att fastställa vilka tabeller som saknar primär nyckel på käll servern:

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>Replikeringsfördröjning på grund av tids krävande frågor på replik servern

Det är möjligt att arbets belastningen på replik servern kan förhindra att SQL-tråden hålls igång med IO-tråden. Detta är en av de vanligaste orsakerna till fördröjning vid hög replikering om det finns en tids krävande fråga på replik servern. I det här fallet ska den [långsamma fråg logg](concepts-server-logs.md) filen vara aktive rad på replik servern för att hjälpa till att felsöka problemet. Långsamma frågor kan öka resurs förbrukningen eller sakta ned servern, vilket innebär att repliken inte kan fångas upp med huvud servern. I det här scenariot måste du finjustera långsamma frågor. Snabbare frågor förhindrar blockering av SQL-tråd och förbättrar replikeringens fördröjning avsevärt.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>Replikeringsfördröjning på grund av DDL-frågor på käll servern
Om det finns ett tids krävande DDL-kommando, t. ex. [Alter Table](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) , som körs på käll servern och anta att det tog 1 timme att köra. Under den tiden kan det finnas tusentals andra frågor som körs parallellt på käll servern. När DDL: en replikeras till repliken måste MySQL-motorn köra DDL: en i en enda replikeringsrelation för att säkerställa konsekvens på databasen. Därför kommer alla andra replikerade frågor att blockeras och måste vänta i en timme eller mer tills DDL-åtgärden har slutförts på replik servern. Detta gäller oavsett om DDL-åtgärden är online eller inte. Med DDL-åtgärder förväntas replikeringen se en ökad replikeringsfördröjning.

Om du har aktiverat [långsam frågekörning](concepts-server-logs.md) på käll servern kan du identifiera det här scenariot genom att titta på långsamma fråge loggar för att se om ett DDL-kommando kördes på käll servern. Även om det går att ta bort index, byta namn på och skapa, bör du använda InPlace-algoritmen för ALTER-tabellen som kan innebära att kopiera tabell data och återskapa tabellen. Det finns vanligt vis stöd för samplacering av algoritmen för samtidig DML, men ett exklusivt lås av metadata i tabellen kan fattas kort under bearbetnings-och körnings faserna i åtgärden. För CREATE INDEX-instruktionen kan därför ALGORITMen och låset användas för att påverka tabell kopierings metoden och nivån av samtidighet för att läsa och skriva, men om du lägger till ett full text-eller RUMS index kommer det fortfarande att förhindra DML-åtgärder. Nedan visas ett exempel på hur du skapar ett index med ALGORITMer och lås satser:

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

För DDL-instruktion som kräver en låsning kan du tyvärr inte undvika replikeringsfördröjning, i stället för att dessa typer av DDL-åtgärder ska utföras under låg belastnings tider, till exempel under Nighttime för att minska potentiell påverkan.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>Replikeringsfördröjning på grund av en replik servers lägre SKU

I Azure Database for MySQL Läs repliker skapas med samma server konfiguration som huvud servern. Replik Server konfigurationen kan ändras efter att den har skapats. Men om replik servern kommer att nedgraderas kan arbets belastningen orsaka högre resursförbrukning som i sin tur kan leda till replikeringsfördröjning. Detta kan observeras genom övervakning av processor-och minnes förbrukningen för replik servern från Azure Monitor. I det här scenariot rekommenderar vi att replik serverns konfiguration ska vara lika med eller större värden än källan för att säkerställa att repliken kan hållas kvar med huvud servern.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>Förbättra replikeringsfördröjning med Server parameter justering på käll servern

I Azure Database for MySQL optimeras replikeringen så att den körs med parallella trådar på repliker som standard. För hög concurrency-arbetsbelastningar på käll servern där replik servern inte kan fångas upp, kan replikeringsfördröjning förbättras genom att parameter binlog_group_commit_sync_delay på käll servern konfigureras. Den här parametern styr hur många mikrosekunder den binära loggen ska vänta innan den binära logg filen synkroniseras. Förmånen är att i stället för att omedelbart tillämpa varje transaktion som har allokerats skickar Master de binära logg uppdateringarna i bulk. Detta minskar IO på repliken och hjälper till att förbättra prestandan. I det här scenariot kan det vara praktiskt att ange binlog_group_commit_sync_delay till 1000 eller så och övervaka svars tiden för replikeringen. Den här parametern ska anges försiktigt och utnyttjas endast för höga samtidiga arbets belastningar. För låga samtidiga scenarion med många singleton-transaktioner kan du ange binlog_group_commit_sync_delay kan läggas till i svars tiden eftersom IO-tråden väntar på Mass binära logg uppdateringar medan bara några transaktioner kan allokeras. 

## <a name="next-steps"></a>Nästa steg
Läs mer om [Översikt över MySQL BinLog-replikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
