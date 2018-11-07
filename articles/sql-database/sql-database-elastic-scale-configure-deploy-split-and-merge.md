---
title: Distribuera en tjänst för dela / sammanslå | Microsoft Docs
description: Använd dela / sammanslå för att flytta data mellan shardade databaser.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 253a50f50e94888a5d764e51a4ff49cc0c1424c6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253541"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Distribuera en tjänst för dela / sammanslå som flyttar data mellan shardade databaser

Dela / sammanslå-verktyget kan du flytta data mellan shardade databaser. Se [flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Ladda ned paket för dela / Sammanslå
1. Hämta den senaste NuGet-versionen från [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Öppna en kommandotolk och navigera till katalogen där du hämtade nuget.exe. Nedladdningen innehåller PowerShell-kommandon.
3. Ladda ned det senaste dela / Sammanslå-paketet till den aktuella katalogen i kommandot nedan:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Filerna är placerade i en katalog med namnet **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** där *x.x.xxx.x* visar versionsnumret. Hitta dela / sammanslå Service-filer i den **content\splitmerge\service** underkatalog, och dela / Sammanslå PowerShell-skript (och nödvändiga DLL-filer) i den **content\splitmerge\powershell** underkatalog.

## <a name="prerequisites"></a>Förutsättningar
1. Skapa en Azure SQL DB-databas som ska användas som databas för dela / sammanslå status. Gå till [Azure-portalen](https://portal.azure.com). Skapa en ny **SQL Database**. Ge ett namn för databasen och skapar en ny administratör och lösenord. Kom ihåg att registrera namn och lösenord för senare användning.
2. Se till att din Azure SQL DB-server tillåter Azure Services för att ansluta till den. I portalen, i den **brandväggsinställningar**, se till att den **Tillåt åtkomst till Azure-tjänster** är inställt på **på**. Klicka på ikonen ”spara”.
3. Skapa ett Azure Storage-konto för diagnostik-utdata.
4. Skapa en Azure-molntjänst för dela / Sammanslå-tjänsten.

## <a name="configure-your-split-merge-service"></a>Konfigurera din tjänst för dela / Sammanslå
### <a name="split-merge-service-configuration"></a>Tjänstkonfigurationen för dela / Sammanslå
1. I den mapp dit du hämtade sammansättningarna för dela / Sammanslå, skapar du en kopia av den **ServiceConfiguration.Template.cscfg** som levererades tillsammans med **SplitMergeService.cspkg** och Byt namn **ServiceConfiguration.cscfg**.
2. Öppna **ServiceConfiguration.cscfg** i en textredigerare som Visual Studio som validerar indata som format för certifikattumavtryck.
3. Skapa en ny databas eller välj en befintlig databas att fungera som databasen status för dela / Sammanslå åtgärder och hämta anslutningssträngen för databasen. 
   
   > [!IMPORTANT]
   > Just nu, status-databasen måste använda latinska sortering (SQL\_Latin1\_Allmänt\_CP1\_CI\_AS). Mer information finns i [Windows Collation Name (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Med Azure SQL DB är vanligtvis anslutningssträngen i formatet:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Ange den här anslutningssträngen i cscfg-fil i både den **SplitMergeWeb** och **SplitMergeWorker** rollen avsnitt i ElasticScaleMetadata-inställningen.
5. För den **SplitMergeWorker** roll, ange en giltig anslutningssträng till Azure storage för den **WorkerRoleSynchronizationStorageAccountConnectionString** inställningen.

### <a name="configure-security"></a>Konfigurera säkerhet
Detaljerade anvisningar om hur du konfigurerar säkerheten för tjänsten som avser den [säkerhetskonfiguration för dela / Sammanslå](sql-database-elastic-scale-split-merge-security-configuration.md).

För en enkel test-distribution för den här självstudiekursen blir en minimal uppsättning konfigurationssteg utförs att tjänsten komma igång och körs. Dessa steg aktiverar endast en/datorkontot körs kan kommunicera med tjänsten.

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Skapa en ny katalog och från den här katalogen kör du följande kommando med en [Kommandotolken för utvecklare för Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) fönster:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Du uppmanas att ange ett lösenord för att skydda den privata nyckeln. Ange ett starkt lösenord och bekräfta att den. Du uppmanas sedan lösenordet som ska användas en gång till därefter. Klicka på **Ja** i slutet för att importera den till arkivet Betrodda rotcertifikatutfärdare för certifiering myndigheter.

### <a name="create-a-pfx-file"></a>Skapa en PFX-fil
Kör följande kommando från samma fönster där makecert utfördes; Använd samma lösenord som du använde för att skapa certifikatet:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importera klientcertifikatet till det personliga arkivet
1. Dubbelklicka i Windows Explorer **MyCert.pfx**.
2. I den **guiden Importera certifikat** Välj **aktuell användare** och klicka på **nästa**.
3. Kontrollera sökvägen till filen och klicka på **nästa**.
4. Ange lösenordet, lämna **inkluderar alla utökade egenskaper** markerat och klicka på **nästa**.
5. Lämna **automatiskt Välj certifikatarkiv [...]**  markerat och klicka på **nästa**.
6. Klicka på **Slutför** och **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Ladda upp PFX-filen till Molntjänsten
1. Gå till [Azure-portalen](https://portal.azure.com).
2. Välj **molntjänster**.
3. Välj den molntjänst som du skapade ovan för dela/Sammanslå-tjänsten.
4. Klicka på **certifikat** på den översta menyn.
5. Klicka på **överför** i det nedre fältet.
6. Välj PFX-filen och ange samma lösenord som ovan.
7. När klar kopiera tumavtrycket för certifikatet från den nya posten i listan.

### <a name="update-the-service-configuration-file"></a>Uppdatera tjänstkonfigurationsfilen
Klistra in tumavtrycket för certifikatet kopieras över till attributet/tumavtrycksvärde för de här inställningarna.
För worker-roll:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

För webbroll:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Observera att distributioner separata certifikat för produktion bör kan använda för CA: N för kryptering, certifikat och klientcertifikat. Detaljerade anvisningar om detta finns i [säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Distribuera din tjänst
1. Gå till [Azure Portal](https://portal.azure.com)
2. Välj den molntjänst som du skapade tidigare.
3. Klicka på **Översikt**.
4. Välj mellanlagringsmiljön och klicka sedan på **överför**.
5. I dialogrutan anger du en distributionsetikett. Klicka på ”från lokala' för både” paketet ”och” Configuration ”och välj den **SplitMergeService.cspkg** fil- och cscfg-filen som du konfigurerade tidigare.
6. Kontrollera att kryssrutan **distribuera även om en eller flera roller innehåller en enda instans** kontrolleras.
7. Tryck på knappen skalstreck i det nedre högra hörnet starta distributionen. Kommer det att ta ett par minuter att slutföra.


## <a name="troubleshoot-the-deployment"></a>Felsöka distributionen
Om det går inte att använda din webbroll, är det sannolikt ett problem med säkerhetskonfigurationen. Kontrollera att SSL har konfigurerats enligt beskrivningen ovan.

Om din arbetsroll inte går att använda, men din webbroll lyckas, är det troligen ett problem med att ansluta till status-databasen som du skapade tidigare.

* Kontrollera att anslutningssträngen i din cscfg är korrekt.
* Kontrollera att servern och databasen finns och att det användar-id och lösenord är korrekta.
* För Azure SQL DB måste anslutningssträngen ha formatet:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Se till att servernamnet inte börjar med **https://**.
* Se till att din Azure SQL DB-server tillåter Azure Services för att ansluta till den. Gör detta genom att öppna databasen i portalen och se till att den **Tillåt åtkomst till Azure-tjänster** är inställt på ** på ***.

## <a name="test-the-service-deployment"></a>Testa service-distributionen
### <a name="connect-with-a-web-browser"></a>Ansluta med en webbläsare
Fastställa webbslutpunkt för dela / Sammanslå-tjänsten. Du hittar den i portalen genom att gå till den **översikt** av cloud Services och leta under **webbplatsens URL** på höger sida. Ersätt **http://** med **https://** eftersom standardsäkerhetsinställningarna inaktivera HTTP-slutpunkt. Läs in sidan för den här URL: en i webbläsaren.

### <a name="test-with-powershell-scripts"></a>Testa med PowerShell-skript
Distributionen och din miljö kan testas genom att köra PowerShell-skript inkluderade exemplet.

Skriptfilerna som ingår är:

1. **SetupSampleSplitMergeEnvironment.ps1** – ställer in en test-datanivå för dela/Sammanslå (se tabellen nedan detaljerad beskrivning)
2. **ExecuteSampleSplitMerge.ps1** -kör teståtgärder på testet data på nivån (se tabellen nedan detaljerad beskrivning)
3. **GetMappings.ps1** – översta exempel på skript som skriver ut det aktuella tillståndet för shard-mappningar.
4. **ShardManagement.psm1** -helper-skript som omsluter ShardManagement-API
5. **SqlDatabaseHelpers.psm1** -helper-skript för att skapa och hantera SQL-databaser
   
   <table style="width:100%">
     <tr>
       <th>PowerShell-fil</th>
       <th>Steg</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Skapar en databas</td>
     </tr>
     <tr>
       <td>2.    Skapar 2 shard-databaser.
     </tr>
     <tr>
       <td>3.    Skapar en skärvkarta för dessa databaser (ta bort alla befintliga shardkartor på de här databaserna). </td>
     </tr>
     <tr>
       <td>4.    Skapar en liten exempeltabell i båda fragmenten och fyller i tabellen i en av shards.</td>
     </tr>
     <tr>
       <td>5.    Deklarerar SchemaInfo för den fragmenterade (sharded) tabellen.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-fil</th>
       <th>Steg</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Skickar en split-begäran till webbservergrupp dela / Sammanslå tjänst som delar upp halva data från den första sharden till andra fragment.</td>
     </tr>
     <tr>
       <td>2.    Avsöker webbservergrupp för begärandestatus dela och väntar tills begäran har slutförts.</td>
     </tr>
     <tr>
       <td>3.    Skickar en kopplingsbegäran till webbservergrupp dela / Sammanslå Service som flyttar data från andra shard tillbaka till det första fragmentet.</td>
     </tr>
     <tr>
       <td>4.    Avsöker webbservergrupp för status för merge-begäran och väntar tills begäran har slutförts.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Använd PowerShell för att kontrollera distributionen
1. Öppna ett nytt PowerShell-fönster och navigera till katalogen där du hämtade paketet dela / Sammanslå och gå sedan till katalogen ”powershell”.
2. Skapa en Azure SQL database-server (eller välj en befintlig server) där karthanteraren och shards kommer att skapas.
   
   > [!NOTE]
   > SetupSampleSplitMergeEnvironment.ps1 skriptet skapar de här databaserna på samma server som standard att skriptet är enkel. Detta är inte en begränsning av tjänsten dela / Sammanslå själva.
   >
   
   En SQL-autentiseringsinloggning med läs-/ skrivåtkomst till databaser krävs för dela / Sammanslå-tjänsten att flytta data och uppdatera fragmentkartan. Eftersom tjänsten dela / Sammanslå körs i molnet kan stöder det för närvarande inte integrerad autentisering.
   
   Kontrollera att Azure SQL-servern är konfigurerad för att tillåta åtkomst från IP-adressen för den dator som kör dessa skript. Du hittar den här inställningen under Azure SQL-servern / configuration / tillåtna IP-adresser.
3. Kör SetupSampleSplitMergeEnvironment.ps1-skript för att skapa exemplet-miljö.
   
   Kör skriptet kommer att radera alla befintliga fragment management kartdata strukturer på databasen och shards. Det kan vara praktiskt att köra skriptet igen om du vill initiera fragmentkartan eller shards.
   
   Kommandorad:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Kör Getmappings.ps1-skript för att visa de mappningar som för närvarande finns i exempel-miljö.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Kör ExecuteSampleSplitMerge.ps1-skript för att köra en split-åtgärden (flytta halva data på den första sharden till den andra sharden) och sedan en merge-operation (flytta data tillbaka till det första fragmentet). Om du har konfigurerat SSL och kvar http-slutpunkt som har inaktiverats, se till att använda https:// slutpunkten i stället.
   
   Kommandorad:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Om du får den nedanstående fel, det är troligen ett problem med din webbslutpunkt certifikat. Försök att ansluta till Web-slutpunkt med din favorit webbläsare och kontrollera om det finns ett certifikat-fel.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Om den lyckades utdata bör se ut som den nedan:
   
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
6. Experimentera med andra datatyper! Alla dessa skript tar en valfri parameter för - ShardKeyType som låter dig ange nyckeltypen. Standardvärdet är Int32, men du kan också ange Int64, Guid eller Binary.

## <a name="create-requests"></a>Startförfrågan
Tjänsten kan användas med hjälp av webbgränssnittet eller genom att importera och använda SplitMerge.psm1 PowerShell-modulen som kommer att skicka dina förfrågningar via web-roll.

Tjänsten kan flytta data i både shardade tabeller och referenstabeller. En fragmenterade (sharded) tabellen har en nyckelkolumn för horisontell partitionering och har olika raddata på varje shard. En tabell är inte fragmenterade (sharded) så att den innehåller samma raddata i varje fragment. Referenstabeller är användbara för data som inte ändras ofta och som används för att ansluta med delat tabeller i frågor.

För att kunna utföra en åtgärd för dela / sammanslå, måste du deklarera shardade tabeller och register som du vill ha flyttats. Detta åstadkoms med den **SchemaInfo** API. Detta API är den **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** namnområde.

1. För varje fragmenterade (sharded) tabellen, skapar du en **ShardedTableInfo** objekt som beskriver tabellens överordnade schemanamn (valfritt, standardvärdet är ”dbo”), tabellnamnet och kolumnnamnet i den tabell som innehåller nyckeln för horisontell partitionering.
2. För varje tabell, skapar du en **ReferenceTableInfo** objekt som beskriver tabellens överordnade schemanamn (valfritt, standardvärde ”dbo”) och tabellnamnet.
3. Lägga till ovanstående TableInfo objekten till en ny **SchemaInfo** objekt.
4. Hämta en referens till en **ShardMapManager** objekt och anropa **GetSchemaInfoCollection**.
5. Lägg till den **SchemaInfo** till den **SchemaInfoCollection**, att tillhandahålla mappningsnamn fragment.

Ett exempel på detta kan ses i skriptet SetupSampleSplitMergeEnvironment.ps1.

Dela / Sammanslå-tjänsten skapar inte måldatabasen (eller schemat för alla tabeller i databasen) åt dig. De måste skapas i förväg innan du skickar en begäran till tjänsten.

## <a name="troubleshooting"></a>Felsökning
Du kan se den under meddelandet när du kör powershell-exempelskript:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Detta fel innebär att dina SSL-certifikat inte är korrekt konfigurerad. Följ anvisningarna i avsnittet ”ansluta med en webbläsare'.

Om du inte kan skicka begäranden kan du se följande:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

I det här fallet konfigurationsfilen, särskilt kontrollera inställningen för **WorkerRoleSynchronizationStorageAccountConnectionString**. Det här felet indikerar vanligtvis att arbetsrollen inte kunde har initierats metadata-databasen vid första användningen. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

