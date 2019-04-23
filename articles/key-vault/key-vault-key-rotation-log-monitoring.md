---
title: Konfigurera Azure Key Vault med nyckelgranskning för slutpunkt till slutpunkt och | Microsoft Docs
description: Använd den här guiden hjälper dig att konfigurera nyckelrotation och övervaka loggarna för nyckelvalvet.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: fb3300a45f905eb57fcc4880269e4a9bed9dac0c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789897"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Konfigurera Azure Key Vault med nyckelgranskning och -

## <a name="introduction"></a>Introduktion

När du har ett nyckelvalv kan börja du använda det för att lagra nycklar och hemligheter. Dina program behöver inte längre att bevara dina nycklar eller hemligheter, men kan begära dem från valvet efter behov. Key vault kan du uppdatera nycklar och hemligheter utan att påverka beteendet för ditt program, vilket öppnar en bred möjligheterna för din nyckel och hemlighetshantering.

>[!IMPORTANT]
> Exemplen i den här artikeln tillhandahålls enbart i illustrationssyfte. De är inte avsedda för användning i produktion. 

Den här artikeln beskriver:

- Ett exempel på hur du använder Azure Key Vault för att lagra en hemlighet. Den hemlighet som lagras är Azure storage-kontonyckel som används av ett program i den här artikeln. 
- Så här implementerar du en schemalagd rotation för den lagringskontonyckeln.
- Så här övervakar du nyckeln valv granskningsloggar och genererar aviseringar när oväntade begäranden görs.

> [!NOTE]
> Den här artikeln beskriver inte i detalj installationen av ditt nyckelvalv. Den här information finns i [vad är Azure Key Vault?](key-vault-overview.md). Anvisningar för plattformsoberoende kommandoradsgränssnitt finns i [hantera Nyckelvalv med hjälp av Azure CLI](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Konfigurera Key Vault

Om du vill aktivera ett program att hämta en hemlighet från Key Vault måste du först skapa hemligheten och överföra den till valvet.

Starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

```powershell
Connect-AzAccount
```

I popup-fönstret, anger du användarnamnet och lösenordet för ditt Azure-konto. PowerShell får alla prenumerationer som är associerade med det här kontot. PowerShell använder den första som standard.

Om du har flera prenumerationer kan behöva du ange det som användes för att skapa ett nyckelvalv. Ange följande för att visa prenumerationerna för ditt konto:

```powershell
Get-AzSubscription
```

Ange om du vill ange den prenumeration som är associerad med nyckelvalvet som du ska logga:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Eftersom den här artikeln visar att lagra en lagringskontonyckel som en hemlighet, måste du hämta den lagringskontonyckeln.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

När du hämtar din hemlighet (i det här fallet din lagringskontonyckel), måste du konvertera nyckeln till en säker sträng och sedan skapa en hemlighet med värdet i ditt nyckelvalv.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Hämta sedan URI: N för den hemlighet som du skapade. Du behöver den här URI: N i ett senare steg att anropa nyckelvalvet och hämta din hemlighet. Kör följande PowerShell-kommando och anteckna ID-värde, som är hemlighetens URI:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurera programmet

Du kan använda kod för att hämta och använda den när du har utfört några fler steg nu när du har en hemlighet som lagras.

Du måste först registrera ditt program med Azure Active Directory. Sedan berätta för Key Vault information om ditt program så att den tillåter förfrågningar från ditt program.

> [!NOTE]
> Programmet måste skapas på samma Azure Active Directory-klient som ditt nyckelvalv.

1. Öppna **Azure Active Directory**.
2. Välj **Appregistreringar**. 
3. Välj **ny programregistrering** lägga till ett program till Azure Active Directory.

    ![Öppna program i Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Under **skapa**, lämna programtyp som **webbapp / API** och namnge ditt program. Ge ditt program en **inloggnings-URL**. Den här URL: en kan vara allt du vill använda för den här demon.

    ![Skapa programregistrering](./media/keyvault-keyrotation/create-app.png)

5. När programmet har lagts till i Azure Active Directory, öppnas appen på sidan. Välj **inställningar**, och välj sedan **egenskaper**. Kopiera den **program-ID** värde. Du behöver det i senare steg.

Nu ska du generera en nyckel för ditt program så att den kan interagera med Azure Active Directory. Om du vill skapa en nyckel, Välj **nycklar** under **inställningar**. Anteckna den nyligen skapade nyckeln för ditt Azure Active Directory-program. Du behöver den senare. Nyckeln inte tillgängliga när du har lämnat det här avsnittet. 

![Azure Active Directory-appnycklar](./media/keyvault-keyrotation/create-key.png)

Innan du upprättar alla anrop från ditt program till nyckelvalvet måste du se nyckelvalvet om ditt program och dess behörigheter. Följande kommando använder valvnamnet och program-ID från din app i Azure Active Directory för att bevilja programmet **hämta** åtkomst till ditt nyckelvalv.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Du är nu redo att börja bygga dina program-anrop. I ditt program måste du installera NuGet-paket som krävs för att interagera med Azure Key Vault och Azure Active Directory. Ange följande kommandon från Visual Studio Package Manager-konsolen. Den aktuella versionen av Azure Active Directory-paketet är 3.10.305231913 vid skrivning av den här artikeln, så kontrollera den senaste versionen och uppdatera efter behov.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Skapa en klass för att lagra metoden för din Azure Active Directory-autentisering i din programkod. I det här exemplet kallas den klassen **Utils**. Lägg till följande `using` instruktionen:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Lägg sedan till följande metod för att hämta JWT-token från Azure Active Directory. Underhållsfunktioner, kanske du vill flytta hårdkodade strängvärden till din webb- eller konfiguration.

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

Lägga till nödvändig kod för att anropa Key Vault och hämta din hemligt värde. Först måste du lägga till följande `using` instruktionen:

```csharp
using Microsoft.Azure.KeyVault;
```

Lägg till metodanrop att anropa Key Vault och hämta din hemlighet. I den här metoden kan ange du hemlig-URI som du sparade i föregående steg. Observera användningen av den **GetToken** metod från den **Utils** klass som du skapade tidigare.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

När du kör ditt program bör du nu att autentisera till Azure Active Directory och hämta sedan din hemligt värde från Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotation av med Azure Automation

> [!IMPORTANT]
> Azure Automation-runbooks fortfarande kräver användning av den `AzureRM` modulen.

Du är nu redo att konfigurera en strategi för rotation för de värden som du lagrar som Key Vault-hemligheter. Hemligheter kan roteras på flera olika sätt:

- Som en del av en manuell process
- Via programmering med hjälp av API-anrop
- Via en Azure Automation-skript

För den här artikeln, ska du ändra åtkomstnyckel för ett Azure storage-konto med hjälp av PowerShell i kombination med Azure Automation. Sedan ska du uppdatera en hemlighet i nyckelvalvet med den nya nyckeln.

Om du vill tillåta Azure Automation till att ange hemliga värden i ditt nyckelvalv måste du hämta klient-ID för anslutningen med namnet **AzureRunAsConnection**. Den här anslutningen skapades när du har skapat din instans av Azure Automation. För att hitta detta ID, Välj **tillgångar** från Azure Automation-instans. Därifrån väljer **anslutningar**, och välj sedan den **AzureRunAsConnection** tjänstens huvudnamn. Anteckna den **ApplicationId** värde.

![Azure Automation-klient-ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

I **tillgångar**väljer **moduler**. Välj **galleriet**, och sök sedan efter och importera uppdaterade versioner av vart och ett av följande moduler:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Vid skrivning av den här artikeln behövs bara modulerna som vi nämnde tidigare som ska uppdateras i följande skript. Om ditt automation-jobb misslyckas kontrollerar du att du har importerat alla nödvändiga moduler och deras beroenden.

När du har hämtat det program-ID för Azure Automation-anslutningen kan se du ditt nyckelvalv för att att det här programmet har behörighet att uppdatera hemligheter i valvet. Använd följande PowerShell-kommando:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Välj sedan **Runbooks** under ditt Azure Automation-instans och välj sedan **Lägg till Runbook**. Välj **Snabbregistrering**. Namnge din runbook och välj **PowerShell** som runbooktyp av. Du kan lägga till en beskrivning. Välj slutligen **skapa**.

![Skapa runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Klistra in följande PowerShell-skript i redigerarfönstret för din nya runbook:

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

$secret = Set-AzureRmKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

I redigerarfönstret väljer **Testfönster** att testa ditt skript. När skriptet har körts utan problem, du kan välja **publicera**, och du kan koppla ett schema för runbooken i fönstret konfiguration för runbook.

## <a name="key-vault-auditing-pipeline"></a>Key Vault granskning pipeline

När du konfigurerar ett nyckelvalv, kan du aktivera granskning för att samla in loggar vid åtkomstbegäranden som görs till nyckelvalvet. Dessa loggar lagras i en angiven Azure storage-konto och kan hämtas, övervakas och analyseras. Följande scenario använder Azure functions, Azure logic apps och key vault-granskningsloggar för att skapa en pipeline som skickar ett e-postmeddelande när en app som inte matchar app-ID för webbappen hämtar hemligheter från valvet.

Du måste först aktiverar loggning på ditt nyckelvalv. Använd följande PowerShell-kommandon. (Du kan se alla detaljer i [i den här artikeln om key-vault-loggning](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

När loggning är aktiverat, starta granskningsloggar som lagras i det angivna lagringskontot. Dessa loggar innehålla händelser om hur och när nyckelvalven används och av vem.

> [!NOTE]
> Du kan komma åt loggningsinformationen 10 minuter efter nyckelvalv igen. Den blir ofta tillgänglig tidigare än så.

Nästa steg är att [skapa en Azure Service Bus-kö](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Den här kön är där key vault-granskningsloggar skickas. När granskningsloggen meddelanden finns i kön, hämtas de av logikappen och agerar på dem. Skapa en Service Bus-instans med följande steg:

1. Skapa ett Service Bus-namnområde (om du redan har en som du vill använda, gå vidare till steg 2).
2. Bläddra till Service Bus-instans i Azure-portalen och välj det namnområde som du vill skapa kön i.
3. Välj **skapa en resurs** > **Företagsintegrering** > **Service Bus**, och sedan ange informationen som krävs.
4. Hitta information för Service Bus-anslutning genom att markera namnområdet och sedan välja **anslutningsinformation**. Du behöver den här informationen för nästa avsnitt.

Nästa [skapa en Azure-funktion](../azure-functions/functions-create-first-azure-function.md) att avsöka nyckelvalvsloggarna i storage-konto och hämta nya händelser. Den här funktionen aktiveras enligt ett schema.

Om du vill skapa en funktionsapp i Azure, Välj **skapa en resurs**, Sök på marketplace efter **Funktionsapp**, och välj sedan **skapa**. Du kan använda en befintlig värd plan eller skapa en ny när du skapar. Du kan också välja dynamisk som är värd för. Mer information om värdalternativ för Azure Functions finns i [så här skalar du Azure Functions](../azure-functions/functions-scale.md).

När Azure function-app har skapats går du till den och väljer den **Timer** scenario och **C\#**  för språket. Välj sedan **skapa den här funktionen**.

![Azure Functions starta bladet](./media/keyvault-keyrotation/Azure_Functions_Start.png)

På den **utveckla** fliken, Byt ut koden run.csx med följande:

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
> Ändra variablerna i den föregående koden så att den pekar till lagringskontot där nyckelvalvsloggarna skrivs till Service Bus-instans som du skapade tidigare och att sökvägen till lagring för key vault-loggarna.

Funktionen hämtar senaste loggfilen från lagringskontot där nyckelvalvsloggarna skrivs, hämtar de senaste händelserna från filen och skickar dem till en Service Bus-kö. 

Eftersom en enskild fil kan ha flera händelser, skapar du en sync.txt-fil som funktionen även hänsyn till att fastställa tidsstämpeln för den senaste händelse som hämtades. Med den här filen säkerställer att du inte skicka samma händelse flera gånger. 

Sync.txt-filen innehåller en tidsstämpel för senaste påträffade händelsen. När loggarna har lästs in kan måste de sorteras utifrån deras tidsstämplar så att de är korrekt beställt.

För den här funktionen refererar vi till några ytterligare bibliotek som inte är tillgängliga direkt i Azure Functions. För att inkludera dessa bibliotek, måste vi Azure Functions för att hämta dem med hjälp av NuGet. Under den **kod** väljer **visa filer**.

![Alternativet ”Visa filer”](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Lägg till en fil med namnet project.json med följande innehåll:

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

När du har valt **spara**, Azure Functions hämtar nödvändiga binärfiler.

Växla till den **integrera** fliken och ge parametern timer ett beskrivande namn att användas i funktionen. I den föregående koden förväntar sig att timern ska anropas *myTimer*. Ange en [CRON-uttryck](../app-service/webjobs-create.md#CreateScheduledCRON) för timern på följande sätt: `0 * * * * *`. Det här uttrycket gör att funktionen ska köras en gång i minuten.

På samma **integrera** fliken, Lägg till indata av typen **Azure Blob storage**. Denna indata pekar till den sync.txt-fil som innehåller tidsstämpeln för den senaste händelsen har tittat på av funktionen. Denna indata ska användas i funktionen genom att använda parameternamnet. Azure Blob storage-indata i den föregående koden förväntar sig parameternamn måste vara *inputBlob*. Välj det lagringskonto där filen sync.txt kommer att finnas (det kan vara samma eller ett annat lagringskonto). I sökvägsfältet, ange sökvägen till filen i formatet `{container-name}/path/to/sync.txt`.

Lägg till utdata av typen **Azure Blob storage**. Dessa utdata ska peka på sync.txt-filen som du definierade i indata. Dessa utdata används av funktionen för att skriva tidsstämpeln för den senaste händelsen har tittat på. Den föregående koden förväntar sig den här parametern anropas *outputBlob*.

Funktionen är nu klar. Se till att växla tillbaka till den **utveckla** fliken och spara koden. Kontrollera utdatafönstret för alla kompileringsfel och försök igen. Om koden kompilerar ska sedan koden nu kontrollerar nyckelvalvsloggarna varje minut och skicka alla nya händelser till definierade Service Bus-kön. Du bör se loggningsinformation skriva till loggfönstret varje gång som funktionen utlöses.

### <a name="azure-logic-app"></a>Azure logic app

Därefter skapar du en Azure logic app som tar upp händelser att funktionen är push-överföring till Service Bus-kö, tolkar innehållet och skickar ett e-postmeddelande baserat på ett villkor som matchas.

[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) genom att välja **skapa en resurs** > **integrering** > **Logikapp**.

När logikappen har skapats går du till den och väljer **redigera**. I redigeraren logic app väljer **Service Bus-kö** och ange dina autentiseringsuppgifter för Service Bus för att ansluta till kön.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Välj **Lägg till ett villkor**. Växla till Avancerad redigerare och ange följande kod i villkoret. Ersätt *APP_ID* med faktiska app-ID för din webbapp:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Det här uttrycket returnerar i stort sett **FALSKT** om den *appid* från den inkommande händelsen (som är brödtexten i Service Bus-meddelande) är inte den *appid* av appen.

Skapa nu en åtgärd under **om Nej, gör INGENTING**.

![Med Azure Logic Apps Välj åtgärd](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

För åtgärden, väljer **Office 365 – skicka e-post**. Fyll i fälten för att skapa ett e-postmeddelande ska skickas när definierade villkoret returnerar **FALSKT**. Om du inte har Office 365, leta efter alternativ att uppnå samma resultat.

Nu har du en pipeline för slutpunkt till slutpunkt som söker efter nya key vault-granskningsloggar en gång i minuten. Nya loggar som påträffas skickar den till en Service Bus-kö. Logikappen utlöses när ett nytt meddelande hamnar i kön. Om den *appid* i händelsen matchar inte app-ID för det anropande programmet, skickas ett e-postmeddelande.
