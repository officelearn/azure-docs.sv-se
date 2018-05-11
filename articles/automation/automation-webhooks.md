---
title: Starta en Azure Automation-runbook med en webhook
description: En webhook som gör att en klient att starta en runbook i Azure Automation från ett HTTP-anrop.  Den här artikeln beskriver hur du skapar en webhook och hur du anropar en om du vill starta en runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: bb64d0c5d94bb198b6ece2ea50a7fc248b93c7dd
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Starta en Azure Automation-runbook med en webhook
En *webhook* kan du starta en viss runbook i Azure Automation via en HTTP-begäran. Detta gör att externa tjänster, till exempel Visual Studio Team Services, GitHub, Azure logganalys eller anpassade program att starta runbooks utan att implementera en fullständig lösning med hjälp av Azure Automation-API.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Du kan jämföra webhooks andra metoder för att starta en runbook [starta en runbook i Azure Automation](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Information om en webhook
I följande tabell beskrivs de egenskaper som du måste konfigurera för en webhook.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Namn |Du kan ange vilket namn som helst för en webhook eftersom detta inte är exponerad för klienten.  Den används endast för dig för att identifiera runbook i Azure Automation. <br>  Som bästa praxis, bör du ge webhooken ett namn som är relaterade till klienter som använder den. |
| URL |URL till webhooken är unika adressen som en klient anropar med en HTTP POST till att starta runbook kopplad till webhooken.  Det genereras automatiskt när du har skapat webhooken.  Du kan inte ange en anpassad URL. <br> <br>  URL: en innehåller en säkerhetstoken som gör att runbook anropas av en tredjeparts-system utan ytterligare autentisering. Det bör därför behandlas som ett lösenord.  Av säkerhetsskäl bör visa du bara URL: en i Azure-portalen när webhook har skapats. Du bör anteckna URL-Adressen i en säker plats för framtida användning. |
| Upphörandedatum |Varje webhook har ett sista giltighetsdatum som den kan inte längre användas som ett certifikat.  Den här upphör att gälla kan ändras när du har skapat webhooken. |
| Enabled |En webhook är aktiverad som standard när den skapas.  Om du den inaktiverad, kommer ingen klient att kunna använda den.  Du kan ange den **aktiverad** egenskapen när du skapar webhooken eller när som helst när den skapas. |

### <a name="parameters"></a>Parametrar
En webhook kan definiera värden för runbookparametrar som används när runbook startas genom att webhooken. Webhooken måste innehålla värden för alla obligatoriska parametrar runbook och kan innehålla värden för valfria parametrar. Ett parametervärde som konfigurerats för att en webhook kan ändras även när du har skapat webhooken. Flera webhooks som är kopplad till en enda runbook kan använda olika parametervärden.

När en klient startar en runbook med en webhook, kan inte det åsidosätta de parametervärden som definierats i webhooken.  Om du vill ta emot data från klienten, runbook kan acceptera en enda parameter med namnet **$WebhookData** av typen [objekt] som innehåller data som klienten innehåller i POST-begäran.

![Webhookdata egenskaper](media/automation-webhooks/webhook-data-properties.png)

Den **$WebhookData** objekt har följande egenskaper:

| Egenskap  | Beskrivning |
|:--- |:--- |
| WebhookName |Namnet på webhook. |
| RequestHeader |Hash-tabell som innehåller huvuden inkommande POST-begäran. |
| requestBody |Innehållet i den inkommande POST-begäranden.  Detta behåller all formatering, till exempel sträng, JSON, XML, eller formulär-kodade data. Runbook måste vara skriven för att arbeta med dataformatet som förväntas. |

Det finns ingen konfiguration av webhooken krävs för att stödja den **$WebhookData** parametern och runbook inte krävs för att godkänna.  Om runbook inte definierar parametern ignoreras någon information om den begäran som skickades från klienten.

Om du anger ett värde för $WebhookData när du skapar webhooken, att värdet kommer att åsidosättas när webhooken startar runbook med data från klienten POST-begäran, även om klienten inte innehåller några data i begärandetexten.  Om du startar en runbook med hjälp av en annan metod än en webhook $WebhookData, kan du ange ett värde för $Webhookdata som identifieras av runbook.  Det här värdet ska vara ett objekt med samma [egenskaper](#details-of-a-webhook) som $Webhookdata så att runbook fungerar korrekt med det som om den arbetade med faktiska WebhookData skickades av en webhook.

Till exempel om du startar följande runbook från Azure Portal och vill skicka vissa exempel WebhookData för att testa, eftersom WebhookData är ett objekt som ska den skickas som JSON i Användargränssnittet.

![WebhookData-parameter från Gränssnittet](media/automation-webhooks/WebhookData-parameter-from-UI.png)

För ovan runbook, om du har följande egenskaper för parametern WebhookData:

1. WebhookName: *MyWebhook*
2. RequestHeader: *från = testanvändare*
3. RequestBody: *[”VM1”, ”VM2”]*

Du skulle sedan överföra följande JSON-värde i Användargränssnittet för WebhookData-parametern:  

* {”WebhookName”: ”MyWebhook”, ”RequestHeader”: {”från”: ”testanvändare”}, ”RequestBody” ”: [\"VM1\",\"VM2\"]”}

![Starta WebhookData parametern från Gränssnittet](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Värdena för alla indataparametrar loggas med runbook-jobbet.  Detta innebär att alla indata som angetts av klienten i webhook-begäran är inloggade och tillgänglig för alla med åtkomst till automation-jobb.  Därför bör du vara försiktig om känslig information, inklusive i webhook-anrop.
>

## <a name="security"></a>Säkerhet
Säkerheten för en webhook är beroende av säkerheten för URL: en som innehåller en säkerhetstoken som gör att de kan anropas. Azure Automation utför inte någon autentisering på begäran så länge det görs till rätt URL. Därför bör du inte använda webhooks för runbooks som utför mycket känsliga funktioner utan att använda ett annat sätt att verifiera begäran.

Du kan inkludera logik i runbook för att fastställa att den anropades av en webhook genom att kontrollera den **WebhookName** -egenskapen för parametern $WebhookData. Runbook kan utföra ytterligare verifiering genom att söka efter specifika informationen i den **RequestHeader** eller **RequestBody** egenskaper.

En annan strategi är att runbooken som utför vissa verifiering av ett externt villkor när det tog emot en webhook-begäran.  Anta till exempel att en runbook som anropas av GitHub när det finns ett nytt genomförande till GitHub-lagringsplatsen.  Runbook kan ansluta till GitHub för att verifiera ett nytt genomförande inträffat faktiskt precis innan du fortsätter.

## <a name="creating-a-webhook"></a>Skapat en webhook
Använd följande procedur för att skapa en ny webhook kopplad till en runbook i Azure-portalen.

1. Från den **Runbooks sidan** i Azure-portalen klickar du på den runbook som webhooken startar att visa dess detaljsida.
2. Klicka på **Webhook** längst upp på sidan för att öppna den **lägga till Webhook** sidan. <br>
   ![Knappen Webhooks](media/automation-webhooks/webhooks-button.png)
3. Klicka på **skapa nya webhook** att öppna den **skapa webhook sidan**.
4. Ange en **namn**, **förfallodatum** för webhooken och om den ska aktiveras. Se [information om en webhook](#details-of-a-webhook) mer dessa egenskaper.
5. Klicka på Kopiera-ikonen och tryck på Ctrl + C för att kopiera Webbadressen till webhooken.  Registrera den på en säker plats.  **När du har skapat webhooken kan du hämta URL: en igen.** <br>
   ![Webhooksadressen](media/automation-webhooks/copy-webhook-url.png)
6. Klicka på **parametrar** att ange värden för runbook-parametrar.  Om runbooken har obligatoriska parametrar, kommer sedan du inte att kunna skapa webhooken om värden har angetts.
7. Klicka på **skapa** att skapa webhooken.

## <a name="using-a-webhook"></a>Med hjälp av en webhook
Om du vill använda en webhook när den har skapats, måste klientprogrammet utfärda en HTTP POST med URL-Adressen för webhooken.  Syntaxen för webhooken har följande format.

    http://<Webhook Server>/token?=<Token Value>

Klienten får ett av följande returkoder från POST-begäran.  

| Kod | Text | Beskrivning |
|:--- |:--- |:--- |
| 202 |Accepterad |Begäran accepterades och runbook placerades i kö. |
| 400 |Felaktig förfrågan |Begäran accepterades inte av något av följande skäl. <ul> <li>Webhook har gått ut.</li> <li>Webhook har inaktiverats.</li> <li>Token i URL-Adressen är ogiltig.</li>  </ul> |
| 404 |Kunde inte hittas |Begäran accepterades inte av något av följande skäl. <ul> <li>Webhooken hittades inte.</li> <li>Runbook hittades inte.</li> <li>Det gick inte att hitta kontot.</li>  </ul> |
| 500 |Internt serverfel |URL: en är giltig, men ett fel uppstod.  Skicka begäran igen. |

Under förutsättning att begäran lyckas, webhook svaret innehåller jobb-id i JSON-format på följande sätt. Den innehåller en enda jobb-id, men JSON-format kan för eventuella framtida förbättringar.

    {"JobIds":["<JobId>"]}  

Klienten kan inte fastställa när runbook-jobbet har slutförts eller dess status för slutförande från webhooken.  Det kan avgöra den här informationen med jobb-id med en annan metod, till exempel [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) eller [Azure Automation API](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### <a name="example"></a>Exempel
I följande exempel använder Windows PowerShell för att starta en runbook med en webhook.  Observera att alla språk som kan göra en HTTP-begäran kan använda en webhook; Windows PowerShell används bara exempel.

Runbook förväntar sig en lista över virtuella datorer som har formaterats i JSON i brödtexten i begäran. Vi också inklusive information om som startar runbook och datum och tid som startas i huvudet i begäran.      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


Följande bild visar informationen i huvudet (med hjälp av en [Fiddler](http://www.telerik.com/fiddler) trace) från den här begäran. Detta inkluderar standard sidhuvuden för HTTP-begäran förutom anpassat datum och från-huvuden som vi har lagt till.  Var och en av dessa värden är tillgänglig för runbook i den **RequestHeaders** -egenskapen för **WebhookData**.

![Knappen Webhooks](media/automation-webhooks/webhook-request-headers.png)

Följande bild visar brödtexten i begäran (med hjälp av en [Fiddler](http://www.telerik.com/fiddler) trace) som är tillgängligt för runbook i den **RequestBody** -egenskapen för **WebhookData**. Detta formateras som JSON eftersom som har det format som ingick i brödtexten i begäran.     

![Knappen Webhooks](media/automation-webhooks/webhook-request-body.png)

Följande bild visar den begäran som skickas från Windows PowerShell och resulterande svar.  Jobb-id extraheras från svaret och konverteras till en sträng.

![Knappen Webhooks](media/automation-webhooks/webhook-request-response.png)

Följande exempel-runbook som accepterar föregående exempelbegäran och startar de virtuella datorerna som anges i begärandetexten.

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param (
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {

            # Collect properties of WebhookData
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."

            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred

            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook."
        }
    }


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>Starta runbooks som svar på Azure-aviseringar
Webhook-aktiverade runbooks som kan användas för att ta hänsyn till [Azure aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Resurser i Azure kan övervakas genom att samla in statistik som prestanda, tillgänglighet och användningsinformation med hjälp av Azure-aviseringar. Du kan ta emot en avisering baserat på övervakning mått eller händelser för din Azure-resurser, för närvarande Automation-konton stöder endast mått. När värdet för ett visst mått överstiger tröskelvärdet tilldelade eller om konfigurerade händelsen utlöses sedan ett meddelande skickas till tjänstadministratör eller medadministratörer för att lösa aviseringen, mer information om mått och händelser hittar du [ Azure-aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Förutom att använda Azure-aviseringar som ett system, kan du också startar runbooks som svar på aviseringar. Azure Automation ger möjlighet att köra webhook-aktiverade runbooks med Azure-aviseringar. När ett mått överskrider det konfigurerade tröskelvärdet sedan varningsregeln blir aktiv och utlöser automation-webhook som i sin tur kör runbook.

![Webhook-konfigurationer](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>Varningskontext
Överväg att en Azure-resurs, till exempel en virtuell dator, CPU-användning för den här datorn är ett mått för KPI. Om CPU-användning är 100% eller mer än en viss mängd under lång tid, kanske du vill starta om den virtuella datorn för att åtgärda problemet. Detta kan lösas genom att konfigurera en aviseringsregel till den virtuella datorn och den här regeln tar CPU-procent som dess mått. CPU-procent här tas bara som ett exempel men det finns många andra mått som du kan konfigurera att Azure-resurser och starta om den virtuella datorn är en åtgärd som vidtagits för att åtgärda det här problemet kan du konfigurera runbook för att vidta andra åtgärder.

När detta varningsregeln blir aktiv och utlöser webhook-aktiverade-runbook skickas aviseringssammanhanget till runbook. [Aviseringskontext](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) innehåller information, inklusive **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** och **tidsstämpel** som krävs att identifiera den resurs där den kommer att åtgärda runbook. Varna kontext är inbäddad i body-delen av den **WebhookData** objekt som skickas till runbook och den kan nås med **Webhook.RequestBody** egenskapen

### <a name="example"></a>Exempel
Skapa en virtuell Azure-dator i din prenumeration och koppla en [aviseringen för att övervaka mått för CPU-procent](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Kontrollera att du fylla i fältet webhook med URL-Adressen till webhooken som skapades när webhooken medan varningen.

Följande exempel-runbook utlöses när varningsregeln aktiveras och samlar det in aviseringskontext-parametrar som krävs att identifiera den resurs där den kommer att åtgärda runbook.

    workflow Invoke-RunbookUsingAlerts
    {
        param (      
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData.
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookBody    =   $WebhookData.RequestBody
            $WebhookHeaders =   $WebhookData.RequestHeader

            # Outputs information on the webhook name that called This
            Write-Output "This runbook was started from webhook $WebhookName."


            # Obtain the WebhookBody containing the AlertContext
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody)
            Write-Output "`nWEBHOOK BODY"
            Write-Output "============="
            Write-Output $WebhookBody

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information
            Write-Output "`nALERT CONTEXT DATA"
            Write-Output "==================="
            Write-Output $AlertContext.name
            Write-Output $AlertContext.subscriptionId
            Write-Output $AlertContext.resourceGroupName
            Write-Output $AlertContext.resourceName
            Write-Output $AlertContext.resourceType
            Write-Output $AlertContext.resourceId
            Write-Output $AlertContext.timestamp

            # Act on the AlertContext data, in our case restarting the VM.
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine.
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential"
            Add-AzureAccount -Credential $cred
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN"

            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        }
        else  
        {
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }



## <a name="next-steps"></a>Nästa steg
* Mer information om olika sätt att starta en runbook finns [starta en Runbook](automation-starting-a-runbook.md).
* Information om hur du visar Status för en Runbook-jobb finns [Runbook som körs i Azure Automation](automation-runbook-execution.md).
* Information om hur du använder Azure Automation för att vidta åtgärder för Azure aviseringar finns [åtgärda Azure VM aviseringar med Automation-Runbooks](automation-azure-vm-alert-integration.md).
