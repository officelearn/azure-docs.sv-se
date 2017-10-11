---
title: Data beroende routning med Azure SQL Database | Microsoft Docs
description: "Hur du använder klassen ShardMapManager i .NET-appar för data-beroende routning, en funktion i delat databaser i Azure SQL Database"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
editor: 
ms.assetid: cad09e15-5561-4448-aa18-b38f54cda004
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: ddove
ms.openlocfilehash: 6b68bbb0133afd1493acdb58f79f3eeaf6a8d7cd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="data-dependent-routing"></a>Databeroende routning
**Data beroende routning** är möjligheten att använda data i en fråga för att vidarebefordra begäran till en lämplig databas. Detta är ett grundläggande mönster när du arbetar med delat databaser. Kontexten för begäran kan även användas för routning av begäran, särskilt om nyckeln för horisontell partitionering inte är en del av frågan. Varje specifik fråga eller en transaktion i ett program som använder data beroende routning är begränsad åtkomst till en enskild databas per begäran. För Azure SQL Database elastisk verktyg för den här routning sker med hjälp av  **[ShardMapManager klassen](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)**  i ADO.NET-program.

Programmet behöver inte följa olika anslutningssträngar eller DB platser som är associerade med olika datasegment i delat miljö. I stället den [Fragmentera kartan Manager](sql-database-elastic-scale-shard-map-management.md) öppnas anslutningar till rätt databaser vid behov, baserat på data i kartan Fragmentera och värdet för nyckeln för horisontell partitionering som är mål för programmets begäran. Nyckeln är vanligtvis den *customer_id*, *tenant_id*, *date_key*, eller vissa specifika identifierare som är en grundläggande parameter för databasbegäran). 

Mer information finns i [skala ut SQL Server med Data beroende routning](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Hämta klientbiblioteket
För att få klassen, installera den [klientbibliotek för elastisk databas](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). 

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Med hjälp av en ShardMapManager i ett beroende routning program
Program ska skapa en instans av den **ShardMapManager** under initieringen med factory anropet  **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)**. I detta exempel både en **ShardMapManager** och en specifik **ShardMap** som den innehåller har initierats. Det här exemplet illustrerar GetSqlShardMapManager och [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx) metoder.

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Använda lägsta behörighet autentiseringsuppgifter som är möjliga för att hämta Fragmentera kartan
Om ett program inte manipulering Fragmentera kartan sig själv, de autentiseringsuppgifter som används i standardmetoden har bara läsbehörighet på den **globala Fragmentera kartan** databas. Autentiseringsuppgifterna är vanligtvis olika från autentiseringsuppgifter som används för att öppna anslutningar till hanteraren Fragmentera kartan. Se även [autentiseringsuppgifter används för att komma åt klientbibliotek för elastisk databas](sql-database-elastic-scale-manage-credentials.md). 

## <a name="call-the-openconnectionforkey-method"></a>Anropa metoden OpenConnectionForKey
Den  **[ShardMap.OpenConnectionForKey metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)**  returnerar en ADO.Net-anslutning som är redo för att utfärda kommandon till rätt databas baserat på värdet för den **nyckeln** parameter. Fragmentera informationen cachelagras i program genom den **ShardMapManager**, så dessa begäranden inte normalt innefattar en databassökning mot den **globala Fragmentera kartan** databas. 

    // Syntax: 
    public SqlConnection OpenConnectionForKey<TKey>(
        TKey key,
        string connectionString,
        ConnectionOptions options
    )


* Den **nyckeln** parametern används som en sökning-nyckel till Fragmentera mappningen för att fastställa en lämplig databas för begäran. 
* Den **connectionString** används för att skicka användarens autentiseringsuppgifter för önskad anslutning. Inget databasnamn eller servernamn ingår i detta *connectionString* eftersom metoden avgör databasen och server använder den **ShardMap**. 
* Den  **[connectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)**  ska anges till **ConnectionOptions.Validate** om en miljö där Fragmentera mappar kan ändras och rader kan flyttas till andra databaser på grund av delade eller merge-åtgärder. Detta innebär en kortfattad fråga till lokal Fragmentera kartan på målet databasen (inte till globala Fragmentera kartan) innan anslutningen levereras till programmet. 

Om verifiering mot den lokala Fragmentera mappningen misslyckas (som anger att cacheminnet är felaktig), kommer Fragmentera kartan Manager fråga globala Fragmentera kartan för att hämta nya rätt värde för sökningen, uppdatera cachen och hämta och returnera lämpliga databasanslutningen. 

Använd **ConnectionOptions.None** endast när Fragmentera mappningsändringar inte förväntas när ett program är online. I så fall cachelagrade värden kan antas att alltid för att vara korrekt och extra fram och åter validering anropet till måldatabasen kan hoppas på ett säkert sätt. Som minskar databastrafik. Den **connectionOptions** kan också anges via ett värde i en konfigurationsfil för att indikera om ändringar för horisontell partitionering är förväntat eller inte under en viss tidsperiod.  

Det här exemplet används värdet för ett integer-nyckeln **CustomerID**med hjälp av en **ShardMap** objekt med namnet **customerShardMap**.  

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
    // constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

Den **OpenConnectionForKey** metoden returnerar en ny redan öppen anslutning till rätt databas. Anslutningar som används i det här sättet fortfarande dra full nytta av ADO.Net anslutningspooler. Så länge transaktioner och begäranden kan betjänas av en Fragmentera samtidigt, bör detta vara den endast ändringen som krävs i ett program som redan använder ADO.Net. 

Den  **[OpenConnectionForKeyAsync metoden](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)**  finns även om programmet gör Använd asynkron programmering med ADO.Net. Sitt beteende är data beroende routning motsvarar ADO. Net's  **[Connection.OpenAsync](https://msdn.microsoft.com/library/hh223688\(v=vs.110\).aspx)**  metod.

## <a name="integrating-with-transient-fault-handling"></a>Integrera med tillfälligt fel hantering
Bästa praxis för att utveckla program för åtkomst av data i molnet är att se till att tillfälliga fel som fångas av appen och att åtgärderna är ett nytt försök flera gånger innan ge ett fel. Tillfälligt fel hantering för molnprogram beskrivs på [hantering av tillfälliga fel](https://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx). 

Tillfälligt fel hantering kan samexistera naturligt med mönstret Data beroende routning. Ett krav är att försöka hela data access begäran inklusive den **med** block som hämtade data beroende routning anslutningen. Exemplet ovan kan skrivas enligt följande (anteckning markeras ändra). 

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exempel – data beroende routning med tillfälligt fel hantering
<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connect to the shard for a customer ID. 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @&quot;UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID&quot;; 

            cmd.Parameters.AddWithValue(&quot;@customerID&quot;, customerId); 
            cmd.Parameters.AddWithValue(&quot;@newPersonID&quot;, newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(&quot;Update completed&quot;); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Paket som är nödvändiga för att implementera tillfälligt fel hantering laddas ned automatiskt när du skapar exempelprogrammet elastisk databas. Paket finns också separat på [Enterprise Library - tillfälliga fel hanterar programmet Block](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Använd version 6.0 eller senare. 

## <a name="transactional-consistency"></a>Transaktionskonsekvens
Transaktionell egenskaper är garanterat för alla åtgärder lokalt på en Fragmentera. Till exempel köra transaktioner som har skickats via data beroende routning inom omfånget för mål-Fragmentera för anslutningen. För tillfället finns inga funktioner för att ta med flera anslutningar i en transaktion och det finns därför inga transaktionella garantier för åtgärder som utförs i shards.

## <a name="next-steps"></a>Nästa steg
Koppla från en Fragmentera eller återansluta ett Fragmentera finns [med hjälp av klassen RecoveryManager för att åtgärda problem med Fragmentera karta](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

