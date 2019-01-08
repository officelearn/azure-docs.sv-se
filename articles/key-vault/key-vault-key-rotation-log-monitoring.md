---
title: Konfigurera Azure Key Vault med slutpunkt till slutpunkt nyckelrotation och granskning – Azure Key Vault | Microsoft Docs
description: Använd den här anvisningen för att hjälpa dig att börja arbeta med nyckelgranskning och -övervakning key vault-loggar.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 4dbfd993a8464c569d30f11e305d4bae000a778f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077716"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Konfigurera Azure Key Vault med nyckelgranskning och -

## <a name="introduction"></a>Introduktion

När du har ett nyckelvalv kan börja du använda det för att lagra nycklar och hemligheter. Dina program behöver inte längre att bevara dina nycklar eller hemligheter, men kan begära dem från valvet efter behov. På så sätt kan du uppdatera nycklar och hemligheter utan att påverka beteendet för ditt program, vilket öppnar en bred möjligheter kring din nyckel och hemlighetshantering.

>[!IMPORTANT]
> Exemplen i den här artikeln tillhandahålls enbart i illustrationssyfte. De är inte avsedda för användning i produktion. 

Den här artikeln beskriver:

- Ett exempel på hur du använder Azure Key Vault för att lagra en hemlighet. Den hemlighet som lagras är nyckeln för Azure Storage-konto som används av ett program i den här självstudien. 
- Här visas också implementering av en schemalagd rotation för den lagringskontonyckeln.
- Den visar hur du övervakar granskningsloggar för nyckelvalvet och skapar varningar när oväntade begäranden som görs.

> [!NOTE]
> Den här självstudien är inte avsedd att förklarar i detalj installationen av ditt nyckelvalv. Den här informationen finns i [Komma igång med Azure Key Vault](key-vault-get-started.md). Anvisningar för plattformsoberoende kommandoradsgränssnitt finns i [hantera Key Vault med CLI](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Konfigurera Key Vault

Om du vill aktivera ett program att hämta en hemlighet från Key Vault måste du först skapa hemligheten och överföra den till valvet. Detta kan åstadkommas genom att starta en Azure PowerShell-session och logga in på ditt Azure-konto med följande kommando:

```powershell
Connect-AzureRmAccount
```

Ange användarnamnet och lösenordet för ditt Azure-konto i popup-fönstret i webbläsaren. PowerShell får alla prenumerationer som är associerade med det här kontot. PowerShell använder den första som standard.

Om du har flera prenumerationer kan behöva du ange det som användes för att skapa ett nyckelvalv. Ange följande för att visa prenumerationerna för ditt konto:

```powershell
Get-AzureRmSubscription
```

Ange om du vill ange den prenumeration som är associerad med nyckelvalvet som du ska logga:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Eftersom den här artikeln visar att lagra en lagringskontonyckel som en hemlighet, måste du hämta den lagringskontonyckeln.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

När du hämtar din hemlighet (i det här fallet din lagringskontonyckel), måste du konvertera som till en säker sträng och sedan skapa en hemlighet med värdet i ditt nyckelvalv.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Hämta sedan URI: N för den hemlighet som du skapade. Detta används i ett senare steg när du anropar nyckelvalvet för att hämta din hemlighet. Kör följande PowerShell-kommando och anteckna ID-värde, som är den hemliga URI:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurera programmet

Du kan använda kod för att hämta och använda den nu när du har en hemlighet som lagras. Det finns några få steg som krävs för att uppnå detta. Det första och viktigaste steget registrera ditt program med Azure Active Directory och sedan om Key Vault information om ditt program så att den tillåter förfrågningar från ditt program.

> [!NOTE]
> Programmet måste skapas på samma Azure Active Directory-klient som ditt nyckelvalv.
>
>

1. Gå till Azure Active Directory.
2. Välj **appregistreringar** 
3. Välj **ny programregistrering** att lägga till ett program till Azure Active Directory.

    ![Öppna program i Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. I den **skapa** avsnittet lämna programtyp som **WEBBAPP och/eller webb-API** och namnge ditt program. Ge ditt program en **inloggnings-URL**. Detta kan vara vad som helst som du vill använda för den här demon.

    ![Skapa programregistrering](./media/keyvault-keyrotation/create-app.png)

5. När programmet har lagts till Azure Active Directory, kommer du till appen på sidan. Välj **inställningar** och välj sedan Egenskaper. Kopiera den **program-ID** värde. Det krävs i senare steg.

Nu ska du generera en nyckel för ditt program så att den kan interagera med Azure Active Directory. Du kan skapa en nyckel under genom att navigera till den **nycklar** avsnittet **inställningar**. Anteckna den nyligen skapade nyckeln från ditt Azure Active Directory-program för användning i ett senare steg. Observera att nyckeln inte är tillgängliga när du navigerar utanför det här avsnittet. 

![Azure Active Directory-Appnycklar](./media/keyvault-keyrotation/create-key.png)

Innan du upprättar alla anrop från ditt program till nyckelvalvet måste du se nyckelvalvet om ditt program och dess behörigheter. Följande kommando tar valvnamnet och program-ID från din app för Azure Active Directory och ger **hämta** åtkomst till nyckelvalvet för programmet.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Nu är du redo att börja bygga dina program-anrop. I ditt program måste du installera NuGet-paket som krävs för att interagera med Azure Key Vault och Azure Active Directory. Ange följande kommandon från Visual Studio Package Manager-konsolen. Vid skrivning av den här artikeln är den aktuella versionen av Azure Active Directory-klientpaketet 3.10.305231913, så du kanske vill kontrollera den senaste versionen och uppdateras.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

Skapa en klass för att lagra metoden för din Azure Active Directory-autentisering i din programkod. I det här exemplet kallas den klassen **Utils**. Lägg till följande med instruktionen:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Lägg sedan till följande metod för att hämta JWT-token från Azure Active Directory. För underhåll, kan du flytta hårdkodade strängvärden till din webb- eller konfiguration.

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

Lägga till nödvändig kod för att anropa Key Vault och hämta din hemligt värde. Först måste du lägga till följande med instruktionen:

```csharp
using Microsoft.Azure.KeyVault;
```

Lägg till metodanrop att anropa Key Vault och hämta din hemlighet. I den här metoden kan ange du hemlig-URI som du sparade i föregående steg. Observera användningen av den **GetToken** metod från den **Utils** klassen som skapade tidigare.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

När du kör ditt program bör du nu att autentisera till Azure Active Directory och hämta sedan din hemligt värde från Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotation av med Azure Automation

Det finns olika alternativ för att implementera en strategi för rotation för värden som du lagrar som Azure Key Vault-hemligheter. Hemligheter kan roteras som en del av en manuell process, de kan roteras programmässigt med hjälp av API-anrop eller de kan roteras därtill ett automationsskript. För den här artikeln, kommer du att använda Azure PowerShell i kombination med Azure Automation för att ändra en åtkomstnyckel för Azure Storage-konto. Sedan uppdaterar du en hemlighet i nyckelvalvet med den nya nyckeln.

Om du vill tillåta Azure Automation till att ange hemliga värden i ditt nyckelvalv måste du hämta klient-ID för anslutningen med namnet AzureRunAsConnection som skapades när du har skapat din instans av Azure Automation. Du hittar detta ID genom att välja **tillgångar** från Azure Automation-instans. Därifrån kan du välja **anslutningar** och välj sedan den **AzureRunAsConnection** tjänsten principen. Anteckna den **program-ID**.

![Azure Automation-klient-ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

I **tillgångar**, Välj **moduler**. Från **moduler**väljer **galleriet**, och söker sedan efter och **Import** uppdaterade versioner av vart och ett av följande moduler:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Vid skrivning av den här artikeln behövs bara modulerna som vi nämnde tidigare som ska uppdateras i följande skript. Om du upptäcker att ditt automation-jobb som misslyckas, kontrollerar du att du har importerat alla nödvändiga moduler och deras beroenden.
>
>

När du har hämtat det program-ID för Azure Automation-anslutningen kan se du ditt nyckelvalv för att att det här programmet har åtkomst till uppdatera hemligheter i valvet. Detta kan åstadkommas med följande PowerShell-kommando:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Välj sedan **Runbooks** under ditt Azure Automation-instans och välj sedan **Lägg till en Runbook**. Välj **Snabbregistrering**. Namnge din runbook och välj **PowerShell** som runbooktyp av. Du har möjlighet att lägga till en beskrivning. Klicka slutligen på **skapa**.

![Skapa runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Klistra in följande PowerShell-skript i redigerarfönstret för din nya runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
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

Editor-fönstret Välj **Testfönster** att testa ditt skript. När skriptet körs utan fel kan du välja **publicera**, och du kan koppla ett schema för runbook tillbaka i fönstret konfiguration för runbook.

## <a name="key-vault-auditing-pipeline"></a>Key Vault granskning pipeline
När du konfigurerar ett nyckelvalv, kan du aktivera granskning för att samla in loggar vid åtkomstbegäranden som görs till nyckelvalvet. Dessa loggar lagras i en angiven Azure Storage-konto och kan hämtas, övervakas och analyseras. Följande scenario använder Azure functions, Azure logic apps och granskningsloggar för nyckelvalv för att skapa en pipeline för att skicka ett e-postmeddelande när en app som stämmer överens med app-ID för webbappen hämtar hemligheter från valvet.

Du måste först aktiverar loggning på ditt nyckelvalv. Detta kan göras via följande PowerShell-kommandon (fullständig information kan ses i [key-vault-loggning](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

När den är aktiverad, granskningsloggar börja samla in till det angivna lagringskontot. Dessa loggar innehålla händelser om hur och när nyckelvalven används och av vem.

> [!NOTE]
> Du kan komma åt loggningsinformationen 10 minuter efter nyckelvalv igen. Vanligtvis är snabbare än så.
>
>

Nästa steg är att [skapa en Azure Service Bus-kö](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Det här är där nyckelvalv granskningsloggar skickas. När granskningsloggmeddelanden i kön logikappen hämtar dem och fungerar på dem. Skapa ett service bus med följande steg:

1. Skapa ett Service Bus-namnområde (om du redan har en som du vill använda för den här finns, hoppa till steg 2).
2. Bläddra till en service bus i Azure-portalen och välj det namnområde som du vill skapa kön i.
3. Välj **skapa en resurs**, **Företagsintegrering**, **Service Bus**, och sedan ange informationen som krävs.
4. Välj anslutningsinformation för Service Bus genom att välja namnområdet och klicka på **anslutningsinformation**. Du behöver den här informationen för nästa avsnitt.

Nästa [skapa en Azure-funktion](../azure-functions/functions-create-first-azure-function.md) att avsöka key vault-loggar i storage-konto och hämta nya händelser. Det här är en funktion som utlöses enligt ett schema.

Om du vill skapa en Azure-funktion, Välj **skapa en resurs**, Sök på marketplace efter _Funktionsapp_, och klicka på **skapa**. Du kan använda en befintlig värd plan eller skapa en ny när du skapar. Du kan också välja dynamisk som är värd för. Mer information om funktionen som är värd för alternativ finns på [så här skalar du Azure Functions](../azure-functions/functions-scale.md).

När Azure-funktion skapas, navigera till den och välj en timer-funktion och C\#. Klicka sedan på **skapa den här funktionen**.

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
> Se till att ersätta variabler i den föregående koden så att den pekar till lagringskontot där nyckelvalvsloggarna skrivs, service bus som du skapade tidigare och sökvägen till storage-loggarna för nyckelvalvet.
>
>

Funktionen hämtar senaste loggfilen från lagringskontot där nyckelvalvsloggarna skrivs, hämtar de senaste händelserna från filen och skickar dem till en Service Bus-kö. Eftersom en enskild fil kan ha flera händelser, skapar du en sync.txt-fil som funktionen även hänsyn till att fastställa tidsstämpeln för den senaste händelse som hämtades. Detta säkerställer att du inte skicka samma händelse flera gånger. Den här sync.txt-filen innehåller en tidsstämpel för senaste påträffade händelsen. Loggarna, när du har lästs in, måste sorteras utifrån tidsstämpel så de sorteras korrekt.

För den här funktionen refererar vi till några ytterligare bibliotek som inte är tillgängliga direkt i Azure Functions. För att inkludera dessa, behöver vi Azure Functions för att hämta dem med hjälp av NuGet. Välj den **visa filer** alternativet.

![Visa filer](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Och Lägg till en fil med namnet project.json med följande innehåll:

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

Vid **spara**, Azure Functions hämtar nödvändiga binärfiler.

Växla till den **integrera** fliken och ge parametern timer ett beskrivande namn att användas i funktionen. I den föregående koden det förväntar sig att timern ska anropas *myTimer*. Ange en [CRON-uttryck](../app-service/webjobs-create.md#CreateScheduledCRON) på följande sätt: 0 \* \* \* \* \* för timern som gör att funktionen ska köras en gång i minuten.

På samma **integrera** fliken, Lägg till indata av typen **Azure Blob Storage**. Detta att peka sync.txt-fil med tidsstämpel för senaste händelsen har tittat på av funktionen. Det här är tillgängliga i funktionen av parameternamnet. Azure Blob Storage-indata i den föregående koden förväntar sig parameternamn måste vara *inputBlob*. Välj det lagringskonto där sync.txt-filen ska placeras (det kan vara samma eller ett annat lagringskonto). I sökvägsfältet, ange sökvägen där filen finns i formatet {container-name}/path/to/sync.txt.

Lägg till utdata av typen *Azure Blob Storage* utdata. Detta ska peka på sync.txt-filen som du definierade i indata. Detta används av funktionen för att skriva tidsstämpel för senaste händelsen har tittat på. Den föregående koden förväntar sig den här parametern anropas *outputBlob*.

Funktionen är nu klar. Se till att växla tillbaka till den **utveckla** fliken och spara koden. Kontrollera utdatafönstret för alla kompileringsfel och korrigera dem enlighet med detta. Om koden kompilerar ska sedan koden nu kontrollerar nyckelvalvsloggarna varje minut och push-överföra några nya händelser till definierade Service Bus-kön. Du bör se loggningsinformation skriva till loggfönstret varje gång som funktionen utlöses.

### <a name="azure-logic-app"></a>Azure logic app

Därefter måste du skapa en Azure logic app som tar upp händelser att funktionen är push-överföring till Service Bus-kö, tolkar innehållet och skickar ett e-postmeddelande baserat på ett villkor som matchas.

[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) genom att gå till **New > Logikapp**.

När logikappen har skapats och gå till den och välj **redigera**. Inom logic app-redigerare, välja **Service Bus-kö** och ange dina autentiseringsuppgifter för Service Bus för att ansluta till kön.

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Välj sedan **Lägg till ett villkor**. Växla till Avancerad redigerare i villkoret och ange följande kod, ersätta APP_ID med den faktiska APP_ID på din webbapp:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Det här uttrycket returnerar i stort sett **FALSKT** om den *appid* från den inkommande händelsen (som är brödtexten i Service Bus-meddelande) är inte den *appid* av appen.

Skapa nu en åtgärd under **om Nej, gör ingenting**.

![Välj åtgärd för Azure Logic App](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

För åtgärden, väljer **Office 365 – skicka e-post**. Fyll i fälten för att skapa ett e-postmeddelande ska skickas när definierade villkoret returnerar **FALSKT**. Om du inte har Office 365 kan du titta på alternativ för att uppnå samma resultat.

Nu har du en heltäckande pipeline som söker efter nya nyckelvalv granskningsloggar en gång i minuten. Nya loggar som påträffas skickar den till en service bus-kö. Logikappen utlöses när ett nytt meddelande hamnar i kön. Om den *appid* i händelsen matchar inte app-ID för det anropande programmet, skickas ett e-postmeddelande.
