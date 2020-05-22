---
title: Skapa och hantera åtgärds grupper i Azure Portal
description: Lär dig hur du skapar och hanterar åtgärds grupper i Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 4/17/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 8075574556375b7c07de2abd6c5aff792880b497
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738826"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Skapa och hantera åtgärds grupper i Azure Portal
En åtgärds grupp är en samling aviserings inställningar som definieras av ägaren av en Azure-prenumeration. Azure Monitor-och Service Health-aviseringar använder åtgärds grupper för att meddela användare om att en avisering har utlösts. Olika aviseringar kan använda samma åtgärds grupp eller olika åtgärds grupper beroende på användarens krav. Du kan konfigurera upp till 2 000 åtgärds grupper i en prenumeration.

Du konfigurerar en åtgärd för att meddela en person via e-post eller SMS, och de får en bekräftelse som anger att de har lagts till i åtgärds gruppen.

Den här artikeln visar hur du skapar och hanterar åtgärds grupper i Azure Portal.

Varje åtgärd består av följande egenskaper:

* **Namn**: en unik identifierare i åtgärds gruppen.  
* **Åtgärds typ**: åtgärden har utförts. Exempel på detta är att skicka ett röst samtal, SMS, e-post; eller utlöser olika typer av automatiserade åtgärder. Se typer längre fram i den här artikeln.
* **Information**: motsvarande information som varierar beroende på *Åtgärds typ*.

Information om hur du använder Azure Resource Manager mallar för att konfigurera åtgärds grupper finns i [Åtgärds grupp Resource Manager-mallar](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Skapa en åtgärds grupp med hjälp av Azure Portal

1. Sök efter och välj **övervaka**i [Azure Portal](https://portal.azure.com). I **övervaknings** fönstret samlas alla övervaknings inställningar och data i en vy.

1. Välj **Aviseringar** och sedan **Hantera åtgärder**.

    ![Knappen hantera åtgärder](./media/action-groups/manage-action-groups.png)
    
1. Välj **Lägg till åtgärds grupp**och fyll i fälten.

    ![Kommandot Lägg till åtgärds grupp](./media/action-groups/add-action-group.png)
    
1. Ange ett namn i rutan **Åtgärds grupp namn** och ange ett namn i rutan **kort namn** . Det korta namnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

      ![Dialog rutan Lägg till åtgärds grupp](./media/action-groups/action-group-define.png)

1. **Prenumerations** rutan fylls med den aktuella prenumerationen. Den här prenumerationen är den som åtgärds gruppen sparas i.

1. Välj den **resurs grupp** som åtgärds gruppen ska sparas i.

1. Definiera en lista med åtgärder. Ange följande för varje åtgärd:

    1. **Namn**: Ange en unik identifierare för den här åtgärden.

    1. **Åtgärds typ**: Välj Automation-Runbook, Azure Function, e-Azure Resource Manager roll, e-post/SMS/push/röst, ITSM, Logic app, säker webhook, webhook.

    1. **Information**: baserat på åtgärds typ anger du ett telefonnummer, en e-postadress, en webhook-URI, en Azure-app, en ITSM-anslutning eller en Automation-Runbook. För ITSM-åtgärd anger du även **arbets objekt** och andra fält som ditt ITSM-verktyg kräver.
    
    1. **Vanligt aviserings schema**: du kan välja att aktivera det [vanliga aviserings schemat](https://aka.ms/commonAlertSchemaDocs), vilket ger fördelen att ha en enda utöknings bar och enhetlig aviserings nytto last för alla aviserings tjänster i Azure Monitor.

1. Välj **OK** för att skapa åtgärds gruppen.

## <a name="manage-your-action-groups"></a>Hantera dina åtgärds grupper

När du har skapat en åtgärds grupp kan du Visa **Åtgärds grupper** genom att välja **Hantera åtgärder** på sidan **aviserings** landning i **övervaknings** fönstret. Välj den åtgärds grupp som du vill hantera för att:

* Lägg till, redigera eller ta bort åtgärder.
* Ta bort åtgärds gruppen.

## <a name="action-specific-information"></a>Åtgärds information

> [!NOTE]
> Se [begränsningar för prenumerations tjänsten för övervakning](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) av numeriska gränser för varje objekt nedan.  

### <a name="automation-runbook"></a>Automation-Runbook
Se begränsningar för [Azure-prenumerations tjänsten](../../azure-resource-manager/management/azure-subscription-service-limits.md) för begränsningar i Runbook-nyttolaster.

Du kan ha ett begränsat antal Runbook-åtgärder i en åtgärds grupp. 

### <a name="azure-app-push-notifications"></a>Push-meddelanden i Azure App
Du kan ha ett begränsat antal Azure App-åtgärder i en åtgärds grupp.

### <a name="email"></a>E-post
E-postmeddelanden kommer att skickas från följande e-postadresser. Kontrol lera att e-postfiltreringen är korrekt konfigurerad
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Du kan ha ett begränsat antal e-poståtgärder i en åtgärds grupp. Se artikeln [rate relimiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>E-post till Azure Resource Manager-rollen
Skicka e-post till medlemmarna i prenumerationens roll. E-post skickas endast till **användar medlemmar i Azure AD** i rollen. E-post kommer inte att skickas till Azure AD-grupper eller tjänstens huvudnamn.

Du kan ha ett begränsat antal e-poståtgärder i en åtgärds grupp. Se artikeln [rate relimiting information](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="function"></a>Funktion
Anropar en befintlig HTTP trigger-slutpunkt i [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Du kan ha ett begränsat antal funktions åtgärder i en åtgärds grupp.

### <a name="itsm"></a>ITSM
ITSM-åtgärden kräver en ITSM-anslutning. Lär dig hur du skapar en [ITSM-anslutning](../../azure-monitor/platform/itsmc-overview.md).

Du kan ha ett begränsat antal ITSM-åtgärder i en åtgärds grupp. 

### <a name="logic-app"></a>Logikapp
Du kan ha ett begränsat antal Logic app-åtgärder i en åtgärds grupp.

### <a name="secure-webhook"></a>Säker webhook
Med åtgärden åtgärds grupper webhook kan du dra nytta av Azure Active Directory för att skydda anslutningen mellan din åtgärds grupp och din skyddade webb-API (webhook-slutpunkt). Det övergripande arbets flödet för att dra nytta av den här funktionen beskrivs nedan. En översikt över Azure AD-program och tjänst huvud namn finns i [Översikt över Microsoft Identity Platform (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Skapa ett Azure AD-program för ditt skyddade webb-API. Se https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurera ditt skyddade API så att det anropas av en daemon-app.
    
1. Aktivera åtgärds grupper för att använda Azure AD-programmet.

    > [!NOTE]
    > Du måste vara medlem i [rollen Azure AD-programadministratör](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) för att köra det här skriptet.
    
    - Ändra PowerShell-skriptets Connect-AzureAD-anrop för att använda ditt Azure AD-klient-ID.
    - Ändra PowerShell-skriptets variabel $myAzureADApplicationObjectId att använda objekt-ID: t för ditt Azure AD-program
    - Kör det ändrade skriptet.
    
1. Konfigurera åtgärds gruppens säkra webhook-åtgärd.
    - Kopiera värdet $myApp. ObjectId från skriptet och ange det i fältet program objekt-ID i definition av webhook-åtgärd.
    
    ![Säker webhook-åtgärd](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Secure webhook PowerShell-skript

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
Mer viktig information finns i [frekvens begränsa information](./../../azure-monitor/platform/alerts-rate-limiting.md) och [SMS-aviseringar](../../azure-monitor/platform/alerts-sms-behavior.md) . 

Du kan ha ett begränsat antal SMS-åtgärder i en åtgärds grupp.

> [!NOTE]
> Om användar gränssnittet Azure Portal åtgärds grupp inte tillåter att du väljer din landskod, stöds SMS inte för ditt land/din region.  Om lands-/region koden inte är tillgänglig kan du rösta för att få ditt land/region tillagt i [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). Under tiden är ett arbete runt att låta din åtgärds grupp anropa en webhook till en tredjeparts-SMS-provider med stöd i ditt land/din region.  

Priser för länder/regioner som stöds finns på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Röst
Mer viktig information finns i artikeln om [pris begränsning](./../../azure-monitor/platform/alerts-rate-limiting.md) .

Du kan ha ett begränsat antal röst åtgärder i en åtgärds grupp.

> [!NOTE]
> Om användar gränssnittet Azure Portal åtgärds grupp inte tillåter att du väljer landskod, stöds inte röst samtal för ditt land/din region. Om lands-/region koden inte är tillgänglig kan du rösta för att få ditt land/region tillagt i [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Under tiden är ett arbete runt att be din åtgärds grupp att anropa en webhook till en röst samtals leverantör från tredje part med support i ditt land/din region.  

Priser för länder/regioner som stöds finns på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook
Webhook-försök görs med följande regler. Webhook-anropet görs om högst 2 gånger när följande HTTP-status koder returneras: 408, 429, 503, 504 eller HTTP-slutpunkten svarar inte. Det första återförsöket görs efter 10 sekunder. Det andra återförsöket sker efter 100 sekunder. Efter två haveri anropar ingen åtgärds grupp slut punkten i 30 minuter. 

Käll-IP-adressintervall
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

Om du vill ta emot uppdateringar om ändringar av dessa IP-adresser rekommenderar vi att du konfigurerar en Service Health avisering, som övervakar informations meddelanden om tjänsten åtgärds grupper.

Du kan ha ett begränsat antal webhook-åtgärder i en åtgärds grupp.



## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [SMS-aviserings beteende](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Få en [förståelse för aktivitets logg aviseringens webhook-schema](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Läs mer om [ITSM-anslutningsprogram](../../azure-monitor/platform/itsmc-overview.md)
* Läs mer om [hastighets begränsning](../../azure-monitor/platform/alerts-rate-limiting.md) av aviseringar.
* Få en [Översikt över aktivitets logg aviseringar](../../azure-monitor/platform/alerts-overview.md)och lär dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurerar aviseringar när ett meddelande om tjänst hälsa har publicerats](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
