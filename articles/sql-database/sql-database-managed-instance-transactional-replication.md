---
title: Transaktionsreplikering
description: Lär dig mer om att använda SQL Server transaktionell replikering med enkla, pooliska och instans databaser i Azure SQL Database.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80607599"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Transaktionell replikering med enkel-, pool-och instans databaser i Azure SQL Database

Transaktionsreplikering är en funktion i Azure SQL Database och SQL Server som gör det möjligt att replikera data från en tabell i Azure SQL Database eller en SQL Server till de tabeller som placerats i fjärrdatabaser. Med den här funktionen kan du synkronisera flera tabeller i olika databaser.

## <a name="when-to-use-transactional-replication"></a>När du ska använda Transaktionsreplikering

Transaktionell replikering är användbart i följande scenarier:
- Publicera ändringar som gjorts i en eller flera tabeller i en databas och distribuera dem till en eller flera SQL Server eller Azure SQL-databaser som prenumererar på ändringarna.
- Behåll flera distribuerade databaser i synkroniserat tillstånd.
- Migrera databaser från en SQL Server eller en hanterad instans till en annan databas genom att kontinuerligt publicera ändringarna.

## <a name="overview"></a>Översikt

Nyckel komponenterna i Transaktionsreplikering visas på följande bild:  

![replikering med SQL Database](media/replication-to-sql-database/replication-to-sql-database.png)

**Utgivaren** är en instans eller server som publicerar ändringar som gjorts i vissa tabeller (artiklar) genom att skicka uppdateringarna till distributören. Det finns stöd för att publicera till en Azure SQL-databas från en lokal SQL Server i följande versioner av SQL Server:

- SQL Server 2019 (för hands version)
- SQL Server 2016 till SQL 2017
- SQL Server 2014 SP1 CU3 eller senare (12.00.4427)
- SQL Server 2014 RTM-CU10 (12.00.2556)
- SQL Server 2012 SP3 eller senare (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- För andra versioner av SQL Server som inte stöder publicering till objekt i Azure är det möjligt att använda metoden [republishing](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) för att flytta data till nyare versioner av SQL Server. 

**Distributören** är en instans eller server som samlar in ändringar i artiklarna från en utgivare och distribuerar dem till prenumeranterna. Distributören kan antingen Azure SQL Database Hanterad instans eller SQL Server (vilken version som helst som är lika med eller högre än utgivar versionen). 

**Prenumeranten** är en instans eller server som tar emot de ändringar som gjorts i utgivaren. Prenumeranter kan vara antingen enskilda, pooler och instans databaser i Azure SQL Database-eller SQL Server-databaser. En prenumerant på en databas med en eller flera databaser måste konfigureras som push-prenumerant. 

| Roll | Enkla databaser och databaser i pooler | Instans databaser |
| :----| :------------- | :--------------- |
| **Utgivare** | Nej | Ja | 
| **Möjligheter** | Nej | Ja|
| **Pull-prenumerant** | Nej | Ja|
| **Push-prenumerant**| Ja | Ja|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > En pull-prenumeration stöds inte när distributören är en instans databas och prenumeranten inte är det. 

Det finns olika [typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replikering | Enkla databaser och databaser i pooler | Instans databaser|
| :----| :------------- | :--------------- |
| [**Standard transaktion**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ja (endast som prenumerant) | Ja | 
| [**Ögonblicksbild**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ja (endast som prenumerant) | Ja|
| [**Sammanfoga replikering**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nej | Nej|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nej | Nej|
| [**Dubbelriktad**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nej | Ja|
| [**Uppdaterings bara prenumerationer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nej | Nej|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Försök att konfigurera replikering med en äldre version kan resultera i fel nummer MSSQL_REPL20084 (processen kunde inte ansluta till prenumeranten.) och MSSQ_REPL40532 (det går inte att öppna \<server namnet> begärd inloggning. Inloggningen misslyckades.)
  > - Om du vill använda alla funktioner i Azure SQL Database måste du använda de senaste versionerna av [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Support mat ris för instans databaser och lokala system
  Matrisen för replikering stöds för instans databaser är samma som den för SQL Server lokalt. 
  
| **Utgivare**   | **Möjligheter** | **Prenumerant** |
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
- En Azure Storage konto resurs för arbets katalogen som används av replikeringen. 
- Port 445 (TCP utgående) måste vara öppen i säkerhets reglerna för under nätet för hanterade instanser för att få åtkomst till Azure-filresursen. 
- Port 1433 (TCP utgående) måste öppnas om utgivaren/distributören finns på en hanterad instans och prenumeranten inte är det. Du kan också behöva ändra den hanterade instansen NSG utgående säkerhets `allow_linkedserver_outbound` regel för för port 1433 **-destinations tjänst tag gen** från `virtualnetwork` till `internet`. 
- Alla typer av replikeringspartner (utgivare, distributör, pull-prenumerant och push-prenumerant) kan placeras på hanterade instanser, men utgivaren och distributören måste antingen vara både i molnet eller både lokalt.
- Om utgivaren, distributören och/eller prenumeranten finns i olika virtuella nätverk måste VPN-peering upprättas mellan varje entitet, så att det finns VPN-peering mellan utgivaren och distributören och/eller det finns VPN-peering mellan distributören och prenumeranten. 


>[!NOTE]
> - Du kan stöta på fel 53 när du ansluter till en Azure Storage-fil om den utgående nätverks säkerhets gruppen (NSG) port 445 är blockerad när distributören är en instans databas och prenumeranten är lokalt. [Uppdatera vNet-NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) för att lösa problemet. 


### <a name="compare-data-sync-with-transactional-replication"></a>Jämför datasynkronisering med transaktionell replikering

| | Datasynkronisering | Transaktionsreplikering |
|---|---|---|
| Fördelar | – Stöd för aktiv-aktiv<br/>– Dubbelriktad mellan lokala och Azure SQL Database | -Nedre latens<br/>– Transaktionell konsekvens<br/>-Återanvänd befintlig topologi efter migrering |
| Nackdelar | – 5 min eller mer svars tid<br/>– Ingen transaktionell konsekvens<br/>-Högre prestanda påverkan | -Det går inte att publicera från Azure SQL Database enskild databas eller databas i pooler<br/>– Kostnad för hög underhåll |
| | | |

## <a name="common-configurations"></a>Vanliga konfigurationer

I allmänhet måste utgivaren och distributören vara antingen i molnet eller lokalt. Följande konfigurationer stöds: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Utgivare med lokal distributör på en hanterad instans

![Enskild instans som utgivare och distributör](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Utgivare och distributör konfigureras i en enda hanterad instans och distribuerar ändringar till andra hanterade instanser, enskilda databaser, databaser i pooler eller SQL Server lokalt. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Utgivare med fjär distributör på en hanterad instans

I den här konfigurationen publicerar en hanterad instans ändringar i distributören som placerats på en annan hanterad instans som kan hantera många hanterade instanser och distribuera ändringar till en eller flera mål på hanterade instanser, en databas, en poolad databas eller SQL Server.

![Separata instanser för utgivare och distributör](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Utgivare och distributör konfigureras på två hanterade instanser. Det finns vissa begränsningar med den här konfigurationen: 

- Båda hanterade instanser finns på samma vNet.
- Båda hanterade instanser finns på samma plats.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Utgivare och distributör lokalt med en prenumerant på en enskild, pool och instans databas 

![Azure SQL DB som prenumerant](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
I den här konfigurationen är en Azure SQL Database (enkel, poolad och instans databas) en prenumerant. Den här konfigurationen stöder migrering från lokal plats till Azure. Om en prenumerant finns på en databas med en eller flera databaser måste den vara i push-läge.  

## <a name="with-failover-groups"></a>Med failover-grupper

Om geo-replikering har Aktiver ATS på en **utgivare** eller en **distributörs** instans i en [failover-grupp](sql-database-auto-failover-group.md), måste den hanterade instans administratören rensa alla publikationer på den gamla primära servern och konfigurera om dem på den nya primära servern efter en redundansväxling. Följande aktiviteter behövs i det här scenariot:

1. Stoppa alla migreringsjobb som körs på databasen, om det finns några.
2. Släpp metadata för prenumerationer från utgivare genom att köra följande skript på utgivar databasen:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Ta bort metadata för prenumerationen från prenumeranten. Kör följande skript i prenumerations databasen på prenumerations instansen:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Tvinga bort alla replikeringsalternativ från utgivaren genom att köra följande skript i den publicerade databasen:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Framtvinga släppa gamla distributörer från den ursprungliga primära instansen (om det växlar tillbaka till en gammal primär som används för en distributör). Kör följande skript på huvud databasen i den tidigare hanterade distributörs instansen:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Om geo-replikering har Aktiver ATS på en **prenumerants** instans i en failover-grupp ska publikationen konfigureras för att ansluta till slut punkten för den lyssnare som hanterar den hanterade instansen för en prenumerant. I händelse av en redundansväxling beror efterföljande åtgärder av den hanterade instans administratören på vilken typ av redundansväxling som har inträffat: 

- För en redundansväxling utan data förlust fortsätter replikeringen att fungera efter redundansväxlingen. 
- Replikeringen fungerar även för redundans med data förlust. Den kommer att replikera de förlorade ändringarna igen. 
- För en redundansväxling med data förlust, men data förlusten ligger utanför lagrings perioden för distributions databasen, måste den hanterade instans administratören initiera om prenumerations databasen. 

## <a name="next-steps"></a>Nästa steg

- [Konfigurera replikering mellan en MI-utgivare och prenumerant](replication-with-sql-database-managed-instance.md)
- [Konfigurera replikering mellan en MI-utgivare, MI-distributör och SQL Server prenumerant](sql-database-managed-instance-configure-replication-tutorial.md)
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) med hjälp av Azure SQL Database Server namnet som prenumeranten (till exempel `N'azuresqldbdns.database.windows.net` och Azure SQL Database namn som mål databas (till exempel **AdventureWorks**. )


Mer information om hur du konfigurerar Transaktionsreplikering finns i följande Självstudier:



## <a name="see-also"></a>Se även  

- [Replikering med en MI-och failover-grupp](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replikering till SQL-databas](replication-to-sql-database.md)
- [Replikering till hanterad instans](replication-with-sql-database-managed-instance.md)
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
