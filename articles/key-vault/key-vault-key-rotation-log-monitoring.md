---
title: Konfigurera Azure Key Vault slutpunkt till slutpunkt viktiga rotation och granskning | Microsoft Docs
description: Använd den här anvisningar som hjälper dig att börja arbeta med viktiga rotation och övervakning nyckelvalv loggar.
services: key-vault
documentationcenter: ''
author: swgriffith
manager: mbaldwin
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: stgriffi
ms.openlocfilehash: 701edc784dcde067e8ec585a3b31d6075207fbbf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Konfigurera Azure Key Vault med nyckelgranskning och -rotation från slutpunkt till slutpunkt
## <a name="introduction"></a>Introduktion
När du har skapat ditt nyckelvalv kommer du att kunna börja använda det valvet för att lagra dina nycklar och hemligheter. Dina program inte längre behöver spara dina nycklar och hemligheter, utan i stället begär dem från nyckelvalvet efter behov. På så sätt kan du uppdatera nycklar och hemligheter utan att påverka beteendet för programmet, vilket öppnar ett brett möjligheter runt din nyckel och hemliga hantering.

>[!IMPORTANT]
> Exemplen i den här artikeln tillhandahålls endast för illustration. De är inte avsedda för produktion. 

Den här artikeln beskriver hur ett exempel på hur Azure Key Vault för att lagra en hemlighet i det här fallet en nyckel för Azure Storage-konto som används av ett program. Här visas också implementering av en schemalagd rotation av den lagringskontonyckel. Slutligen går den igenom en demonstration av hur du övervakar nyckelvalv granskningsloggar och generera aviseringar när oväntat begäranden som görs.

> [!NOTE]
> Den här kursen är inte avsedd som förklarar i detalj installationen av nyckelvalvet. Den här informationen finns i [Komma igång med Azure Key Vault](key-vault-get-started.md). Plattformsoberoende kommandoradsgränssnittet instruktioner finns i [hantera Key Vault med hjälp av CLI](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Konfigurera Key Vault
Om du vill aktivera ett program att hämta en hemlighet från Nyckelvalvet, måste du först skapa hemligheten och överföra den till ditt valv. Detta kan åstadkommas genom att starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

```powershell
Login-AzureRmAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. PowerShell får alla prenumerationer som är associerade med det här kontot. PowerShell använder den första som standard.

Om du har flera prenumerationer kan behöva du ange det konto som användes för att skapa nyckelvalvet. Ange följande om du vill visa prenumerationer för ditt konto:

```powershell
Get-AzureRmSubscription
```

Ange om du vill ange den prenumeration som är kopplad till nyckelvalvet du loggar:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Eftersom den här artikeln visar lagra en lagringskontonyckel som en hemlighet, måste du hämta den lagringskontonyckel.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

När du har hämtat ditt hemliga (i det här fallet din lagringskontonyckel), måste du konvertera som till en säker sträng och sedan skapa en hemlighet som har värdet i nyckelvalvet.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Hämta sedan URI: N för den hemlighet som du skapade. Detta används i ett senare steg när du anropar nyckelvalvet att hämta ditt hemliga. Kör följande PowerShell-kommando och anteckna ID-värde, som är den hemliga URI:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurera programmet
Du kan använda koden för att hämta och använda den nu när du har en hemlighet som lagras. Det finns några steg som krävs för att uppnå detta. Det första och viktigaste steget registrera ditt program med Azure Active Directory och sedan uppmanar Key Vault information om programmet så att den kan tillåta förfrågningar från ditt program.

> [!NOTE]
> Programmet måste skapas på samma Azure Active Directory-klientorganisation som ditt nyckelvalv.
>
>

Öppna fliken program i Azure Active Directory.

![Öppna program i Azure Active Directory](./media/keyvault-keyrotation/AzureAD_Header.png)

Välj **Lägg till** att lägga till ett program till Azure Active Directory.

![Välj Lägg till](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Lämna programtyp som **WEB APPLICATION och/eller webb-API** och namnge ditt program.

![Ett namn för programmet](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Ge programmet en **SIGN-ON-URL** och en **APP-ID URI**. Dessa kan vara vad du vill använda för den här demon och de kan ändras senare om det behövs.

![Ange nödvändiga URL: er](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

När programmet har lagts till Azure Active Directory, kommer du till sidan program. Klicka på den **konfigurera** fliken och leta sedan reda på och kopiera den **klient-ID** värde. Anteckna klient-ID för senare steg.

Generera en nyckel för tillämpningsprogrammet bredvid, så den kan interagera med Azure Active Directory. Du kan skapa det under den **nycklar** i avsnittet den **Configuration** fliken. Anteckna den nyligen skapade nyckeln från Azure Active Directory-program för användning i ett senare steg.

![Azure Active Directory App nycklar](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Innan du upprättar ett anrop från ditt program i nyckelvalvet, måste du se nyckelvalvet om programmet och dess behörighet. Följande kommando tar valvnamnet och klient-ID från din app i Azure Active Directory och ger **hämta** åtkomst till nyckelvalvet för programmet.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Du är nu redo att börja bygga program-anrop. I ditt program måste du installera NuGet-paket som krävs för att interagera med Azure Key Vault och Azure Active Directory. Ange följande kommandon från Visual Studio Package Manager-konsolen. Den aktuella versionen av Azure Active Directory-paketet är 3.10.305231913, så du kanske vill bekräfta den senaste versionen och uppdateras vid skrivning av den här artikeln.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Skapa en klass för att lagra metoden för Azure Active Directory-autentisering i din programkod. I det här exemplet är klassen kallas **verktyg för webbplatsuppgradering**. Lägg till följande med instruktionen:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Lägg till följande metod för att hämta JWT-token från Azure Active Directory. Underhålla, kanske du vill flytta hårdkodade strängvärden till webb- eller konfigurationen.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Lägg till den kod som behövs för att anropa Key Vault och hämta det hemliga värdet. Först måste du lägga till följande med instruktionen:

```csharp
using Microsoft.Azure.KeyVault;
```

Lägg till metodanrop att anropa Key Vault och hämta ditt hemliga. I den här metoden kan ange du hemlighet URI som du sparade i föregående steg. Observera användningen av den **GetToken** metod från den **verktyg för webbplatsuppgradering** klassen som skapade tidigare.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

När du kör ditt program nu bör du autentiserar till Azure Active Directory och sedan hämta din hemligt värde från Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Viktiga rotation med hjälp av Azure Automation
Det finns olika alternativ för att implementera en rotation strategi för värden som du lagrar som Azure Key Vault hemligheter. Hemligheter kan roteras som en del av en manuell process, de kan roteras programmässigt med hjälp av API-anrop eller kan roteras med ett Automation-skript. Vid tillämpningen av den här artikeln kommer du att använda Azure PowerShell tillsammans med Azure Automation för att ändra snabbtangent Azure Storage-konto. Sedan uppdaterar du en hemlighet i nyckelvalvet med den nya nyckeln.

Om du vill tillåta Azure Automation att ställa in hemliga värden i nyckelvalvet, måste du hämta klient-ID för anslutningen med namnet AzureRunAsConnection som skapades när du har etablerat Azure Automation-instans. Du hittar det här ID genom att välja **tillgångar** från Azure Automation-instans. Därifrån kan du välja **anslutningar** och välj sedan den **AzureRunAsConnection** tjänsten principen. Anteckna den **program-ID**.

![Azure Automation-klient-ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

I **tillgångar**, Välj **moduler**. Från **moduler**väljer **galleriet**, och sök sedan efter och **importera** uppdaterade versioner av vart och ett av följande moduler:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Vid skrivning av den här artikeln behövs endast modulerna som tidigare meddelanden som ska uppdateras i följande skript. Om du tycker att ditt automation-jobb inte fungerar kan du bekräfta att du har importerat alla moduler som krävs och deras beroenden.
>
>

När du har hämtat program-ID för Azure Automation-anslutning, måste du se nyckelvalvet att det här programmet har åtkomst till uppdatera hemligheter i ditt valv. Detta kan åstadkommas med följande PowerShell-kommando:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Välj därefter **Runbooks** under din Azure Automation-instans och välj sedan **lägga till en Runbook**. Välj **Snabbregistrering**. Din runbook och välj **PowerShell** som runbook-typen. Du har möjlighet att lägga till en beskrivning. Klicka slutligen på **skapa**.

![Skapa runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Klistra in följande PowerShell-skript i fönstret Redigeraren för din nya runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Editor-fönstret Välj **Test fönstret** Testa skriptet. När skriptet körs utan fel, kan du välja **publicera**, och du kan koppla ett schema för runbook tillbaka i fönstret runbook konfiguration.

## <a name="key-vault-auditing-pipeline"></a>Key Vault granskning pipeline
När du ställer in ett nyckelvalv som du kan aktivera granskning för att samla in loggar på åtkomstbegäranden som görs till nyckelvalvet. Dessa loggar lagras i ett avsedda Azure Storage-konto och kan hämtas, övervakas och analyseras. Följande scenario använder Azure functions, Azure logikappar och nyckelvalv granskningsloggar för att skapa en pipeline för att skicka ett e-postmeddelande när en app som matchar det app-ID för webbappen hämtar hemligheter från valvet.

Först måste du aktivera loggning på nyckelvalvet. Detta kan göras via följande PowerShell-kommandon (fullständig information kan visas vid [key vault loggning](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

När den är aktiverad, granskningsloggar start insamling till avsedda storage-konto. Dessa loggar innehålla händelser om hur och när ditt nyckelvalv kan nås och av vem.

> [!NOTE]
> Du kan komma åt din loggningsinformation 10 minuter efter nyckelvalv igen. Det kommer vanligtvis vara snabbare än så.
>
>

Nästa steg är att [skapa en Azure Service Bus-kö](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Detta är där nyckelvalv granskningsloggar push. När granskningsloggmeddelanden i kön logikappen hämtar dem och fungerar på dem. Skapa en service bus med följande steg:

1. Skapa en Service Bus-namnrymd (om du redan har en som du vill använda för detta, hoppar du till steg 2).
2. Bläddra till service bus i Azure-portalen och markera det namnområde som du vill skapa kön i.
3. Välj **skapar du en resurs**, **Enterprise Integration**, **Service Bus**, och ange nödvändig information.
4. Välj information för Service Bus-anslutning genom att välja namnområdet och klicka på **anslutningsinformationen**. Du behöver den här informationen för nästa avsnitt.

Nästa [skapa en Azure-funktion](../azure-functions/functions-create-first-azure-function.md) att avsöka nyckelvalv loggar i lagringskontot och hämta nya händelser. Det här är en funktion som utlöses enligt ett schema.

Om du vill skapa en Azure-funktion, Välj **skapar du en resurs**, söka marketplace för _Funktionsapp_, och klicka på **skapa**. Du kan använda en befintlig värd plan eller skapa en ny vid skapandet. Du kan också välja dynamisk värd. Mer information om funktionen som värd för alternativ finns på [så här skalar du Azure Functions](../azure-functions/functions-scale.md).

När funktionen Azure skapas, navigera till den och väljer en timer funktionen och C\#. Klicka på **skapa den här funktionen**.

![Azure Functions starta bladet](./media/keyvault-keyrotation/Azure_Functions_Start.png)

På den **utveckla** fliken ersätter run.csx koden med följande:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```


> [!NOTE]
> Se till att ersätta variabler i föregående kod så att den pekar till ditt lagringskonto där nyckelvalv loggarna skrivs, service bus som du skapade tidigare och sökvägen till nyckelvalvet lagring loggar.
>
>

Funktionen hämtar senaste loggfilen från lagringskontot där nyckelvalv loggarna skrivs, hämtar de senaste händelserna från filen och skickar dem till en Service Bus-kö. Eftersom en enda fil kan ha flera händelser, bör du skapa en sync.txt-fil som funktionen också kontrollerar för att fastställa tidsstämpeln för den senaste händelse som hämtades. Detta säkerställer att du inte push samma händelse flera gånger. Sync.txt filen innehåller en tidsstämpel för senaste påträffade händelsen. Loggar, vid lästs in, måste sorteras utifrån tidsstämpel så de sorteras på rätt sätt.

För den här funktionen referera vi några ytterligare bibliotek som inte är tillgängliga direkt i Azure Functions. Om du vill inkludera dessa måste Azure Functions kan hämta dem med hjälp av NuGet. Välj den **visa filer** alternativet.

![Visa filer](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Och Lägg till en fil som heter project.json med följande innehåll:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Vid **spara**, Azure Functions laddar ned nödvändiga binärfiler.

Växla till den **integrera** fliken och ge parametern timer ett beskrivande namn för att användas i funktionen. I föregående kod den förväntar sig timern anropas *myTimer*. Ange en [CRON-uttryck](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) enligt följande: 0 \* \* \* \* \* för timer som gör att funktionen ska köras en gång i minuten.

På samma **integrera** lägger du till indata av typen **Azure Blob Storage**. Detta att peka sync.txt-fil som innehåller tidsstämpel för senaste händelsen tittat på av funktionen. Det här är tillgängliga i funktionen av parameternamnet. I föregående kod Azure Blob Storage-indata förväntar parameternamnet ska *inputBlob*. Väljer det lagringskonto där sync.txt-filen ska placeras (det kan vara samma eller ett annat lagringskonto). I sökvägsfältet, ange sökvägen där filen finns i formatet {container-name}/path/to/sync.txt.

Lägga till utdata av typen *Azure Blob Storage* utdata. Detta peka mot sync.txt-filen som du har definierat i indata. Detta används av funktionen för att skriva tidsstämpel för senaste händelsen tittar på. Föregående kod förväntar sig att den här parametern anropas *outputBlob*.

Funktionen är nu klar. Se till att växla tillbaka till den **utveckla** fliken och spara koden. Kontrollera utdatafönstret för några kompileringsfel och korrigera dem i enlighet med detta. Om koden kompilerar ska koden nu kontrollerar nyckelvalv loggar varje minut och därefter överföra alla nya händelser till definierade Service Bus-kö. Du bör se loggningsinformation skriva till loggfönstret varje gång funktionen utlöses.

### <a name="azure-logic-app"></a>Azure logikapp
Nu måste du skapa ett Azure logikappen som hämtar händelser att funktionen är push-installation till Service Bus-kö, tolkar innehållet och skickar ett e-post baserat på ett villkor som matchas.

[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) genom att gå till **New > Logikapp**.

När logikappen har skapats, navigera till den och välj **redigera**. I Redigeraren för logik-app väljer **Service Bus-kö** och ange dina autentiseringsuppgifter för Service Bus för att ansluta till kön.

![Logik för Azure App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Välj nästa **Lägg till ett villkor**. Växla till redigeraren i villkoret och ange följande kod, ersätta APP_ID med den faktiska APP_ID av ditt webbprogram:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Det här uttrycket returnerar i stort sett **FALSKT** om den *appid* från inkommande händelse (vilket är Service Bus-postmeddelandets brödtext) är inte den *appid* av appen.

Nu ska du skapa en åtgärd under **om Nej, gör ingenting**.

![Azure Logikapp välja åtgärd](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Åtgärden, Välj **Office 365 – skicka e-post**. Fyll i fälten för att skapa ett e-postmeddelande ska skickas när det definierade villkoret returnerar **FALSKT**. Om du inte har Office 365 kan du titta på alternativ för att uppnå samma resultat.

Nu har du en slutpunkt till slutpunkt-pipeline som söker efter nya nyckelvalv granskningsloggar en gång i minuten. Den skickar nya loggar påträffas till en service bus-kö. Logikappen utlöses när ett nytt meddelande hamnar i kön. Om den *appid* i händelsen inte matchar det app-ID för det anropande programmet, skickas ett e-postmeddelande.
