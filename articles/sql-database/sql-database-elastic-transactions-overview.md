---
title: Distribuerade transaktioner över molndatabaser
description: Översikt över Elastic Database-transaktioner med Azure SQL Database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.custom: scale out apps
ms.workload: On Demand
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 0de19870b39bc679d653f3d9bdf4fea3a2c100c1
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44722154"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Distribuerade transaktioner över molndatabaser
Transaktioner för elastiska databaser för Azure SQL Database (SQL DB) kan du köra transaktioner som sträcker sig över flera databaser i SQL-databas. Transaktioner för elastiska databaser för SQL-databas är tillgängliga för .NET-program med hjälp av ADO .NET och integrera med en bekant programmering upplevelse med hjälp av den [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) klasser. I biblioteket finns [.NET Framework 4.6.1 (Webbinstallationsprogram)](https://www.microsoft.com/download/details.aspx?id=49981).

Lokalt nödvändiga sådant scenario vanligtvis körs Microsoft Distributed Transaction Coordinator (MSDTC). Eftersom MSDTC inte är tillgängliga för Platform-as-a-Service-program i Azure, har möjlighet att samordna distribuerade transaktioner nu direkt integrerats i SQL DB. Program kan ansluta till en SQL-databas för att starta distribuerade transaktioner och en av databaserna kommer transparent att samordna den distribuerade transaktionen som visas i följande bild. 

  ![Distribuerade transaktioner med Azure SQL Database med elastisk databastransaktioner ][1]

## <a name="common-scenarios"></a>Vanliga scenarier
Transaktioner för elastiska databaser för SQL DB gör det möjligt att göra ändringar i data som lagras i flera olika SQL-databaser. Förhandsversionen av fokuserar på klientsidan utveckling upplevelser i C# och .NET. En serversidan upplevelse med T-SQL är planerat för ett senare tillfälle.  
Elastisk databastransaktioner riktar sig till följande scenarier:

* Flera databasprogram i Azure: med det här scenariot data är lodrätt partitionerad över flera databaser i SQL-databas, så att olika typer av data som finns på olika databaser. Vissa åtgärder kräver ändringar av data som sparas i två eller flera databaser. Programmet använder elastiska databastransaktioner för samordning ändringarna på databaser och Atomicitet.
* Fragmenterade (sharded) databasprogram i Azure: med det här scenariot datanivå använder den [Elastic Database-klientbiblioteket](sql-database-elastic-database-client-library.md) eller self-partitionering för att partitionera data vågrätt över flera databaser i SQL-databas. En framstående användningsfall är behovet av att utföra ändringar för ett delat program för flera innehavare när ändringar sträcker sig över klienter. Se exempelvis en överföring från en klient till en annan, både som finns på olika databaser. En andra fallet är detaljerade horisontell partitionering för kapacitetsbehoven för en stor klient vilket i sin tur vanligtvis innebär att vissa atomiska operationer måste sträcka ut över flera databaser som används för samma klient. En tredje är atomiska uppdateringar för referensdata som replikeras över databaser. Atomiska, överförda, åtgärder enligt dessa principer kan nu koordinerad över flera databaser med hjälp av förhandsversionen.
  Elastisk databastransaktioner använda två faser för transaktionen Atomicitet över databaser. Det är ett bra alternativ för transaktioner som rör mindre än 100 databaserna i taget i en enda transaktion. Dessa begränsningar tillämpas inte, men en förvänta prestanda och slutförandefrekvenser för elastisk databastransaktioner påverkas när överskrider gränserna.

## <a name="installation-and-migration"></a>Installation och migrering
Funktioner för transaktioner för elastiska databaser i SQL DB sker via uppdateringar av .NET-biblioteken System.Data.dll och System.Transactions.dll. DLL-filer Se till att genomförande används där det är nödvändigt för att säkerställa Atomicitet. Om du vill börja utveckla program med elastiska databastransaktioner, installera [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) eller en senare version. När du kör en tidigare version av .NET framework kan transaktioner att kunna uppgradera till en distribuerad transaktion och ett undantag aktiveras.

Efter installationen kan du använda den distribuerade transaktionen API: er i System.Transactions med anslutningar till SQL DB. Om du har befintliga MSDTC-program med hjälp av dessa API: er kan bara återskapa dina befintliga program för .NET 4.6 när du har installerat 4.6.1 Framework. Om dina projekt riktar .NET 4.6, används automatiskt uppdaterade DLL-filer från den nya Framework-versionen och distribuerad transaktion API-anrop i kombination med anslutningar till SQL DB ska lyckas.

Kom ihåg att elastiska databastransaktioner inte behöver installera MSDTC. Elastisk databastransaktioner hanteras direkt i stället av och inom SQL DB. Detta förenklar avsevärt scenarier för molnet eftersom en distribution av MSDTC inte är nödvändigt att använda distribuerade transaktioner med SQL DB. Avsnitt 4 förklarar i detalj hur du distribuerar transaktioner elastiska databaser och nödvändiga .NET framework tillsammans med dina molnprogram till Azure.

## <a name="development-experience"></a>-Utveckling
### <a name="multi-database-applications"></a>Flera databasprogram
Följande exempelkod använder bekant programmering upplevelsen med .NET System.Transactions. Klassen TransactionScope upprättar en omgivande transaktionen i .NET. (”Omgivande transaktionen” är en som finns i den aktuella tråden.) Alla anslutningar öppnas i TransactionScope delta i transaktionen. Om olika databaser delta upphöjda transaktionen automatiskt till en distribuerad transaktion. Resultatet av transaktionen styrs av ställa in omfång utföra om du vill ange ett genomförande.

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

### <a name="sharded-database-applications"></a>Shardade databasprogram
Transaktioner för elastiska databaser för SQL-databas har också stöd för samordning av distribuerade transaktioner där du använder metoden OpenConnectionForKey av klientbiblioteket för elastiska databaser för att öppna anslutningar för en utskalad nivå. Överväg att fall där du behöver garantera transaktionell konsekvens för ändringar i flera olika horisontell partitionering nyckelvärden. Anslutningar till shards som är värd för nyckelvärden för olika horisontell partitionering är asynkrona med OpenConnectionForKey. I vanliga fall kan anslutningar vara till olika shards, så att säkerställa transaktionella garantier kräver en distribuerad transaktion. Följande kodexempel illustrerar den här metoden. Det förutsätts att en variabel med namnet shardmap används för att representera en skärvkarta från klientbibliotek för elastiska databaser:

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


## <a name="net-installation-for-azure-cloud-services"></a>Installation av .NET för Azure Cloud Services
Azure tillhandahåller flera erbjudanden som värd för .NET-program. En jämförelse av olika erbjudanden är tillgängliga i [jämförelse mellan Azure App Service, Cloud Services och virtuella datorer](../app-service/choose-web-site-cloud-service-vm.md). Om gästoperativsystemet av erbjudandet är mindre än .NET 4.6.1 som krävs för elastiska transaktioner, måste du uppgradera gästoperativsystemet till 4.6.1. 

För Azure App Services stöds uppgraderingar av gästoperativsystemet för närvarande inte. För Azure Virtual Machines kan bara logga in på den virtuella datorn och kör installationsprogrammet för senaste .NET framework. För Azure Cloud Services måste du inkludera installation av en nyare version av .NET i startåtgärder för din distribution. Koncept och steg finns dokumenterade i [installera .NET på en Molntjänstroll](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Observera att installationsprogrammet för .NET 4.6.1 kan kräva mer tillfällig lagring under bootstrapping på Azure-molntjänster än installationsprogrammet för .NET 4.6. För att säkerställa en lyckad installation, måste du öka tillfällig lagring för dina Azure-molntjänst i filen ServiceDefinition.csdef i avsnittet LocalResources och miljöinställningar för din startåtgärd som visas i följande exempel:

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
Elastisk databastransaktioner stöds mellan olika logiska servrar i Azure SQL Database. När transaktioner mellan logisk server gränser, måste deltagande servrar först anges i en relation för dubbelriktad kommunikation. När relationen kommunikation har upprättats, kan alla databaser i någon av de två servrarna delta i elastiska transaktioner med databaser från den andra servern. En kommunikation-relationen måste finnas för alla typer av logiska servrar med transaktioner som omfattar fler än två logiska servrar.

Använd följande PowerShell-cmdletar för att hantera kommunikation mellan servrar relationer för elastisk databastransaktioner:

* **Ny AzureRmSqlServerCommunicationLink**: Använd denna cmdlet för att skapa en ny relation för kommunikation mellan två logiska servrar i Azure SQL-databas. Relationen är symmetriska vilket innebär att båda servrarna kan initiera transaktioner med den andra servern.
* **Get-AzureRmSqlServerCommunicationLink**: Använd denna cmdlet för att hämta befintliga kommunikation relationer och deras egenskaper.
* **Ta bort AzureRmSqlServerCommunicationLink**: Använd denna cmdlet för att ta bort en befintlig relation för kommunikation. 

## <a name="monitoring-transaction-status"></a>Övervaka transaktionsstatus för
Använda dynamiska hanteringsvyer (DMV) i SQL-databas för att övervakarstatus och förlopp för pågående elastisk databas-transaktioner. Alla DMV: er som rör transaktioner är relevanta för distribuerade transaktioner i SQL-databas. Du hittar här DMV: er motsvarande lista: [transaktion relaterade Dynamic Management Views och Functions (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Dessa DMV: er är särskilt användbara:

* **sys.DM\_tran\_active\_transaktioner**: Visar en lista över aktiva transaktioner just nu och deras status. Kolumnen UOW (arbetsenheten) kan identifiera de olika underordnade transaktioner som hör till samma distribuerad transaktion. Alla transaktioner inom samma distribuerad transaktion har samma UOW värde. Se den [DMV dokumentation](https://msdn.microsoft.com/library/ms174302.aspx) för mer information.
* **sys.DM\_tran\_databasen\_transaktioner**: tillhandahåller ytterligare information om transaktioner, till exempel placeringen av transaktionen i loggen. Se den [DMV dokumentation](https://msdn.microsoft.com/library/ms186957.aspx) för mer information.
* **sys.DM\_tran\_Lås**: innehåller information om de lås som för tillfället hålls av pågående transaktioner. Se den [DMV dokumentation](https://msdn.microsoft.com/library/ms190345.aspx) för mer information.

## <a name="limitations"></a>Begränsningar
Följande begränsningar gäller för närvarande till transaktioner för elastiska databaser i SQL-databas:

* Endast transaktioner mellan databaser i SQL-databas stöds. Andra [X / Open XA](https://en.wikipedia.org/wiki/X/Open_XA) resursprovidrar och utanför SQL DB-databaser kan inte ingå i transaktioner elastiska databaser. Det innebär att transaktioner elastiska databaser inte kan sträcka ut över lokalt SQL Server och Azure SQL Database. Fortsätta att använda MSDTC för distribuerade transaktioner lokalt. 
* Endast klienten koordinerad transaktioner från en .NET-program stöds. Serversidan stöd för T-SQL, till exempel BEGIN DISTRIBUTED TRANSACTION är planerad, men ännu inte tillgänglig. 
* Transaktioner över WCF-tjänster stöds inte. Du har till exempel den metod för en WCF-tjänst, som kör en transaktion. Omsluta anropet i ett transaktionsomfång misslyckas som en [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Nästa steg
Frågor kan du kontakta oss på den [SQL Database-forumet](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) och för funktionsförfrågningar om Lägg till dem till den [SQL Database-Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



