---
title: Skapa och hantera åtgärdsgrupper i Azure portal
description: Lär dig hur du skapar och hanterar åtgärdsgrupper i Azure-portalen.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/08/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 842965aa49ae4cd546fe9c107107d2a2ceebebbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705259"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Skapa och hantera åtgärdsgrupper i Azure portal
En åtgärdsgrupp är en samling av aviseringsinställningarna som definieras av ägaren av en Azure-prenumeration. Azure Monitor och Service Health-aviseringar använda åtgärdsgrupper för att meddela användare att en avisering har utlösts. Olika typer av aviseringar kan använda samma åtgärdsgruppen eller annan åtgärdsgrupper beroende på användarens krav. Du kan konfigurera upp till 2 000 åtgärdsgrupper i en prenumeration.

Du kan konfigurera en åtgärd för att meddela en person med e-post eller SMS, de får en bekräftelse som anger de har lagts till i åtgärdsgruppen.

Den här artikeln visar hur du skapar och hanterar åtgärdsgrupper i Azure-portalen.

Varje åtgärd består av följande egenskaper:

* **Namn på**: En unik identifierare i åtgärdsgruppen.  
* **Åtgärdstyp**: Utföra åtgärden. Exempel är att skicka en röst-anrop, SMS, e-postmeddelandet, eller utlösa olika typer av automatiska åtgärder. Se typer senare i den här artikeln.
* **Information om**: Den motsvarande information som varierar beroende på *åtgärdstyp*.

Information om hur du använder Azure Resource Manager-mallar för att konfigurera åtgärdsgrupper finns i [åtgärd grupp Resource Manager-mallar](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Skapa en grupp med hjälp av Azure-portalen

1. I den [Azure-portalen](https://portal.azure.com)väljer **övervakaren**. Den **övervakaren** fönstret konsoliderar alla dina övervakningsinställningar och -data i en vy.

    ![”Övervakningstjänsten”](./media/action-groups/home-monitor.png)
    
1. Välj **aviseringar** därefter **hanterar åtgärder**.

    ![Hantera åtgärder knappen](./media/action-groups/manage-action-groups.png)
    
1. Välj **Lägg till åtgärdsgrupp**, och Fyll i fälten.

    ![Kommandot ”Lägg till åtgärdsgrupp”](./media/action-groups/add-action-group.png)
    
1. Ange ett namn i den **namn på åtgärdsgrupp** och ange ett namn i den **kortnamnet** box. Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

      ![Dialogrutan Lägg till åtgärdsgrupp ”](./media/action-groups/action-group-define.png)

1. Den **prenumeration** rutan autofills med din aktuella prenumeration. Den här prenumerationen är där åtgärdsgruppen sparas.

1. Välj den **resursgrupp** i åtgärdsgruppen har sparats.

1. Definiera en lista med åtgärder. Ange följande för varje åtgärd:

    1. **Namn på**: Ange en unik identifierare för den här åtgärden.

    1. **Åtgärdstyp**: Välj e-post/SMS/Push/röst, Logikapp, Webhook, ITSM eller Automation-Runbook.

    1. **Information om**: Baserat på typen av, ange ett telefonnummer, e-postadress, webhook URI, Azure-app, ITSM-anslutningen eller Automation-runbook. För ITSM-åtgärden dessutom ange **arbetsobjekt** och kräver att andra fält ITSM-verktyg.
    
    1. **Gemensamma avisering schemat**: Du kan välja att aktivera den [gemensamma avisering schemat](https://aka.ms/commonAlertSchemaDocs), som innehåller fördelen att en enda extensible och enhetlig avisering nyttolast över alla aviseringen tjänster i Azure Monitor.

1. Välj **OK** skapa åtgärdsgruppen.

## <a name="manage-your-action-groups"></a>Hantera din åtgärdsgrupper

När du har skapat en åtgärdsgrupp den syns i den **åtgärdsgrupper** delen av den **övervakaren** fönstret. Välj åtgärdsgrupp som du vill hantera att:

* Lägga till, redigera eller ta bort åtgärder.
* Ta bort åtgärdsgruppen.

## <a name="action-specific-information"></a>Information om specifika

> [!NOTE]
> Se [prenumerationstjänsten för övervakning](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) för numeriska gränser för vart och ett av objekten nedan.  

### <a name="azure-app-push-notifications"></a>Azure-app Push-meddelanden
Du kan ha ett begränsat antal åtgärder för Azure i en åtgärdsgrupp.

### <a name="email"></a>Email
E-postmeddelanden skickas från följande e-postadresser. Kontrollera att din e-filtrering har konfigurerats på rätt sätt
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Du kan ha ett begränsat antal e poståtgärder i en åtgärdsgrupp. Se den [begränsar information frekvensbegränsningen](./../../azure-monitor/platform/alerts-rate-limiting.md) artikeln.

### <a name="itsm"></a>ITSM
ITSM-åtgärden kräver en ITSM-anslutningen. Lär dig hur du skapar en [ITSM-anslutningen](../../azure-monitor/platform/itsmc-overview.md).

Du kan ha ett begränsat antal ITSM-åtgärder i en åtgärdsgrupp. 

### <a name="logic-app"></a>Logikapp
Du kan ha ett begränsat antal Logic App-åtgärder i en åtgärdsgrupp.

### <a name="function"></a>Funktion
Funktionstangenterna för Funktionsappar som har konfigurerats som åtgärder är skrivskyddade via API-funktioner, som för närvarande kräver v2 funktionsappar att konfigurera appinställningen ”AzureWebJobsSecretStorageType” till ”filer”. Mer information finns i [ändras till hantering av nycklar i Functions V2]( https://aka.ms/funcsecrets).

Du kan ha ett begränsat antal funktionen åtgärder i en åtgärdsgrupp.

### <a name="automation-runbook"></a>Automation-Runbook
Referera till den [Azure-prenumerationstjänsten](../../azure-subscription-service-limits.md) för gränser för Runbook-nyttolaster.

Du kan ha ett begränsat antal Runbook-åtgärder i en åtgärdsgrupp. 

### <a name="sms"></a>SMS
Se den [begränsar information frekvensbegränsningen](./../../azure-monitor/platform/alerts-rate-limiting.md) och [SMS Avisera beteende](../../azure-monitor/platform/alerts-sms-behavior.md) ytterligare viktig information.

Du kan ha ett begränsat antal SMS åtgärder i en åtgärdsgrupp.  

### <a name="voice"></a>Röst
Se den [begränsar information frekvensbegränsningen](./../../azure-monitor/platform/alerts-rate-limiting.md) artikeln.

Du kan ha ett begränsat antal röst åtgärder i en åtgärdsgrupp.

### <a name="webhook"></a>Webhook
Webhooks görs med hjälp av följande regler. Webhook-anrop görs ett nytt högst 2 gånger när följande HTTP-Statuskoder returneras: 408, 429, 503, 504, eller om HTTP-slutpunkten inte svarar. Det första återförsöket görs efter 10 sekunder. Andra återförsök sker efter 100 sekunder. Efter två fel anropar inga åtgärdsgrupp slutpunkten i 30 minuter. 

Käll-IP-adressintervall
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

För att få uppdateringar om ändringar av dessa IP-adresser, rekommenderar vi att du konfigurerar en avisering för Tjänsthälsa som övervakar för informationsmeddelanden om tjänsten åtgärdsgrupper.

Du kan ha ett begränsat antal Webhook-åtgärder i en åtgärdsgrupp.

#### <a name="secure-webhook"></a>Säker Webhook
**Skydda Webhook-funktionen finns för närvarande i förhandsversion.**

Åtgärden grupper Webhook-åtgärd kan du dra nytta av Azure Active Directory för säker anslutning mellan din åtgärdsgrupp och din skyddade webb-API (webhook-slutpunkt). Det totala arbetsflödet för att utnyttja den här funktionen beskrivs nedan. En översikt över Azure AD-program och tjänstens huvudnamn finns i [översikt över Microsoft identity-plattformen (v2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Skapa ett Azure AD-program för dina skyddade webb-API. Se https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurera din skyddade API anropas av en daemon-app.
    
1. Aktivera åtgärdsgrupper för att använda ditt Azure AD-program.

    > [!NOTE]
    > Du måste vara medlem i den [administratörsrollen för Azure AD Application](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) att köra det här skriptet.
    
    - Ändra PowerShell-skriptet Connect-AzureAD anrop för att använda Azure AD-klient-ID.
    - Ändra PowerShell-skriptet variabeln $myAzureADApplicationObjectId om du vill använda objekt-ID för din Azure AD-program
    - Kör det ändrade skriptet.
    
1. Konfigurera åtgärden grupp Webhook-åtgärd.
    - Kopiera värdet $myApp.ObjectId från skriptet och Skriv in den i fältet programobjekt-ID i definitionen för Webhook-åtgärd.
    
    ![Säker Webhook-åtgärd](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>Skydda Webhook PowerShell-skript

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


## <a name="next-steps"></a>Nästa steg
* Läs mer om [SMS Avisera beteende](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Få en [förståelse för avisering webhook för aktivitetslogg](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Läs mer om [ITSM-anslutningsprogram](../../azure-monitor/platform/itsmc-overview.md)
* Läs mer om [hastighetsbegränsning](../../azure-monitor/platform/alerts-rate-limiting.md) på aviseringar.
* Hämta en [översikt över aktivitetsloggaviseringar](../../azure-monitor/platform/alerts-overview.md), och lär dig hur du får aviseringar.  
* Lär dig hur du [konfigurera aviseringar när en avisering om tjänstens hälsa publiceras](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
