---
title: Transaktionsreplikering
description: Lär dig mer om hur du använder SQL Server transaktionsreplikering med enskilda, poolade och instansdatabaser i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607599"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Transaktionsreplikering med databaser med enstaka, poolade och instanser i Azure SQL Database

Transaktionsreplikering är en funktion i Azure SQL Database och SQL Server som gör att du kan replikera data från en tabell i Azure SQL Database eller en SQL Server till tabellerna som placeras på fjärrdatabaser. Med den här funktionen kan du synkronisera flera tabeller i olika databaser.

## <a name="when-to-use-transactional-replication"></a>När ska transaktionsreplikering användas

Transaktionsreplikering är användbart i följande scenarier:
- Publicera ändringar som gjorts i en eller flera tabeller i en databas och distribuera dem till en eller flera SQL Server- eller Azure SQL-databaser som prenumererade på ändringarna.
- Förvara flera distribuerade databaser i synkroniserat tillstånd.
- Migrera databaser från en SQL Server eller hanterad instans till en annan databas genom att kontinuerligt publicera ändringarna.

## <a name="overview"></a>Översikt

De viktigaste komponenterna i transaktionsreplikering visas i följande bild:  

![replikering med SQL Database](media/replication-to-sql-database/replication-to-sql-database.png)

**Publisher** är en instans eller server som publicerar ändringar som gjorts i vissa tabeller (artiklar) genom att skicka uppdateringarna till distributören. Publicering till alla Azure SQL-databaser från en lokal SQL Server stöds av följande versioner av SQL Server:

- SQL Server 2019 (förhandsgranskning)
- SQL Server 2016 till SQL 2017
- SQL Server 2014 SP1 CU3 eller senare (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 eller senare (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- För andra versioner av SQL Server som inte stöder publicering till objekt i Azure är det möjligt att använda metoden för [att publicera om data](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) för att flytta data till nyare versioner av SQL Server. 

**Distributören** är en instans eller server som samlar in ändringar i artiklarna från en Utgivare och distribuerar dem till prenumeranterna. Distributören kan vara antingen Azure SQL Database hanterad instans eller SQL Server (vilken version som helst så länge den är lika med eller högre än Publisher-versionen). 

**Prenumeranten** är en instans eller server som tar emot de ändringar som gjorts i Publisher. Prenumeranter kan vara antingen enskilda databaser, poolade och instansdatabaser i Azure SQL Database- eller SQL Server-databaser. En Prenumerant i en enda eller poolad databas måste konfigureras som push-abonnent. 

| Roll | Enstaka och poolade databaser | Instansdatabaser |
| :----| :------------- | :--------------- |
| **Publisher** | Inga | Ja | 
| **Distributör** | Inga | Ja|
| **Dra prenumerant** | Inga | Ja|
| **Push-prenumerant**| Ja | Ja|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > En pull-prenumeration stöds inte när distributören är en instansdatabas och abonnenten inte är det. 

Det finns olika [typer av replikering:](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)


| Replikering | Enstaka och poolade databaser | Instansdatabaser|
| :----| :------------- | :--------------- |
| [**Standard transaktionell**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ja (endast som prenumerant) | Ja | 
| [**Ögonblicksbild**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ja (endast som prenumerant) | Ja|
| [**Koppla replikering**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Inga | Inga|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Inga | Inga|
| [**Dubbelriktad**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Inga | Ja|
| [**Uppdateringsbara prenumerationer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Inga | Inga|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Om du försöker konfigurera replikering med hjälp av en äldre version kan det leda till felnummer MSSQL_REPL20084 \<(Processen kunde inte ansluta till Prenumeranten.) och MSSQ_REPL40532 (Det går inte att öppna servernamnet> som begärs av inloggningen. Inloggningen misslyckades.)
  > - Om du vill använda alla funktioner i Azure SQL Database måste du använda de senaste versionerna av [SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools (SSDT).](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Supportmatris för instansdatabaser och lokala system
  Replikeringsstödbarhetsmatrisen för instansdatabaser är samma som den för SQL Server lokalt. 
  
| **Publisher**   | **Distributör** | **Abonnent** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Krav

- Anslutningen använder SQL-autentisering mellan replikeringsdeltagare. 
- En Azure Storage-kontoresurs för arbetskatalogen som används av replikering. 
- Port 445 (TCP-utgående) måste vara öppen i säkerhetsreglerna i det hanterade instansundernätet för att komma åt Azure-filresursen. 
- Port 1433 (TCP-utgående) måste öppnas om utgivaren/distributören är på en hanterad instans och abonnenten inte är det. Du kan också behöva ändra den hanterade instansen `allow_linkedserver_outbound` NSG utgående säkerhetsregel för `virtualnetwork` port `internet`1433 Mål **Service tag** från till . 
- Alla typer av replikeringsdeltagare (Utgivare, Distributör, Pull-prenumerant och Push-prenumerant) kan placeras på hanterade instanser, men utgivaren och distributören måste vara antingen både i molnet eller både lokalt.
- Om antingen utgivaren, distributören och/eller abonnenten finns i olika virtuella nätverk måste VPN-peering upprättas mellan varje enhet, så att det finns VPN-peering mellan utgivaren och distributören, och/eller så finns VPN-peering mellan distributören och abonnenten. 


>[!NOTE]
> - Du kan stöta på fel 53 när du ansluter till en Azure Storage File om den utgående nätverkssäkerhetsgruppen (NSG) port 445 blockeras när distributören är en instansdatabas och prenumeranten är lokal. [Uppdatera vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) för att lösa problemet. 


### <a name="compare-data-sync-with-transactional-replication"></a>Jämföra datasynkronisering med transaktionsreplikering

| | Datasynkronisering | Transaktionsreplikering |
|---|---|---|
| Fördelar | - Aktivt stöd<br/>- Dubbelriktad mellan lokala och Azure SQL Database | - Lägre latens<br/>- Transaktionell konsekvens<br/>- Återanvänd befintlig topologi efter migreringen |
| Nackdelar | - 5 min eller mer latens<br/>- Ingen transaktionell konsekvens<br/>- Högre prestandapåverkan | - Det går inte att publicera från En enda databas eller poolad databas från Azure SQL Database<br/>- Höga underhållskostnader |
| | | |

## <a name="common-configurations"></a>Vanliga konfigurationer

I allmänhet måste utgivaren och distributören vara antingen i molnet eller lokalt. Följande konfigurationer stöds: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Utgivare med lokal distributör på en hanterad instans

![En instans som utgivare och distributör](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Utgivare och distributör konfigureras i en enda hanterad instans och distribuerar ändringar till andra hanterade instanser, en databas, poolade databaser eller SQL Server lokalt. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Utgivare med fjärrdistributör i en hanterad instans

I den här konfigurationen publicerar en hanterad instans ändringar till distributör som placeras på en annan hanterad instans som kan hantera många källhanterade instanser och distribuera ändringar till ett eller flera mål på hanterad instans, en enda databas, poolad databas eller SQL Server.

![Separata instanser för utgivare och distributör](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Utgivare och distributör är konfigurerade på två hanterade instanser. Det finns vissa begränsningar med den här konfigurationen: 

- Båda hanterade instanserna finns på samma virtuella nätverk.
- Båda hanterade instanserna finns på samma plats.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Utgivare och distributör lokalt med en abonnent i en enda, poolad och instansdatabas 

![Azure SQL DB som prenumerant](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
I den här konfigurationen är en Azure SQL Database (enkel, poolad och instansdatabas) en prenumerant. Den här konfigurationen stöder migrering från lokalt till Azure. Om en prenumerant finns i en enda eller poolad databas måste den vara i push-läge.  

## <a name="with-failover-groups"></a>Med redundansgrupper

Om geo-replikering är aktiverat på en **utgivar-** eller **distributörsinstans** i en [redundansgrupp](sql-database-auto-failover-group.md)måste administratören för hanterade instans rensa alla publikationer på den gamla primärt och konfigurera om dem på den nya primären efter en redundansväxling. Följande aktiviteter behövs i det här scenariot:

1. Stoppa alla replikeringsjobb som körs i databasen, om det finns några.
2. Släpp prenumerationsmetadata från utgivaren genom att köra följande skript i utgivardatabasen:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Släpp prenumerationsmetadata från prenumeranten. Kör följande skript i prenumerationsdatabasen vid prenumerationsinstans:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Tappa alla replikeringsobjekt med kraft från utgivaren genom att köra följande skript i den publicerade databasen:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Kraftfullt släppa gamla distributör från ursprungliga primära instans (om inte tillbaka över till en gammal primär som brukade ha en distributör). Kör följande skript i huvuddatabasen i gammal distributörshanterad instans:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Om geo-replikering är aktiverat på en **prenumerantinstans** i en redundansgrupp bör publikationen konfigureras för att ansluta till slutpunkten för redundansgruppens lyssnare för den hanterade instansen för prenumeranten. I händelse av en redundans beror efterföljande åtgärder från den hanterade instansadministratören på vilken typ av redundans som inträffade: 

- För en redundans utan dataförlust fortsätter replikeringen att fungera efter redundans. 
- För en redundans med dataförlust fungerar replikering också. Det kommer att replikera de förlorade ändringarna igen. 
- För en redundans med dataförlust, men dataförlusten ligger utanför lagringsperioden för distributionsdatabasen, måste administratören för hanterade instanserialisera prenumerationsdatabasen igen. 

## <a name="next-steps"></a>Nästa steg

- [Konfigurera replikering mellan en MI-utgivare och prenumerant](replication-with-sql-database-managed-instance.md)
- [Konfigurera replikering mellan en MI-utgivare, MI-distributör och SQL Server-prenumerant](sql-database-managed-instance-configure-replication-tutorial.md)
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) med hjälp av Servernamnet för `N'azuresqldbdns.database.windows.net` Azure SQL Database som prenumerant (till exempel azure SQL-databasnamnet som måldatabas (till exempel **Adventureworks**. )


Mer information om hur du konfigurerar transaktionsreplikering finns i följande självstudier:



## <a name="see-also"></a>Se även  

- [Replikering med en mi-grupp och en redundansgrupp](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replikering till SQL-databas](replication-to-sql-database.md)
- [Replikering till hanterad instans](replication-with-sql-database-managed-instance.md)
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
