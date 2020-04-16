---
title: Konfigurera Azure Key Vault med end-to-end nyckelrotation och granskning | Microsoft-dokument
description: Använd den här hjälpguiden för att konfigurera nyckelrotation och övervaka nyckelvalvsloggar.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d2981495a256ce5fb8f8f3584e68ac91541f9d62
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430258"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Konfigurera Azure Key Vault med nyckelrotation och granskning

## <a name="introduction"></a>Introduktion

När du har ett nyckelvalv kan du börja använda det för att lagra nycklar och hemligheter. Dina program behöver inte längre spara dina nycklar eller hemligheter, men kan begära dem från valvet efter behov. Med ett nyckelvalv kan du uppdatera nycklar och hemligheter utan att påverka programmets beteende, vilket öppnar upp en bredd av möjligheter för din nyckel och hemliga hantering.

Den här artikeln går igenom hur du implementerar en schemalagd rotation av lagringskontonycklar, övervakar nyckelvalvets granskningsloggar och väcker aviseringar när oväntade begäranden görs. 

Du måste först skapa ett nyckelvalv med valfri metod:

- [Ställ in och hämta en hemlighet från Azure Key Vault med hjälp av Azure CLI](quick-create-cli.md)
- [Ange och hämta en hemlighet från Azure Key Vault med Azure PowerShell](quick-create-powershell.md)
- [Ange och hämta en hemlighet från Azure Key Vault med Azure-portal](quick-create-portal.md)


## <a name="store-a-secret"></a>Lagra en hemlighet

Om du vill att ett program ska kunna hämta en hemlighet från Key Vault måste du först skapa hemligheten och ladda upp det till valvet.

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

```powershell
Connect-AzAccount
```

I popup-webbläsarfönstret anger du användarnamn och lösenord för ditt Azure-konto. PowerShell hämtar alla prenumerationer som är associerade med det här kontot. PowerShell använder den första som standard.

Om du har flera prenumerationer kan du behöva ange den som användes för att skapa nyckelvalvet. Ange följande för att se prenumerationerna för ditt konto:

```powershell
Get-AzSubscription
```

Om du vill ange vilken prenumeration som är associerad med nyckelvalvet som du ska logga anger du:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Eftersom den här artikeln visar att lagra en lagringskontonyckel som en hemlighet måste du hämta nyckeln för lagringskontot.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

När du har hämtat din hemlighet (i det här fallet din lagringskontonyckel) måste du konvertera nyckeln till en säker sträng och sedan skapa en hemlighet med det värdet i nyckelvalvet.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Hämta sedan URI:n för hemligheten du skapade. Du behöver den här URI:n i ett senare steg för att ringa nyckelvalvet och hämta din hemlighet. Kör följande PowerShell-kommando och anteckna ID-värdet, som är hemlighetens URI:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurera programmet

Nu när du har en hemlighet lagrad kan du använda kod för att hämta och använda den efter att ha utfört några steg till.

Först måste du registrera ditt program med Azure Active Directory. Tala sedan om för Key Vault din programinformation så att den kan tillåta begäranden från ditt program.

> [!NOTE]
> Ditt program måste skapas på samma Azure Active Directory-klient som ditt nyckelvalv.

1. Öppna **Azure Active Directory**.
2. Välj **Appregistreringar**. 
3. Välj **Ny programregistrering** om du vill lägga till ett program i Azure Active Directory.

    ![Öppna program i Azure Active Directory](../media/keyvault-keyrotation/azure-ad-application.png)

4. Under **Skapa**lämnar du programtypen som **webbapp/API** och ger ditt program ett namn. Ge ditt program en **inloggnings-URL**. Denna webbadress kan vara vad du vill för denna demo.

    ![Skapa programregistrering](../media/keyvault-keyrotation/create-app.png)

5. När programmet har lagts till i Azure Active Directory öppnas programsidan. Välj **Inställningar**och välj sedan **Egenskaper**. Kopiera **värdet för program-ID.** Du behöver det i senare steg.

Skapa sedan en nyckel för ditt program så att den kan interagera med Azure Active Directory. Om du vill skapa en nyckel väljer du **Tangenter** under **Inställningar**. Anteckna den nygenererade nyckeln för ditt Azure Active Directory-program. Du behöver den senare. Nyckeln är inte tillgänglig när du har lämnat det här avsnittet. 

![Azure Active Directory-appnycklar](../media/keyvault-keyrotation/create-key.png)

Innan du upprättar några samtal från ditt program i nyckelvalvet måste du berätta för nyckelvalvet om ditt program och dess behörigheter. Följande kommando använder valvets namn och program-ID från din Azure Active Directory-app för att bevilja programmet **Hämta** åtkomst till ditt nyckelvalv.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Du är nu redo att börja bygga dina programsamtal. I ditt program måste du installera NuGet-paket som krävs för att interagera med Azure Key Vault och Azure Active Directory. Ange följande kommandon i konsolen Visual Studio Package Manager. Vid skrivandet av den här artikeln är den aktuella versionen av Azure Active Directory-paketet 3.10.305231913, så bekräfta den senaste versionen och uppdateringen efter behov.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Skapa en klass för att lagra metoden för din Azure Active Directory-autentisering i programkoden. I det här exemplet kallas den klassen **Utils**. Lägg till `using` följande sats:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Lägg sedan till följande metod för att hämta JWT-token från Azure Active Directory. För underhållsbarhet kanske du vill flytta de hårdkodade strängvärdena till webb- eller programkonfigurationen.

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

Lägg till den kod som krävs för att anropa Key Vault och hämta ditt hemliga värde. Först måste du lägga `using` till följande uttryck:

```csharp
using Microsoft.Azure.KeyVault;
```

Lägg till metodanrop för att anropa Key Vault och hämta din hemlighet. I den här metoden anger du den hemliga URI som du sparade i ett tidigare steg. Observera att metoden **GetToken** används från klassen **Utils** som du skapade tidigare.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

När du kör ditt program bör du nu autentisera till Azure Active Directory och sedan hämta ditt hemliga värde från Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Nyckelrotation med Azure Automation

> [!IMPORTANT]
> Azure Automation-runbooks kräver fortfarande `AzureRM` användning av modulen.

Du är nu redo att ställa in en rotationsstrategi för de värden du lagrar som Key Vault hemligheter. Hemligheter kan roteras på flera sätt:

- Som en del av en manuell process
- Programmässigt med hjälp av API-anrop
- Genom ett Azure Automation-skript

I den här artikeln används PowerShell i kombination med Azure Automation för att ändra åtkomstnyckeln för ett Azure-lagringskonto. Du kommer sedan att uppdatera en nyckel valv hemlighet med den nya nyckeln.

Om du vill att Azure Automation ska kunna ange hemliga värden i nyckelvalvet måste du hämta klient-ID:et för anslutningen som heter **AzureRunAsConnection**. Den här anslutningen skapades när du etablerade din Azure Automation-instans. Om du vill hitta det här ID:t väljer du **Resurser** från din Azure Automation-instans. Därifrån väljer du **Anslutningar**och väljer sedan huvudhuvudet för **AzureRunAsConnection-tjänsten.** Anteckna **ApplicationId-värdet.**

![Azure Automation-klient-ID](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Välj **Moduler**i **Tillgångar**. Välj **Galleri**och sök sedan efter och importera uppdaterade versioner av var och en av följande moduler:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Vid skrivandet av den här artikeln behövde endast de tidigare noterade modulerna uppdateras för följande skript. Om ditt automatiseringsjobb misslyckas bekräftar du att du har importerat alla nödvändiga moduler och deras beroenden.

När du har hämtat program-ID:n för din Azure Automation-anslutning måste du tala om för nyckelvalvet att det här programmet har behörighet att uppdatera hemligheter i valvet. Använd följande PowerShell-kommando:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Välj sedan **Runbooks** under din Azure Automation-instans och välj sedan **Lägg till runbook**. Välj **Snabbregistrering**. Namnge runbooken och välj **PowerShell** som runbooktyp. Du kan lägga till en beskrivning. Slutligen väljer du **Skapa**.

![Skapa runbook](../media/keyvault-keyrotation/Create_Runbook.png)

Klistra in följande PowerShell-skript i redigeringsfönstret för den nya runbooken:

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

I redigeringsfönstret väljer du **Testfönster** för att testa skriptet. När skriptet körs utan fel kan du välja **Publicera**och sedan kan du använda ett schema för runbooken i konfigurationsfönstret för runbook.

## <a name="key-vault-auditing-pipeline"></a>Granskning av nyckelvalvspipeline

När du konfigurerar ett nyckelvalv kan du aktivera granskning för att samla in loggar på åtkomstbegäranden som görs till nyckelvalvet. Dessa loggar lagras i ett angivet Azure-lagringskonto och kan dras ut, övervakas och analyseras. I följande scenario används Azure-funktioner, Azure-logikappar och granskningsloggar för nyckelvalv för att skapa en pipeline som skickar ett e-postmeddelande när en app som inte matchar webbappens app-ID hämtar hemligheter från valvet.

Först måste du aktivera loggning i nyckelvalvet. Använd följande PowerShell-kommandon. (Du kan se fullständig information i den [här artikeln om nyckelvalv-loggning](../general/logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

När loggning har aktiverats börjar granskningsloggar lagras i det angivna lagringskontot. Dessa loggar innehåller händelser om hur och när dina nyckelvalv nås och av vem.

> [!NOTE]
> Du kan komma åt loggningsinformationen 10 minuter efter nyckelvalvsåtgärden. Det kommer ofta att finnas tillgänglig tidigare än så.

Nästa steg är att [skapa en Azure Service Bus kö](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Den här kön är där nyckelvalv granskningsloggar skjuts. När granskningsloggmeddelandena finns i kön hämtar logikappen dem och fungerar på dem. Skapa en Service Bus-instans med följande steg:

1. Skapa ett servicebussnamnområde (om du redan har ett som du vill använda går du vidare till steg 2).
2. Bläddra till Service Bus-instansen i Azure-portalen och välj det namnområde som du vill skapa kön i.
3. Välj Skapa en**servicebuss****för företagandet** >  **Create a resource** > och ange sedan den information som krävs.
4. Hitta servicebussanslutningsinformationen genom att välja namnområdet och välj sedan **Anslutningsinformation**. Du behöver den här informationen för nästa avsnitt.

Skapa [sedan en Azure-funktion](../../azure-functions/functions-create-first-azure-function.md) för att avsöka nyckelvalvloggarna i lagringskontot och hämta nya händelser. Den här funktionen utlöses enligt ett schema.

Om du vill skapa en Azure-funktionsapp väljer du **Skapa en resurs,** söker på marknadsplatsen efter **Funktionsapp**och väljer sedan **Skapa**. När du skapar kan du använda en befintlig värdplan eller skapa en ny. Du kan också välja dynamisk hosting. Mer information om värdalternativen för Azure Functions finns i [Så här skalar du Azure Functions](../../azure-functions/functions-scale.md).

När Azure-funktionsappen har skapats går du till den och väljer **timerscenariot** och **C\# ** för språket. Välj sedan **Skapa den här funktionen**.

![Startblad för Azure-funktioner](../media/keyvault-keyrotation/Azure_Functions_Start.png)

Ersätt filen run.csx-kod med följande på fliken **Utveckla:**

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
> Ändra variablerna i föregående kod för att peka på ditt lagringskonto där nyckelvalvsloggarna skrivs, till servicebussinstansen som du skapade tidigare och till den specifika sökvägen till lagringsloggarna för nyckelvalv.

Funktionen hämtar den senaste loggfilen från lagringskontot där nyckelvalvsloggarna skrivs, tar de senaste händelserna från filen och skickar dem till en Service Bus-kö. 

Eftersom en enskild fil kan ha flera händelser bör du skapa en sync.txt-fil som funktionen också tittar på för att fastställa tidsstämpeln för den senaste händelsen som plockades upp. Om du använder den här filen ser du till att du inte skickar samma händelse flera gånger. 

Filen sync.txt innehåller en tidsstämpel för den senaste händelsen påträffades. När loggarna läses in måste de sorteras baserat på deras tidsstämplar för att säkerställa att de är rätt ordnade.

För den här funktionen refererar vi till ytterligare ett par bibliotek som inte är tillgängliga direkt i Azure Functions. För att inkludera dessa bibliotek behöver vi Azure Functions för att hämta dem med NuGet. Välj **Visa filer**under rutan **Kod** .

![Alternativet "Visa filer"](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Lägg till en fil som heter project.json med följande innehåll:

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

När du har valt **Spara**hämtas de binärfiler som krävs.

Växla till fliken **Integrera** och ge timerparametern ett meningsfullt namn att använda i funktionen. I föregående kod förväntar sig funktionen att timern kallas *myTimer*. Ange ett [CRON-uttryck](../../app-service/webjobs-create.md#CreateScheduledCRON) för timern `0 * * * * *`enligt följande: . Det här uttrycket gör att funktionen körs en gång i minuten.

Lägg till en indata av typen **Azure Blob storage**på samma fliken **Integrera** . Den här indata pekar på filen sync.txt som innehåller tidsstämpeln för den senaste händelsen som funktionen tittade på. Den här indata kommer att nås i funktionen med hjälp av parameternamnet. I föregående kod förväntar sig Azure Blob-lagringsindata att parameternamnet ska *matas inBlob*. Välj det lagringskonto där filen sync.txt ska finnas (det kan vara samma eller ett annat lagringskonto). Ange sökvägen till filen i formatet `{container-name}/path/to/sync.txt`i sökvägsfältet .

Lägg till en utdata av typen **Azure Blob storage**. Den här utdata pekar på filen sync.txt som du definierade i indata. Den här utdata används av funktionen för att skriva tidsstämpeln för den senaste händelsen tittade på. Den föregående koden förväntar sig att den här parametern kallas *outputBlob*.

Funktionen är nu klar. Se till att växla tillbaka till fliken **Utveckla** och spara koden. Kontrollera i utdatafönstret om det finns eventuella kompileringsfel och korrigera dem efter behov. Om koden kompileras ska koden nu kontrollera nyckelvalvloggarna varje minut och skicka in nya händelser i den definierade Service Bus-kön. Du bör se loggningsinformation skriva ut till loggfönstret varje gång funktionen utlöses.

### <a name="azure-logic-app"></a>Azure logikapp

Därefter måste du skapa en Azure logic app som hämtar de händelser som funktionen driver till Service Bus-kön, tolkar innehållet och skickar ett e-postmeddelande baserat på ett villkor som matchas.

[Skapa en logikapp](../../logic-apps/quickstart-create-first-logic-app-workflow.md) genom att välja Skapa en > **resursintegrationslogikapp** > **Logic App**. **Create a resource**

När logikappen har skapats går du till den och väljer **Redigera**. I logikappredigeraren väljer du **Servicebusskö** och anger dina Service Bus-autentiseringsuppgifter för att ansluta den till kön.

![Servicebuss för Azure Logic-appar](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Välj **Lägg till ett villkor**. I villkoret växlar du till den avancerade redigeraren och anger följande kod. Ersätt *APP_ID* med webbappens faktiska app-ID:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Det här uttrycket returnerar i huvudsak **false** om *appid* från den inkommande händelsen (som är brödtexten i Service Bus-meddelandet) inte är *appens appid.*

Nu, skapa en åtgärd under **OM NEJ, GÖR INGENTING**.

![Azure Logic Apps väljer åtgärd](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

För åtgärden väljer du **Office 365 - skicka e-post**. Fyll i fälten för att skapa ett e-postmeddelande som ska skickas när det definierade villkoret returnerar **falskt**. Om du inte har Office 365 letar du efter alternativ för att uppnå samma resultat.

Du har nu en end-to-end pipeline som söker efter nya nyckelvalv granskningsloggar en gång i minuten. Den skickar nya loggar som den hittar till en Service Bus-kö. Logikappen utlöses när ett nytt meddelande hamnar i kön. Om *appid* i händelsen inte matchar app-ID för det anropande programmet, skickas ett e-postmeddelande.
