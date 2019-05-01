---
title: Transaktionsreplikering med Azure SQL Database | Microsoft-Docs ”
description: Lär dig om hur du använder SQL Server-Transaktionsreplikering med enkel poolats markerar och instans databaser i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: c8d5df0f83a7ae37b9f06a5e255e9809288b6d67
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917042"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Transaktionsreplikering med enda, pooler och databaser i Azure SQL Database-instans

Transaktionsreplikering är en funktion i Azure SQL Database och SQL Server som gör det möjligt att replikera data från en tabell i Azure SQL Database eller en SQL Server till de tabeller som placeras på fjärr-databaser. Den här funktionen kan du synkronisera flera tabeller i olika databaser.

## <a name="when-to-use-transactional-replication"></a>När du ska använda Transaktionsreplikering

Transaktionsreplikering är användbart i följande scenarier:
- Publicera ändringar som gjorts i en eller flera tabeller i en databas och distribuera dem till en eller flera SQL Server eller Azure SQL-databaser som prenumererar på för att ändringarna.
- Ha flera distribuerade databaser i synkroniserat tillstånd.
- Migrera databaser från en SQL Server eller hanterad instans till en annan databas genom att kontinuerligt publicera ändringarna.

## <a name="overview"></a>Översikt

De viktigaste komponenterna i Transaktionsreplikering visas i följande bild:  

![replikering med SQL-databas](media/replication-to-sql-database/replication-to-sql-database.png)

Den **Publisher** är en instans eller en server som publicerar ändringar som görs på några tabeller (artikel) genom att skicka uppdateringar till distributören. Publicera till alla Azure SQL stöds database från en lokal SQL Server av följande versioner av SQL Server:

- SQL Server 2019 (förhandsversion)
- SQLServer 2016 till SQL 2017
- SQL Server 2014 SP1 CU3 eller större (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 eller större (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- För andra versioner av SQL Server som inte stöder publicering till objekt i Azure, är det möjligt att använda den [publicera data](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) metod för att flytta data till nyare versioner av SQL Server. 

Den **distributören** är en instans eller en server som samlar in ändringar i artiklarna från en utgivare och distribuerar dem till prenumeranter. Distributören kan vara antingen Azure SQL Database Managed Instance eller SQL Server (alla versioner som hur lång tid det är lika med eller högre än versionen som utgivare). 

Den **prenumerant** är en instans eller en server som tar emot ändringar som görs på utgivaren. Prenumeranter kan vara antingen enkel poolats markerar och instans databaser i Azure SQL Database eller SQL Server-databaser. En prenumerant för en enskild eller grupperade databas måste konfigureras som push-prenumerant. 

| Roll | Enkel och delade databaser | Instansdatabaser |
| :----| :------------- | :--------------- |
| **Utgivare** | Nej | Ja | 
| **Distributören** | Nej | Ja|
| **Pull-prenumerant** | Nej | Ja|
| **Push-prenumerant**| Ja | Ja|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > En mottagarinitierad prenumeration stöds inte när distributören är en instans-databas och prenumeranten finns inte. 

Det finns olika [typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replikering | Enkel och delade databaser | Instansdatabaser|
| :----| :------------- | :--------------- |
| [**transaktionell**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ja (endast som prenumerant) | Ja | 
| [**ögonblicksbild**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ja (endast som prenumerant) | Ja|
| [**Sammanslagningsreplikering**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nej | Nej|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nej | Nej|
| **One-way** | Ja | Ja|
| [**Bidirectional**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nej | Ja|
| [**Uppdateringsbara prenumerationer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nej | Nej|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Försök att konfigurera replikering med en äldre version kan resultera i fel antal MSSQL_REPL20084 (processen inte kunde ansluta till prenumeranten.) och MSSQ_REPL40532 (det går inte att öppna servern \<namn > begärdes vid inloggningen. Inloggningen misslyckades.)
  > - Om du vill använda alla funktioner i Azure SQL Database, måste du använda de senaste versionerna av [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Support matrix för instans-databaser och lokala system
  Replikering support matrix exempelvis databaser är samma som för SQL Server lokalt. 
  
  | **Utgivare**   | **Distributören** | **Prenumerant** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Krav

- Anslutningen använder SQL-autentisering mellan replikering deltagare. 
- Ett Azure Storage-konto-resurs för arbetskatalogen som används för replikeringen. 
- Port 445 (TCP utgående) måste vara öppna i säkerhetsregler för hanterad instans-undernätet för att komma åt Azure-filresursen. 
- Port 1433 (TCP utgående) måste öppnas om utgivare/distributören finns på en hanterad instans och prenumeranten är på plats.

  >[!NOTE]
  > Du kan stöta på fel 53 när du ansluter till en Azure Storage-fil om utgående network security group (NSG) port 445 blockeras när distributören är en instans-databas och prenumeranten är på plats. [Uppdatera vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) att lösa problemet. 

### <a name="compare-data-sync-with-transactional-replication"></a>Jämför Data Sync med Transaktionsreplikering

| | Datasynkronisering | Transaktionsreplikering |
|---|---|---|
| Fördelar | -Aktiv-aktiv-stöd<br/>Dubbelriktad kommunikation mellan lokala och Azure SQL Database | – Lägre fördröjning<br/>-Transaktionell konsekvens<br/>-Återanvända befintliga topologi efter migreringen |
| Nackdelar | – 5 minuter eller mer fördröjning<br/>– Ingen transaktionell konsekvens<br/>-Högre prestandapåverkan | -Det går inte att publicera från Azure SQL Database enkel databas eller databas i pool<br/>-Hög underhållskostnad |
| | | |

## <a name="common-configurations"></a>Vanliga konfigurationer

I allmänhet måste utgivaren och distributören vara antingen i molnet eller lokalt. Följande konfigurationer stöds: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Utgivaren med lokala distributören på en hanterad instans

![Enskild instans som utgivaren och distributören](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Utgivaren och distributören är konfigurerade i en enda hanterad instans och fördela ändringar till andra hanterad instans, enkel databas, databas eller SQL Server lokalt. I den här konfigurationen utgivare/distributören hanterad instans kan inte konfigureras med [Geo-replikering och automatisk redundansgrupper](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Utgivaren med fjärrdistributören på en hanterad instans

I den här konfigurationen en hanterad instans publicerar ändringar i distributören placeras på en annan hanterad instans som kan hantera många källa hanterade instanser och distribuera ändringarna till en eller flera mål på hanterad instans, enkel databas, databas, eller SQLServer.

![Separata instanser för utgivaren och distributören](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Utgivaren och distributören konfigureras på två hanterade instanser. I den här konfigurationen

- Både hanterade instanser är på samma virtuella nätverk.
- Både hanterade instanser är på samma plats.
- Hanterade instanser som är värdar för publiceras och distributören databaser kan inte [georeplikerad med automatisk redundans-groups](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Utgivaren och distributören lokalt med en prenumerant på en enda pool-, och database-instans 

![Azure SQL-databas som prenumerant](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
I den här konfigurationen är en Azure SQL Database (enkel, pooler och database-instans) en prenumerant. Den här konfigurationen stöder migrering från en lokal plats till Azure. Om en prenumerant finns på en enda eller grupperade databas, måste den vara i push-läget.  


## <a name="next-steps"></a>Nästa steg

1. [Konfigurera Transaktionsreplikering för en hanterad instans](replication-with-sql-database-managed-instance.md). 
1. [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Skapa en utgivarinitierad prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) genom att använda Azure SQL Database-servernamnet som prenumeranten (till exempel `N'azuresqldbdns.database.windows.net` och Azure SQL Database-namn som måldatabasen (till exempel **Adventureworks**. )



## <a name="see-also"></a>Se även  

- [Replikering till SQL-databas](replication-to-sql-database.md)
- [Replikering till Managed Instance](replication-with-sql-database-managed-instance.md)
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en utgivarinitierad prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
