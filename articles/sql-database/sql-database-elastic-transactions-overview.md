---
title: Distribuerade transaktioner över molndatabaser
description: Översikt över elastisk databastransaktioner med Azure SQL-databas
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.custom: scale out apps
ms.workload: On Demand
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c0dfc8e2b71e0d81943ef8306c58421ff1d78c6c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646957"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Distribuerade transaktioner över molndatabaser
Elastisk databastransaktioner för Azure SQL Database (SQL DB) gör att du kan köra transaktioner som sträcker sig över flera databaser i SQL-databas. Elastisk databastransaktioner för SQL-databas är tillgängliga för .NET-program med hjälp av ADO .NET och integrera med den gamla programmering upplevelse med den [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) klasser. Biblioteket finns [.NET Framework 4.6.1 (Webbinstallationsprogram)](https://www.microsoft.com/download/details.aspx?id=49981).

Lokalt krävs ett sådant scenario vanligtvis kör Microsoft Distributed Transaction Coordinator (MSDTC). Eftersom MSDTC inte är tillgänglig för Platform as a Service program i Azure, har möjligheten att koordinera distribuerade transaktioner nu direkt integrerats i SQL-databas. Du kan ansluta till en SQL-databas för att starta distribuerade transaktioner och en av databaserna kommer transparent samordna den distribuerade transaktionen som visas i följande bild. 

  ![Distribuerade transaktioner med Azure SQL Database med transaktioner för elastisk databas ][1]

## <a name="common-scenarios"></a>Vanliga scenarier
Elastisk databastransaktioner för SQL-databas gör det möjligt att göra ändringar i data som lagras i flera olika SQL-databaser. Förhandsgranskningen fokuserar på klientsidan development upplevelser i C# och .NET. En serversidan upplevelse med T-SQL är planerad för ett senare tillfälle.  
Elastisk databastransaktioner riktar sig till följande scenarier:

* Flera databasprogram i Azure: med det här scenariot data är lodrätt partitionerad över flera databaser i SQL-databas så att olika typer av data som finns på olika databaser. Vissa åtgärder kräver ändringar av data som sparas i två eller flera databaser. Programmet använder elastisk databastransaktioner för samordning ändringarna över databaser och odelbarhet.
* Delat databasprogram i Azure: med det här scenariot datanivå använder den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md) eller self horisontell partitionering vågrätt partitionera data över flera databaser i SQL-databas. Framträdande användningsfall är behovet av att utföra ändringar för ett delat program för flera innehavare när ändringar span innehavare. Se exempelvis en överföring från en klient till en annan, både som finns på olika databaser. Andra fall är detaljerade delning för kapacitetsbehov för en stor klient som i sin tur vanligtvis innebär att vissa atomiska åtgärder måste sträcka ut över flera databaser som används för samma klientorganisation. Ett tredje fall är atomiska uppdateringar att referera till data som replikeras över databaser. Atomic, överförda, operationer längs dessa rader kan nu koordineras över flera databaser i förhandsgranskningen.
  Elastisk databastransaktioner använder två faser för att säkerställa transaktion odelbarhet över databaser. Det är passar bra för transaktioner som rör färre än 100 databaser samtidigt i en enda transaktion. Dessa begränsningar tillämpas inte, men en förvänta prestanda och slutförandefrekvenser för elastisk databastransaktioner för när dessa begränsningar.

## <a name="installation-and-migration"></a>Installation och migrering
Funktioner för elastisk databastransaktioner i SQL DB tillhandahålls via uppdateringar av .NET-biblioteken System.Data.dll och System.Transactions.dll. DLL-filer Se till att tvåfasincheckning används vid behov för att kontrollera odelbarhet. Om du vill börja utveckla program med elastiska databastransaktioner, installera [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) eller en senare version. När du kör på en tidigare version av .NET framework transaktioner misslyckas att befordra en distribuerad transaktion och ett undantag aktiveras.

Efter installationen kan du använda den distribuerade transaktionen API: er i System.Transactions med anslutningar till SQL-databas. Om du har befintliga MSDTC-program med hjälp av dessa API: er kan bara återskapa dina befintliga program för .NET 4.6 när du har installerat 4.6.1 Framework. Om dina projekt riktar .NET 4.6, använder de automatiskt uppdaterade DLL-filer från den nya Framework-versionen och distribuerade transaktioner API-anrop i kombination med anslutningar till SQL-databas ska lyckas.

Kom ihåg att elastisk databastransaktioner inte behöver installera MSDTC. Elastisk databastransaktioner hanteras i stället direkt efter och inom SQL-databas. Detta förenklar avsevärt scenarier eftersom en distribution av MSDTC inte är nödvändigt att använda distribuerade transaktioner med SQL-databas. Avsnitt 4 som beskrivs i detalj hur du distribuerar elastisk databastransaktioner och nödvändiga .NET framework tillsammans med dina molnprogram till Azure.

## <a name="development-experience"></a>Utvecklingsarbetet
### <a name="multi-database-applications"></a>Flera databasprogram
Följande exempelkod använder bekant programmering med .NET System.Transactions. Klassen TransactionScope upprättar en omgivande transaktion i .NET. (En ”omgivande transaktion” är en som finns i den aktuella tråden.) Alla anslutningar öppnas i TransactionScope delta i transaktionen. Om olika databaser delta utökas automatiskt transaktionen till en distribuerad transaktion. Resultatet av transaktionen styrs genom att ange omfånget utföra om du vill ange ett genomförande.

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

### <a name="sharded-database-applications"></a>Delat databasprogram
Elastisk databastransaktioner för SQL DB stöder också koordinera distribuerade transaktioner där du använder metoden OpenConnectionForKey av klientbiblioteket för elastisk databas för att öppna anslutningar för skaländras ut data nivå. Överväg att fall där du måste garantera transaktionskonsekvens för ändringar i flera nyckelvärden för olika horisontell partitionering. Anslutningar till shards värd nyckelvärden för olika horisontell partitionering är asynkrona med OpenConnectionForKey. I vanliga fall kan anslutningar vara till olika delar så att säkerställa transaktionella garantier kräver en distribuerad transaktion. Följande kodexempel illustrerar den här metoden. Det förutsätts att en variabel med namnet shardmap används för att representera en Fragmentera karta från klientbibliotek för elastisk databas:

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


## <a name="net-installation-for-azure-cloud-services"></a>Installation av .NET för Azure-molntjänster
Azure tillhandahåller flera erbjudanden som värd för .NET-program. En jämförelse av olika erbjudanden finns i [jämförelse mellan Azure App Service, molntjänster och virtuella datorer](../app-service/choose-web-site-cloud-service-vm.md). Om gästoperativsystemet av erbjudandet är mindre än .NET 4.6.1 som krävs för elastiska transaktioner, måste du uppgradera gästoperativsystemet till 4.6.1. 

För Azure App Services stöds uppgraderingar gästoperativsystemet för närvarande inte. Azure virtuella datorer bara logga in på den virtuella datorn och kör installationsprogrammet för senaste .NET framework. För Azure Cloud Services måste du inkludera installation av en nyare version av .NET i Start-uppgifter för din distribution. Koncept och steg finns dokumenterade i [installera .NET på en rolltjänst för molnet](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Observera att installationsprogrammet för .NET 4.6.1 kan kräva mer tillfällig lagring under bootstrapping process på Azure-molntjänster än installationsprogrammet för .NET 4.6. För att säkerställa en lyckad installation, måste du öka tillfällig lagring för din Azure-molntjänst i filen ServiceDefinition.csdef i avsnittet LocalResources och miljöinställningarna för din startaktivitet som visas i följande exempel:

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
Elastisk databastransaktioner stöds mellan olika logiska servrar i Azure SQL Database. När transaktioner mellan logisk server gränser, måste deltagande servrar först anges i en relation för ömsesidig kommunikation. När relationen kommunikation har upprättats kan någon databas i någon av de två servrarna delta i elastisk transaktioner med databaser från den andra servern. En relation för kommunikation måste finnas för alla typer av logiska servrar med transaktioner som sträcker sig över mer än två logiska servrar.

Använd följande PowerShell-cmdlets för att hantera relationer för kommunikation mellan servrar för elastisk databastransaktioner:

* **Nya AzureRmSqlServerCommunicationLink**: Använd den här cmdlet för att skapa en ny relation för kommunikation mellan två logiska servrar i Azure SQL DB. Relationen är symmetriska vilket innebär att båda servrarna kan initiera transaktioner med den andra servern.
* **Get-AzureRmSqlServerCommunicationLink**: Använd denna cmdlet för att hämta befintliga kommunikation relationer och deras egenskaper.
* **Ta bort AzureRmSqlServerCommunicationLink**: Använd denna cmdlet för att ta bort en befintlig relation för kommunikation. 

## <a name="monitoring-transaction-status"></a>Övervaka status för transaktioner
Använd dynamiska hanteringsvyer (av DMV: er) i SQL-databas för att övervaka status och förlopp för pågående elastisk databas-transaktioner. Alla av DMV: er relaterade till transaktioner är relevanta för distribuerade transaktioner i SQL-databas. Du kan hitta här av DMV: er motsvarande lista: [transaktion relaterade dynamiska hanteringsvyer och funktioner (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Dessa av DMV: er är särskilt användbar:

* **sys.DM\_tran\_active\_transaktioner**: Visar aktiva transaktioner just nu och deras status. Kolumnen UOW (arbetsenheten) kan identifiera de olika underordnade transaktioner som tillhör samma distribuerade transaktioner. Alla transaktioner inom samma distribuerade transaktioner utföra samma UOW värde. Finns det [DMV dokumentationen](https://msdn.microsoft.com/library/ms174302.aspx) för mer information.
* **sys.DM\_tran\_databasen\_transaktioner**: tillhandahåller ytterligare information om transaktioner, till exempel placeringen av transaktionen i loggen. Finns det [DMV dokumentationen](https://msdn.microsoft.com/library/ms186957.aspx) för mer information.
* **sys.DM\_tran\_Lås**: innehåller information om de lås som för tillfället hålls av en pågående transaktioner. Finns det [DMV dokumentationen](https://msdn.microsoft.com/library/ms190345.aspx) för mer information.

## <a name="limitations"></a>Begränsningar
Följande begränsningar gäller för närvarande transaktioner för elastisk databas i SQL-databas:

* Endast transaktioner mellan databaser i SQL-databas stöds. Andra [X / Open XA](https://en.wikipedia.org/wiki/X/Open_XA) resursproviders och databaser utanför SQL-databas kan inte ingå i elastisk databastransaktioner. Det innebär att elastisk databastransaktioner inte sträcka ut över lokala SQL Server och Azure SQL-databaser. Fortsätta att använda MSDTC för distribuerade transaktioner lokalt. 
* Endast klienten koordineras transaktioner från ett .NET-program stöds. Serversidan stöd för T-SQL, till exempel BÖRJA DISTRIBUTED TRANSACTION är planerade men ännu inte tillgänglig. 
* Transaktioner över WCF-tjänster stöds inte. Du har till exempel metod en WCF-tjänst som utför en transaktion. Omslutande anropet inom ett transaktionsomfång misslyckas som en [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Nästa steg
För frågor kan du nå oss på den [SQL Database-forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) och för funktionsbegäranden, Lägg till dem till den [SQL-databas Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png



