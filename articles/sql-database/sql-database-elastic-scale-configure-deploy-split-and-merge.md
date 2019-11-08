---
title: Distribuera en tjänst för att dela/sammanslå
description: Använd dela-merge för att flytta data mellan shardade-databaser.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: e7438674981115599637ac1763a8d24444fd0552
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823691"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Distribuera en tjänst för delad sammanslagning för att flytta data mellan shardade-databaser

Med verktyget Dela-sammanslagning kan du flytta data mellan shardade-databaser. Se [Flytta data mellan utskalade moln databaser](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Hämta de delade sammanfognings paketen
1. Hämta den senaste versionen av NuGet från [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).
2. Öppna en kommando tolk och navigera till den katalog där du laddade ned NuGet. exe. Hämtningen innehåller PowerShell-kommandon.
3. Hämta det senaste delade sammanslagnings paketet till den aktuella katalogen med kommandot nedan:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Filerna placeras i en katalog med namnet **Microsoft. Azure. SqlDatabase. ElasticScale. service. SplitMerge. x. x. xxx. x** där *x. x. xxx. x* visar versions numret. Hitta filerna för delade sammanslagna tjänster i **content\splitmerge\service** under katalog och PowerShell-skripten för delad sammanslagning (och obligatoriska klient-dll: er) i under katalogen för **content\splitmerge\powershell** .

## <a name="prerequisites"></a>Nödvändiga komponenter
1. Skapa en Azure SQL DB-databas som ska användas som databas för delad sammanslagnings status. Gå till [Azure-portalen](https://portal.azure.com). Skapa en ny **SQL Database**. Ge databasen ett namn och skapa en ny administratör och ett nytt lösen ord. Se till att du registrerar namnet och lösen ordet för senare användning.
2. Se till att Azure SQL DB-servern tillåter att Azure-tjänster ansluter till den. I **brand Väggs inställningarna**i portalen kontrollerar du att inställningen **Tillåt åtkomst till Azure-tjänster** är inställd på **på**. Klicka på ikonen "Spara".
3. Skapa ett Azure Storage-konto för diagnostiska utdata.
4. Skapa en Azure-molnbaserad tjänst för tjänsten för delad sammanslagning.

## <a name="configure-your-split-merge-service"></a>Konfigurera tjänsten för delad sammanslagning
### <a name="split-merge-service-configuration"></a>Konfiguration av tjänsten för delad sammanslagning
1. I mappen där du laddade ned de delade sammanfognings sammansättningarna skapar du en kopia av filen **ServiceConfiguration. template. cscfg** som levererades tillsammans med **SplitMergeService. cspkg** och byter namn på den **ServiceConfiguration. cscfg**.
2. Öppna **ServiceConfiguration. cscfg** i en text redigerare, till exempel Visual Studio som validerar indata, till exempel formatet på certifikat tumavtrycken.
3. Skapa en ny databas eller Välj en befintlig databas som ska fungera som status databas för delnings sammanslagnings åtgärder och hämta anslutnings strängen för databasen. 
   
   > [!IMPORTANT]
   > För tillfället måste status databasen använda den latinska sorteringen (SQL\_Latin1\_General\_CP1\_CI\_AS). Mer information finns i [Windows sorterings namn (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Med Azure SQL DB är anslutnings strängen vanligt vis av formatet:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Ange den här anslutnings strängen i cscfg-filen i både **SplitMergeWeb** -och **SplitMergeWorker** -roll-avsnittet i inställningen ElasticScaleMetadata.
5. För **SplitMergeWorker** -rollen anger du en giltig anslutnings sträng till Azure Storage för inställningen **WorkerRoleSynchronizationStorageAccountConnectionString** .

### <a name="configure-security"></a>Konfigurera säkerhet
Detaljerade anvisningar om hur du konfigurerar säkerheten för tjänsten finns i [säkerhets konfigurationen för delad sammanslagning](sql-database-elastic-scale-split-merge-security-configuration.md).

I samband med en enkel test distribution för den här självstudien utförs en minimal uppsättning konfigurations steg för att hämta och köra tjänsten. De här stegen gör det möjligt att bara köra en dator/ett konto för att kommunicera med tjänsten.

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Skapa en ny katalog och från den här katalogen kör följande kommando med hjälp av en [kommando tolk för utvecklare i Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) -fönstret:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Du uppmanas att ange ett lösen ord för att skydda den privata nyckeln. Ange ett starkt lösen ord och bekräfta det. Du uppmanas sedan att ange lösen ordet en gång till. Klicka på **Ja** i slutet för att importera det till rot arkivet betrodda certifikat utfärdare.

### <a name="create-a-pfx-file"></a>Skapa en PFX-fil
Kör följande kommando från samma fönster där MakeCert kördes. Använd samma lösen ord som du använde för att skapa certifikatet:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importera klient certifikatet till det personliga arkivet
1. I Utforskaren dubbelklickar du på **cert. pfx**.
2. Välj **Aktuell användare** i **guiden Importera certifikat** och klicka på **Nästa**.
3. Bekräfta sökvägen till filen och klicka på **Nästa**.
4. Skriv lösen ordet, lämna **ta med alla utökade egenskaper** markerade och klicka på **Nästa**.
5. Lämna **automatiskt certifikat arkivet [...]** markerat och klicka på **Nästa**.
6. Klicka på **Slutför** och **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Överför PFX-filen till moln tjänsten
1. Gå till [Azure-portalen](https://portal.azure.com).
2. Välj **Cloud Services**.
3. Välj den moln tjänst som du skapade ovan för delnings-/sammanslagnings tjänsten.
4. Klicka på **certifikat** på den översta menyn.
5. Klicka på **överför** i det nedre fältet.
6. Välj PFX-filen och ange samma lösen ord som ovan.
7. När du är klar kopierar du tumavtryck för certifikatet från den nya posten i listan.

### <a name="update-the-service-configuration-file"></a>Uppdatera tjänst konfigurations filen
Klistra in tumavtryck för certifikatet som kopierats ovan i attributen tumavtryck/Value för dessa inställningar.
För arbets rollen:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

För webb rollen:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Observera att för produktions distributioner bör separata certifikat användas för certifikat utfärdaren, för kryptering, Server certifikat och klient certifikat. Detaljerade anvisningar finns i [säkerhets konfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Distribuera din tjänst
1. Gå till [Azure Portal](https://portal.azure.com)
2. Välj den moln tjänst som du skapade tidigare.
3. Klicka på **Översikt**.
4. Välj mellanlagrings miljö och klicka sedan på **Ladda upp**.
5. I dialog rutan anger du en distributions etikett. För både "paket" och "konfiguration" klickar du på "från lokal" och väljer filen **SplitMergeService. cspkg** och din cscfg-fil som du konfigurerade tidigare.
6. Kontrol lera att kryss rutan **distribuera även om en eller flera roller innehåller en enda instans** har marker ATS.
7. Tryck på knappen för skal streck längst ned till höger för att starta distributionen. Det tar några minuter att slutföra det.


## <a name="troubleshoot-the-deployment"></a>Felsöka distributionen
Om din webb roll inte kan anslutas är det sannolikt ett problem med säkerhets konfigurationen. Kontrol lera att SSL har kon figurer ATS enligt beskrivningen ovan.

Om din arbets roll inte kan anslutas, men din webb roll lyckas, är det förmodligen problem att ansluta till status databasen som du skapade tidigare.

* Kontrol lera att anslutnings strängen i din cscfg är korrekt.
* Kontrol lera att servern och databasen finns och att användar-ID och lösen ord är korrekta.
* För Azure SQL DB ska anslutnings strängen ha formatet:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Se till att Server namnet inte börjar med **https://** .
* Se till att Azure SQL DB-servern tillåter att Azure-tjänster ansluter till den. Det gör du genom att öppna din databas i portalen och kontrol lera att inställningen **Tillåt åtkomst till Azure-tjänster** är inställd på **på** **.

## <a name="test-the-service-deployment"></a>Testa tjänst distributionen
### <a name="connect-with-a-web-browser"></a>Ansluta till en webbläsare
Bestäm webb slut punkten för din tjänst för delad sammanslagning. Du hittar detta i portalen genom att gå till **översikten** över moln tjänsten och titta under webbplats- **URL** till höger. Ersätt **http://** med **https://** eftersom standard säkerhets inställningarna inaktiverar http-slutpunkten. Läs in sidan för denna URL i webbläsaren.

### <a name="test-with-powershell-scripts"></a>Testa med PowerShell-skript
Du kan testa distributionen och din miljö genom att köra PowerShell-skript som ingår i exemplet.

De skript filer som ingår är:

1. **SetupSampleSplitMergeEnvironment. ps1** – konfigurerar en test data nivå för delning/sammanslagning (se tabellen nedan för detaljerad beskrivning)
2. **ExecuteSampleSplitMerge. ps1** – kör test åtgärder på test data nivån (se tabellen nedan för detaljerad beskrivning)
3. **GetMappings. ps1** – exempel skriptet på den översta nivån som skriver ut det aktuella läget för Shard-mappningar.
4. **ShardManagement. psm1** – hjälp skript som omsluter API för ShardManagement
5. **SqlDatabaseHelpers. psm1** – hjälp skript för att skapa och hantera SQL-databaser
   
   <table style="width:100%">
     <tr>
       <th>PowerShell-fil</th>
       <th>Steg</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment. ps1</th>
       <td>1.    Skapar en Shard Map Manager-databas</td>
     </tr>
     <tr>
       <td>2.    Skapar 2 Shard-databaser.
     </tr>
     <tr>
       <td>3.    Skapar en Shard-karta för dessa databaser (tar bort alla befintliga Shard-mappningar i dessa databaser). </td>
     </tr>
     <tr>
       <td>4.    Skapar en liten exempel tabell i både Shards och fyller tabellen i en av Shards.</td>
     </tr>
     <tr>
       <td>5.    Deklarerar SchemaInfo för shardade-tabellen.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-fil</th>
       <th>Steg</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge. ps1 </th>
       <td>1.    Skickar en delad begäran till webb klient delen för den delade sammanslagnings tjänsten, som delar upp hälften av data från den första Shard till den andra Shard.</td>
     </tr>
     <tr>
       <td>2.    Avsöker webb klient delen för status för delad begäran och väntar tills begäran har slutförts.</td>
     </tr>
     <tr>
       <td>3.    Skickar en merge-begäran till webb klient delen för den delade sammanslagnings tjänsten, som flyttar data från den andra Shard tillbaka till den första Shard.</td>
     </tr>
     <tr>
       <td>4.    Avsöker webb klient delen för status för sammanslagnings begäran och väntar tills begäran har slutförts.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Använd PowerShell för att verifiera distributionen
1. Öppna ett nytt PowerShell-fönster och navigera till den katalog där du laddade ned det delade paketet och navigera sedan till katalogen "PowerShell".
2. Skapa en Azure SQL Database Server (eller Välj en befintlig server) där mappnings hanteraren för Shard och Shards kommer att skapas.
   
   > [!NOTE]
   > Skriptet SetupSampleSplitMergeEnvironment. ps1 skapar alla dessa databaser på samma server som standard för att hålla skriptet enkelt. Detta är inte en begränsning för själva tjänsten för delad sammanslagning.
   >
   
   En SQL-autentisering med Läs-och Skriv behörighet till databaser krävs för att tjänsten för delad sammanslagning ska flytta data och uppdatera Shard-kartan. Eftersom tjänsten för delad sammanslagning körs i molnet stöder den inte integrerad autentisering.
   
   Kontrol lera att Azure SQL-servern är konfigurerad för att tillåta åtkomst från IP-adressen för den dator som kör dessa skript. Du kan hitta den här inställningen under Azure SQL Server/Configuration/Allowed IP-adresser.
3. Kör skriptet SetupSampleSplitMergeEnvironment. ps1 för att skapa exempel miljön.
   
   Genom att köra det här skriptet raderas alla befintliga Shard för mappnings hanterings data i Shard Map Manager-databasen och Shards. Det kan vara användbart att köra skriptet igen om du vill initiera om Shard-kartan eller Shards.
   
   Exempel kommando rad:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Kör skriptet Getmappings. ps1 för att visa de mappningar som finns i exempel miljön.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Kör skriptet ExecuteSampleSplitMerge. ps1 för att köra en delad åtgärd (flytta hälften av data på de första Shard till den andra Shard) och sedan en sammanslagnings åtgärd (flytta tillbaka data till den första Shard). Om du har konfigurerat SSL och lämnat http-slutpunkten inaktive rad, se till att du använder https://-slutpunkten istället.
   
   Exempel kommando rad:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Om du får felet nedan är det förmodligen ett problem med webb slut punktens certifikat. Försök att ansluta till webb slut punkten med din favorit webbläsare och kontrol lera om det finns ett certifikat fel.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Om det lyckas bör utdata se ut så här:
   
   ```
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```
6. Experimentera med andra data typer! Alla dessa skript tar en valfri-ShardKeyType-parameter som gör att du kan ange nyckel typen. Standardvärdet är Int32, men du kan också ange Int64, GUID eller Binary.

## <a name="create-requests"></a>Startförfrågan
Tjänsten kan användas antingen med hjälp av webb gränssnittet eller genom att importera och använda PowerShell-modulen SplitMerge. psm1 som skickar dina begär Anden via webb rollen.

Tjänsten kan flytta data i både shardade-tabeller och referens tabeller. En shardade-tabell har en horisontell partitionering-nyckel kolumn och har olika rad data för varje Shard. En referens tabell är inte shardade så den innehåller samma rad data på varje Shard. Referens tabeller är användbara för data som inte ändras ofta och som används för att ansluta till shardade-tabeller i frågor.

För att kunna utföra en delnings sammanslagning måste du deklarera de shardade-tabeller och referens tabeller som du vill flytta. Detta åstadkoms med **SchemaInfo** -API: et. Detta API finns i namn området **Microsoft. Azure. SqlDatabase. ElasticScale. ShardManagement. schema** .

1. För varje shardade-tabell skapar du ett **ShardedTableInfo** -objekt som beskriver tabellens överordnade schema namn (valfritt, standardvärdet "dbo"), tabell namnet och kolumn namnet i den tabellen som innehåller horisontell partitionering-nyckeln.
2. Skapa ett **ReferenceTableInfo** -objekt som beskriver tabellens överordnade schema namn (valfritt, standard är "dbo") och tabell namnet för varje referens tabell.
3. Lägg till ovanstående TableInfo-objekt i ett nytt **SchemaInfo** -objekt.
4. Hämta en referens till ett **ShardMapManager** -objekt och anropa **GetSchemaInfoCollection**.
5. Lägg till **SchemaInfo** i **SchemaInfoCollection**och ange namnet på Shard-kartan.

Ett exempel på detta kan visas i skriptet SetupSampleSplitMergeEnvironment. ps1.

Tjänsten för delad sammanslagning skapar inte mål databasen (eller schemat för tabeller i databasen) åt dig. De måste skapas i förväg innan en begäran skickas till tjänsten.

## <a name="troubleshooting"></a>Felsökning
Du kan se meddelandet nedan när du kör PowerShell-skripten:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Det här felet innebär att SSL-certifikatet inte har kon figurer ATS korrekt. Följ anvisningarna i avsnittet ansluta till en webbläsare.

Om du inte kan skicka begär Anden kan du se följande:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

I det här fallet kontrollerar du konfigurations filen, i synnerhet inställningen för **WorkerRoleSynchronizationStorageAccountConnectionString**. Det här felet indikerar vanligt vis att arbets rollen inte kunde initiera metadata-databasen vid första användningen. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

