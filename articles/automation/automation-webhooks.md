---
title: Börja använda en Azure Automation-runbook med en webhook
description: En webhook som gör att en klient för att starta en runbook i Azure Automation från ett HTTP-anrop.  Den här artikeln beskrivs hur du skapar en webhook och hur du anropar en om du vill starta en runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3da4ecb1193959fcc8782f8aa5fdf32c130ee238
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840154"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Börja använda en Azure Automation-runbook med en webhook

En *webhook* gör att du kan starta en viss runbook i Azure Automation via en HTTP-begäran. Detta gör att externa tjänster som Azure DevOps Services, GitHub, Azure Log Analytics eller anpassade program att starta runbooks utan att implementera en komplett lösning med hjälp av Azure Automation-API.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Du kan jämföra webhooks för att andra metoder för att starta en runbook [starta en runbook i Azure Automation](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Information om en webhook

I följande tabell beskrivs de egenskaper som du måste konfigurera för en webhook.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Namn |Du kan ange ett namn som du vill använda för en webhook eftersom detta inte är exponerad klienten. Den används endast för dig för att identifiera runbook i Azure Automation. <br> Som bästa praxis bör du ge webhooken ett namn som är relaterade till den klient som använder den. |
| URL |URL: en för webhooken är den unika adress som en klient anropar med en HTTP POST att starta runbooken länkad till webhooken. Det genereras automatiskt när du skapar webhooken. Du kan inte ange en anpassad URL. <br> <br> URL: en innehåller en säkerhetstoken som gör att runbook anropas av en tredjeparts-system utan ytterligare autentisering. Den ska därför behandlas som ett lösenord. Av säkerhetsskäl kan du bara se URL: en i Azure-portalen när webhooken skapas. Observera att URL: en på en säker plats för framtida användning. |
| Upphörandedatum |Varje webhook om du har ett förfallodatum vid vilken tidpunkt den kan inte längre användas som ett certifikat. Den här förfallodatum kan ändras när webhooken skapas. |
| Enabled |En webhook aktiveras som standard när den skapas. Om du ställer in det på inaktiverad är ingen klient kan använda den. Du kan ange den **aktiverad** egenskapen när du skapar webhooken eller när som helst när den har skapats. |

### <a name="parameters"></a>Parametrar

En webhook kan definiera värden för runbookparametrar som används när runbook startas av den webhooken. Webhooken måste innehålla värden för alla obligatoriska parametrar för runbook och kan innehålla värden för valfria parametrar. Ett parametervärde som konfigurerats för att en webhook kan ändras även när du har skapat webhooken. Flera webhooks som är länkad till en enda runbook kan använda olika parametervärden.

När en klient startar en runbook med en webhook, kan inte det åsidosätta de parametervärden som definierats i webhooken. Om du vill ta emot data från klienten, runbook kan acceptera en enda parameter med namnet **$WebhookData** av typen [objekt] som innehåller data som klienten lägger till i POST-begäran.

![Webhookdata-egenskaper](media/automation-webhooks/webhook-data-properties.png)

Den **$WebhookData** objekt har följande egenskaper:

| Egenskap  | Beskrivning |
|:--- |:--- |
| WebhookName |Namnet på webhooken. |
| RequestHeader |Hash-tabell som innehåller huvuden inkommande POST-begäran. |
| RequestBody |Innehållet i den inkommande POST-begäran. Detta bevarar all formatering, till exempel sträng, JSON, XML, eller formuläret kodade data. Runbooken måste vara skrivna för att arbeta med dataformat som förväntas. |

Ingen konfiguration krävs för att stödja webhookens den **$WebhookData** parameter och en runbook inte krävs för att acceptera den. Om runbook inte definierar parametern ignoreras någon information om begäran som skickats från klienten.

Om du anger ett värde för $WebhookData när du skapar webhooken, att värde åsidosätts när webhooken startar runbook med data från klienten POST-begäran, även om klienten inte innehåller några data i begärandetexten. Om du startar en runbook som har $WebhookData med hjälp av en annan metod än en webhook, kan du ange ett värde för $Webhookdata som känns igen av runbook. Det här värdet ska vara ett objekt med samma [egenskaper](#details-of-a-webhook) som $Webhookdata så att runbooken fungerar korrekt med det som om den fungerade med faktiska WebhookData som skickas av en webhook.

Till exempel om du startar följande runbook från Azure-portalen och vill skicka vissa exempel WebhookData för testning, eftersom WebhookData är ett objekt som ska den skickas som JSON i Användargränssnittet.

![WebhookData-parametern från Användargränssnittet](media/automation-webhooks/WebhookData-parameter-from-UI.png)

På följande runbook, om du har följande egenskaper för WebhookData-parametern:

* WebhookName: *MyWebhook*
* RequestBody: *[{”ResourceGroup”: ”myResourceGroup”, ”Name”: ”vm01”}, {”ResourceGroup”: ”myResourceGroup”, ”Name”: ”vm02”}]*

Sedan skickar du följande JSON-värde i Användargränssnittet för WebhookData-parametern. I följande exempel med transport returneras och radmatningstecken matchar formatet som skickas från en webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Starta WebhookData-parametern från Användargränssnittet](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Värdena för alla indataparametrar loggas med runbook-jobbet. Detta innebär att alla indata av klienten i webhook-begäran kommer loggas som är tillgänglig för alla med åtkomst till automation-jobb.  Därför bör du vara försiktig inklusive känslig information i webhook-anrop.

## <a name="security"></a>Säkerhet

Säkerheten för en webhook som förlitar sig på sekretessen för dess URL som innehåller en säkerhetstoken som gör att det kan anropas. Azure Automation utför inte någon autentisering på begäran så länge som det görs till rätt URL. Därför bör webhooks inte användas för runbooks som utför mycket känsliga funktioner utan att använda ett annat sätt att verifiera begäran.

Du kan inkludera logik i din runbook för att fastställa att anropet av en webhook genom att markera den **WebhookName** egenskapen för parametern $WebhookData. Runbook kan utföra ytterligare verifiering genom att söka efter specifika informationen i den **RequestHeader** eller **RequestBody** egenskaper.

En annan strategi är att ha en runbook som utför vissa verifiering av en extern villkor när en webhook-begäran togs emot. Anta exempelvis att en runbook som anropas av GitHub när det finns en ny allokering till en GitHub-lagringsplats. Runbook kan ansluta till GitHub för att verifiera att en ny allokering inträffat innan du fortsätter.

## <a name="creating-a-webhook"></a>Skapa en webhook

Använd följande procedur för att skapa en ny webhook länkad till en runbook i Azure-portalen.

1. Från den **Runbooks sidan** i Azure-portalen klickar du på den runbook som webhooken börjar att visa dess detaljsida. Se till att runbook **Status** är **publicerad**.
2. Klicka på **Webhook** överst på sidan för att öppna den **Lägg till Webhook** sidan.
3. Klicka på **Skapa ny webhook** att öppna den **skapa webhook sidan**.
4. Ange en **namn**, **förfallodatum** för webhooken och om den ska aktiveras. Se [information om en webhook](#details-of-a-webhook) mer dessa egenskaper.
5. Klicka på kopieringsikonen för och tryck på Ctrl + C för att kopiera Webbadressen till webhooken. Registrera den på en säker plats. **När du skapar webhooken kan inte kan du hämta URL: en igen.**

   ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)

1. Klicka på **parametrar** att ange värden för runbook-parametrar. Om runbooken har obligatoriska parametrar, är du inte kunna skapa webhooken om inte värden anges.
1. Klicka på **skapa** du skapar webhooken.

## <a name="using-a-webhook"></a>Med en webhook

Om du vill använda en webhook när den har skapats, måste klientprogrammet utfärda en HTTP POST med URL-Adressen för webhooken. Syntaxen för webhooken har följande format:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klienten får något av följande returkoder från POST-begäran.

| Kod | Text | Beskrivning |
|:--- |:--- |:--- |
| 202 |Accepterad |Begäran accepterades och runbook har i kö. |
| 400 |Felaktig begäran |Begäran godkändes inte av något av följande skäl: <ul> <li>Webhooken har gått ut.</li> <li>Webhooken har inaktiverats.</li> <li>Token i URL: en är ogiltig.</li>  </ul> |
| 404 |Kunde inte hittas |Begäran godkändes inte av något av följande skäl: <ul> <li>Webhook hittades inte.</li> <li>Runbook hittades inte.</li> <li>Kontot hittades inte.</li>  </ul> |
| 500 |Internt serverfel |URL: en är giltig, men ett fel uppstod. Skicka begäran igen. |

Förutsatt att begäran har lyckats, webhook svaret innehåller jobb-ID i JSON-format på följande sätt. Det innehåller ett enskilt jobb-ID, men JSON-format som tillåter för eventuella framtida förbättringar.

```json
{"JobIds":["<JobId>"]}
```

Klienten kan inte fastställa när runbook-jobbet har slutförts eller deras slutförandestatus från webhooken. Det kan ta reda på den här informationen med jobb-ID med en annan metod som [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) eller [Azure Automation API](/rest/api/automation/job).

## <a name="sample-runbook"></a>Exempel-runbook

Följande exempel-runbook accepterar webhookdata och startar de virtuella datorerna som anges i begärandetexten. Testa denna runbook i ditt Automation-konto under **Runbooks**, klickar du på **+ Lägg till en runbook**. Om du inte vet hur du skapar en runbook, se [skapa en runbook](automation-quickstart-create-runbook.md).

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

I följande exempel använder Windows PowerShell för att starta en runbook med en webhook. Alla språk som kan skicka en HTTP-begäran kan använda en webhook; Windows PowerShell används här som ett exempel.

Runbook förväntar sig en lista över virtuella datorer som har formaterats i JSON i brödtexten i begäran. Runbook validerar också att rubrikerna som innehåller ett specifikt definierat meddelandet för att verifiera webhook anroparen är giltig.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-RestMethod -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

I följande exempel visas brödtexten i begäran som är tillgänglig för runbooken i den **RequestBody** egenskapen för **WebhookData**. Detta formateras som JSON eftersom det var det format som ingick i brödtexten i begäran.

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

Följande bild visar den begäran som skickas från Windows PowerShell och det resulterande svaret. Jobb-ID är extraheras från svaret och konverteras till en sträng.

![Webhooks-knappen](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Nästa steg

* Läs hur du använder Azure Automation för att vidta åtgärder för Azure-aviseringar i [använda en avisering för att utlösa en Azure Automation-runbook](automation-create-alert-triggered-runbook.md).
