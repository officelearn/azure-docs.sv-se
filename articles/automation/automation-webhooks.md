---
title: Starta en Azure Automation-runbook med en webhook
description: En webhook som gör att en klient att starta en runbook i Azure Automation från ett HTTP-anrop.  Den här artikeln beskriver hur du skapar en webhook och hur du anropar en om du vill starta en runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e047dffa86915b0cd6e8829ea27e0335e7f88cb2
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757164"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Starta en Azure Automation-runbook med en webhook

En *webhook* kan du starta en viss runbook i Azure Automation via en HTTP-begäran. Detta gör att externa tjänster, till exempel Visual Studio Team Services, GitHub, Azure logganalys eller anpassade program att starta runbooks utan att implementera en fullständig lösning med hjälp av Azure Automation-API.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Du kan jämföra webhooks andra metoder för att starta en runbook [starta en runbook i Azure Automation](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Information om en webhook

I följande tabell beskrivs de egenskaper som du måste konfigurera för en webhook.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Namn |Du kan ange vilket namn som helst för en webhook eftersom detta inte är exponerad för klienten. Den används endast för dig för att identifiera runbook i Azure Automation. <br> Som bästa praxis bör du ge webhooken ett namn som är relaterade till den klient som använder den. |
| URL |URL till webhooken är unika adressen som en klient anropar med en HTTP POST till att starta runbook kopplad till webhooken. Det genereras automatiskt när du har skapat webhooken. Du kan inte ange en anpassad URL. <br> <br> URL: en innehåller en säkerhetstoken som gör att runbook anropas av en tredjeparts-system utan ytterligare autentisering. Det bör därför behandlas som ett lösenord. Av säkerhetsskäl bör visa du bara URL: en i Azure-portalen när webhook har skapats. Observera att URL: en på en säker plats för framtida användning. |
| Upphörandedatum |Varje webhook har ett sista giltighetsdatum som den kan inte längre användas som ett certifikat. Den här upphör att gälla kan ändras när du har skapat webhooken. |
| Enabled |En webhook är aktiverad som standard när den skapas. Om du anger det inaktiverad är ingen klient kan du använda den. Du kan ange den **aktiverad** egenskapen när du skapar webhooken eller när som helst när den skapas. |

### <a name="parameters"></a>Parametrar

En webhook kan definiera värden för runbookparametrar som används när runbook startas genom att webhooken. Webhooken måste innehålla värden för alla obligatoriska parametrar runbook och kan innehålla värden för valfria parametrar. Ett parametervärde som konfigurerats för att en webhook kan ändras även när du har skapat webhooken. Flera webhooks som är kopplad till en enda runbook kan använda olika parametervärden.

När en klient startar en runbook med en webhook, kan inte det åsidosätta de parametervärden som definierats i webhooken. Om du vill ta emot data från klienten, runbook kan acceptera en enda parameter med namnet **$WebhookData** av typen [objekt] som innehåller data som klienten innehåller i POST-begäran.

![Webhookdata egenskaper](media/automation-webhooks/webhook-data-properties.png)

Den **$WebhookData** objekt har följande egenskaper:

| Egenskap  | Beskrivning |
|:--- |:--- |
| WebhookName |Namnet på webhook. |
| RequestHeader |Hash-tabell som innehåller huvuden inkommande POST-begäran. |
| requestBody |Innehållet i den inkommande POST-begäranden. Detta bevarar formatering, till exempel sträng, JSON, XML, eller formulär-kodade data. Runbook måste vara skriven för att arbeta med dataformatet som förväntas. |

Det finns ingen konfiguration av webhooken krävs för att stödja den **$WebhookData** parametern och runbook inte krävs för att godkänna. Om runbook inte definierar parametern ignoreras någon information om den begäran som skickades från klienten.

Om du anger ett värde för $WebhookData när du skapar webhooken, att värde åsidosätts när webhooken startar runbook med data från klienten POST-begäran, även om klienten inte innehåller några data i begärandetexten. Om du startar en runbook med hjälp av en annan metod än en webhook $WebhookData, kan du ange ett värde för $Webhookdata som identifieras av runbook. Det här värdet ska vara ett objekt med samma [egenskaper](#details-of-a-webhook) som $Webhookdata så att runbook fungerar korrekt med det som om den arbetade med faktiska WebhookData skickades av en webhook.

Till exempel om du startar följande runbook från Azure-portalen och vill skicka vissa exempel WebhookData för att testa, eftersom WebhookData är ett objekt som ska den skickas som JSON i Användargränssnittet.

![WebhookData-parameter från Gränssnittet](media/automation-webhooks/WebhookData-parameter-from-UI.png)

På följande runbook, om du har följande egenskaper för parametern WebhookData:

* WebhookName: *MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup', 'Name': 'vm01'}, {'ResourceGroup': 'myResourceGroup', 'Name': 'vm02'}]*

Du kan sedan överföra följande JSON-värde i Användargränssnittet för parametern WebhookData. I följande exempel med transport returneras och radmatningstecken matchar det format som skickas från en webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Starta WebhookData parametern från Gränssnittet](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Värdena för alla indataparametrar loggas med runbook-jobbet. Detta innebär att alla indata som angetts av klienten i webhook-begäran är inloggade och tillgänglig för alla med åtkomst till automation-jobb.  Därför bör du vara försiktig om känslig information, inklusive i webhook-anrop.

## <a name="security"></a>Säkerhet

Säkerheten för en webhook är beroende av säkerheten för den URL som innehåller en säkerhetstoken som gör att de kan anropas. Azure Automation utför inte någon autentisering på begäran så länge det görs till rätt URL. Därför bör du inte använda webhooks för runbooks som utför mycket känsliga funktioner utan att använda ett annat sätt att verifiera begäran.

Du kan inkludera logik i runbook för att fastställa att den anropades av en webhook genom att kontrollera den **WebhookName** -egenskapen för parametern $WebhookData. Runbook kan utföra ytterligare verifiering genom att söka efter specifika informationen i den **RequestHeader** eller **RequestBody** egenskaper.

En annan strategi är att runbooken som utför vissa verifiering av ett externt villkor när det tog emot en webhook-begäran. Anta till exempel att en runbook som anropas av GitHub när det finns ett nytt genomförande till GitHub-lagringsplatsen. Runbook kan ansluta till GitHub för att verifiera att en ny commit inträffat innan du fortsätter.

## <a name="creating-a-webhook"></a>Skapat en webhook

Använd följande procedur för att skapa en ny webhook kopplad till en runbook i Azure-portalen.

1. Från den **Runbooks sidan** i Azure-portalen klickar du på den runbook som webhooken börjar att visa dess detaljsida.
1. Klicka på **Webhook** längst upp på sidan för att öppna den **lägga till Webhook** sidan.
1. Klicka på **skapa nya webhook** att öppna den **skapa webhook sidan**.
1. Ange en **namn**, **förfallodatum** för webhooken och om den ska aktiveras. Se [information om en webhook](#details-of-a-webhook) mer dessa egenskaper.
1. Klicka på Kopiera-ikonen och tryck på Ctrl + C för att kopiera Webbadressen till webhooken. Registrera den på en säker plats. **När du har skapat webhooken kan du hämta URL: en igen.**

   ![Webhook-URL](media/automation-webhooks/copy-webhook-url.png)

1. Klicka på **parametrar** att ange värden för runbook-parametrar. Om runbooken har obligatoriska parametrar, sedan går det inte att skapa webhooken om värden har angetts.
1. Klicka på **skapa** att skapa webhooken.

## <a name="using-a-webhook"></a>Med hjälp av en webhook

Om du vill använda en webhook när den har skapats, måste klientprogrammet utfärda en HTTP POST med URL-Adressen för webhooken. Syntaxen för webhooken är i följande format:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klienten tar emot något av följande returkoder från POST-begäran.

| Kod | Text | Beskrivning |
|:--- |:--- |:--- |
| 202 |Accepterad |Begäran accepterades och runbook placerades i kö. |
| 400 |Felaktig förfrågan |Begäran accepterades inte av något av följande skäl: <ul> <li>Webhook har gått ut.</li> <li>Webhook har inaktiverats.</li> <li>Token i URL-Adressen är ogiltig.</li>  </ul> |
| 404 |Kunde inte hittas |Begäran accepterades inte av något av följande skäl: <ul> <li>Webhooken hittades inte.</li> <li>Runbook hittades inte.</li> <li>Det gick inte att hitta kontot.</li>  </ul> |
| 500 |Internt serverfel |URL: en är giltig, men ett fel uppstod. Skicka begäran igen. |

Under förutsättning att begäran lyckas, webhook svaret innehåller jobb-id i JSON-format på följande sätt. Den innehåller en enda jobb-id, men JSON-format kan för eventuella framtida förbättringar.

```json
{"JobIds":["<JobId>"]}
```

Klienten kan inte fastställa när runbook-jobbet har slutförts eller dess status för slutförande från webhooken. Det kan avgöra den här informationen med jobb-id med en annan metod, till exempel [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) eller [Azure Automation API](/rest/api/automation/job).

## <a name="sample-runbook"></a>Exempel-runbook

Följande exempel-runbook som accepterar den accepterar webhook data och startar de virtuella datorerna som anges i begärandetexten. Att testa denna runbook i Automation-konto under **Runbooks**, klickar du på **+ Lägg till en runbook**. Om du inte vet hur du skapar en runbook finns [skapar en runbook](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

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

I följande exempel använder Windows PowerShell för att starta en runbook med en webhook. Alla språk som kan göra en HTTP-begäran kan använda en webhook; Windows PowerShell används här som ett exempel.

Runbook förväntar sig en lista över virtuella datorer som har formaterats i JSON i brödtexten i begäran.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms

$response = Invoke-RestMethod -Method Post -Uri $uri -Body $body
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

I följande exempel visas brödtexten i begäran som är tillgängliga för runbook i den **RequestBody** -egenskapen för **WebhookData**. Detta formateras som JSON eftersom som har det format som ingick i brödtexten i begäran.

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

Följande bild visar den begäran som skickas från Windows PowerShell och resulterande svar. Jobb-id extraheras från svaret och konverteras till en sträng.

![Knappen Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du använder Azure Automation för att vidta åtgärder för Azure aviseringar finns [använder en avisering för att utlösa en Azure Automation-runbook](automation-create-alert-triggered-runbook.md).
