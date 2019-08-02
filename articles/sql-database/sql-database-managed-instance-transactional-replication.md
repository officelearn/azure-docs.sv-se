---
title: Transaktionell replikering med Azure SQL Database | Microsoft Docs "
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
ms.openlocfilehash: db295f7644cae96eb00670cecf6e4eeba9bb6bed
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567226"
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
- För andra versioner av SQL Server som inte stöder publicering till objekt i Azure är det möjligt att använda metoden republishing för att flytta data till nyare versioner av SQL Server. [](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) 

**Distributören** är en instans eller server som samlar in ändringar i artiklarna från en utgivare och distribuerar dem till prenumeranterna. Distributören kan antingen Azure SQL Database Hanterad instans eller SQL Server (vilken version som helst som är lika med eller högre än utgivar versionen). 

**Prenumeranten** är en instans eller server som tar emot de ändringar som gjorts i utgivaren. Prenumeranter kan vara antingen enskilda, pooler och instans databaser i Azure SQL Database-eller SQL Server-databaser. En prenumerant på en databas med en eller flera databaser måste konfigureras som push-prenumerant. 

| Role | Enkla databaser och databaser i pooler | Instans databaser |
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
| [**Ögonblicks bild**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ja (endast som prenumerant) | Ja|
| [**Sammanfoga replikering**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nej | Nej|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nej | Nej|
| [**Dubbelriktad**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nej | Ja|
| [**Uppdaterings bara prenumerationer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nej | Nej|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Försök att konfigurera replikering med en äldre version kan resultera i fel nummer MSSQL_REPL20084 (processen kunde inte ansluta till prenumeranten.) och MSSQ_REPL40532 (det går inte att öppna \<Server namnet > begärd av inloggningen. Inloggningen misslyckades.)
  > - Om du vill använda alla funktioner i Azure SQL Database måste du använda de senaste versionerna av [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Support mat ris för instans databaser och lokala system
  Matrisen för replikering stöds för instans databaser är samma som den för SQL Server lokalt. 
  
  | **Utgivare**   | **Möjligheter** | **Prenumerant** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Krav

- Anslutningen använder SQL-autentisering mellan replikeringsdeltagare. 
- En Azure Storage konto resurs för arbets katalogen som används av replikeringen. 
- Port 445 (TCP utgående) måste vara öppen i säkerhets reglerna för under nätet för hanterade instanser för att få åtkomst till Azure-filresursen. 
- Port 1433 (TCP utgående) måste öppnas om utgivaren/distributören finns på en hanterad instans och prenumeranten är lokalt.

  >[!NOTE]
  > Du kan stöta på fel 53 när du ansluter till en Azure Storage-fil om den utgående nätverks säkerhets gruppen (NSG) port 445 är blockerad när distributören är en instans databas och prenumeranten är lokalt. [Uppdatera vNet-NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) för att lösa problemet. 

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

Utgivare och distributör konfigureras i en enda hanterad instans och distribuerar ändringar till andra hanterade instanser, enskilda databaser, databaser i pooler eller SQL Server lokalt. I den här konfigurationen kan utgivare/distributör-hanterad instans inte konfigureras med [geo-replikering och automatisk redundans-grupper](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Utgivare med fjär distributör på en hanterad instans

I den här konfigurationen publicerar en hanterad instans ändringar i distributören som placerats på en annan hanterad instans som kan hantera många hanterade instanser och distribuera ändringar till en eller flera mål på hanterade instanser, en enskild databas, en poolad databas eller SQL Server.

![Separata instanser för utgivare och distributör](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Utgivare och distributör konfigureras på två hanterade instanser. I den här konfigurationen

- Båda hanterade instanser finns på samma vNet.
- Båda hanterade instanser finns på samma plats.
- Hanterade instanser som är värdar för publicerade och distributions databaser kan inte vara [geo-replikerade med automatisk redundans-grupper](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Utgivare och distributör lokalt med en prenumerant på en enskild, pool och instans databas 

![Azure SQL DB som prenumerant](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
I den här konfigurationen är en Azure SQL Database (enkel, poolad och instans databas) en prenumerant. Den här konfigurationen stöder migrering från lokal plats till Azure. Om en prenumerant finns på en databas med en eller flera databaser måste den vara i push-läge.  


## <a name="next-steps"></a>Nästa steg

1. [Konfigurera replikering mellan två hanterade instanser](replication-with-sql-database-managed-instance.md). 
1. [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) med hjälp av Azure SQL Database Server namnet som prenumeranten (till exempel `N'azuresqldbdns.database.windows.net` och Azure SQL Database namn som mål databas (till exempel **AdventureWorks**. )



## <a name="see-also"></a>Se även  

- [Replikering till SQL-databas](replication-to-sql-database.md)
- [Replikering till hanterad instans](replication-with-sql-database-managed-instance.md)
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
