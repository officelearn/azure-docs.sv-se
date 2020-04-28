---
title: Övervaka Azure AD B2C med Azure Monitor
titleSuffix: Azure AD B2C
description: Lär dig hur du loggar Azure AD B2C händelser med Azure Monitor med hjälp av delegerad resurs hantering.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392883"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Övervaka Azure AD B2C med Azure Monitor

Använd Azure Monitor för att dirigera Azure Active Directory B2C (Azure AD B2C) inloggnings-och [gransknings](view-audit-logs.md) loggar till olika övervaknings lösningar. Du kan behålla loggarna för långsiktig användning eller integrera med SIEM-verktyg från tredje part för att få insikter om din miljö.

Du kan dirigera logg händelser till:

* Ett Azure [Storage-konto](../storage/blobs/storage-blobs-introduction.md).
* En Azure [Event Hub](../event-hubs/event-hubs-about.md) (och integrera med dina Splunk-och Sumo Logic-instanser).
* En [Log Analytics arbets yta](../azure-monitor/platform/resource-logs-collect-workspace.md) (för att analysera data, skapa instrument paneler och avisering om vissa händelser).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här artikeln distribuerar du en Azure Resource Manager-mall med hjälp av modulen Azure PowerShell.

* [Azure PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) version 6.13.1 eller högre

Du kan också använda [Azure Cloud Shell](https://shell.azure.com), som innehåller den senaste versionen av modulen Azure PowerShell.

## <a name="delegated-resource-management"></a>Delegerad resurs hantering

Azure AD B2C utnyttjar [Azure Active Directory övervakning](../active-directory/reports-monitoring/overview-monitoring.md). Om du vill aktivera *diagnostikinställningar* i Azure Active Directory i Azure AD B2C klienten använder du [delegerad resurs hantering](../lighthouse/concepts/azure-delegated-resource-management.md).

Du auktoriserar en användare eller grupp i Azure AD B2Cs katalogen ( **tjänst leverantören**) för att konfigurera Azure Monitor-instansen i klienten som innehåller din Azure-prenumeration ( **kunden**). För att skapa auktoriseringen distribuerar du en [Azure Resource Manager](../azure-resource-manager/index.yml) -mall till Azure AD-klienten som innehåller prenumerationen. I följande avsnitt får du stegvisa anvisningar genom processen.

## <a name="create-or-choose-resource-group"></a>Skapa eller Välj en resurs grupp

Det här är resurs gruppen som innehåller mål Azure Storage-kontot, händelsehubben eller Log Analytics arbets yta för att ta emot data från Azure Monitor. Du anger resurs gruppens namn när du distribuerar Azure Resource Manager-mallen.

[Skapa en resurs grupp](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) eller Välj en befintlig i den Azure Active Directory (Azure AD) som innehåller din Azure-prenumeration, *inte* den katalog som innehåller din Azure AD B2C-klient.

I det här exemplet används en resurs grupp med namnet *Azure-AD-B2C-Monitor* i den *centrala regionen USA* .

## <a name="delegate-resource-management"></a>Delegera resurs hantering

Sedan samlar du in följande information:

**Katalog-ID** för din Azure AD B2Cs katalog (även kallat klient-ID).

1. Logga in på [Azure Portal](https://portal.azure.com/) som en användare med rollen *användar administratör* (eller högre).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. Välj **Azure Active Directory**, Välj **Egenskaper**.
1. Registrera **katalog-ID: t**.

**Objekt-ID** för den Azure AD B2C grupp eller användare som du vill ge *deltagar* behörighet till resurs gruppen som du skapade tidigare i katalogen som innehåller din prenumeration.

För att förenkla hanteringen rekommenderar vi att du använder Azure AD-användargrupper *för varje* roll, så att du kan lägga till eller ta bort enskilda användare i gruppen i stället för att tilldela behörigheter direkt till den användaren. I den här genom gången lägger du till en användare.

1. Med **Azure Active Directory** fortfarande markerat i Azure Portal väljer du **användare**och väljer sedan en användare.
1. Registrera användarens **objekt-ID**.

### <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager mall

Om du vill publicera din Azure AD-klient ( **kunden**) skapar du en [Azure Resource Manager mall](../lighthouse/how-to/onboard-customer.md) för ditt erbjudande med följande information. Värdena `mspOfferName` och `mspOfferDescription` visas när du visar erbjudande information på [sidan tjänst leverantörer](../lighthouse/how-to/view-manage-service-providers.md) i Azure Portal.

| Field   | Definition |
|---------|------------|
| `mspOfferName`                     | Ett namn som beskriver den här definitionen. Till exempel *Azure AD B2C hanterade tjänster*. Det här värdet visas för kunden som titeln på erbjudandet. |
| `mspOfferDescription`              | En kort beskrivning av ditt erbjudande. Till exempel kan *Azure Monitor i Azure AD B2C*.|
| `rgName`                           | Namnet på den resurs grupp som du skapade tidigare i Azure AD-klienten. Till exempel *Azure-AD-B2C-Monitor*. |
| `managedByTenantId`                | **Katalog-ID** för Azure AD B2C klient organisationen (även kallat klient-ID). |
| `authorizations.value.principalId` | **Objekt-ID** för den B2C grupp eller användare som ska ha åtkomst till resurser i den här Azure-prenumerationen. I den här genom gången anger du användarens objekt-ID som du registrerade tidigare. |

Ladda ned Azure Resource Manager mall och parameterstyrda filer:

- [rgDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Sedan uppdaterar du parameter filen med de värden du registrerade tidigare. Följande JSON-kodfragment visar ett exempel på en fil med parametrar för Azure Resource Manager-mall. För `authorizations.value.roleDefinitionId`använder du det [inbyggda roll](../role-based-access-control/built-in-roles.md) svärdet för *deltagar rollen* `b24988ac-6180-42a0-ab88-20f7382dd24c`.

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

### <a name="deploy-the-azure-resource-manager-templates"></a>Distribuera Azure Resource Manager mallar

När du har uppdaterat parameter filen distribuerar du Azure Resource Manager-mallen till Azure-klienten som en distribution på prenumerations nivå. Eftersom det här är en distribution på prenumerations nivå kan den inte initieras i Azure Portal. Du kan distribuera med hjälp av Azure PowerShell-modulen eller Azure CLI. Metoden Azure PowerShell visas nedan.

Logga in på katalogen som innehåller din prenumeration genom att använda [Connect-AzAccount](/powershell/azure/authenticate-azureps). Använd- `-tenant` flaggan för att tvinga autentisering till rätt katalog.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Använd cmdleten [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) för att visa en lista över de prenumerationer som det aktuella kontot kan komma åt under Azure AD-klienten. Registrera ID: t för den prenumeration som du vill projicera i Azure AD B2C-klienten.

```PowerShell
Get-AzSubscription
```

Växla sedan till den prenumeration som du vill projicera till Azure AD B2C klient organisationen:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Slutligen distribuerar du Azure Resource Manager mall och de parameter-filer som du laddade ned och uppdaterade tidigare. Ersätt värdena `Location`, `TemplateFile`och `TemplateParameterFile` enligt detta.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

Lyckad distribution av mallen ger utdata som liknar följande (utdata trunkerade för det kortfattat):

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

När du har distribuerat mallen kan det ta några minuter innan resurs projektionen har slutförts. Du kan behöva vänta några minuter (vanligt vis högst fem) innan du går vidare till nästa avsnitt för att välja prenumerationen.

## <a name="select-your-subscription"></a>Välj din prenumeration

När du har distribuerat mallen och väntat några minuter tills resurs projektionen har slutförts, associerar du din prenumeration till din Azure AD B2C katalog med följande steg.

1. **Logga ut** från Azure Portal om du är inloggad. Detta och följande steg är gjorda för att uppdatera dina autentiseringsuppgifter i Portal sessionen.
1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure AD B2C-administratörs konto.
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält.
1. Välj den katalog som innehåller din prenumeration.

    ![Växla katalog](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Kontrol lera att du har valt rätt katalog och prenumeration. I det här exemplet är alla kataloger och prenumerationer markerade.

    ![Alla kataloger som valts i katalog & prenumerations filter](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

Diagnostiska inställningar definierar var loggar och mått för en resurs ska skickas. Möjliga destinationer är:

- [Azure Storage-konto](../azure-monitor/platform/resource-logs-collect-storage.md)
- Lösningar för [Event Hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md) .
- [Log Analytics-arbetsyta](../azure-monitor/platform/resource-logs-collect-workspace.md)

Om du inte redan har gjort det skapar du en instans av din valda måltyp i resurs gruppen som du angav i [mallen Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Skapa diagnostikinställningar

Du är redo att [skapa diagnostikinställningar](../active-directory/reports-monitoring/overview-monitoring.md) i Azure Portal.

Konfigurera övervaknings inställningar för Azure AD B2C aktivitets loggar:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. Välj **Azure Active Directory**
1. Under **Övervakning** väljer du **Diagnostikinställningar**.
1. Om det finns befintliga inställningar på resursen visas en lista över inställningar som redan har kon figurer ATS. Välj antingen **Lägg till diagnostisk inställning** för att lägga till en ny inställning eller **Redigera** inställning för att redigera en befintlig. Varje inställning får inte ha fler än en av varje mål typ..

    ![Fönstret diagnostikinställningar i Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Ange ett namn för inställningen om det inte redan har en.
1. Markera kryss rutan för varje mål för att skicka loggarna. Välj **Konfigurera** för att ange inställningarna enligt beskrivningen i följande tabell.

    | Inställning | Beskrivning |
    |:---|:---|
    | Arkivera till ett lagringskonto | Namn på lagrings konto. |
    | Strömma till en händelsehubb | Namn området där Event Hub skapas (om det här är din första gången strömnings loggar) eller strömmas till (om det redan finns resurser som är strömmande till den här namn rymden).
    | Skicka till Log Analytics | Namn på arbets yta. |

1. Välj **AuditLogs** och **SignInLogs**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du lägger till och konfigurerar diagnostikinställningar i Azure Monitor finns i [Självstudier: samla in och analysera resurs loggar från en Azure-resurs](../azure-monitor/insights/monitor-azure-resource.md).

Information om hur du strömmar Azure AD-loggar till en Event Hub finns i [självstudie: strömma Azure Active Directory loggar till en Azure Event Hub](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
