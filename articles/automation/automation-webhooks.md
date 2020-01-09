---
title: Starta en Azure Automation Runbook med en webhook
description: En webhook som gör att en klient kan starta en Runbook i Azure Automation från ett HTTP-anrop.  Den här artikeln beskriver hur du skapar en webhook och hur du anropar en för att starta en Runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: fbf3a48d1e7cb3dd80b6c418d7c916184756b6fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418977"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Starta en Azure Automation Runbook med en webhook

Med en *webhook* kan du starta en viss runbook i Azure Automation via en enda http-begäran. Detta gör att externa tjänster som Azure DevOps Services, GitHub, Azure Monitor loggar eller anpassade program kan starta Runbooks utan att implementera en fullständig lösning med hjälp av Azure Automation API.
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Du kan jämföra Webhooks med andra metoder för att starta en runbook när du [startar en Runbook i Azure Automation](automation-starting-a-runbook.md)

> [!NOTE]
> Det finns inte stöd för att använda en webhook för att starta en python-Runbook.

## <a name="details-of-a-webhook"></a>Information om en webhook

I följande tabell beskrivs de egenskaper som du måste konfigurera för en webhook.

| Egenskap | Beskrivning |
|:--- |:--- |
| Namn |Du kan ange vilket namn du vill för en webhook eftersom det inte visas för klienten. Den används bara för att identifiera runbooken i Azure Automation. <br> Vi rekommenderar att du ger webhooken ett namn som är relaterat till klienten som använder den. |
| URL |URL: en för webhook är den unika adress som en klient anropar med ett HTTP-inlägg för att starta den Runbook som är länkad till webhooken. Den skapas automatiskt när du skapar webhooken. Du kan inte ange en anpassad URL. <br> <br> URL: en innehåller en säkerhetstoken som gör att runbooken kan anropas av ett system från tredje part utan ytterligare autentisering. Av den anledningen bör det behandlas som ett lösen ord. Av säkerhets skäl kan du bara Visa webb adressen i Azure Portal när webhooken skapas. Notera URL: en på en säker plats för framtida användning. |
| Förfallodatum |Precis som ett certifikat har varje webhook ett förfallo datum då den inte längre kan användas. Detta förfallo datum kan ändras efter att webhooken har skapats så länge webhooken inte har upphört att gälla. |
| Enabled |En webhook är aktive rad som standard när den skapas. Om du ställer in den på inaktive rad kan ingen klient använda den. Du kan ställa in egenskapen **Enabled** när du skapar webhooken eller när den skapas. |

### <a name="parameters"></a>Parametrar

En webhook kan definiera värden för Runbook-parametrar som används när Runbook startas av webhooken. Webhooken måste innehålla värden för alla obligatoriska parametrar för runbooken och kan innehålla värden för valfria parametrar. Ett parameter värde som har kon figurer ATS för en webhook kan ändras även när webhooken har skapats. Flera webhook-länkar till en enda Runbook kan var och en använda olika parameter värden.

När en klient startar en Runbook med en webhook kan den inte åsidosätta de parameter värden som definierats i webhooken. För att ta emot data från klienten kan runbooken acceptera en enda parameter med namnet **$WebhookData**. Den här parametern är av en typ [Object] som innehåller data som klienten inkluderar i POST-begäran.

![Egenskaper för Webhookdata](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** -objektet har följande egenskaper:

| Egenskap | Beskrivning |
|:--- |:--- |
| WebhookName |Namnet på webhooken. |
| RequestHeader |Hash-tabell som innehåller rubrikerna för inkommande POST-begäran. |
| RequestBody |Bröd texten i begäran om inkommande POST. Detta behåller all formatering, till exempel sträng-, JSON-, XML-eller form-kodade data. Runbooken måste skrivas för att fungera med det data format som förväntas. |

Det krävs ingen konfiguration av webhook för att stödja **$WebhookData** -parametern och det krävs ingen Runbook för att acceptera den. Om runbooken inte definierar parametern, ignoreras all information om begäran som skickas från klienten.

> [!NOTE]
> När du anropar en webhook bör du alltid lagra alla parameter värden om anropet Miss lyckas. Om det uppstår ett nätverks avbrott eller anslutnings problem kan du inte hämta misslyckade webhook-anrop.

Om du anger ett värde för $WebhookData när du skapar webhooken, åsidosätts värdet när webhooken startar runbooken med data från klient POST förfrågan, även om klienten inte innehåller några data i begär ande texten. Om du startar en Runbook som har $WebhookData med en annan metod än en webhook kan du ange ett värde för $Webhookdata som identifieras av runbooken. Värdet ska vara ett objekt med samma [Egenskaper](#details-of-a-webhook) som $Webhookdata så att runbooken fungerar korrekt med det som om det fungerade med faktiska Webhookdata som skickats av en webhook.

Om du till exempel startar följande Runbook från Azure Portal och vill skicka några exempel-WebhookData för testning, eftersom WebhookData är ett objekt, ska det skickas som JSON i användar gränssnittet.

![WebhookData-parameter från UI](media/automation-webhooks/WebhookData-parameter-from-UI.png)

För följande Runbook, om du har följande egenskaper för parametern WebhookData:

* WebhookName: *MyWebhook*
* RequestBody: *[{' ResourceGroup ': ' myResourceGroup ', ' name ': ' VM01 '}, {' ResourceGroup ': ' myResourceGroup ', ' namn ': ' VM02 '}]*

Sedan skickar du följande JSON-värde i användar gränssnittet för parametern WebhookData. Följande exempel med tecknen vagn returer och rad matningar matchar formatet som skickas från en webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Starta WebhookData-parametern från användar gränssnittet](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Värdena för alla indataparametrar loggas med Runbook-jobbet. Det innebär att alla indata som tillhandahålls av klienten i webhook-begäran loggas och är tillgängliga för alla som har åtkomst till Automation-jobbet.  Av den anledningen bör du vara försiktig med att inkludera känslig information i webhook-anrop.

## <a name="security"></a>Säkerhet

Säkerheten för en webhook är beroende av sekretessen för dess URL, som innehåller en säkerhetstoken som gör att den kan anropas. Azure Automation utför ingen autentisering på begäran så länge den har gjorts till rätt URL. Därför bör Webhooks inte användas för Runbooks som utför mycket känsliga funktioner utan att använda alternativa metoder för att verifiera begäran.

Du kan inkludera logik i runbooken för att fastställa att den anropades av en webhook genom att kontrol lera egenskapen **WebhookName** för parametern $WebhookData. Runbooken kan utföra ytterligare verifiering genom att söka efter viss information i egenskaperna **RequestHeader** eller **RequestBody** .

En annan strategi är att låta Runbook utföra en del validering av ett externt villkor när den tar emot en webhook-begäran. Anta till exempel en Runbook som anropas av GitHub när det finns ett nytt genomförande till en GitHub-lagringsplats. Runbooken kan ansluta till GitHub för att verifiera att en ny incheckning har skett innan du fortsätter.

## <a name="creating-a-webhook"></a>Skapa en webhook

Använd följande procedur för att skapa en ny webhook som är länkad till en Runbook i Azure Portal.

1. På **sidan Runbooks** i Azure Portal klickar du på den Runbook som webhooken börjar visa sin informations sida. Se till att Runbook- **statusen** har **publicerats**.
2. Klicka på **webhook** överst på sidan för att öppna sidan **Lägg till webhook** .
3. Klicka på **Skapa ny webhook** för att öppna **sidan Skapa webhook**.
4. Ange ett **namn**, **utgångs datum** för webhooken och om det ska aktive ras. Se [information om en webhook](#details-of-a-webhook) för mer information om dessa egenskaper.
5. Klicka på Kopiera-ikonen och tryck på CTRL + C för att kopiera URL: en för webhooken. Registrera den på ett säkert ställe. **När du skapar webhooken kan du inte hämta URL: en igen.**

   ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)

1. Klicka på **parametrar** för att ange värden för Runbook-parametrarna. Om runbooken har obligatoriska parametrar kan du inte skapa webhooken utan att ange värdena.
1. Skapa webhooken genom att klicka på **Skapa**.

## <a name="using-a-webhook"></a>Använda en webhook

Om du vill använda en webhook när den har skapats måste klient programmet utfärda ett HTTP-inlägg med URL: en för webhooken. Webhookens syntax är i följande format:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klienten får en av följande retur koder från POST-begäran.

| Programmera | Text | Beskrivning |
|:--- |:--- |:--- |
| 202 |Godkänd |Begäran accepterades och Runbook har placerats i kö. |
| 400 |Felaktig begäran |Begäran godtogs inte av någon av följande orsaker: <ul> <li>Webhooken har upphört att gälla.</li> <li>Webhooken är inaktive rad.</li> <li>Token i URL: en är ogiltig.</li>  </ul> |
| 404 |Hittades inte |Begäran godtogs inte av någon av följande orsaker: <ul> <li>Det gick inte att hitta webhooken.</li> <li>Det gick inte att hitta Runbook-flödet.</li> <li>Det gick inte att hitta kontot.</li>  </ul> |
| 500 |Internt Server fel |URL: en var giltig, men ett fel uppstod. Skicka begäran igen. |

Förutsatt att begäran lyckas innehåller webhook-svaret jobb-ID i JSON-format på följande sätt. Det innehåller ett enda jobb-ID, men JSON-formatet möjliggör framtida framtida förbättringar.

```json
{"JobIds":["<JobId>"]}
```

Klienten kan inte avgöra när Runbook-jobbet har slutförts eller om dess slut för ande status från webhooken är slutförd. Den kan avgöra denna information med jobb-ID med en annan metod, till exempel [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) eller [Azure Automation-API: et](/rest/api/automation/job).

## <a name="renew-webhook"></a>Förnya en webhook

När en webhook skapas har den en giltighets tid på ett år. Efter det året förfaller webhooken automatiskt. När en webhook har gått ut kan den inte återaktiveras. den måste tas bort och återskapas. Om en webhook inte har nått sin förfallo tid kan den utökas.

Om du vill utöka en webhook navigerar du till den Runbook som innehåller webhooken. Välj **Webhooks** under **resurser**. Klicka på webhooken som du vill utöka. den här åtgärden öppnar **webhook** -sidan.  Välj ett nytt förfallo datum och tid och klicka på **Spara**.

## <a name="sample-runbook"></a>Exempel-Runbook

Följande exempel-Runbook accepterar webhook-data och startar de virtuella datorer som anges i begär ande texten. Testa denna Runbook genom att klicka på **+ Lägg till en Runbook**i Automation-kontot under **Runbooks**. Om du inte vet hur du skapar en Runbook, se [skapa en Runbook](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Testa exemplet

I följande exempel används Windows PowerShell för att starta en Runbook med en webhook. Alla språk som kan göra en HTTP-begäran kan använda en webhook; Windows PowerShell används här som ett exempel.

Runbooken förväntar sig en lista över virtuella datorer som är formaterade i JSON i bröd texten i begäran. Runbooken validerar även att huvuden innehåller ett definierat meddelande för att verifiera att webhook-anroparen är giltig.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

I följande exempel visas bröd texten i begäran som är tillgänglig för runbooken i egenskapen **RequestBody** för **WebhookData**. Värdet formateras som JSON eftersom det var det format som ingick i bröd texten i begäran.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Följande bild visar den begäran som skickas från Windows PowerShell och det resulterande svaret. Jobb-ID: t extraheras från svaret och konverteras till en sträng.

![Webhooks-knapp](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du använder Azure Automation för att vidta åtgärder på Azure-aviseringar finns i [använda en avisering för att utlösa en Azure Automation Runbook](automation-create-alert-triggered-runbook.md).

