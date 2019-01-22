---
title: Utöka aviseringar från Log Analytics till Azure
description: 'Den här artikeln beskrivs de verktyg och API: et som du kan utöka aviseringar från Log Analytics till Azure-aviseringar.'
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: dc8c1733f506870765523b17c1fc3e283ff9cbdb
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423283"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Utöka aviseringar från Log Analytics till Azure-aviseringar
Funktionen för säkerhetsvarningar i Azure Log Analytics ersätts av Azure-aviseringar. Som en del av denna övergång utökas aviseringar som du ursprungligen konfigurerades i Log Analytics till Azure. Om du inte vill vänta tills de flyttas automatiskt till Azure, kan du starta processen:

- Manuellt från Operations Management Suite-portalen. 
- Via programmering med hjälp av AlertsVersion-API.  

> [!NOTE]
> Microsoft aviseringar automatiskt skapade i offentligt moln instanser av Log Analytics till Azure-aviseringar startar för den 14 maj 2018 i en serie med återkommande tills slutförts. Om du har några problem med att skapa [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md), använda [dessa åtgärdssteg](alerts-extend-tool.md#troubleshooting) att hämta åtgärdsgrupper som skapas automatiskt. Du kan använda de här stegen till 5 juli 2018. *Inte tillämpligt för Azure Government och nationellt molnanvändare av Log Analytics*. 

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Alternativ 1: Startar från Operations Management Suite-portalen
Följande steg beskriver hur du utökar aviseringar för arbetsytan från Operations Management Suite-portalen.  

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Välj en arbetsyta i fönstret Log Analytics-prenumerationer och välj sedan den **OMS-portalen** panelen.
![Skärmbild av Log Analytics-prenumeration fönstret med OMS-portalen panelen markerat](media/alerts-extend-tool/azure-portal-01.png) 
3. När du blir omdirigerad till Operations Management Suite-portalen, väljer du den **inställningar** ikon.
![Skärmbild av Operations Management Suite-portalen med inställningsikonen markerat](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. Från den **inställningar** väljer **aviseringar**.  
5. Välj **kopieras till Azure**.
![Skärmbild av Operations Management Suite aviseringsinställningar portalsidan med utöka till Azure markerat](media/alerts-extend-tool/ExtendInto.png)
6. En tre steg-guiden som visas i den **aviseringar** fönstret. Läs översikten och välj **nästa**.
![Skärmbild av steg 1 i guiden](media/alerts-extend-tool/ExtendStep1.png)  
7. I det andra steget, visas en sammanfattning av de föreslagna ändringarna lista lämpliga [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md) för aviseringarna. Om liknande åtgärder ses över fler än en avisering, föreslår guiden associera en enda åtgärd-grupp med alla.  Namngivningskonventionen är följande: *WorkspaceName_AG_#Number*. Om du vill fortsätta, Välj **nästa**.
![Skärmbild av steg 2 i guiden](media/alerts-extend-tool/ExtendStep2.png)  
8. I det sista steget i guiden väljer du **Slutför**, och bekräfta när du uppmanas att starta processen. Du kan också ange en e-postadress så att du meddelas när processen är klar och alla aviseringar har flyttats till Azure-aviseringar.
![Skärmbild av steg 3 i guiden](media/alerts-extend-tool/ExtendStep3.png)

När guiden har slutförts på den **aviseringsinställningar** alternativet för att utöka aviseringar till Azure-sidan tas bort. Dina aviseringar flyttas till Azure i bakgrunden, och det kan ta lite tid. Under åtgärden, du kan inte ändra aviseringar från Operations Management Suite-portalen. Du kan se aktuell status från banderoll överst på portalen. Om du tidigare angav en e-postadress, får du ett e-postmeddelande när processen har slutförts.  


Aviseringar fortsätter att listas i Operations Management Suite-portalen, även när de har flyttas till Azure.
![Skärmbild av Operations Management Suite aviseringsinställningar portalsidan](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Alternativ 2: Använd AlertsVersion API
Du kan använda Log Analytics AlertsVersion API för att utöka aviseringar från Log Analytics till Azure-aviseringar från alla klienter som kan anropa en REST-API. Du kan komma åt API: et från PowerShell med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg med öppen källkod. Du kan spara resultaten i JSON.  

Om du vill använda API: et måste skapa du först en GET-begäran. Detta utvärderar och returnerar en sammanfattning av de föreslagna ändringarna innan du försöker att utöka till Azure med hjälp av en POST-begäran. I resultatlistan dina aviseringar och en föreslagen lista över [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md), i JSON-format. Om liknande åtgärder ses över fler än en avisering, föreslår tjänsten att associera dem alla med en enskild åtgärdsgrupp. Namngivningskonventionen är följande: *WorkspaceName_AG_#Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Om GET-begäran lyckas HTTP-statuskoden 200 returneras, tillsammans med en lista över aviseringar och föreslagna åtgärdsgrupper i JSON-data. Följande är ett exempel på ett svar:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Om den angivna arbetsytan inte har några Varningsregler som definierats returnerar JSON-data på följande:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Om alla aviseringsregler i den angivna arbetsytan har redan utökats till Azure kan är svaret på GET-begäran:

```json
{
    "version": 2
}
```

Initiera en POST-svar för att initiera migrerar aviseringarna till Azure. Svaret POST bekräftar din avsikt, samt godkännande, om du vill ha aviseringar utökas från Log Analytics till Azure-aviseringar. Aktiviteten och aviseringarna bearbetas så som visas, baserat på resultatet när du har utfört GET-svar tidigare. Du kan också ange en lista över e-postadresser som skickar en rapport i Log Analytics när schemalagda bakgrundsprocessen för att migrera aviseringarna har slutförts. Du kan använda i följande exempel för begäran:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Resultatet av migrera aviseringar i Azure-aviseringar kan variera beroende på sammanfattningen tillhandahålls av GET-svar. När schemalagts är aviseringar i Log Analytics inte tillgänglig för tillfället till ändringen i Operations Management Suite-portalen. Du kan dock skapa nya aviseringar. 

Om POST-begäran lyckas returnerar ett HTTP 200 OK-status, tillsammans med följande svar:

```json
{
    "version": 2
}
```

Det här svaret anger aviseringarna har har utökats till Azure-aviseringar. Versionsegenskapen är endast för att kontrollera om aviseringar har utökats till Azure och har ingen relation till den [Log Analytics Search API](../../azure-monitor/platform/api-alerts.md). Någon e-postadresser angivna med INLÄGGET begäran skickas en rapport när aviseringar har utökats till Azure har. Om alla aviseringar i den angivna arbetsytan har redan schemalagts för utsträckning, är svaret på din POST-begäran att försöket har tillåts inte (ett 403 statuskod). Om du vill visa felmeddelanden eller förstå om processen har fastnat, kan du skicka en GET-begäran. Om det finns ett felmeddelande, returneras den, tillsammans med den sammanfattande informationen.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```


## <a name="option-3-use-a-custom-powershell-script"></a>Alternativ 3: Använda en anpassad PowerShell-skript
 Om Microsoft inte har har valt att låta dina aviseringar från Operations Management Suite-portalen till Azure, kan du göra det manuellt till 5 juli 2018. De två alternativen för manuell utökning beskrivs i föregående två avsnitt.

Alla aviseringar från Operations Management Suite-portalen har utökats till Azure efter 5 juli 2018. Användare som inte har gjort det [nödvändiga reparationsstegen som föreslås](#troubleshooting) sina aviseringar som körs utan aktiveringen åtgärder eller meddelanden, på grund av bristen på ska ha associerade [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md). 

Skapa [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md) för aviseringar manuellt i Log Analytics använder du följande exempelskript:
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occurred while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>Om det anpassade PowerShell-skriptet 
Här följer viktig information om hur du använder skriptet:
- En förutsättning är installationen av [ARMclient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager.
- Du måste ha rollen deltagare eller ägare i Azure-prenumeration för att köra skriptet.
- Du måste ange följande parametrar:
    - $subscriptionId: Azure-prenumerationens ID som är associerade med Operations Management Suite Log Analytics-arbetsytan.
    - $resourceGroup: Azure-resursgrupp för Operations Management Suite Log Analytics-arbetsytan.
    - $workspaceName: Namnet på Operations Management Suite Log Analytics-arbetsytan.

### <a name="output-of-the-custom-powershell-script"></a>Utdata från det anpassade PowerShell-skriptet
Skriptet är utförlig och matar ut stegen under tiden den pågår: 
- Den visar en sammanfattning som innehåller information om de befintliga Operations Management Suite Log Analytics-aviseringarna i arbetsytan. Sammanfattningen innehåller även information om Azure Åtgärdsgrupperna skapas för de åtgärder som är kopplade till standardrisknivåer. 
- Du uppmanas att gå vidare med tillägget eller avsluta när du har visat sammanfattningen.
- Om du fortsätter med tillägget nya Azure åtgärdsgrupper skapas och alla befintliga aviseringar som är associerade med dem. 
- Skriptet avslutas genom att visa meddelandet ”tillägget”! Vid eventuella mellanliggande fel visar skriptet efterföljande fel.

## <a name="troubleshooting"></a>Felsökning 
Under processen med att utöka aviseringar, problem som kan förhindra att systemet skapar nödvändiga [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md). I sådana fall kan du se ett felmeddelande i sidhuvudet i den **avisering** avsnittet av Operations Management Suite-portalen eller i GET anropa klar-API: et.

> [!IMPORTANT]
> Om offentliga Azure-molnet baserat Log Analytics-användare vidta inte följande steg innan du 5 juli 2018, aviseringar kommer att köras i Azure men kommer inte att utlösa en åtgärd eller meddelande. Om du vill få meddelanden för aviseringar måste du manuellt redigera och Lägg till [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md), eller använda föregående [anpassade PowerShell-skript](#option-3---using-custom-powershell-script).

Här följer anvisningarna för reparation för varje fel:
- **Fel: Omfång Lås finns på gruppnivå/resursgrupp för skrivåtgärder**:   ![Skärmbild av i Operations Management Suite aviseringsinställningar portalsidan felmeddelande omfång Lås markerad](media/alerts-extend-tool/ErrorScopeLock.png)

    När omfång Lock är aktiverat begränsar funktionen nya ändringar i gruppen prenumeration eller resursgrupp som innehåller arbetsytan Log Analytics (Operations Management Suite). Systemet kan inte utöka aviseringarna till Azure och skapa nödvändiga åtgärdsgrupper.
    
    Lös genom att ta bort den *ReadOnly* låset på din prenumeration eller resursgrupp grupp som innehåller arbetsytan. Du kan göra detta med hjälp av Azure portal, PowerShell, Azure CLI eller API: et. Mer information finns i [Lås Resursanvändning](../../azure-resource-manager/resource-group-lock-resources.md). 
    
    När du löser felet med hjälp av stegen som visas i artikeln utökar Operations Management Suite dina aviseringar till Azure inom schemalagda körning av nästa dag. Du behöver inte vidta några ytterligare åtgärder eller initiera något.

- **Fel: Principen finns på gruppnivå/resursgrupp**:   ![Skärmbild av i Operations Management Suite aviseringsinställningar portalsidan felmeddelande princip markerat](media/alerts-extend-tool/ErrorPolicy.png)

    När [Azure Policy](../../governance/policy/overview.md) är tillämpas, begränsar alla nya resurser i en prenumeration eller resursgrupp grupp som innehåller arbetsytan Log Analytics (Operations Management Suite). Systemet kan inte utöka aviseringarna till Azure och skapa nödvändiga åtgärdsgrupper.
    
    Lös genom att redigera principen som orsakar den *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* fel, vilket förhindrar att skapa nya resurser i din prenumeration eller resursgrupp grupp som innehåller arbetsytan. Du kan göra detta med hjälp av Azure portal, PowerShell, Azure CLI eller API: et. Du kan granska åtgärder för att hitta lämplig princip som orsakar fel. Mer information finns i [visa aktivitetsloggar för att granska åtgärder](../../azure-resource-manager/resource-group-audit.md). 
    
    När du löser felet med hjälp av stegen som visas i artikeln utökar Operations Management Suite dina aviseringar till Azure inom schemalagda körning av nästa dag. Du behöver inte vidta några ytterligare åtgärder eller initiera något.


## <a name="next-steps"></a>Nästa steg

* Mer information om den nya [Azure Alerts uppleva](../../azure-monitor/platform/alerts-overview.md).
* Lär dig mer om [loggaviseringar i Azure Alerts](alerts-unified-log.md).

