---
title: Konfigurera Azure Key Vault med nyckel rotation från slut punkt till slut punkt och granskning | Microsoft Docs
description: Använd den här guiden för att hjälpa dig att konfigurera nyckel rotation och övervaka Key Vault-loggar.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a0aa20a8d1ddecfe401a4e099a4f298971779501
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720120"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Konfigurera Azure Key Vault med nyckel rotation och granskning

## <a name="introduction"></a>Introduktion

När du har ett nyckel valv kan du börja använda det för att lagra nycklar och hemligheter. Dina program behöver inte längre Spara dina nycklar eller hemligheter, men kan begära dem från valvet efter behov. Med ett nyckel valv kan du uppdatera nycklar och hemligheter utan att påverka programmets beteende, vilket innebär att du kan öppna en mängd olika möjligheter för din nyckel och hemliga hantering.

>[!IMPORTANT]
> Exemplen i den här artikeln är endast avsedda som illustration. De är inte avsedda att användas för produktion. 

Den här artikeln vägleder dig genom:

- Ett exempel på att använda Azure Key Vault för att lagra en hemlighet. I den här artikeln är hemligheten som lagras den Azure Storage-konto nyckel som används av ett program. 
- Hur du implementerar en schemalagd rotation av lagrings konto nyckeln.
- Så här övervakar du gransknings loggarna i Key Vault och genererar aviseringar när oväntade begär Anden görs.

> [!NOTE]
> Den här artikeln beskriver inte den första konfigurationen av ditt nyckel valv. Mer information finns i [Vad är Azure Key Vault?](key-vault-overview.md). Instruktioner för plattforms oberoende kommando rads gränssnitt finns i [hantera Key Vault med Azure CLI](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Konfigurera Key Vault

Om du vill aktivera ett program för att hämta en hemlighet från Key Vault måste du först skapa hemligheten och överföra den till ditt valv.

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

```powershell
Connect-AzAccount
```

I fönstret popup-webbläsare anger du användar namn och lösen ord för ditt Azure-konto. PowerShell kommer att hämta alla prenumerationer som är associerade med det här kontot. PowerShell använder som standard den första.

Om du har flera prenumerationer kan du behöva ange den som användes för att skapa ett nyckel valv. Ange följande för att se prenumerationerna för ditt konto:

```powershell
Get-AzSubscription
```

Om du vill ange den prenumeration som är associerad med nyckel valvet loggar du in:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Eftersom den här artikeln visar hur du lagrar en lagrings konto nyckel som en hemlighet måste du hämta lagrings konto nyckeln.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

När du har hämtat din hemlighet (i det här fallet din lagrings konto nyckel) måste du konvertera nyckeln till en säker sträng och sedan skapa en hemlighet med det värdet i nyckel valvet.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Hämta sedan URI för hemligheten som du skapade. Du behöver denna URI i ett senare steg för att anropa nyckel valvet och hämta din hemlighet. Kör följande PowerShell-kommando och anteckna ID-värdet, vilket är hemlighetens URI:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurera programmet

Nu när du har lagrat en hemlighet kan du använda kod för att hämta och använda den när du har utfört några fler steg.

Först måste du registrera ditt program med Azure Active Directory. Se sedan Key Vault din programinformation så att den kan tillåta förfrågningar från ditt program.

> [!NOTE]
> Ditt program måste skapas på samma Azure Active Directory-klient som nyckel valvet.

1. Öppna **Azure Active Directory**.
2. Välj **Appregistreringar**. 
3. Välj **ny program registrering** för att lägga till ett program i Azure Active Directory.

    ![Öppna program i Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Under **skapa**, låt program typen vara **Web App/API** och ge programmet ett namn. Ge ditt program en **inloggnings-URL**. Den här URL: en kan vara vad du vill för den här demon.

    ![Skapa program registrering](./media/keyvault-keyrotation/create-app.png)

5. När programmet har lagts till i Azure Active Directory öppnas sidan program. Välj **Inställningar**och välj sedan **Egenskaper**. Kopiera **programmets ID-** värde. Du behöver den i senare steg.

Sedan genererar du en nyckel för programmet så att det kan interagera med Azure Active Directory. Om du vill skapa en nyckel väljer du **nycklar** under **Inställningar**. Anteckna den nyligen genererade nyckeln för ditt Azure Active Directory-program. Du behöver den senare. Nyckeln är inte tillgänglig när du har lämnat det här avsnittet. 

![Azure Active Directory app-nycklar](./media/keyvault-keyrotation/create-key.png)

Innan du upprättar några anrop från ditt program till nyckel valvet måste du meddela nyckel valvet om ditt program och dess behörigheter. Följande kommando använder valv namnet och program-ID: t från din Azure Active Directory-app för att ge **programmet åtkomst till ditt** nyckel valv.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Nu är du redo att börja skapa dina program samtal. I ditt program måste du installera de NuGet-paket som krävs för att interagera med Azure Key Vault och Azure Active Directory. I Visual Studio Package Manager-konsolen anger du följande kommandon. Vid skrivning av den här artikeln är den aktuella versionen av det Azure Active Directory paketet 3.10.305231913, så bekräfta den senaste versionen och uppdatera efter behov.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

I din program kod skapar du en klass som innehåller metoden för din Azure Active Directory autentisering. I det här exemplet kallas klassen **utils**. Lägg till följande `using`-instruktion:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Lägg sedan till följande metod för att hämta JWT-token från Azure Active Directory. Du kanske vill flytta de hårdkodade sträng värdena till din webb-eller program konfiguration.

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

Lägg till den kod som krävs för att anropa Key Vault och hämta ditt hemliga värde. Först måste du lägga till följande `using`-instruktion:

```csharp
using Microsoft.Azure.KeyVault;
```

Lägg till metod anrop för att anropa Key Vault och hämta din hemlighet. I den här metoden anger du den hemliga URI som du sparade i föregående steg. Observera användningen av **GetToken** -metoden från klassen **utils** som du skapade tidigare.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

När du kör programmet bör du nu autentisera till Azure Active Directory och sedan hämta ditt hemliga värde från Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Nyckel rotation med Azure Automation

> [!IMPORTANT]
> Azure Automation runbooks kräver fortfarande att `AzureRM`-modulen används.

Nu är du redo att konfigurera en rotations strategi för de värden som du lagrar som Key Vault hemligheter. Det går att rotera hemligheter på flera sätt:

- Som en del av en manuell process
- Programmering med API-anrop
- Via ett Azure Automation-skript

I den här artikeln använder du PowerShell kombinerat med Azure Automation för att ändra åtkomst nyckeln för ett Azure Storage-konto. Sedan uppdaterar du nyckel valv hemligheten med den nya nyckeln.

Om du vill tillåta Azure Automation att ange hemliga värden i ditt nyckel valv måste du hämta klient-ID för anslutningen med namnet **AzureRunAsConnection**. Den här anslutningen skapades när du etablerade Azure Automation-instansen. Om du vill hitta detta ID väljer du **till gångar** från Azure Automation-instansen. Därifrån väljer du **anslutningar**och väljer sedan **AzureRunAsConnection** -tjänstens huvud namn. Anteckna värdet för **ApplicationId** .

![Azure Automation klient-ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

I **till gångar**väljer du **moduler**. Välj **Galleri**och Sök sedan efter och importera uppdaterade versioner av var och en av följande moduler:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> När den här artikeln skrevs är det bara de tidigare noterade modulerna som behövde uppdateras för följande skript. Om ditt Automation-jobb Miss lyckas bekräftar du att du har importerat alla nödvändiga moduler och deras beroenden.

När du har hämtat program-ID: t för din Azure Automation-anslutning måste du meddela ditt nyckel valv att det här programmet har behörighet att uppdatera hemligheter i ditt valv. Använd följande PowerShell-kommando:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Välj sedan **Runbooks** under Azure Automation-instansen och välj sedan **Lägg till Runbook**. Välj **Snabbregistrering**. Namnge din Runbook och välj **PowerShell** som Runbook-typ. Du kan lägga till en beskrivning. Välj slutligen **skapa**.

![Skapa runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Klistra in följande PowerShell-skript i redigerings fönstret för din nya Runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
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

# Optionally you can set the following as parameters
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

I redigerings fönstret väljer du **test fönster** för att testa skriptet. När skriptet har körts utan fel kan du välja **publicera**, så kan du använda ett schema för Runbook i fönstret konfiguration av Runbook.

## <a name="key-vault-auditing-pipeline"></a>Key Vault gransknings pipeline

När du konfigurerar ett nyckel valv kan du aktivera granskning för att samla in loggar på åtkomst begär Anden som gjorts till nyckel valvet. Dessa loggar lagras i ett angivet Azure Storage-konto och kan hämtas, övervakas och analyseras. I följande scenario används Azure Functions, Azure Logic Apps och gransknings loggar för nyckel valvet för att skapa en pipeline som skickar ett e-postmeddelande när en app som inte matchar appens ID för webbappen hämtar hemligheter från valvet.

Först måste du aktivera loggning i nyckel valvet. Använd följande PowerShell-kommandon. (Du kan se den fullständiga informationen i [den här artikeln om nyckel valv loggning](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

När loggning har Aktiver ATS börjar gransknings loggar att lagras på det angivna lagrings kontot. Dessa loggar innehåller händelser om hur och när dina nyckel valv används, och av vem.

> [!NOTE]
> Du kan komma åt loggnings informationen 10 minuter efter nyckel valvs åtgärden. Den kommer ofta att vara tillgänglig tidigare än den.

Nästa steg är att [skapa en Azure Service Bus kö](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). I den här kön överförs nyckel valvs gransknings loggar. När Gransknings logg meddelanden finns i kön, använder Logic-appen dem och fungerar på dem. Skapa en Service Bus-instans med följande steg:

1. Skapa ett Service Bus namn område (om du redan har ett som du vill använda kan du gå vidare till steg 2).
2. Bläddra till Service Bus-instansen i Azure Portal och välj det namn område som du vill skapa kön i.
3. Välj **skapa en resurs** > **Enterprise-integration** > **Service Bus**och ange sedan den information som krävs.
4. Hitta Service Bus anslutnings information genom att markera namn området och sedan välja **anslutnings information**. Du behöver den här informationen för nästa avsnitt.

Skapa sedan [en Azure-funktion](../azure-functions/functions-create-first-azure-function.md) för att söka i Key Vault-loggarna i lagrings kontot och hämta nya händelser. Den här funktionen aktive ras enligt ett schema.

Om du vill skapa en Azure Function-app väljer du **skapa en resurs**, söker i marketplace efter **Funktionsapp**och väljer sedan **skapa**. När du har skapat kan du använda en befintlig värd plan eller skapa en ny. Du kan också välja dynamisk värd. Mer information om värd alternativen för Azure Functions finns i [skala Azure Functions](../azure-functions/functions-scale.md).

När du har skapat Azure Function-appen går du till den och väljer **tids** scenariot och **C-\#** för språket. Välj sedan **skapa den här funktionen**.

![Azure Functions start bladet](./media/keyvault-keyrotation/Azure_Functions_Start.png)

På fliken **utveckla** ersätter du koden kör. CSX med följande:

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
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
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

            //Required to order by time as they might not be in the file
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
> Ändra variablerna i föregående kod så att de pekar på ditt lagrings konto där Key Vault-loggarna skrivs, till Service Bus-instansen som du skapade tidigare och till den angivna sökvägen till lagrings loggarna Key Vault.

Funktionen hämtar den senaste logg filen från lagrings kontot där Key Vault-loggarna skrivs, hämtar de senaste händelserna från filen och skickar dem till en Service Bus kö. 

Eftersom en enskild fil kan ha flera händelser bör du skapa en Sync. txt-fil som funktionen också tittar på för att fastställa tidsstämpeln för den senaste händelsen som hämtades. Genom att använda den här filen ser du till att du inte pushar samma händelse flera gånger. 

Filen Sync. txt innehåller en tidsstämpel för den senast påträffade händelsen. När loggarna läses in måste de sorteras baserat på deras tidsstämplar för att kontrol lera att de har beställts korrekt.

För den här funktionen refererar vi till ett par ytterligare bibliotek som inte är tillgängliga i rutan i Azure Functions. För att inkludera dessa bibliotek behöver vi Azure Functions för att hämta dem med hjälp av NuGet. Välj **Visa filer**under rutan **kod** .

![Alternativet "Visa filer"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Lägg till en fil med namnet Project. JSON med följande innehåll:

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

När du har valt **Spara**kommer Azure Functions att ladda ned de binärfiler som krävs.

Växla till fliken **integrera** och ge parametern timer ett meningsfullt namn som ska användas i funktionen. I föregående kod förväntar sig funktionen att timern ska kallas min *tid*. Ange ett [cron-uttryck](../app-service/webjobs-create.md#CreateScheduledCRON) för timern enligt följande: `0 * * * * *`. Det här uttrycket gör att funktionen körs en gång i minuten.

På samma **integrera** -flik lägger du till en indata från typen **Azure Blob Storage**. Den här indatan pekar på filen Sync. txt som innehåller tidsstämpeln för den senaste händelsen som såg ut av funktionen. Den här indatamängden används i funktionen med hjälp av parameter namnet. I föregående kod förväntar Azure Blob Storage-indata parameter namnet som *inputBlob*. Välj det lagrings konto där filen Sync. txt ska placeras (det kan vara samma eller ett annat lagrings konto). I fältet sökväg anger du sökvägen till filen i formatet `{container-name}/path/to/sync.txt`.

Lägg till utdata av typen **Azure Blob Storage**. Utdata pekar på filen Sync. txt som du definierade i indata. Dessa utdata används av funktionen för att skriva tidsstämpeln för den senaste händelsen som tittat på. Föregående kod förväntar sig att den här parametern kallas *outputBlob*.

Funktionen är nu klar. Se till att växla tillbaka till fliken **utveckla** och spara koden. Kontrol lera fönstret utdata för eventuella kompileringsfel och korrigera dem efter behov. Om koden kompileras bör nu koden kontrol lera nyckel valvets loggar varje minut och skicka nya händelser till den definierade Service Bus kön. Du bör se loggnings information som skrivs ut till logg fönstret varje gång funktionen utlöses.

### <a name="azure-logic-app"></a>Azure Logic-app

Därefter måste du skapa en Azure Logic-app som hämtar de händelser som funktionen skickar till Service Bus kön, tolkar innehållet och skickar ett e-postmeddelande baserat på ett villkor som matchas.

[Skapa en logisk app](../logic-apps/quickstart-create-first-logic-app-workflow.md) genom att välja **skapa en resurs** > **integration** > **Logic app**.

När du har skapat Logic-appen går du till den och väljer **Redigera**. I Logic app-redigeraren väljer du **Service Bus kö** och anger dina Service Bus autentiseringsuppgifter för att ansluta den till kön.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Välj **Lägg till ett villkor**. I villkoret växlar du till avancerad redigerare och anger följande kod. Ersätt *APP_ID* med det faktiska app-ID: t för din webbapp:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Det här uttrycket returnerar i princip **falskt** om *AppID* från den inkommande händelsen (som är bröd texten i Service Bus meddelandet) inte är *AppID* för appen.

Skapa nu en åtgärd under **om Nej, gör ingenting**.

![Azure Logic Apps Välj åtgärd](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

För åtgärden väljer du **Office 365 – skicka e-post**. Fyll i fälten för att skapa ett e-postmeddelande som ska skickas när det definierade villkoret returnerar **false**. Om du inte har Office 365 kan du leta efter alternativ för att få samma resultat.

Nu har du en pipeline från slut punkt till slut punkt som söker efter nya gransknings loggar för nyckel valvet en gång i minuten. Nya loggar som hittas i en Service Bus-kö skickas. Logic app utlöses när ett nytt meddelande hamnar i kön. Om *AppID* i händelsen inte matchar app-ID: t för det anropande programmet, skickar det ett e-postmeddelande.
