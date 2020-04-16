---
title: Övervaka Azure AD B2C med Azure Monitor
titleSuffix: Azure AD B2C
description: Lär dig hur du loggar Azure AD B2C-händelser med Azure Monitor med hjälp av delegerad resurshantering.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 99e04c95156e40eed8c2b9aa88a2bee6f39e90c9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392883"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Övervaka Azure AD B2C med Azure Monitor

Använd Azure Monitor för att dirigera Azure Active Directory B2C(Azure AD B2C) inloggnings- och [granskningsloggar](view-audit-logs.md) till olika övervakningslösningar. Du kan behålla loggarna för långvarig användning eller integrera med siem-verktyg (Security Information and Event Management) från tredje part för att få insikter i din miljö.

Du kan dirigera logghändelser till:

* Ett [Azure-lagringskonto](../storage/blobs/storage-blobs-introduction.md).
* En [Azure-händelsenav](../event-hubs/event-hubs-about.md) (och integrera med dina Splunk- och Sumo Logic-instanser).
* En [Log Analytics-arbetsyta](../azure-monitor/platform/resource-logs-collect-workspace.md) (för att analysera data, skapa instrumentpaneler och avisera specifika händelser).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Krav

Om du vill slutföra stegen i den här artikeln distribuerar du en Azure Resource Manager-mall med hjälp av Azure PowerShell-modulen.

* [Azure PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) version 6.13.1 eller senare

Du kan också använda [Azure Cloud Shell](https://shell.azure.com), som innehåller den senaste versionen av Azure PowerShell-modulen.

## <a name="delegated-resource-management"></a>Delegerad resurshantering

Azure AD B2C utnyttjar [Azure Active Directory-övervakning](../active-directory/reports-monitoring/overview-monitoring.md). Om du vill aktivera *diagnostikinställningar* i Azure Active Directory i din Azure AD B2C-klient använder du [delegerad resurshantering](../lighthouse/concepts/azure-delegated-resource-management.md).

Du auktoriserar en användare eller grupp i din Azure AD B2C-katalog **(Tjänsteleverantören)** för att konfigurera Azure Monitor-instansen i klienten som innehåller din Azure-prenumeration **(Kunden**). Om du vill skapa auktoriseringen distribuerar du en [Azure Resource Manager-mall](../azure-resource-manager/index.yml) till din Azure AD-klient som innehåller prenumerationen. Följande avsnitt går igenom processen.

## <a name="create-or-choose-resource-group"></a>Skapa eller välj resursgrupp

Det här är resursgruppen som innehåller mål-Azure-lagringskontot, händelsehubben eller logganalysarbetsytan för att ta emot data från Azure Monitor. Du anger resursgruppsnamnet när du distribuerar Azure Resource Manager-mallen.

[Skapa en resursgrupp](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) eller välj en befintlig i Azure Active Directory (Azure AD) som innehåller din Azure-prenumeration, *inte* katalogen som innehåller din Azure AD B2C-klient.

I det här exemplet används en resursgrupp med namnet *azure-ad-b2c-monitor* i regionen *Centrala USA.*

## <a name="delegate-resource-management"></a>Delegera resurshantering

Samla sedan in följande information:

**Katalog-ID** för din Azure AD B2C-katalog (kallas även klient-ID).

1. Logga in på [Azure-portalen](https://portal.azure.com/) som användare med rollen *Användaradministratör* (eller högre).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Azure Active Directory**, välj **Egenskaper**.
1. Registrera **katalog-ID**.

**Objekt-ID** för Azure AD B2C-gruppen eller användaren som du vill ge *deltagarebehörighet* till resursgruppen som du skapade tidigare i katalogen som innehåller din prenumeration.

För att underlätta hanteringen rekommenderar vi *groups* att du använder Azure AD-användargrupper för varje roll, så att du kan lägga till eller ta bort enskilda användare i gruppen i stället för att tilldela behörigheter direkt till den användaren. I den här genomgången lägger du till en användare.

1. När **Azure Active Directory** fortfarande är markerat i Azure-portalen väljer du **Användare**och väljer sedan en användare.
1. Registrera användarens **objekt-ID**.

### <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall

Om du vill använda din Azure AD-klientorganisation **(kunden)** skapar du en [Azure Resource Manager-mall](../lighthouse/how-to/onboard-customer.md) för ditt erbjudande med följande information. `mspOfferName` Värdena `mspOfferDescription` och är synliga när du visar erbjudandeinformation på [sidan Tjänstleverantörer](../lighthouse/how-to/view-manage-service-providers.md) i Azure-portalen.

| Field   | Definition |
|---------|------------|
| `mspOfferName`                     | Ett namn som beskriver den här definitionen. Azure *AD B2C Managed Services*. Det här värdet visas för kunden som erbjudandetitel. |
| `mspOfferDescription`              | En kort beskrivning av ditt erbjudande. Aktiverar till exempel *Azure Monitor i Azure AD B2C*.|
| `rgName`                           | Namnet på resursgruppen som du skapar tidigare i din Azure AD-klientorganisation. Till exempel *azure-ad-b2c-monitor*. |
| `managedByTenantId`                | **Katalog-ID** för din Azure AD B2C-klientorganisation (kallas även klient-ID). |
| `authorizations.value.principalId` | **Objekt-ID** för B2C-gruppen eller användaren som har åtkomst till resurser i den här Azure-prenumerationen. För den här genomgången anger du användarens objekt-ID som du spelade in tidigare. |

Hämta Mall- och parameterfilerna i Azure Resource Manager:

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Uppdatera sedan parameterfilen med de värden som du spelade in tidigare. Följande JSON-kodavsnitt visar ett exempel på en Azure Resource Manager-mallparametrarfil. Använd `authorizations.value.roleDefinitionId`det [inbyggda rollvärdet](../role-based-access-control/built-in-roles.md) för *rollen* `b24988ac-6180-42a0-ab88-20f7382dd24c`Deltagare för .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Distribuera Azure Resource Manager-mallarna

När du har uppdaterat din parameterfil distribuerar du Azure Resource Manager-mallen till Azure-klienten som en distribution på prenumerationsnivå. Eftersom det här är en distribution på prenumerationsnivå kan den inte initieras i Azure-portalen. Du kan distribuera med hjälp av Azure PowerShell-modulen eller Azure CLI. Azure PowerShell-metoden visas nedan.

Logga in på katalogen som innehåller din prenumeration med [Connect-AzAccount](/powershell/azure/authenticate-azureps). Använd `-tenant` flaggan för att tvinga autentisering till rätt katalog.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Använd cmdleten [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) för att lista de prenumerationer som det aktuella kontot kan komma åt under Azure AD-klienten. Registrera ID:t för den prenumeration som du vill projicera i din Azure AD B2C-klientorganisation.

```PowerShell
Get-AzSubscription
```

Byt sedan till den prenumeration som du vill projicera till Azure AD B2C-klienten:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Distribuera slutligen Mallen och parameterfilerna i Azure Resource Manager som du hämtade och uppdaterade tidigare. Ersätt `Location` `TemplateFile` `TemplateParameterFile` värdena och värdena i enlighet med detta.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

En lyckad distribution av mallen ger utdata som liknar följande (utdata trunkeras för korthet):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

När du har distribuerat mallen kan det ta några minuter innan resursprojektionen har slutförts. Du kan behöva vänta några minuter (vanligtvis inte mer än fem) innan du går vidare till nästa avsnitt för att välja prenumerationen.

## <a name="select-your-subscription"></a>Välj din prenumeration

När du har distribuerat mallen och väntat några minuter på att resursprojektionen ska slutföras associerar du din prenumeration till din Azure AD B2C-katalog med följande steg.

1. **Logga ut** från Azure-portalen om du för närvarande är inloggad. Detta och följande steg görs för att uppdatera dina autentiseringsuppgifter i portalsessionen.
1. Logga in på [Azure-portalen](https://portal.azure.com) med ditt administrativa Azure AD B2C-konto.
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet.
1. Välj den katalog som innehåller din prenumeration.

    ![Växla katalog](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Kontrollera att du har valt rätt katalog och prenumeration. I det här exemplet väljs alla kataloger och prenumerationer.

    ![Alla kataloger som valts i katalogfiltret & prenumeration](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

Diagnostikinställningar definierar var loggar och mått för en resurs ska skickas. Möjliga destinationer är:

- [Azure-lagringskonto](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Lösningar för eventhubbar.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Log Analytics-arbetsyta](../azure-monitor/platform/resource-logs-collect-workspace.md)

Om du inte redan har gjort det skapar du en instans av den valda måltypen i resursgruppen som du angav i [Azure Resource Manager-mallen](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Skapa diagnostikinställningar

Du är redo att [skapa diagnostikinställningar](../active-directory/reports-monitoring/overview-monitoring.md) i Azure-portalen.

Så här konfigurerar du övervakningsinställningar för Azure AD B2C-aktivitetsloggar:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Azure Active Directory**
1. Under **Övervakning** väljer du **Diagnostikinställningar**.
1. Om det finns befintliga inställningar på resursen visas en lista över inställningar som redan har konfigurerats. Välj antingen **Lägg till diagnostikinställning** om du vill lägga till en ny inställning eller **redigera** inställningen för att redigera en befintlig. Varje inställning får inte ha mer än en av måltyperna..

    ![Fönstret Inställningar för diagnostik i Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Ge din inställning ett namn om den inte redan har ett.
1. Markera rutan för varje mål för att skicka loggarna. Välj **Konfigurera om** du vill ange deras inställningar enligt beskrivningen i följande tabell.

    | Inställning | Beskrivning |
    |:---|:---|
    | Arkivera till ett lagringskonto | Namn på lagringskonto. |
    | Strömma till en händelsehubb | Namnområdet där händelsehubben skapas (om det här är första gången du direktuppspelar loggar) eller strömmas till (om det redan finns resurser som strömmar loggkategorin till det här namnområdet).
    | Skicka till Log Analytics | Namn på arbetsyta. |

1. Välj **AuditLogs** och **SignInLogs**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du lägger till och konfigurerar diagnostikinställningar i Azure Monitor finns i [Självstudiekurs: Samla in och analysera resursloggar från en Azure-resurs](../azure-monitor/insights/monitor-azure-resource.md).

Information om hur du streamar Azure AD-loggar till en händelsehubb finns i [Självstudiekurs: Strömma Azure Active Directory-loggar till en Azure-händelsehubb](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
