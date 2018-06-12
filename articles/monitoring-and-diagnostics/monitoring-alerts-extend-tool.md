---
title: Utöka aviseringar från loggen Analytcs till Azure
description: Den här artikeln beskriver de verktyg och API som du kan utöka aviseringar från Log Analytics till Azure-aviseringar.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 21ba95a7b3efff177afe63d22da3f6ba9848ded2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301039"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Utöka aviseringar från Log Analytics till Azure-aviseringar
Funktionen aviseringar i Azure Log Analytics ersätts av Azure-aviseringar. Som en del av denna övergång utökas aviseringar som du ursprungligen konfigurerade i logganalys till Azure. Om du inte vill vänta tills de automatiskt att flyttas till Azure kan du starta processen:

- Manuellt från Operations Management Suite-portalen. 
- Programmässigt med AlertsVersion API.  

> [!NOTE]
> Microsoft utöka aviseringar som skapats i Log Analytics till Azure aviseringar automatiskt startar för den 14 maj 2018 i en serie med återkommande tills slutförts. Microsoft scheman migrera aviseringarna till Azure och under den här övergången kan aviseringar hanteras från både Operations Management Suite-portalen och Azure-portalen. Den här processen är inte destruktiva eller interruptive.  

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Alternativ 1: Initiera från Operations Management Suite-portalen
Följande steg beskriver hur du utökar aviseringar för arbetsytan från Operations Management Suite-portalen.  

1. Välj i Azure-portalen **alla tjänster**. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Välj en arbetsyta i fönstret logganalys prenumerationer och välj sedan den **OMS-portalen** panelen.
![Skärmbild av logganalys prenumeration rutan med OMS-portalen panelen markerat](./media/monitor-alerts-extend/azure-portal-01.png) 
3. När du omdirigeras till Operations Management Suite-portal, väljer du den **inställningar** ikon.
![Skärmbild av Operations Management Suite-portalen med inställningsikonen markerat](./media/monitor-alerts-extend/oms-portal-settings-option.png) 
4. Från den **inställningar** väljer **aviseringar**.  
5. Välj **utökar till Azure**.
![Skärmbild av Operations Management Suite aviseringsinställningar Portalsida med utöka till Azure markerat](./media/monitor-alerts-extend/ExtendInto.png)
6. En tre steg-guide visas i den **aviseringar** fönstret. Läs översikten och välj **nästa**.
![Skärmbild av steg 1 i guiden](./media/monitor-alerts-extend/ExtendStep1.png)  
7. I det andra steget du se en översikt över föreslagna ändringar med lämpliga [åtgärdsgrupper](monitoring-action-groups.md) för aviseringar. Om liknande åtgärder ses över mer än en avisering, föreslår guiden associera en enda åtgärdsgrupp med alla.  Namngivningskonventionen är följande: *WorkspaceName_AG_ #Number*. Om du vill fortsätta, Välj **nästa**.
![Skärmbild av steg 2 i guiden](./media/monitor-alerts-extend/ExtendStep2.png)  
8. I det sista steget i guiden väljer du **Slutför**, och bekräfta när du uppmanas att starta processen. Du kan också kan du ange en e-postadress så att du meddelas när processen är klar och alla aviseringar har flyttats till Azure-aviseringar.
![Skärmbild av steg 3 i guiden](./media/monitor-alerts-extend/ExtendStep3.png)

När guiden har slutförts på den **aviseringsinställningar** sida alternativet att utöka aviseringar till Azure har tagits bort. Aviseringarna flyttas till Azure i bakgrunden, och detta kan ta lite tid. Under åtgärden, kan du ändra aviseringar från Operations Management Suite-portalen. Du kan se aktuell status från banderoll överst i portalen. Om du tidigare angav en e-postadress, får du ett e-postmeddelande när processen har slutförts.  


Notifieringar fortsätter att visas i Operations Management Suite-portalen, även när de har flyttats till Azure.
![Skärmbild av Operations Management Suite aviseringsinställningar Portalsida](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Alternativ 2: Använd AlertsVersion API
Du kan använda API: et för Log Analytics AlertsVersion utökar aviseringar från Log Analytics till Azure aviseringar från alla klienter som kan anropa REST-API. Du kan komma åt API: et från PowerShell med hjälp av [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod. Du kan spara resultatet i JSON.  

För att använda API: N måste skapa du först en GET-begäran. Detta utvärderar och returnerar en översikt över föreslagna ändringar innan du försöker att utöka till Azure med en POST-begäran. Resultatlistan aviseringar och en föreslagna lista över [åtgärdsgrupper](monitoring-action-groups.md), i JSON-format. Om liknande åtgärder ses över mer än en avisering, föreslår tjänsten att associera dem med en enda åtgärdsgrupp. Namngivningskonventionen är följande: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Om begäran om att hämta lyckas HTTP-statuskoden 200 returneras, tillsammans med en lista över aviseringar och föreslagna åtgärdsgrupper i JSON-data. Följande är ett exempelsvar:

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
                            "serviceUri": "http://test.com"
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
Om den angivna arbetsytan inte har några Varningsregler som definierats returnerar i JSON-data följande:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Om alla Varningsregler i den angivna arbetsytan har redan utökats till Azure, är svar på GET-begäran:

```json
{
    "version": 2
}
```

Initiera en POST-svar för att initiera migrera aviseringarna till Azure. POST-svar bekräftar din avsikt, samt godkännande, om du vill att aviseringar som utökas från Log Analytics till Azure-aviseringar. Aktiviteten och aviseringarna bearbetas så som visas, baserat på resultatet när du har utfört GET-svar tidigare. Du kan också kan du ange en lista över e-postadresser som logganalys skickar en rapport när schemalagda bakgrund migreringsprocessen av aviseringar har slutförts. Du kan använda följande exempel för begäran:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Resultatet av migrera aviseringar i Azure aviseringar kan variera beroende på sammanfattningen som tillhandahålls av GET-svar. Schemalagd aviseringar i logganalys är tillfälligt otillgänglig för redigering i Operations Management Suite-portalen. Du kan dock skapa nya aviseringar. 

Om det lyckas POST-begäran returnerar statusen HTTP 200 OK tillsammans med följande meddelande:

```json
{
    "version": 2
}
```

Svaret anger att aviseringar har har utökats till Azure-aviseringar. Versionsegenskapen är bara för att kontrollera om aviseringar har utökats till Azure och har ingen relation till den [Log Analytics Sök API](../log-analytics/log-analytics-api-alerts.md). Någon e-postadresser angivna med efter begäran skickas en rapport när aviseringar har utökats till Azure har. Om alla aviseringar i den angivna arbetsytan är redan schemalagts utökas, är svaret på POST-begäran att försöket var förbjuden (en 403 statuskod). Om du vill visa felmeddelanden eller förstå om processen har fastnat, kan du skicka en GET-begäran. Om det finns ett felmeddelande, returneras den, tillsammans med en översikt över.

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
                            "serviceUri": "http://test.com"
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


## <a name="option-3-use-a-custom-powershell-script"></a>Alternativ 3: Använd ett anpassat PowerShell.skript
 Om Microsoft inte har utökats aviseringar från Operations Management Suite-portalen till Azure, kan du göra det manuellt till 5 juli 2018. Föregående två avsnitt beskrivs de två alternativen för manuell utökning.

Efter 5 juli 2018, har alla aviseringar från Operations Management Suite-portalen utökats till Azure. Användare som inte har gjort det [nödvändiga steg förslag](#troubleshooting) notifieringar körs utan startar åtgärder eller meddelanden på grund av bristande kommer har associerat [åtgärdsgrupper](monitoring-action-groups.md). 

Att skapa [åtgärdsgrupper](monitoring-action-groups.md) för aviseringar manuellt i logganalys, Använd följande exempelskript:
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
    "Error occured while fetching/parsing Extend summary: $ErrorMessage"
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


### <a name="about-the-custom-powershell-script"></a>Om anpassade PowerShell-skript 
Här följer viktig information om hur du använder skriptet:
- En förutsättning är att installera [ARMclient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager.
- Du måste ha en medarbetare eller ägare roll i Azure-prenumeration om du vill köra skriptet.
- Du måste ange följande parametrar:
    - $subscriptionId: det Azure-prenumerations-ID som är associerade med Operations Management Suite logganalys-arbetsytan.
    - $resourceGroup: Azure-resursgrupp för Operations Management Suite logganalys-arbetsytan.
    - $workspaceName: namnet på Operations Management Suite logganalys-arbetsytan.

### <a name="output-of-the-custom-powershell-script"></a>Utdata från det anpassa PowerShell-skriptet
Skriptet är utförlig och matar ut steg som körs: 
- Den visar en sammanfattning som innehåller information om befintliga Operations Management Suite Log Analytics-aviseringar i arbetsytan. Sammanfattningen innehåller även information om Azure åtgärdsgrupper skapas för de åtgärder som är kopplade till. 
- Du uppmanas att gå vidare med tillägget eller avsluta efter att visa sammanfattningen.
- Om du går vidare med tillägget nya Azure åtgärdsgrupper skapas och alla befintliga aviseringar som är associerade med. 
- Skriptet avslutas genom att visa meddelandet ”filnamnstillägget klar”! Vid eventuella mellanliggande fel visar följande fel i skriptet.

## <a name="troubleshooting"></a>Felsökning 
Under processen med att utöka aviseringar problem kan förhindra att systemet skapar nödvändiga [åtgärdsgrupper](monitoring-action-groups.md). I sådana fall måste du se ett felmeddelande visas i sidhuvudet i den **avisering** avsnittet Operations Management Suite-portalen eller i GET anropa klart-API: et.

> [!IMPORTANT]
> Om du inte vidta följande steg innan den 5 juli 2018 kommer aviseringar att köras i Azure men kommer inte att utlösa en åtgärd eller ett meddelande. Om du vill få meddelanden om aviseringar kan du manuellt redigera och lägga till [åtgärdsgrupper](monitoring-action-groups.md), eller använda den föregående [anpassade PowerShell-skriptet](#option-3---using-custom-powershell-script).

Här följer steg för varje fel:
- **Fel: Scope Lås finns på prenumerationen/resursgruppsnivå för skrivåtgärder**: ![Skärmbild av Operations Management Suite aviseringsinställningar portalsidan, Scope Lås felmeddelande markerat](./media/monitor-alerts-extend/ErrorScopeLock.png)

    När Scope Lock är aktiverat, begränsar funktionen eventuella nya ändringar i prenumerationen eller resursen gruppen som innehåller arbetsytan logganalys (Operations Management Suite). Systemet kan inte utöka aviseringar i Azure och skapa grupper för nödvändiga åtgärder.
    
    Lös genom att ta bort den *ReadOnly* Lås för prenumerationen eller resursen gruppen som innehåller arbetsytan. Du kan göra detta med hjälp av Azure portal, PowerShell, Azure CLI eller API: et. Läs mer i [Lås Resursanvändning](../azure-resource-manager/resource-group-lock-resources.md). 
    
    När du har löst felet med hjälp av stegen som beskrivs i artikeln utökar Operations Management Suite aviseringarna till Azure i nästa dag schemalagda körning. Du behöver inte göra något ytterligare eller starta något.

- **Fel: Principen finns på prenumerationen/resursgruppsnivå**: ![Skärmbild av Operations Management Suite aviseringsinställningar portalsidan, med felmeddelande i princip markerat](./media/monitor-alerts-extend/ErrorPolicy.png)

    När [Azure princip](../azure-policy/azure-policy-introduction.md) är tillämpas, begränsar en ny resurs i en grupp för prenumerationen eller resursen som innehåller arbetsytan logganalys (Operations Management Suite). Systemet kan inte utöka aviseringar i Azure och skapa grupper för nödvändiga åtgärder.
    
    Lös genom att redigera principen som orsakar den *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* fel, vilket förhindrar att skapa nya resurser på din prenumerationen eller resursen grupp som innehåller arbetsytan. Du kan göra detta med hjälp av Azure portal, PowerShell, Azure CLI eller API: et. Du kan granska åtgärder för att hitta den lämpliga principen som orsakar felet. Läs mer i [visa aktivitetsloggar om du vill granska åtgärder](../azure-resource-manager/resource-group-audit.md). 
    
    När du har löst felet med hjälp av stegen som beskrivs i artikeln utökar Operations Management Suite aviseringarna till Azure i nästa dag schemalagda körning. Du behöver inte göra något ytterligare eller starta något.


## <a name="next-steps"></a>Nästa steg

* Mer information om den nya [Azure aviseringar uppstår](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Logga varningar i Azure aviseringar](monitor-alerts-unified-log.md).
