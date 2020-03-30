---
title: Distribuerade transaktioner över molndatabaser
description: Översikt över elastiska databastransaktioner med Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 3ca3e9074f28d66068d49b80915e98600759d9be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68568282"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Distribuerade transaktioner över molndatabaser

Med elastiska databastransaktioner för Azure SQL Database (SQL DB) kan du köra transaktioner som sträcker sig över flera databaser i SQL DB. Elastiska databastransaktioner för SQL DB är tillgängliga för .NET-program som använder ADO .NET och integreras med den välbekanta programmeringsupplevelsen med hjälp av [klasserna System.Transaction.](https://msdn.microsoft.com/library/system.transactions.aspx) Hämta biblioteket finns i [.NET Framework 4.6.1 (Web Installer)](https://www.microsoft.com/download/details.aspx?id=49981).

Lokalt krävs vanligtvis ett sådant scenario som kör Microsoft Distributed Transaction Coordinator (MSDTC). Eftersom MSDTC inte är tillgängligt för Plattform-som-en-Tjänst-program i Azure har möjligheten att samordna distribuerade transaktioner nu integrerats direkt i SQL DB. Program kan ansluta till valfri SQL-databas för att starta distribuerade transaktioner, och en av databaserna kommer att samordna den distribuerade transaktionen på ett transparent sätt, vilket visas i följande bild. 

  ![Distribuerade transaktioner med Azure SQL Database med elastiska databastransaktioner ][1]

## <a name="common-scenarios"></a>Vanliga scenarier

Elastiska databastransaktioner för SQL DB gör det möjligt för program att göra atomära ändringar av data som lagras i flera olika SQL-databaser. Förhandsversionen fokuserar på utvecklingsupplevelser på klientsidan i C# och .NET. En server-side upplevelse med T-SQL planeras för en senare tid.  
Elastiska databastransaktioner är inriktat på följande scenarier:

* Flerdatabasprogram i Azure: Med det här scenariot är data lodrätt partitionerade över flera databaser i SQL DB så att olika typer av data finns i olika databaser. Vissa åtgärder kräver ändringar av data som lagras i två eller flera databaser. Programmet använder elastiska databastransaktioner för att samordna ändringarna mellan databaser och säkerställa atomicitet.
* Fragmenterade databasprogram i Azure: Med det här scenariot använder datanivån [klientbiblioteket elastisk databas](sql-database-elastic-database-client-library.md) eller självsharding för att horisontellt partitionera data över många databaser i SQL DB. Ett tydligt användningsfall är behovet av att utföra atomära ändringar för ett fragmenterat program för flera innehavare när ändringar sträcker sig över klienter. Tänk till exempel på en överföring från en klient till en annan, som båda finns i olika databaser. Ett andra fall är finkornig fragmentering för att tillgodose kapacitetsbehov för en stor klient, vilket i sin tur vanligtvis innebär att vissa atomära åtgärder måste sträcka sig över flera databaser som används för samma klient. Ett tredje fall är atomära uppdateringar av referensdata som replikeras över databaser. Atomic, verkställs, operationer längs dessa linjer kan nu samordnas över flera databaser med hjälp av förhandsgranskningen.
  Elastiska databastransaktioner använder tvåfasförseringar för att säkerställa transaktionsatomalitet mellan databaser. Det är en bra passform för transaktioner som omfattar mindre än 100 databaser åt gången inom en enda transaktion. Dessa gränser tillämpas inte, men man bör förvänta sig prestanda och framgång för elastiska databastransaktioner att drabbas när överskrider dessa gränser.

## <a name="installation-and-migration"></a>Installation och migrering

Funktionerna för elastiska databastransaktioner i SQL DB tillhandahålls genom uppdateringar av .NET-biblioteken System.Data.dll och System.Transactions.dll. DLL-skalen säkerställer att tvåfassåtagande används vid behov för att säkerställa atomicitet. Om du vill börja utveckla program med elastiska databastransaktioner installerar du [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) eller en senare version. När du kör på en tidigare version av .NET-ramverket kan transaktionerna inte befordras till en distribuerad transaktion och ett undantag utlöses.

Efter installationen kan du använda api:er för distribuerade transaktioner i System.Transactions med anslutningar till SQL DB. Om du har befintliga MSDTC-program med dessa API:er kan du helt enkelt återskapa dina befintliga program för .NET 4.6 efter installation av 4.6.1 Framework. Om dina projekt är inriktade på .NET 4.6 använder de automatiskt de uppdaterade DLL:erna från den nya Framework-versionen och distribuerade transaktions-API-anrop i kombination med anslutningar till SQL DB kommer nu att lyckas.

Kom ihåg att elastiska databastransaktioner inte kräver installation av MSDTC. I stället hanteras elastiska databastransaktioner direkt av och inom SQL DB. Detta förenklar avsevärt molnscenarier eftersom en distribution av MSDTC inte är nödvändig för att använda distribuerade transaktioner med SQL DB. I avsnitt 4 beskrivs mer i detalj hur du distribuerar elastiska databastransaktioner och det nödvändiga .NET-ramverket tillsammans med dina molnprogram till Azure.

## <a name="development-experience"></a>Erfarenhet av utveckling

### <a name="multi-database-applications"></a>Program med flera databaser

Följande exempelkod använder den välbekanta programmeringsupplevelsen med .NET System.Transactions. Klassen TransactionScope upprättar en omgivande transaktion i .NET. (En "omgivande transaktion" är en transaktion som finns i den aktuella tråden.) Alla anslutningar som öppnats i TransactionScope deltar i transaktionen. Om olika databaser deltar upphöjs transaktionen automatiskt till en distribuerad transaktion. Resultatet av transaktionen styrs genom att ange omfånget för att slutföra för att ange ett genomförande.

    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }

### <a name="sharded-database-applications"></a>Fragmenterade databasprogram

Elastiska databastransaktioner för SQL DB stöder också samordning av distribuerade transaktioner där du använder Metoden OpenConnectionForKey i klientbiblioteket för elastisk databas för att öppna anslutningar för en utskalad datanivå. Överväg fall där du behöver garantera transaktionskonsekvens för ändringar över flera olika sharding nyckelvärden. Anslutningar till shards som är värdar för de olika sharding nyckelvärden är förmedlade med OpenConnectionForKey. I det allmänna fallet kan anslutningarna vara till olika shards så att säkerställa transaktionsgarantier kräver en distribuerad transaktion. Följande kodexempel illustrerar den här metoden. Det förutsätter att en variabel som kallas shardmap används för att representera en fragmentkarta från klientbiblioteket för elastisk databas:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>.NET-installation för Azure Cloud Services

Azure tillhandahåller flera erbjudanden som är värd för .NET-program. En jämförelse av de olika erbjudandena är tillgänglig i [Azure App Service, Cloud Services och Virtual Machines jämförelse](/azure/architecture/guide/technology-choices/compute-decision-tree). Om gästoperativsystemet för erbjudandet är mindre än .NET 4.6.1 som krävs för elastiska transaktioner måste du uppgradera gästoperativsystemet till 4.6.1. 

För Azure App Services stöds för närvarande inte uppgraderingar till gästoperativsystemet. För virtuella Azure-datorer loggar du bara in på den virtuella datorn och kör installationsprogrammet för det senaste .NET-ramverket. För Azure Cloud Services måste du inkludera installationen av en nyare .NET-version i startuppgifterna för distributionen. Begreppen och stegen dokumenteras i [Installera .NET på en molntjänstroll](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Observera att installationsprogrammet för .NET 4.6.1 kan kräva mer tillfällig lagring under bootstrapping-processen på Azure-molntjänster än installationsprogrammet för .NET 4.6. För att säkerställa en lyckad installation måste du öka temporär lagring för din Azure-molntjänst i filen ServiceDefinition.csdef i avsnittet LocalResources och miljöinställningarna för din startuppgift, vilket visas i följande exempel:

    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>

## <a name="transactions-across-multiple-servers"></a>Transaktioner mellan flera servrar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

Elastiska databastransaktioner stöds på olika SQL Database-servrar i Azure SQL Database. När transaktioner korsar SQL Database-servergränser måste de deltagande servrarna först ingås i en ömsesidig kommunikationsrelation. När kommunikationsrelationen har upprättats kan alla databaser på någon av de två servrarna delta i elastiska transaktioner med databaser från den andra servern. Med transaktioner som sträcker sig över mer än två SQL Database-servrar måste en kommunikationsrelation finnas på plats för alla sql database-servrar.

Använd följande PowerShell-cmdlets för att hantera kommunikationsrelationer mellan servrar för elastiska databastransaktioner:

* **New-AzSqlServerCommunicationLink**: Använd den här cmdleten för att skapa en ny kommunikationsrelation mellan två SQL Database-servrar i Azure SQL Database. Relationen är symmetrisk vilket innebär att båda servrarna kan initiera transaktioner med den andra servern.
* **Get-AzSqlServerCommunicationLink**: Använd den här cmdleten för att hämta befintliga kommunikationsrelationer och deras egenskaper.
* **Remove-AzSqlServerCommunicationLink**: Använd den här cmdleten för att ta bort en befintlig kommunikationsrelation. 

## <a name="monitoring-transaction-status"></a>Övervaka transaktionsstatus

Använd DVS (Dynamic Management Views) i SQL DB för att övervaka status och förlopp för dina pågående elastiska databastransaktioner. Alla DMVs relaterade till transaktioner är relevanta för distribuerade transaktioner i SQL DB. Du hittar motsvarande lista över DMVs här: [Transaktionsrelaterade dynamiska hanteringsvyer och funktioner (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Dessa DVS är särskilt användbara:

* **sys.dm\_tran\_aktiva\_transaktioner:** Listor för närvarande aktiva transaktioner och deras status. Kolumnen UOW (Unit Of Work) kan identifiera de olika underordnade transaktioner som tillhör samma distribuerade transaktion. Alla transaktioner inom samma distribuerade transaktion har samma UOW-värde. Mer information finns i [DMV-dokumentationen.](https://msdn.microsoft.com/library/ms174302.aspx)
* **sys.dm\_tran\_databastransaktioner:\_** Ger ytterligare information om transaktioner, till exempel placering av transaktionen i loggen. Mer information finns i [DMV-dokumentationen.](https://msdn.microsoft.com/library/ms186957.aspx)
* **sys.dm\_\_tran lås:** Ger information om de lås som för närvarande innehas av pågående transaktioner. Mer information finns i [DMV-dokumentationen.](https://msdn.microsoft.com/library/ms190345.aspx)

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller för närvarande för elastiska databastransaktioner i SQL DB:

* Endast transaktioner mellan databaser i SQL DB stöds. Andra [X/Open XA-resursleverantörer](https://en.wikipedia.org/wiki/X/Open_XA) och databaser utanför SQL DB kan inte delta i elastiska databastransaktioner. Det innebär att elastiska databastransaktioner inte kan sträckas över på lokal SQL Server och Azure SQL Database. För distribuerade transaktioner lokalt fortsätter du att använda MSDTC. 
* Endast klientkoordinerade transaktioner från ett .NET-program stöds. Stöd på serversidan för T-SQL, till exempel BEGIN DISTRIBUTED TRANSACTION, är planerat men ännu inte tillgängligt. 
* Transaktioner mellan WCF-tjänster stöds inte. Du har till exempel en WCF-tjänstmetod som kör en transaktion. Om du bifogar anropet i ett transaktionsomfång misslyckas som [system.servicemodel.protocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Nästa steg

För frågor, vänligen kontakta oss på [SQL Database forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) och för funktionsförfrågningar, lägg till dem i SQL Database feedback [forum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
