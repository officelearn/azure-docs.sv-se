---
title: Distribuerade transaktioner över molndatabaser
description: Översikt över Elastic Database transaktioner med Azure SQL Database
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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568282"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Distribuerade transaktioner över molndatabaser

Med Elastic Database Transactions för Azure SQL Database (SQL DB) kan du köra transaktioner som sträcker sig över flera databaser i SQL DB. Elastic Database-transaktioner för SQL DB är tillgängliga för .NET-program med ADO .NET och integreras med den välbekanta programmerings upplevelsen med [system. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) -klasser. Information om hur du hämtar biblioteket finns i [.NET Framework 4.6.1 (webb installations program)](https://www.microsoft.com/download/details.aspx?id=49981).

Lokalt, ett sådant scenario som vanligt vis krävs för att köra Microsoft koordinator för distribuerad transaktion (MSDTC). Eftersom MSDTC inte är tillgängligt för Platform-as-a-service-program i Azure, har möjligheten att koordinera distribuerade transaktioner nu integrerats direkt i SQL-databasen. Program kan ansluta till alla SQL Database för att starta distribuerade transaktioner, och en av databaserna översätts transparent till den distribuerade transaktionen, som visas i följande bild. 

  ![Distribuerade transaktioner med Azure SQL Database med Elastic Database-transaktioner ][1]

## <a name="common-scenarios"></a>Vanliga scenarier

Elastic Database-transaktioner för SQL DB gör det möjligt för program att utföra atomiska ändringar av data som lagras i flera olika SQL-databaser. Förhands granskningen fokuserar på utvecklings upplevelser på C# klient sidan i och .net. En upplevelse på Server sidan med T-SQL planeras för en senare tidpunkt.  
Transaktioner i Elastic Database är mål för följande scenarier:

* Program med flera databaser i Azure: I det här scenariot är data lodrätt partitionerade över flera databaser i SQL DB, så att olika typer av data finns på olika databaser. Vissa åtgärder kräver ändringar av data som lagras i två eller flera databaser. Programmet använder elastiska databas transaktioner för att samordna ändringarna mellan databaser och säkerställa Atomicitet.
* Shardade databas program i Azure: I det här scenariot använder data nivån [Elastic Database klient bibliotek](sql-database-elastic-database-client-library.md) eller självhorisontell partitionering för att vågrätt partitionera data över flera databaser i SQL DB. Ett framträdande användnings fall är behovet av att utföra atomiska ändringar i ett shardade program för flera klient organisationer när du ändrar intervallet för klienter. Tänk på en överföring från en klient till en annan, både på olika databaser. Ett andra fall är en detaljerad horisontell partitionering för att tillgodose kapacitets behoven för en stor klient som i sin tur ofta innebär att vissa atomiska åtgärder måste sträckas ut över flera databaser som används för samma klient organisation. Ett tredje fall är atomiska uppdateringar av referens data som replikeras mellan databaser. Atomiska, överförda åtgärder längs dessa rader kan nu samordnas över flera databaser med hjälp av för hands versionen.
  Elastic Database-transaktioner använder en incheckning i två faser för att säkerställa transaktions atomicning mellan databaser. Det passar bra för transaktioner som involverar mindre än 100 databaser i taget inom en enskild transaktion. Dessa gränser tillämpas inte, men en bör förvänta sig prestanda och framgång för att Elastic Database-transaktioner ska bli lidande när gränserna överskrids.

## <a name="installation-and-migration"></a>Installation och migrering

Funktionerna för Elastic Database-transaktioner i SQL DB tillhandahålls via uppdateringar till .NET-biblioteken system. data. dll och system. Transactions. dll. DLL: erna säkerställer att genomförande i två faser används vid behov för att säkerställa atomication. Om du vill börja utveckla program med Elastic Database-transaktioner installerar du [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) eller en senare version. När den körs på en tidigare version av .NET Framework kan transaktionerna inte befordras till en distribuerad transaktion och ett undantag aktive ras.

Efter installationen kan du använda API: er för distribuerade transaktioner i system. Transactions med anslutningar till SQL DB. Om du har befintliga MSDTC-program som använder dessa API: er, behöver du bara återskapa dina befintliga program för .NET 4,6 när du har installerat 4.6.1-ramverket. Om dina projekt är riktade till .NET 4,6, kommer de automatiskt att använda de uppdaterade DLL-filerna från den nya Ramverks versionen och API-anrop för distribuerade transaktioner i kombination med anslutningar till SQL DB.

Kom ihåg att elastiska databas transaktioner inte kräver installation av MSDTC. I stället hanteras elastiska databas transaktioner direkt av och inom SQL DB. Detta fören klar moln scenarier avsevärt eftersom en distribution av MSDTC inte krävs för att använda distribuerade transaktioner med SQL DB. Avsnitt 4 innehåller mer information om hur du distribuerar elastiska databas transaktioner och det nödvändiga .NET Framework tillsammans med dina moln program till Azure.

## <a name="development-experience"></a>Utvecklings upplevelse

### <a name="multi-database-applications"></a>Program med flera databaser

Följande exempel kod använder den välbekanta programmerings upplevelsen med .NET system. Transactions. Klassen TransactionScope upprättar en omgivande transaktion i .NET. (En "omgivande transaktion" är en som finns i den aktuella tråden.) Alla anslutningar som öppnas i TransactionScope deltar i transaktionen. Om olika databaser deltar, höjs transaktionen automatiskt till en distribuerad transaktion. Resultatet av transaktionen styrs genom att ange omfånget till Slutför för att indikera en incheckning.

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

### <a name="sharded-database-applications"></a>Shardade databas program

Elastic Database-transaktioner för SQL DB stöder också samordning av distribuerade transaktioner där du använder OpenConnectionForKey-metoden i klient biblioteket för Elastic Database för att öppna anslutningar för en utskalad data nivå. Överväg fall där du behöver garantera transaktions konsekvens för ändringar i flera olika horisontell partitionering-nyckel värden. Anslutningar till Shards som är värdar för de olika horisontell partitionering-nyckelvärdena sammanställs med hjälp av OpenConnectionForKey. I det allmänna fallet kan anslutningarna vara av olika Shards som garanterar att transaktions garantier kräver en distribuerad transaktion. I följande kod exempel visas den här metoden. Det förutsätter att en variabel med namnet shardmap används för att representera en Shard-karta från klient biblioteket för Elastic Database:

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

Azure tillhandahåller flera erbjudanden som värd för .NET-program. Jämförelse av olika erbjudanden finns i [Azure App Service, Cloud Services och Virtual Machines jämförelse](/azure/architecture/guide/technology-choices/compute-decision-tree). Om gäst operativ systemet för erbjudandet är mindre än .NET 4.6.1 krävs för elastiska transaktioner måste du uppgradera gäst operativ systemet till 4.6.1. 

Uppgraderingar till gäst operativ systemet stöds inte för närvarande av Azure App-tjänster. För Azure Virtual Machines loggar du bara in på den virtuella datorn och kör installations programmet för det senaste .NET Framework. För Azure Cloud Services måste du inkludera installationen av en nyare .NET-version i Start åtgärderna för distributionen. Begreppen och stegen beskrivs i [Installera .net på en moln tjänst roll](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Observera att installations programmet för .NET 4.6.1 kan kräva mer temporär lagring under start processen på Azure Cloud Services än installations programmet för .NET 4,6. För att säkerställa en lyckad installation måste du öka den tillfälliga lagringen för Azures moln tjänst i din service definition. csdef-fil i avsnittet LocalResources och miljö inställningarna för start uppgiften, som du ser i följande exempel:

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

## <a name="transactions-across-multiple-servers"></a>Transaktioner över flera servrar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Elastiska databas transaktioner stöds för olika SQL Database-servrar i Azure SQL Database. När transaktioner korsar SQL Database Server gränser måste de deltagande servrarna först anges i en ömsesidig kommunikations relation. När kommunikations relationen har upprättats kan alla databaser i någon av de två servrarna delta i elastiska transaktioner med databaser från den andra servern. Med transaktioner som sträcker sig över fler än två SQL Database-servrar måste en kommunikations relation finnas för alla par SQL Database servrar.

Använd följande PowerShell-cmdletar för att hantera kommunikation mellan servrar för elastiska databas transaktioner:

* **New-AzSqlServerCommunicationLink**: Använd denna cmdlet för att skapa en ny kommunikations relation mellan två SQL Database-servrar i Azure SQL Database. Relationen är symmetrisk, vilket innebär att båda servrarna kan initiera transaktioner med den andra servern.
* **Get-AzSqlServerCommunicationLink**: Använd denna cmdlet för att hämta befintliga kommunikations relationer och deras egenskaper.
* **Remove-AzSqlServerCommunicationLink**: Använd denna cmdlet för att ta bort en befintlig kommunikations relation. 

## <a name="monitoring-transaction-status"></a>Övervakar transaktions status

Använd DMV: er (Dynamic Management views) i SQL DB för att övervaka status och förlopp för dina pågående elastiska databas transaktioner. Alla DMV: er som är relaterade till transaktioner är relevanta för distribuerade transaktioner i SQL DB. Du kan hitta motsvarande lista med DMV: er här: [Transaktions relaterade vyer och funktioner för dynamisk hantering (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Dessa DMV: er är särskilt användbara:

* **sys.DM\_omlasta\_aktiva\_transaktioner**: Visar aktuella aktiva transaktioner och deras status. I kolumnen UOW (enhets arbete) kan du identifiera de olika underordnade transaktioner som tillhör samma distribuerade transaktion. Alla transaktioner inom samma distribuerade transaktion bär samma UOW-värde. Mer information finns i [DMV-dokumentationen](https://msdn.microsoft.com/library/ms174302.aspx) .
* **sys.DM\_lasta\_databastransaktioner\_** : Innehåller ytterligare information om transaktioner, till exempel placering av transaktionen i loggen. Mer information finns i [DMV-dokumentationen](https://msdn.microsoft.com/library/ms186957.aspx) .
* **sys.dm\_tran\_locks**: Innehåller information om de lås som för närvarande innehas av pågående transaktioner. Mer information finns i [DMV-dokumentationen](https://msdn.microsoft.com/library/ms190345.aspx) .

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller för närvarande för Elastic Database-transaktioner i SQL DB:

* Endast transaktioner mellan databaser i SQL DB stöds. Andra [X/Open XA-](https://en.wikipedia.org/wiki/X/Open_XA) resursposter och databaser utanför SQL DB kan inte delta i Elastic Database-transaktioner. Det innebär att elastiska databas transaktioner inte kan sträckas ut över lokalt SQL Server och Azure SQL Database. Fortsätt att använda MSDTC för distribuerade transaktioner lokalt. 
* Endast klient samordnade transaktioner från ett .NET-program stöds. Stöd på Server sidan för T-SQL, till exempel starta DISTRIBUERAd transaktion, är planerat, men ännu inte tillgängligt. 
* Transaktioner över WCF-tjänster stöds inte. Du kan till exempel ha en WCF-tjänst metod som kör en transaktion. Omslutning av anrop inom ett transaktions omfång Miss fungerar som [system. ServiceModel. ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Nästa steg

För frågor kan du kontakta oss i [SQL Database-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) och för funktions förfrågningar, lägga till dem i forumen för [SQL Database feedback](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
