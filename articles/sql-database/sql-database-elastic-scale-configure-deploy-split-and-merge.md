---
title: "Distribuera en delad kopplingstjänsten | Microsoft Docs"
description: "Använd split-kopplingen för att flytta data mellan delat databaser."
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: ed5545df8d09192eeed21dd2ac379e255b049e30
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-a-split-merge-service"></a>Distribuera en tjänst för att dela/sammanslå
Dela merge-verktyget kan du flytta data mellan delat databaser. Se [flytta data mellan databaser som skalats ut moln](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Hämta de delade dokument-paket
1. Den senaste versionen NuGet från [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Öppna en kommandotolk och gå till den katalog där du hämtade nuget.exe. Hämtningen innehåller PowerShell-kommandon.
3. Hämta senaste delade dokument i den aktuella katalogen med den nedan kommando:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Filerna placeras i en katalog med namnet **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** där *x.x.xxx.x* visar versionsnumret. Hitta delade dokument Service-filer i den **content\splitmerge\service** underkatalog, och den delade dokument PowerShell-skript (och nödvändiga klient DLL-filer) i den **content\splitmerge\powershell** underkatalog.

## <a name="prerequisites"></a>Förutsättningar
1. Skapa en Azure SQL DB-databas som ska användas som databas för delade dokument status. Gå till [Azure-portalen](https://portal.azure.com). Skapa en ny **SQL-databas**. Namnge databasen och skapa en ny administratör och lösenord. Kom ihåg att registrera namn och lösenord för senare användning.
2. Kontrollera att din Azure SQL DB-server tillåter Azure-tjänster att ansluta till den. I portalen i den **brandväggsinställningar**, se till att den **Tillåt åtkomst till Azure-tjänster** inställningen **på**. Klicka på ikonen ”spara”.
   
   ![Tillåtna tjänster][1]
3. Skapa ett Azure Storage-konto som ska användas för diagnostik utdata. Gå till Azure-portalen. I fältet till vänster klickar du på **skapar du en resurs**, klickar du på **Data + lagring**, sedan **lagring**.
4. Skapa en Azure-molntjänst som innehåller delning-kopplingstjänsten.  Gå till Azure-portalen. I fältet till vänster klickar du på **skapar du en resurs**, sedan **Compute**, **Molntjänsten**, och **skapa**. 

## <a name="configure-your-split-merge-service"></a>Konfigurera din delade kopplingstjänsten
### <a name="split-merge-service-configuration"></a>Dela kopplingstjänsten konfiguration
1. I mappen där du sparade sammansättningarna dela dokument, skapar du en kopia av den **ServiceConfiguration.Template.cscfg** som levererades tillsammans med **SplitMergeService.cspkg** och byta namn på det. **ServiceConfiguration.cscfg**.
2. Öppna **ServiceConfiguration.cscfg** i en textredigerare som Visual Studio som validerar indata, till exempel format för certifikattumavtryck.
3. Skapa en ny databas eller välj en befintlig databas att fungera som databasen status för delade Merge-operationer och hämta anslutningssträngen för databasen. 
   
   > [!IMPORTANT]
   > För tillfället status databasen måste använda latinska sortering (SQL\_Latin1\_allmänna\_CP1\_CI\_AS). Mer information finns i [Windows Collation Name (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Med Azure SQL DB är anslutningssträngen vanligtvis i formatet:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Ange den här anslutningssträngen i cscfg-fil i både den **SplitMergeWeb** och **SplitMergeWorker** roll avsnitt i inställningen ElasticScaleMetadata.
5. För den **SplitMergeWorker** roll, ange en giltig anslutningssträng till Azure-lagring för den **WorkerRoleSynchronizationStorageAccountConnectionString** inställningen.

### <a name="configure-security"></a>Konfigurera säkerhet
Detaljerade anvisningar om hur du konfigurerar säkerheten för tjänsten som avser den [delade dokument säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

Vid tillämpningen av en enkel testdistributionen för den här självstudiekursen, kommer en minimal uppsättning konfigurationssteg att utföras på en gång tjänsten körs. Dessa steg aktiverar endast en/datorkontot köra dem att kommunicera med tjänsten.

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Skapa en ny katalog och kör följande kommando använder från den här katalogen en [Developer kommandotolk för Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) fönster:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Du uppmanas att ange ett lösenord för att skydda den privata nyckeln. Ange ett starkt lösenord och bekräfta. Du uppmanas sedan lösenordet som ska användas en gång efter. Klicka på **Ja** i slutet för att importera det till arkivet Betrodda rotcertifikatutfärdare för certifikatutfärdare myndigheter.

### <a name="create-a-pfx-file"></a>Skapa en PFX-fil
Kör följande kommando från samma fönster där makecert utfördes; Använd samma lösenord som du använde för att skapa certifikatet:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importera klientcertifikatet till det personliga arkivet
1. Dubbelklicka i Utforskaren, **MyCert.pfx**.
2. I den **guiden Importera certifikat** Välj **aktuell användare** och på **nästa**.
3. Kontrollera sökvägen till filen och klicka på **nästa**.
4. Skriv in lösenordet, lämna **inkludera alla utökade egenskaper** markerad och klicka på **nästa**.
5. Lämna **automatiskt Välj certifikatarkiv [...]**  markerad och klicka på **nästa**.
6. Klicka på **Slutför** och **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Ladda upp PFX-filen till Molntjänsten
1. Gå till [Azure-portalen](https://portal.azure.com).
2. Välj **molntjänster**.
3. Välj den molntjänst som du skapade ovan för delade/kopplingstjänsten.
4. Klicka på **certifikat** på den översta menyn.
5. Klicka på **överför** i det nedre fältet.
6. Välj PFX-filen och ange samma lösenord som ovan.
7. När klar, kan du kopiera tumavtrycket för certifikatet från den nya posten i listan.

### <a name="update-the-service-configuration-file"></a>Uppdatera konfigurationsfilen för tjänsten
Klistra in tumavtrycket kopieras över till attributet/tumavtrycksvärde för dessa inställningar.
För arbetsrollen:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

För webbrollen:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Observera att distributioner separata certifikat för produktion bör kan använda för att Certifikatutfärdaren för kryptering, certifikat och klientcertifikat. Detaljerad information om detta finns i [säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Distribuera din tjänst
1. Gå till [Azure-portalen](https://manage.windowsazure.com).
2. Klicka på den **molntjänster** till vänster och välj den molntjänst som du skapade tidigare.
3. Klicka på **instrumentpanelen**.
4. Välj mellanlagringsmiljön och klicka sedan på **ladda upp en ny fristående distribution**.
   
   ![Mellanlagring][3]
5. I dialogrutan anger du en distributionsetikett. Klicka på 'Från lokala' för både 'Paketet ”och” Configuration ”och välj den **SplitMergeService.cspkg** fil- och cscfg-fil som du tidigare har konfigurerat.
6. Kontrollera att kryssrutan **distribuera även om en eller flera roller innehåller en enda instans** är markerad.
7. Tryck på knappen för skalstreck i det nedre högra starta distributionen. Trots att det ta några minuter att slutföra.

   ![Ladda upp][4]

## <a name="troubleshoot-the-deployment"></a>Felsöka distributionen
Om det går inte att använda din webbroll, är det troligt problem med säkerhetskonfiguration. Kontrollera att SSL är konfigurerade enligt beskrivningen ovan.

Om arbetsrollen inte anslutas, men din webbroll lyckas, är det troligen problem med att ansluta till databasen status som du skapade tidigare.

* Kontrollera att anslutningssträngen i din cscfg stämmer.
* Kontrollera att servern och databasen finns och att det användar-id och lösenord är korrekta.
* Anslutningssträngen bör vara i formatet för Azure SQL DB:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Kontrollera att servernamnet inte börjar med **https://**.
* Kontrollera att din Azure SQL DB-server tillåter Azure-tjänster att ansluta till den. Det gör du genom att öppna https://manage.windowsazure.com, klicka på ”SQL-databaser till vänster, klicka på” servrar ”längst upp och markera din server. Klicka på **konfigurera** överst och kontrollera att den **Azure Services** inställningen är inställd på ”Ja”. (Se avsnittet förutsättningar överst i den här artikeln).

## <a name="test-the-service-deployment"></a>Testa tjänstdistributionen
### <a name="connect-with-a-web-browser"></a>Ansluta med en webbläsare
Fastställa webbslutpunkten för din delade kopplingstjänsten. Du hittar det i den klassiska Azure-portalen genom att gå till den **instrumentpanelen** Molntjänsten och söker **Webbadress** på höger sida. Ersätt **http://** med **https://** eftersom standardsäkerhetsinställningarna inaktivera HTTP-slutpunkten. Läs in sidan för denna URL i webbläsaren.

### <a name="test-with-powershell-scripts"></a>Testa med PowerShell-skript
Distributionen och din miljö kan testas genom att köra PowerShell-skript med exempel.

Skriptfiler som ingår är:

1. **SetupSampleSplitMergeEnvironment.ps1** -ställer in en test datanivå för delade/Merge (se tabellen nedan ger en detaljerad beskrivning)
2. **ExecuteSampleSplitMerge.ps1** -kör teståtgärder på testet data tjänstnivån (se tabellen nedan ger en detaljerad beskrivning)
3. **GetMappings.ps1** - översta exempel på skript som skriver ut det aktuella tillståndet för Fragmentera mappningarna.
4. **ShardManagement.psm1** -helper-skript som omsluter ShardManagement-API
5. **SqlDatabaseHelpers.psm1** -helper-skript för att skapa och hantera SQL-databaser
   
   <table style="width:100%">
     <tr>
       <th>PowerShell-fil</th>
       <th>Steg</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Skapar en Fragmentera kartan manager-databas</td>
     </tr>
     <tr>
       <td>2.    Skapar 2 Fragmentera databaser.
     </tr>
     <tr>
       <td>3.    Skapar en Fragmentera karta för dessa databaser (ta bort alla befintliga Fragmentera mappar på de databaserna som). </td>
     </tr>
     <tr>
       <td>4.    Skapar en liten exempeltabell i båda shards och fyller i tabellen i en av delar.</td>
     </tr>
     <tr>
       <td>5.    Deklarerar SchemaInfo för delat tabellen.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-fil</th>
       <th>Steg</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Skickar en split-begäran till web-klientdel dela kopplingstjänsten som delar upp halva data från den första Fragmentera till andra Fragmentera.</td>
     </tr>
     <tr>
       <td>2.    Avsöker web-klientdel dela begäran status och väntar tills begäran har slutförts.</td>
     </tr>
     <tr>
       <td>3.    Skickar en kopplingsbegäran till web-klientdel dela kopplingstjänsten som flyttar data från andra Fragmentera tillbaka till den första Fragmentera.</td>
     </tr>
     <tr>
       <td>4.    Avsöker web-klientdel för status för merge-begäran och väntar tills begäran har slutförts.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Använd PowerShell för att kontrollera distributionen
1. Öppna ett nytt PowerShell-fönster och gå till den katalog där du hämtade paketet delade dokument och gå sedan till katalogen ”powershell”.
2. Skapa en Azure SQL database-server (eller välj en befintlig server) där Fragmentera kartan manager och shards kommer att skapas.
   
   > [!NOTE]
   > Skriptet SetupSampleSplitMergeEnvironment.ps1 skapar alla databaser på samma server som standard att skriptet är enkel. Detta är inte en begränsning för delade kopplingstjänsten sig själv.
   >
   
   En SQL-inloggning för autentisering med åtkomst till DBs krävs för delade kopplingstjänsten att flytta data och uppdatera Fragmentera kartan. Eftersom dela kopplingstjänsten körs i molnet, stöder den för närvarande inte integrerad autentisering.
   
   Kontrollera att Azure SQL-servern är konfigurerad för att tillåta åtkomst från IP-adressen för datorn som kör dessa skript. Du hittar den här inställningen under Azure SQL-server / configuration / tillåtna IP-adresser.
3. Kör skriptet SetupSampleSplitMergeEnvironment.ps1 om du vill skapa en exempel-miljö.
   
   Kör skriptet kommer att radera alla befintliga Fragmentera management kartdata strukturer på Fragmentera kartan manager-databasen och shards. Det kan vara praktiskt att köra skriptet om du vill initiera Fragmentera kartan eller delar.
   
   Kommandorad:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Kör skriptet Getmappings.ps1 om du vill visa mappningar som för närvarande finns i exempel-miljö.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Kör skriptet ExecuteSampleSplitMerge.ps1 för att köra en split-åtgärd (flytta halva data på den första Fragmentera till andra Fragmentera) och en merge-operation (flytta data tillbaka till den första Fragmentera). Om du konfigurerade SSL och lämnas http-slutpunkten inaktiverad, se till att använda https:// slutpunkten i stället.
   
   Kommandorad:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Om du får den nedan fel, är det förmodligen ett problem med din webbslutpunkten certifikat. Försök att ansluta till Web-slutpunkten med din favorit webbläsare och kontrollera om det finns fel på certifikatet.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Om det lyckades utdata ska se ut som den nedan:
   
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
6. Experimentera med andra datatyper! Alla dessa skript tar en valfri parameter för - ShardKeyType där du kan ange nyckeltypen. Standardvärdet är Int32, men du kan också ange Int64, Guid eller Binary.

## <a name="create-requests"></a>Startförfrågan
Tjänsten kan användas via webbgränssnittet eller genom att importera och använda SplitMerge.psm1 PowerShell-modulen som kommer att skicka din begäran via rollen webbserver.

Tjänsten kan flytta data i både delat tabeller och referenstabeller. Ett delat tabellen har en nyckelkolumn för horisontell partitionering och har olika raddata på varje Fragmentera. En tabell är inte delat så att den innehåller samma raddata i varje Fragmentera. För referenstabeller är användbart för data som inte ändras ofta och används för att ansluta med delat tabeller i frågor.

Du måste deklarera delat tabeller och referenstabeller som du vill ha flyttats för att kunna utföra en delad merge-operation. Detta åstadkoms med den **SchemaInfo** API. Detta API är i den **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** namnområde.

1. För varje delat tabell skapar en **ShardedTableInfo** -objektet som beskriver tabellens överordnade schemanamn (valfritt, ”dbo” som standard), tabellnamnet och kolumnnamnet i den tabell som innehåller delning nyckeln.
2. Varje tabell, skapa en **ReferenceTableInfo** -objektet som beskriver tabellens överordnade schemanamn (valfritt, standardvärdet är ”dbo”) och tabellnamn.
3. Lägga till ovanstående TableInfo objekt till en ny **SchemaInfo** objekt.
4. Hämta en referens till en **ShardMapManager** objektet och anropet **GetSchemaInfoCollection**.
5. Lägg till den **SchemaInfo** till den **SchemaInfoCollection**, tillhandahåller Fragmentera mappningsnamn.

Ett exempel på detta kan ses i skriptet SetupSampleSplitMergeEnvironment.ps1.

Dela kopplingstjänsten skapar inte måldatabasen (eller schemat för alla tabeller i databasen) för dig. De måste vara skapats i förväg innan du skickar en begäran till tjänsten.

## <a name="troubleshooting"></a>Felsökning
Du kan se den under meddelandet när du kör powershell-exempelskript:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Det här felet betyder att SSL-certifikatet inte har konfigurerats korrekt. Följ anvisningarna i avsnittet ”ansluta med en webbläsare'.

Om du inte kan skicka begäranden visas här:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

I det här fallet konfigurationsfilen, särskilt kontrollera inställningen för **WorkerRoleSynchronizationStorageAccountConnectionString**. Det här felet betyder oftast att arbetsrollen inte kunde har initiera metadata-databasen vid första användningen. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

