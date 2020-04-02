---
title: Skapa och hantera åtgärdsgrupper i Azure-portalen
description: Lär dig hur du skapar och hanterar åtgärdsgrupper i Azure-portalen.
author: dkamstra
ms.topic: conceptual
ms.date: 2/18/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 9bc191bb27ebb0bac631ef5cfa8ddc34bbd8214e
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520892"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Skapa och hantera åtgärdsgrupper i Azure-portalen
En åtgärdsgrupp är en samling meddelandeinställningar som definieras av ägaren till en Azure-prenumeration. Azure Monitor och Service Health-aviseringar använder åtgärdsgrupper för att meddela användarna att en avisering har utlösts. Olika aviseringar kan använda samma åtgärdsgrupp eller olika åtgärdsgrupper beroende på användarens krav. Du kan konfigurera upp till 2 000 åtgärdsgrupper i en prenumeration.

Du konfigurerar en åtgärd för att meddela en person via e-post eller SMS, de får en bekräftelse som anger att de har lagts till i åtgärdsgruppen.

Den här artikeln visar hur du skapar och hanterar åtgärdsgrupper i Azure-portalen.

Varje åtgärd består av följande egenskaper:

* **Namn**: En unik identifierare i åtgärdsgruppen.  
* **Åtgärdstyp**: Åtgärden utförs. Exempel är att skicka ett röstsamtal, SMS, e-post; eller utlösa olika typer av automatiserade åtgärder. Se typer senare i den här artikeln.
* **Detaljer:** Motsvarande information som varierar beroende på *åtgärdstyp*.

Information om hur du använder Azure Resource Manager-mallar för att konfigurera åtgärdsgrupper finns i [Mallar för Resurshanteraren för åtgärdsgrupper](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Skapa en åtgärdsgrupp med hjälp av Azure-portalen

1. Sök efter och välj **Övervaka**i [Azure-portalen](https://portal.azure.com). **Övervakningsfönstret** konsoliderar alla dina övervakningsinställningar och data i en vy.

1. Välj **Aviseringar** och sedan **Hantera åtgärder**.

    ![Knappen Hantera åtgärder](./media/action-groups/manage-action-groups.png)
    
1. Välj **Lägg till åtgärdsgrupp**och fyll i fälten.

    ![Kommandot "Lägg till åtgärdsgrupp"](./media/action-groups/add-action-group.png)
    
1. Ange ett namn i rutan **Namn på åtgärdsgrupp** och ange ett namn i rutan **Kortnamn.** Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

      ![Dialogrutan Lägg till åtgärdsgrupp"](./media/action-groups/action-group-define.png)

1. **Prenumerationslådan** fylls i automatiskt med din aktuella prenumeration. Den här prenumerationen är den prenumeration där åtgärdsgruppen sparas.

1. Välj den **resursgrupp** där åtgärdsgruppen sparas.

1. Definiera en lista över åtgärder. Ange följande för varje åtgärd:

    1. **Namn**: Ange en unik identifierare för den här åtgärden.

    1. **Åtgärdstyp:** Välj Automation Runbook, Azure-funktion, Azure Resource Manager-roll e-post,E-post/SMS/Push/Voice, ITSM, Logic App, Secure Webhook, Webhook.

    1. **Information:** Baserat på åtgärdstypen anger du ett telefonnummer, en e-postadress, webhook URI, Azure-app, ITSM-anslutning eller Automation-runbook. För ITSM-åtgärd anger du dessutom **arbetsobjekt** och andra fält som ITSM-verktyget kräver.
    
    1. **Gemensamt varningsschema:** Du kan välja att aktivera det [gemensamma varningsschemat](https://aka.ms/commonAlertSchemaDocs), vilket ger fördelen av att ha en enda utökningsbar och enhetlig varningsnyttolast över alla varningstjänster i Azure Monitor.

1. Välj **OK** om du vill skapa åtgärdsgruppen.

## <a name="manage-your-action-groups"></a>Hantera dina åtgärdsgrupper

När du har skapat en åtgärdsgrupp kan du visa **åtgärdsgrupper** genom att välja **Hantera åtgärder** från målsidan **För aviseringar** i **fönstret Övervaka.** Välj den åtgärdsgrupp som du vill hantera:

* Lägga till, redigera eller ta bort åtgärder.
* Ta bort åtgärdsgruppen.

## <a name="action-specific-information"></a>Åtgärdsspecifik information

> [!NOTE]
> Se [Begränsningar för prenumerationstjänst för övervakning](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) för numeriska gränser för var och en av objekten nedan.  

### <a name="automation-runbook"></a>Körningsbok för automatisering
Se [Azure-prenumerationstjänstgränserna](../../azure-resource-manager/management/azure-subscription-service-limits.md) för begränsningar för runbook-nyttolaster.

Du kan ha ett begränsat antal Runbook-åtgärder i en åtgärdsgrupp. 

### <a name="azure-app-push-notifications"></a>Push-meddelanden för Azure-appen
Du kan ha ett begränsat antal Azure-appåtgärder i en åtgärdsgrupp.

### <a name="email"></a>E-post
E-postmeddelanden kommer att skickas från följande e-postadresser. Se till att e-postfiltreringen är konfigurerad på rätt sätt
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Du kan ha ett begränsat antal e-poståtgärder i en åtgärdsgrupp. Se artikeln [för hastighetsbegränsningsinformation.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>Azure Resource Manager-roll via e-post
Skicka e-post till medlemmarna i prenumerationens roll. E-post skickas endast till **Azure AD-användare** medlemmar i rollen. E-post skickas inte till Azure AD-grupper eller tjänsthuvudnamn.

Du kan ha ett begränsat antal e-poståtgärder i en åtgärdsgrupp. Se artikeln [för hastighetsbegränsningsinformation.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="function"></a>Funktion
Anropar en befintlig HTTP-utlösarslutpunkt i [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Du kan ha ett begränsat antal funktionsåtgärder i en åtgärdsgrupp.

### <a name="itsm"></a>ITSM
ITSM-åtgärd kräver en ITSM-anslutning. Lär dig hur du skapar en [ITSM-anslutning](../../azure-monitor/platform/itsmc-overview.md).

Du kan ha ett begränsat antal ITSM-åtgärder i en åtgärdsgrupp. 

### <a name="logic-app"></a>Logikapp
Du kan ha ett begränsat antal Logic App-åtgärder i en åtgärdsgrupp.

### <a name="secure-webhook"></a>Säker Webhook
Med åtgärden Åtgärdsgrupper webhook kan du dra nytta av Azure Active Directory för att skydda anslutningen mellan din åtgärdsgrupp och ditt skyddade webb-API (webhook-slutpunkt). Det övergripande arbetsflödet för att dra nytta av den här funktionen beskrivs nedan. En översikt över Azure AD-program och tjänsthuvudnamn finns i [översikt över Microsoft identity platform (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)

1. Skapa ett Azure AD-program för ditt skyddade webb-API. Se https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurera ditt skyddade API så att det anropas av en daemonapp.
    
1. Aktivera åtgärdsgrupper för att använda ditt Azure AD-program.

    > [!NOTE]
    > Du måste vara medlem i [azure AD Application Administrator-rollen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) för att kunna köra skriptet.
    
    - Ändra PowerShell-skriptets Connect-AzureAD-anrop för att använda ditt Azure AD-klient-ID.
    - Ändra Variabeln PowerShell-skriptets variabel $myAzureADApplicationObjectId för att använda objekt-ID:et för ditt Azure AD-program
    - Kör det ändrade skriptet.
    
1. Konfigurera åtgärden Säker webbkrok för åtgärdsgruppen.
    - Kopiera värdet $myApp.ObjectId från skriptet och ange det i fältet Programobjekt-ID i webhook-åtgärdsdefinitionen.
    
    ![Säker Webhook-åtgärd](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Säkert Webhook PowerShell-skript

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Se [hastighetsbegränsningsinformation](./../../azure-monitor/platform/alerts-rate-limiting.md) och [SMS-varningsbeteende](../../azure-monitor/platform/alerts-sms-behavior.md) för ytterligare viktig information.

Du kan ha ett begränsat antal SMS-åtgärder i en åtgärdsgrupp.  

### <a name="voice"></a>Röst
Se artikeln [för hastighetsbegränsningsinformation.](./../../azure-monitor/platform/alerts-rate-limiting.md)

Du kan ha ett begränsat antal röståtgärder i en åtgärdsgrupp.

### <a name="webhook"></a>Webhook
Webhooks görs på nytt med hjälp av följande regler. Webhook-anropet görs på nytt högst 2 gånger när följande HTTP-statuskoder returneras: 408, 429, 503, 504 eller HTTP-slutpunkten svarar inte. Det första återförsöket görs efter 10 sekunder. Det andra återförsöket sker efter 100 sekunder. Efter två fel anropar ingen åtgärdsgrupp slutpunkten i 30 minuter. 

IP-adressintervall för källa
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Om du vill ta emot uppdateringar om ändringar i dessa IP-adresser rekommenderar vi att du konfigurerar en servicehälsovarning som övervakar informationsmeddelanden om tjänsten Åtgärdsgrupper.

Du kan ha ett begränsat antal Webhook-åtgärder i en åtgärdsgrupp.



## <a name="next-steps"></a>Nästa steg
* Läs mer om [SMS-varningsbeteende](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Få en [förståelse för schemat för aktivitetsloggaviseringswebbhook](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Läs mer om [ITSM Connector](../../azure-monitor/platform/itsmc-overview.md)
* Läs mer om [hastighetsbegränsning](../../azure-monitor/platform/alerts-rate-limiting.md) på aviseringar.
* Få en översikt över aviseringar för [aktivitetsloggar](../../azure-monitor/platform/alerts-overview.md)och lär dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurerar aviseringar när ett hälsomeddelande för tjänsten publiceras](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
