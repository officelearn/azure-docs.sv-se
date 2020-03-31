---
title: Starta en Azure Automation-runbook med en webhook
description: En webhook som gör att en klient kan starta en runbook i Azure Automation från ett HTTP-anrop.  I den här artikeln beskrivs hur du skapar en webhook och hur du ringer en för att starta en runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 8cb641f95e7327e80f42df86a56eba8c34e7e598
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367031"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Starta en Azure Automation-runbook med en webhook

En webhook gör det möjligt för en extern tjänst att starta en viss runbook i Azure Automation via en enda HTTP-begäran. Externa tjänster inkluderar Azure DevOps Services, GitHub, Azure Monitor-loggar och anpassade program. En sådan tjänst kan använda en webhook för att starta en runbook utan att implementera en fullständig lösning med hjälp av Azure Automation API. Du kan jämföra webhooks med andra metoder för att starta en runbook i [Starta en runbook i Azure Automation](automation-starting-a-runbook.md).

> [!NOTE]
> Det går inte att använda en webhook för att starta en Python-runbook.

![WebhooksÖversikt](media/automation-webhooks/webhook-overview-image.png)

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="webhook-properties"></a>Egenskaper för Webhook

I följande tabell beskrivs de egenskaper som du måste konfigurera för en webhook.

| Egenskap | Beskrivning |
|:--- |:--- |
| Namn |Namn på webhook. Du kan ange vilket namn du vill, eftersom det inte är exponerat för klienten. Det används bara för att identifiera runbook i Azure Automation. Du bör ge webhook ett namn som är relaterat till klienten som använder den. |
| URL |URL för webhook. Det här är den unika adress som en klient anropar med ett HTTP-INLÄGG för att starta runbooken som är länkad till webhooken. Det genereras automatiskt när du skapar webhooken. Du kan inte ange en anpassad WEBBADRESS. <br> <br> URL:en innehåller en säkerhetstoken som gör att ett tredjepartssystem kan anropa runbooken utan ytterligare autentisering. Därför bör du behandla webbadressen som ett lösenord. Av säkerhetsskäl kan du bara visa URL:en i Azure-portalen när du skapar webhooken. Observera webbadressen på en säker plats för framtida bruk. |
| Förfallodatum | Utgångsdatum för webhook, varefter det inte längre kan användas. Du kan ändra utgångsdatumet efter att webhook har skapats, så länge webhooken inte har upphört att gälla. |
| Enabled | Ange om webhooken är aktiverad som standard när den skapas. Om du anger den här egenskapen till Inaktiverad kan ingen klient använda webhooken. Du kan ange den här egenskapen när du skapar webhooken eller någon annan gång efter att den har skapats. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametrar som används när webhook startar en runbook

En webhook kan definiera värden för runbook parametrar som används när runbook startar. Webhooken måste innehålla värden för obligatoriska runbook-parametrar och kan innehålla värden för valfria parametrar. Ett parametervärde som konfigurerats för en webhook kan ändras även efter att webhook har skapats. Flera webhooks som är länkade till en enda runbook kan använda olika runbook-parametervärden. När en klient startar en runbook med hjälp av en webhook kan den inte åsidosätta parametervärdena som definierats i webhooken.

Om du vill ta emot data från klienten stöder runbooken en enda parameter som kallas `WebhookData`. Den här parametern definierar ett objekt som innehåller data som klienten innehåller i en POST-begäran.

![Egenskaper för WebhookData](media/automation-webhooks/webhook-data-properties.png)

Parametern `WebhookData` har följande egenskaper:

| Egenskap | Beskrivning |
|:--- |:--- |
| `WebhookName` | Namn på webhook. |
| `RequestHeader` | Hashtable innehåller rubrikerna för den inkommande POST-begäran. |
| `RequestBody` | Brödtexten för den inkommande POST-begäran. Den här brödtexten behåller all dataformatering, till exempel sträng, JSON, XML eller formulärkodad. Runbook måste skrivas för att fungera med det dataformat som förväntas. |

Det finns ingen konfiguration av webhook `WebhookData` som krävs för att stödja parametern, och runbook krävs inte för att acceptera den. Om runbooken inte definierar parametern ignoreras alla detaljer om begäran som skickas från klienten.

> [!NOTE]
> När du anropar en webhook ska klienten alltid lagra eventuella parametervärden om samtalet skulle misslyckas. Om det uppstår ett nätverksproblem eller ett anslutningsproblem kan programmet inte hämta misslyckade webhook-anrop.

Om du anger `WebhookData` ett värde för webhook skapas, åsidosätts det när webhook startar runbook med data från klienten POST begäran. Detta händer även om programmet inte innehåller några data i begärandeorganet. 

Om du startar en runbook som definierar `WebhookData` med hjälp av en annan `WebhookData` mekanism än en webhook kan du ange ett värde för den som runbooken känner igen. Det här värdet ska vara [properties](#webhook-properties) ett `WebhookData` objekt med samma egenskaper som parametern så att `WebhookData` runbooken kan arbeta med det på samma sätt som det fungerar med faktiska objekt som skickas av en webhook.

Om du till exempel startar följande runbook från Azure-portalen och vill skicka några exempel webhook-data för testning, måste du skicka data i JSON i användargränssnittet.

![WebhookData-parametern från användargränssnittet](media/automation-webhooks/WebhookData-parameter-from-UI.png)

För nästa runbook-exempel ska vi definiera `WebhookData`följande egenskaper för:

* **WebhookName**: MyWebhook
* **RequestBody**:`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Nu skickar vi följande JSON-objekt i `WebhookData` användargränssnittet för parametern. Det här exemplet, med vagnreturer och nyradstecken, matchar det format som skickas in från en webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Starta webhookdata-parametern från användargränssnittet](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation loggar värdena för alla indataparametrar med runbook-jobbet. Därför loggas alla indata som tillhandahålls av klienten i webhook-begäran och är tillgängliga för alla som har tillgång till automationsjobbet. Av denna anledning bör du vara försiktig med att inkludera känslig information i webhook samtal.

## <a name="webhook-security"></a>Webhook säkerhet

Säkerheten för en webhook förlitar sig på sekretessen för dess WEBBADRESS, som innehåller en säkerhetstoken som gör att webhook kan anropas. Azure Automation utför ingen autentisering på en begäran så länge den görs till rätt URL. Därför bör dina klienter inte använda webhooks för runbooks som utför mycket känsliga åtgärder utan att använda ett alternativt sätt att validera begäran.

Du kan inkludera logik i en runbook för att avgöra om den anropas av en webhook. Låt runbooken `WebhookName` kontrollera `WebhookData` parameterns egenskap. Runbook kan utföra ytterligare validering genom att `RequestHeader` `RequestBody` leta efter viss information i och egenskaper.

En annan strategi är att låta runbook utföra viss validering av ett externt villkor när den tar emot en webhook-begäran. Tänk dig till exempel en runbook som anropas av GitHub varje gång det finns en ny commit till en GitHub-databas. Runbook kan ansluta till GitHub för att verifiera att ett nytt åtagande har inträffat innan du fortsätter.

## <a name="creating-a-webhook"></a>Skapa en webhook

Använd följande procedur för att skapa en ny webhook som är länkad till en runbook i Azure-portalen.

1. På sidan Runbooks i Azure-portalen klickar du på den runbook som webhook börjar visa runbook-informationen. Kontrollera att fältet runbook **Status** är inställt **på Publicerad**.
2. Klicka på **Webhook** högst upp på sidan för att öppna sidan Lägg till webhook.
3. Klicka på **Skapa ny webhook** för att öppna sidan Skapa webhook.
4. Fyll i fälten **Namn** och **förfallodatum** för webhooken och ange om det ska aktiveras. Mer information om dessa egenskaper finns i [Webhook-egenskaper.](#webhook-properties)
5. Klicka på kopieringsikonen och tryck på Ctrl+C för att kopiera webbadressen till webhooken. Spela in den på ett säkert ställe. 

    > [!NOTE]
    > När du har skapat webhooken kan du inte hämta webbadressen igen.

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. Klicka på **Parametrar** om du vill ange värden för runbook-parametrarna. Om runbooken har obligatoriska parametrar kan du inte skapa webhooken om du inte anger värden.
1. Skapa webhooken genom att klicka på **Skapa**.

## <a name="using-a-webhook"></a>Använda en webhook

Om du vill använda en webhook när den `POST` har skapats måste klienten utfärda en HTTP-begäran med webbadressen för webhooken. Syntax:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klienten tar emot en av `POST` följande returkoder från begäran.

| Kod | Text | Beskrivning |
|:--- |:--- |:--- |
| 202 |Accepterad |Begäran accepterades och runbooken har köats. |
| 400 |Felaktig begäran |Begäran godtogs inte av något av följande skäl: <ul> <li>Webhooken har gått ut.</li> <li>Webhooken är inaktiverad.</li> <li>Token i URL:en är ogiltig.</li>  </ul> |
| 404 |Hittades inte |Begäran godtogs inte av något av följande skäl: <ul> <li>Det gick inte att hitta webhooken.</li> <li>Det gick inte att hitta runbooken.</li> <li>Det gick inte att hitta kontot.</li>  </ul> |
| 500 |Internt serverfel |URL:en var giltig, men ett fel uppstod. Skicka begäran igen. |

Förutsatt att begäran lyckas innehåller webhook-svaret jobb-ID i JSON-format som visas nedan. Den innehåller ett enda jobb-ID, men JSON-formatet möjliggör potentiella framtida förbättringar.

```json
{"JobIds":["<JobId>"]}
```

Klienten kan inte avgöra när runbook-jobbet har slutförts eller dess slutförandestatus från webhooken. Den kan ta reda på den här informationen med hjälp av jobb-ID med en annan mekanism, till exempel [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) eller [Azure Automation API](/rest/api/automation/job).

## <a name="renewing-a-webhook"></a><a name="renew-webhook"></a>Förnya en webhook

När en webhook skapas har den en giltighetsperiod på tio år, varefter den automatiskt upphör att gälla. När en webhook har gått ut kan du inte återaktivera den. Du kan bara ta bort och sedan återskapa den. 

Du kan utöka en webhook som inte har nått sin förfallotid. Så här utökar du en webhook:

1. Navigera till runbooken som innehåller webhooken. 
2. Välj **Webhooks** under **Resurser**. 
3. Klicka på den webhook som du vill utöka. 
4. På webhook-sidan väljer du ett nytt utgångsdatum och -tid och klickar på **Spara**.

## <a name="sample-runbook"></a>Exempel på runbook

Följande exempelkörningsbok accepterar webhook-data och startar de virtuella datorer som anges i begärandetexten. Om du vill testa den här runbooken klickar du på **Skapa en runbook**i ditt Automation-konto under **Runbooks.** Om du inte vet hur du skapar en runbook läser du [Skapa en runbook](automation-quickstart-create-runbook.md).

> [!NOTE]
> För icke-grafiska `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` och är alias för [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Du kan använda dessa cmdlets eller så kan du [uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du just har skapat ett nytt Automation-konto.

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

    # Retrieve VMs from Webhook request body
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
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="testing-the-sample"></a>Testa provet

I följande exempel används Windows PowerShell för att starta en runbook med en webhook. Alla språk som kan göra en HTTP-begäran kan använda en webhook. Windows PowerShell används här som exempel.

Runbook förväntar sig en lista över virtuella datorer formaterade i JSON i brödtexten för begäran. Runbooken verifierar också att rubrikerna innehåller ett definierat meddelande för att verifiera att webhook-anroparen är giltig.

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

I följande exempel visas brödtexten för begäran som `RequestBody` är `WebhookData`tillgänglig för runbooken i egenskapen . Det här värdet är formaterat i JSON för att vara kompatibelt med formatet som ingår i brödtexten för begäran.

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

Följande bild visar begäran som skickas från Windows PowerShell och det resulterande svaret. Jobb-ID extraheras från svaret och konverteras till en sträng.

![Knappen Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder Azure Automation för att vidta åtgärder på Azure-aviseringar finns i [Använda en avisering för att utlösa en Azure Automation-runbook](automation-create-alert-triggered-runbook.md).
