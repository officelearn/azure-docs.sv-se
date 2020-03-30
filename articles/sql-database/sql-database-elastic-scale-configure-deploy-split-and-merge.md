---
title: Distribuera en tjänst för att dela/sammanslå
description: Använd även delad koppling för att flytta data mellan fragmenterade databaser.
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
ms.openlocfilehash: 50dbca0b3a761b72134eaa6cfed57e231be4ef13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421024"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Distribuera en tjänst för delad koppling för att flytta data mellan fragmenterade databaser

Med verktyget för delad koppling kan du flytta data mellan fragmenterade databaser. Se [Flytta data mellan utskalade molndatabaser](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Ladda ned Split-Merge-paketen

1. Ladda ner den senaste NuGet-versionen från [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

1. Öppna en kommandotolk och navigera till katalogen där du hämtade nuget.exe. Hämtningen innehåller PowerShell-kommandon.

1. Hämta det senaste Split-Merge-paketet till den aktuella katalogen med kommandot nedan:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Filerna placeras i en katalog med namnet **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** där *x.x.xxx.x* återspeglar versionsnumret. Leta reda på tjänstfilerna för delad koppling i underkatalogen **content\splitmerge\service** och PowerShell-skripten för delad koppling (och obligatoriska klient-dlls) i **underkatalogen content\splitmerge\powershell.**

## <a name="prerequisites"></a>Krav

1. Skapa en Azure SQL DB-databas som ska användas som statusdatabas för delad koppling. Gå till [Azure-portalen](https://portal.azure.com). Skapa en ny **SQL-databas**. Ge databasen ett namn och skapa en ny administratör och ett nytt lösenord. Var noga med att registrera namn och lösenord för senare användning.

1. Kontrollera att din Azure SQL DB-server tillåter Azure Services att ansluta till den. I portalen, i **brandväggsinställningarna,** kontrollerar du att inställningen **Tillåt åtkomst till Azure Services** är inställd på **På**. Klicka på "spara"-ikonen.

1. Skapa ett Azure Storage-konto för diagnostikutdata.

1. Skapa en Azure Cloud-tjänst för din Split-Merge-tjänst.

## <a name="configure-your-split-merge-service"></a>Konfigurera tjänsten Split-Merge

### <a name="split-merge-service-configuration"></a>Tjänstkonfiguration för delad koppling

1. Skapa en kopia av filen *ServiceConfiguration.Template.cscfg* som levererades tillsammans med *SplitMergeService.cspkg* i mappen där du hämtade split-merge-sammansättningarna och döpa om den till *ServiceConfiguration.cscfg*.

1. Öppna *ServiceConfiguration.cscfg* i en textredigerare, till exempel Visual Studio, som validerar indata, till exempel formatet för certifikattumavtryck.

1. Skapa en ny databas eller välj en befintlig databas som ska fungera som statusdatabas för Split-Merge-åtgärder och hämta anslutningssträngen för databasen.

   > [!IMPORTANT]
   > För närvarande måste statusdatabasen använda den latinska\_sorteringen (SQL\_Latin1 General\_CP1\_CI\_AS). Mer information finns i [Windows Collation Name (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).

   Med Azure SQL DB är anslutningssträngen vanligtvis av formuläret:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Ange den här anslutningssträngen i *CSCFG-filen* i rollavsnitten **SplitMergeWeb** och **SplitMergeWorker** i inställningen ElasticScaleMetadata.

1. För **rollen SplitMergeWorker** anger du en giltig anslutningssträng till Azure-lagring för inställningen **WorkerRoleSynchronizationStorageAccountConnectionString.**

### <a name="configure-security"></a>Konfigurera säkerhet

Detaljerade instruktioner för att konfigurera tjänstens säkerhet finns i [säkerhetskonfigurationen för Split-Merge](sql-database-elastic-scale-split-merge-security-configuration.md).

För en enkel testdistribution för den här självstudien utförs en minimal uppsättning konfigurationssteg för att få igång tjänsten. Dessa steg gör det bara möjligt för en dator/konto som kör dem att kommunicera med tjänsten.

### <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Skapa en ny katalog och från den här katalogen kör följande kommando med hjälp av ett [fönstret Developer Command Prompt for Visual Studio:](https://msdn.microsoft.com/library/ms229859.aspx)

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Du ombeds ett lösenord för att skydda den privata nyckeln. Ange ett starkt lösenord och bekräfta det. Du uppmanas sedan att använda lösenordet en gång till efter det. Klicka på **Ja** i slutet om du vill importera det till rotarkivet för betrodda certifikatutfärdare.

### <a name="create-a-pfx-file"></a>Skapa en PFX-fil

Kör följande kommando från samma fönster där makecert kördes. använda samma lösenord som du använde för att skapa certifikatet:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importera klientcertifikatet till det personliga arkivet

1. Dubbelklicka på *MyCert.pfx*i Utforskaren .
2. Välj **Aktuell användare** i guiden **Importera certifikat** och klicka på **Nästa**.
3. Bekräfta sökvägen till filen och klicka på **Nästa**.
4. Skriv lösenordet, lämna **Inkludera alla utökade egenskaper** markerade och klicka på **Nästa**.
5. Lämna **Automatiskt det markerade certifikatarkivet[...]** markerat och klicka på **Nästa**.
6. Klicka på **Slutför** och **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Ladda upp PFX-filen till molntjänsten

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Välj **Molntjänster**.
3. Välj den molntjänst som du skapade ovan för tjänsten Dela/koppla.
4. Klicka på **Certifikat** på den övre menyn.
5. Klicka på **Ladda upp** i det nedre fältet.
6. Markera PFX-filen och ange samma lösenord som ovan.
7. När du är klar kopierar du certifikatets tumavtryck från den nya posten i listan.

### <a name="update-the-service-configuration-file"></a>Uppdatera tjänstkonfigurationsfilen

Klistra in det certifikat tumavtryck som kopierats ovan i tumavtrycks-/värdeattributet för dessa inställningar.
För arbetarrollen:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

För webbrollen:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Observera att för produktionsdistributioner bör separata certifikat användas för certifikatutfärdaren, för kryptering, servercertifikat och klientcertifikat. Detaljerade instruktioner om detta finns i [Säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Distribuera din tjänst

1. Gå till [Azure Portal](https://portal.azure.com)
2. Välj den molntjänst som du skapade tidigare.
3. Klicka på **Översikt**.
4. Välj mellanlagringsmiljön och klicka sedan på **Ladda upp**.
5. Ange en distributionsetikett i dialogrutan. För både "Paket" och "Konfiguration" klickar du på "Från lokal" och väljer filen *SplitMergeService.cspkg* och din cscfg-fil som du konfigurerade tidigare.
6. Kontrollera att kryssrutan **Distribuera även om en eller flera roller innehåller en enskild instans** är markerad.
7. Tryck på kryssknappen längst ned till höger för att påbörja distributionen. Räkna med att det tar några minuter att slutföra.

## <a name="troubleshoot-the-deployment"></a>Felsöka distributionen

Om webbrollen inte är online är det sannolikt ett problem med säkerhetskonfigurationen. Kontrollera att SSL är konfigurerat enligt beskrivningen ovan.

Om din arbetarroll inte är online, men webbrollen lyckas, är det troligen ett problem att ansluta till statusdatabasen som du skapade tidigare.

- Kontrollera att anslutningssträngen i cscfg är korrekt.
- Kontrollera att det finns server och databas och att användar-ID och lösenord är korrekta.
- För Azure SQL DB ska anslutningssträngen vara av formuläret:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Kontrollera att servernamnet inte börjar med **https://**.
- Kontrollera att din Azure SQL DB-server tillåter Azure Services att ansluta till den. Det gör du genom att öppna databasen i portalen och se till att inställningen **Tillåt åtkomst till Azure Services** är inställd på **On****.

## <a name="test-the-service-deployment"></a>Testa tjänstdistributionen

### <a name="connect-with-a-web-browser"></a>Anslut med en webbläsare

Bestäm webbslutpunkten för din Split-Merge-tjänst. Du hittar detta i portalen genom att gå till **översikten över** din molntjänst och titta under **Webbadress** på höger sida. Ersätt **http://** med **https://** eftersom standardsäkerhetsinställningarna inaktiverar HTTP-slutpunkten. Läs in sidan för den här webbadressen i webbläsaren.

### <a name="test-with-powershell-scripts"></a>Testa med PowerShell-skript

Distributionen och din miljö kan testas genom att köra det medföljande exemplet PowerShell-skript.

Skriptfilerna som ingår är:

1. *SetupSampleSplitMergeEnvironment.ps1* - ställer in en testdatanivå för Split/Merge (se tabellen nedan för detaljerad beskrivning)
2. *ExecuteSampleSplitMerge.ps1* - utför teståtgärder på testdatanivån (se tabellen nedan för detaljerad beskrivning)
3. *GetMappings.ps1* - exempelskript på den översta nivån som skriver ut det aktuella tillståndet för fragmentmappningarna.
4. *ShardManagement.psm1* - hjälpskript som sveper in ShardManagement-API:et
5. *SqlDatabaseHelpers.psm1* - hjälpskript för att skapa och hantera SQL-databaser
   
   <table style="width:100%">
     <tr>
       <th>PowerShell-fil</th>
       <th>Steg</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeMiljö.ps1</th>
       <td>1. Skapar en databas för fragmentkarthanteraren</td>
     </tr>
     <tr>
       <td>2. Skapar 2 fragmentdatabaser.
     </tr>
     <tr>
       <td>3. Skapar en fragmentkarta för dessa databaser (tar bort alla befintliga fragmentkartor i dessa databaser). </td>
     </tr>
     <tr>
       <td>4. Skapar en liten exempeltabell i båda shards och fyller i tabellen i en av shards.</td>
     </tr>
     <tr>
       <td>5. Deklarerar SchemaInfo för den fragmenterade tabellen.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-fil</th>
       <th>Steg</th>
     </tr>
   <tr>
       <th rowspan="4">KörSampleSplitMerge.ps1 </th>
       <td>1. Skickar en delad begäran till webbfronten för Split-Merge Service, som delar hälften av data från den första fragmentet till den andra fragmentet.</td>
     </tr>
     <tr>
       <td>2. Söker efter webbfrontend för status för delad begäran och väntar tills begäran är klar.</td>
     </tr>
     <tr>
       <td>3. Skickar en begäran om koppling till webbfronten för Split-Merge Service, som flyttar data från den andra fragmentet tillbaka till den första fragmentet.</td>
     </tr>
     <tr>
       <td>4. Söker efter webbklämningen efter status för kopplad begäran och väntar tills begäran är klar.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Använda PowerShell för att verifiera distributionen

1. Öppna ett nytt PowerShell-fönster och navigera till katalogen där du hämtade Split-Merge-paketet och navigera sedan till katalogen "powershell".

2. Skapa en Azure SQL Database-server (eller välj en befintlig server) där fragmentkarthanteraren och shards ska skapas.

   > [!NOTE]
   > *Skriptet SetupSampleSplitMergeEnvironment.ps1* skapar alla dessa databaser på samma server som standard för att hålla skriptet enkelt. Detta är inte en begränsning av själva split-merge-tjänsten.

   En SQL-autentiseringsinloggning med läs-/skrivåtkomst till DBs kommer att behövas för split-merge-tjänsten för att flytta data och uppdatera fragmentkartan. Eftersom Split-Merge-tjänsten körs i molnet stöder den för närvarande inte integrerad autentisering.

   Kontrollera att Azure SQL-servern är konfigurerad för att tillåta åtkomst från IP-adressen för den dator som kör dessa skript. Du hittar den här inställningen under Azure SQL-servern / konfiguration / tillåtna IP-adresser.

3. Kör *skriptet SetupSampleSplitMergeEnvironment.ps1* för att skapa exempelmiljön.

   Om du kör det här skriptet raderas alla befintliga datastrukturer för fragmentmappningshantering i databasen för fragmentkarthanteraren och shards. Det kan vara användbart att köra skriptet igen om du vill återinitera fragmentkartan eller shards.

   Exempel på kommandorad:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Kör skriptet Getmappings.ps1 för att visa de mappningar som för närvarande finns i exempelmiljön.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Kör skriptet *ExecuteSampleSplitMerge.ps1* för att köra en delad åtgärd (flytta hälften av data på den första fragmentet till den andra fragmentet) och sedan en sammanfogningsåtgärd (flytta tillbaka data till den första fragmentet). Om du har konfigurerat SSL och lämnat http-slutpunkten inaktiverad kontrollerar du att du använder https:// slutpunkten i stället.

   Exempel på kommandorad:

   ```cmd
   .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' 
    -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```

   Om du får felet nedan är det troligen ett problem med webbslutpunktens certifikat. Prova att ansluta till webbslutpunkten med din favoritwebbläsare och kontrollera om det finns ett certifikatfel.

     `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

   Om det lyckades, bör utdata se ut som nedan:

   ```output
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

6. Experimentera med andra datatyper! Alla dessa skript tar en valfri -ShardKeyType-parameter som gör att du kan ange nyckeltypen. Standard är Int32, men du kan också ange Int64, Guid eller Binary.

## <a name="create-requests"></a>Skapa begäranden

Tjänsten kan användas antingen med hjälp av webbgränssnittet eller genom att importera och använda SplitMerge.psm1 PowerShell-modulen som skickar dina begäranden via webbrollen.

Tjänsten kan flytta data i både fragmenterade tabeller och referenstabeller. En fragmenterad tabell har en fragmenteringsnyckelkolumn och har olika raddata för varje fragment. En referenstabell är inte fragmenterad så den innehåller samma raddata för varje shard. Referenstabeller är användbara för data som inte ändras ofta och som används för att sammanfoga med fragmenterade tabeller i frågor.

För att kunna utföra en split-merge-åtgärd måste du deklarera de fragmenterade tabeller och referenstabeller som du vill ha flyttat. Detta åstadkoms med **SchemaInfo** API. Det här API:et finns i namnområdet **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema.**

1. För varje fragmenterad tabell skapar du ett **ShardedTableInfo-objekt** som beskriver tabellens överordnade schemanamn (valfritt, standardvärdet "dbo"), tabellnamnet och kolumnnamnet i tabellen som innehåller fragmenteringsnyckeln.
2. För varje referenstabell skapar du ett **ReferenceTableInfo-objekt** som beskriver tabellens överordnade schemanamn (valfritt, standardvärdet "dbo") och tabellnamnet.
3. Lägg till ovanstående TableInfo-objekt i ett nytt **SchemaInfo-objekt.**
4. Hämta en referens till ett **ShardMapManager-objekt** och anropa **GetSchemaInfoCollection**.
5. Lägg till **SchemaInfo** i **SchemaInfoCollection**och ange fragmentmappningsnamnet.

Ett exempel på detta kan ses i skriptet SetupSampleSplitMergeEnvironment.ps1.

Tjänsten Split-Merge skapar inte måldatabasen (eller schemat för några tabeller i databasen) åt dig. De måste skapas i förväg innan en begäran skickas till tjänsten.

## <a name="troubleshooting"></a>Felsökning

Du kan se meddelandet nedan när du kör exempelskripten för powershell:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Det här felet innebär att SSL-certifikatet inte är korrekt konfigurerat. Följ instruktionerna i avsnittet "Ansluta med en webbläsare".

Om du inte kan skicka förfrågningar kan du se detta:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

I det här fallet kontrollerar du konfigurationsfilen, särskilt inställningen för **WorkerRoleSynchronizationStorageAccountConnectionString**. Det här felet anger vanligtvis att arbetarrollen inte kunde initiera metadatadatabasen vid första användningen.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
