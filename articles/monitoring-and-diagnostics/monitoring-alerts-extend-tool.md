---
title: Hur du utökar aviseringar från loggen Analytcs till Azure | Microsoft Docs
description: Den här artikeln beskriver de verktyg och API som du kan utöka aviseringar från Log Analytics till Azure-aviseringar.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: vinagara
ms.openlocfilehash: 0dce6e6772b4efea90df2e095ac0041641d99061
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763535"
---
# <a name="how-to-extend-alerts-from-log-analytics-into-azure-alerts"></a>Hur du utökar aviseringar från Log Analytics till Azure-aviseringar
Aviseringar i logganalys ersätts med Azure aviseringar och som en del av denna övergång, aviseringar som du konfigurerade i logganalys utökas till Azure.  Om du inte vill vänta tills de automatiskt att flyttas till Azure kan du initiera processen efter något av alternativen:

1. Manuellt från OMS-portalen 
2. Programmässigt med AlertsVersion-API  

> [!NOTE]
> Utöka Microsoft automatiskt aviseringar som skapats i Log Analytics till Azure-aviseringar startar på **14 maj 2018** stegvis tills slutförts. Microsoft kommer att börja planera migrera aviseringarna till Azure från den här dag framåt, och under den här övergången aviseringar kan hanteras från både OMS-portalen och Azure-portalen. Den här processen är icke-förstörande och inte interruptive.  

## <a name="option-1---initiate-from-the-oms-portal"></a>Alternativ 1 - initiera från OMS-portalen
Följande steg beskriver hur du utökar aviseringar för arbetsytan från OMS-portalen.  

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Välj en arbetsyta i fönstret logganalys prenumerationer och välj sedan den **OMS-portalen** panelen.<br><br> ![Loggsökningsknapp](./media/monitor-alerts-extend/azure-portal-01.png)<br><br> 
3. När du omdirigeras till OMS-portalen klickar du på panelen inställningar på den översta högra sidan på sidan.<br><br> ![OMS-portalen inställningsalternativ](./media/monitor-alerts-extend/oms-portal-settings-option.png)<br><br> 
4. Från den **inställningar** väljer **aviseringar**.  
5. Klicka på knappen **utöka till Azure**.<br><br> ![OMS aviseringsinställningar Portalsida med utöka alternativet](./media/monitor-alerts-extend/ExtendInto.png)
6. En guide visas i fönstret med först av tre steg som tillhandahåller en översikt över processen.  Klicka på **nästa** att fortsätta.<br><br> ![Utöka aviseringar från Log Analytics till Azure - steg 1](./media/monitor-alerts-extend/ExtendStep1.png)  
7. I det andra steget presenteras en översikt över föreslagna ändringar med lämpliga [åtgärdsgrupper](monitoring-action-groups.md) för aviseringar. Om liknande åtgärder ses över mer än en avisering förslag tjänsten ska associeras med alla en enda åtgärdsgrupp.  Grupp föreslagna följer namnkonventionen: *WorkspaceName_AG_ #Number*. Fortsätt genom att klicka på **nästa**.<br><br> ![Utöka aviseringar från Log Analytics till Azure - steg 2](./media/monitor-alerts-extend/ExtendStep2.png)  
8. I det sista steget i guiden klickar du på **Slutför** och bekräfta när du uppmanas att starta processen.  Du kan också kan du ange en e-postadresser så att du meddelas när processen är klar och alla aviseringar har flyttats till Azure-aviseringar.<br><br> ![Utöka aviseringar från Log Analytics till Azure - steg3](./media/monitor-alerts-extend/ExtendStep3.png)

När guiden har slutförts kan du se på den **aviseringsinställningar** sida alternativet att utöka aviseringar till Azure har tagits bort.  Aviseringarna flyttas till Azure i bakgrunden, och detta kan ta lite tid.  Under åtgärden, kommer du inte att kunna göra ändringar i aviseringar från OMS-portalen.  Aktuell status visas från banderoll överst i portalen och om du tidigare angav en e-postadress får du ett e-postmeddelande när processen har slutförts.  


Notifieringar fortsätter att visas i OMS-portalen, även när de har flyttats till Azure.<br><br> ![När du har flyttat aviseringar i Log Analytics till Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-the-alertsversion-api"></a>Alternativ 2 - med AlertsVersion API
Du kan använda API: et för Log Analytics AlertsVersion utökar aviseringar från Log Analytics till Azure aviseringar från alla klienter som kan anropa REST-API. Du kan komma åt från PowerShell med [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager. Användning av ARMClient och PowerShell är en av många alternativ för att få åtkomst till API: et.  Med API kommer skriver resultatet i JSON.  

För att använda API: N måste skapa du först en GET-begäran som ska utvärdera och returnera en sammanfattning av de föreslagna ändringarna innan du försöker att utöka till Azure med hjälp av en POST-begäran. Resultatlistan aviseringarna och föreslagna lista över [åtgärdsgrupper](monitoring-action-groups.md) i JSON-format.  Om liknande åtgärder ses över mer än en avisering förslag tjänsten kopplar dem till en enda åtgärdsgrupp.  Åtgärdsgrupper föreslagna följer namnkonventionen: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Om begäran om att hämta lyckas, returneras en HTTP-statuskod 200 tillsammans med en lista över aviseringar och föreslagna åtgärdsgrupper i JSON-data. Följande är ett exempelsvar:

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
Om den angivna arbetsytan inte har några Varningsregler definierats tillsammans med HTTP 200 OK statusen returnerar koden för åtgärden hämta JSON-data:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Om alla Varningsregler i den angivna arbetsytan redan har utökats till Azure - är svar på GET-begäran:

```json
{
    "version": 2
}
```

Initiera en POST-svar för att initiera migrera aviseringarna till Azure. POST-svar bekräftar din avsikt samt godkännande om du vill att aviseringar som utökas från logganalys till Auzre aviseringar.  Aktiviteten och aviseringarna bearbetas som anges baserat på resultatet när du har utfört GET-svar tidigare.  Du kan också kan du ange en lista över e-postadresser som e logganalys en rapport när schemalagda bakgrund migreringsprocessen av aviseringar har slutförts.  Detta utförs med hjälp av följande exempel för begäran:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Resultatet av migrera aviseringar i Azure aviseringar kan variera beroende på sammanfattningen som tillhandahålls av GET-svar.  När schemat, blir aviseringar i logganalys tillfälligt otillgänglig för redigering/ändring i OMS-portalen.  Dock kan du skapa nya aviseringar. 

Om det lyckas POST-begäran returnerar statusen HTTP 200 OK tillsammans med följande meddelande:

```json
{
    "version": 2
}
```

Svaret anger att aviseringar har har utökats till Azure-aviseringar. Versionsegenskapen är bara för att kontrollera om aviseringar har utökats till Azure och har ingen relation till den [Log Analytics Sök API](../log-analytics/log-analytics-api-alerts.md). När aviseringar har utökats till Azure har, postadresser alla-e-angivna med efter begäran skickas en rapport med information om ändringar som utförs.  Om alla aviseringar i arbetsytan angivna är redan schemalagts utökas, är svaret på POST-begäran en 403 status kod betydelse försöket är förbjuden. Om du vill visa felmeddelanden eller förstå om processen har fastnat kan du skicka en GET-begäran och ett felmeddelande om den finns, returneras tillsammans med en översikt över.

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


## <a name="option-3---using-custom-powershell-script"></a>Alternativ 3 - med hjälp av anpassade PowerShell-skript
 När du har kanske 14, 2018 - om Microsoft inte har har utökats aviseringarna från OMS-portalen till Azure; sedan tills **5 juli 2018** -användare manuellt kan göra samma via [alternativ 1 - Via GUI](#option-1---initiate-from-the-oms-portal) eller [alternativ 2 - Via API](#option-2---using-the-alertsversion-api).

Efter **5 juli 2018** -alla aviseringar från OMS-portalen utökas till Azure. Användare som inte har gjort det [nödvändiga steg förslag](#troubleshooting), notifieringar körs utan startar åtgärder eller meddelanden på grund av bristande kommer har associerat [åtgärdsgrupper](monitoring-action-groups.md). 

Att manuellt skapa [åtgärdsgrupper](monitoring-action-groups.md) för aviseringar i logganalys användare kan använda skriptet exemplet nedan.
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

    
        # ARM call to create action group
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


**Använda anpassade PowerShell-skript** 
- Krav är installationen av [ARMclient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager
- Användaren som kör dessa skript har medarbetare eller ägare roll i Azure-prenumeration
- Följande är parametrar måste anges för skriptet:
    - $subscriptionId: det Azure-prenumerations-ID som är kopplade till arbetsytan OMS/LA
    - $resourceGroup: Azure-resursgrupp där ligger arbetsytan OMS/LA
    - $workspaceName: namnet på arbetsytan OMS/LA

**Anpassade PowerShell-skriptets utdata** skriptet är utförlig och kommer att bli stegen som körs. 
- Sammanfattningen, som innehåller information om befintliga OMS/LA aviseringar i arbetsytan och Azure åtgärdsgrupper skapas för de åtgärder som är kopplade till dem visas. 
- Användaren uppmanas att gå vidare med tillägget eller avsluta efter att visa sammanfattningen.
- Om användaren uppmanas för att gå vidare med tillägget, nya åtgärdsgrupper i Azure-kommer att skapas och alla befintliga aviseringar kommer att associeras med dem. 
- I slutändan skriptet avslutas genom att visa meddelandet ”tillägget slutföra!”. Vid eventuella mellanliggande fel visas efterföljande fel.

## <a name="troubleshooting"></a>Felsökning 
Under processen med att utöka aviseringar från OMS i Azure, det kan vara tillfälliga problem som förhindrar att skapa nödvändiga [åtgärdsgrupper](monitoring-action-groups.md). I sådana fall visas ett felmeddelande i OMS-portalen via banderoll i aviseringen och GET-anrop som utfärdat till API: T.

> [!WARNING]
> Om användaren inte tar precribed reparation stegen nedan innan **5 juli 2018** - sedan aviseringar kommer att köras i Azure men utan startar alla åtgärder och meddelanden. Om du vill få meddelanden om aviseringar, måste användare manuellt redigera och lägga till [åtgärdsgrupper](monitoring-action-groups.md) eller använda den [anpassade PowerShell-skript](#option-3---using-custom-powershell-script) som anges ovan.

Nedan visas steg för varje fel:
1. **Fel: Scope Lås finns på prenumerationen/resursgruppsnivå för skrivåtgärder**: ![OMS aviseringsinställningar Portalsida ScopeLock felmeddelande](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. När Scope låsa är aktiverat, att begränsa nya ändringar prenumeration eller resursgrupp som innehåller arbetsytan logganalys (OMS); systemet kan inte utöka (kopiera) aviseringar i Azure och skapa grupper för nödvändiga åtgärder.
    
    b. Lös genom att ta bort den *ReadOnly* Lås för prenumerationen eller resursen gruppen som innehåller arbetsytan; med Azure-portalen, Powershell, Azure CLI eller API. Mer information läser du artikeln på [Lås Resursanvändning](../azure-resource-manager/resource-group-lock-resources.md). 
    
    c. När löst enligt stegen som beskrivs i artikeln kommer OMS utökar aviseringarna till Azure i nästa dag schemalagda körning; utan att någon åtgärd eller initiering.

2. **Fel: Principen finns på prenumerationen/resursgruppsnivå**: ![OMS aviseringsinställningar Portalsida med princip felmeddelande](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. När [Azure princip](../azure-policy/azure-policy-introduction.md) används, att begränsa en ny resurs i prenumeration eller resursgrupp som innehåller arbetsytan logganalys (OMS); systemet kan inte utöka (kopiera) aviseringar i Azure och skapa grupper för nödvändiga åtgärder.
    
    b. Lös genom att redigera principen orsakar *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* fel, vilket förhindrar att skapa nya resurser på din prenumerationen eller resursen grupp som innehåller arbetsytan. Med hjälp av Azure portal, Powershell, Azure CLI eller API; Du kan granska åtgärder för att hitta den lämpliga principen som orsakar felet. Mer information läser du artikeln på [visa aktivitetsloggar om du vill granska åtgärder](../azure-resource-manager/resource-group-audit.md). 
    
    c. När löst enligt stegen som beskrivs i artikeln kommer OMS utökar aviseringarna till Azure i nästa dag schemalagda körning; utan att någon åtgärd eller initiering.


## <a name="next-steps"></a>Nästa steg

* Mer information om den nya [Azure aviseringar uppstår](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Logga varningar i Azure aviseringar](monitor-alerts-unified-log.md).
