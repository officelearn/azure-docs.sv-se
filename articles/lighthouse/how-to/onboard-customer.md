---
title: Publicera en kund till Azure-delegerad resurs hantering – Azure Lighthouse
description: Lär dig att publicera en kund till Azure-delegerad resurs hantering, så att deras resurser kan nås och hanteras via din egen klient.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 09/19/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 402f82059f939de204e489bb36c2c2b82f816a19
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105280"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Registrera en kund för Azure-delegerad resurshantering

Den här artikeln förklarar hur du, som en tjänst leverantör, kan publicera en kund till Azure-delegerad resurs hantering, så att deras delegerade resurser (prenumerationer och/eller resurs grupper) kan nås och hanteras via din egen Azure Active Directory ( Azure AD-klient. Vi kommer att hänvisa till tjänst leverantörer och kunder här, men företag som hanterar flera klienter kan använda samma process för att konsolidera sin hanterings upplevelse.

Du kan upprepa den här processen om du hanterar resurser för flera kunder. Sedan, när en behörig användare loggar in till din klient, kan den användaren auktoriseras mellan kundens hyres omfång för att utföra hanterings åtgärder utan att behöva logga in på varje enskild kund klient.

Du kan associera ditt Microsoft Partner Network (MPN) ID med dina inbyggda prenumerationer för att spåra din påverkan på kund engagemang och få igenkänning. Mer information finns i [Länka ett partner-ID till dina Azure-konton](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). Observera att du måste utföra den här associationen i din tjänst leverantörs klient.

> [!NOTE]
> Kunder kan registreras automatiskt när de köper ett erbjudande för hanterade tjänster (offentligt eller privat) som du har publicerat på Azure Marketplace. Mer information finns i [publicera Managed Services-erbjudanden på Azure Marketplace](publish-managed-services-offers.md). Du kan också använda onboarding-processen som beskrivs här med ett erbjudande som publicerats på Azure Marketplace.

Onboarding-processen kräver att åtgärder tas från både tjänst leverantörens klient organisation och kundens klient organisation. Alla dessa steg beskrivs i den här artikeln.

> [!IMPORTANT]
> För närvarande kan du inte publicera en prenumeration (eller resurs grupp i en prenumeration) för Azure-delegerad resurs hantering om prenumerationen använder Azure Databricks. Om en prenumeration har registrerats för onboarding med **Microsoft. ManagedServices** Resource Provider kan du inte heller skapa en Databricks-arbetsyta för den prenumerationen just nu.

## <a name="gather-tenant-and-subscription-details"></a>Samla in klient-och prenumerations information

För att kunna publicera en kunds klient måste den ha en aktiv Azure-prenumeration. Du måste känna till följande:

- Klient-ID för tjänst leverantörens klient organisation (där du kommer att hantera kundens resurser)
- Klient-ID för kundens klient organisation (som kommer att ha resurser som hanteras av tjänst leverantören)
- Prenumerations-ID för varje specifik prenumeration i kundens klient organisation som ska hanteras av tjänst leverantören (eller som innehåller de resurs grupper som ska hanteras av tjänst leverantören)

Om du inte redan har den här informationen kan du hämta den på något av följande sätt.

### <a name="azure-portal"></a>Azure Portal

Du kan se ditt klient-ID genom att hovra över ditt konto namn längst upp till höger i Azure Portal, eller genom att välja **Växla katalog**. Om du vill välja och kopiera ditt klient-ID söker du efter "Azure Active Directory" i portalen och väljer sedan **Egenskaper** och kopierar värdet som visas i fältet **katalog-ID** . Om du vill hitta ID: t för en prenumeration söker du efter "prenumerationer" och väljer sedan lämpligt prenumerations-ID.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> När du registrerar en prenumeration (eller en eller flera resurs grupper i en prenumeration) med hjälp av processen som beskrivs här, registreras **Microsoft. ManagedServices** Resource Provider för den prenumerationen.

## <a name="define-roles-and-permissions"></a>Definiera roller och behörigheter

Som tjänst leverantör kanske du vill använda flera erbjudanden med en enda kund, vilket kräver olika åtkomst för olika omfång.

För att förenkla hanteringen rekommenderar vi att du använder Azure AD-användargrupper för varje roll, så att du kan lägga till eller ta bort enskilda användare i gruppen i stället för att tilldela behörigheter direkt till den användaren. Du kanske också vill tilldela roller till ett huvud namn för tjänsten. Se till att du följer principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att utföra sitt jobb, vilket bidrar till att minska risken för oavsiktliga fel. Mer information finns i [rekommenderade säkerhets metoder](../concepts/recommended-security-practices.md).

> [!NOTE]
> Roll tilldelningar måste använda [Inbyggda RBAC-roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)(rollbaserad åtkomst kontroll). Alla inbyggda roller stöds för närvarande med Azure-delegerad resurs hantering förutom för ägare och inbyggda roller med [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) -behörighet. Den inbyggda rollen administratör för användar åtkomst stöds för begränsad användning enligt beskrivningen nedan. Rollerna anpassade roller och [klassisk prenumerations administratör](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) stöds inte heller.

För att kunna definiera auktoriseringar måste du känna till ID-värdena för varje användare, användar grupp eller tjänstens huvud namn som du vill bevilja åtkomst. Du behöver också roll Definitions-ID: t för varje inbyggd roll som du vill tilldela. Om du inte redan har gjort det kan du hämta dem på något av följande sätt.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall

För att publicera kunden måste du skapa en [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) -mall som innehåller följande:

|Fält  |Definition  |
|---------|---------|
|**mspName**     |Namn på tjänst leverantör         |
|**mspOfferDescription**     |En kort beskrivning av ditt erbjudande (till exempel "contoso VM Management-erbjudande")      |
|**managedByTenantId**     |Ditt klient-ID         |
|**auktoriseringar**     |**PrincipalId** -värdena för användare/grupper/SPN från din klient, var och en med en **principalIdDisplayName** för att hjälpa kunden att förstå syftet med auktoriseringen och mappas till ett inbyggt **roleDefinitionId** -värde för att ange åtkomst nivå         |

Om du vill publicera en kunds prenumeration använder du lämplig Azure Resource Manager mall som vi tillhandahåller i våra [exempel lagrings platsen](https://github.com/Azure/Azure-Lighthouse-samples/), tillsammans med en motsvarande parameter fil som du ändrar för att matcha konfigurationen och definiera dina auktoriseringar. Separata mallar tillhandahålls beroende på om du registrerar en hel prenumeration, en resurs grupp eller flera resurs grupper i en prenumeration. Vi tillhandahåller också en mall som kan användas för kunder som har köpt ett hanterat tjänst erbjudande som du har publicerat på Azure Marketplace, om du föredrar att publicera deras prenumerationer på det här sättet.

|**För att publicera detta**  |**Använd den här Azure Resource Manager mallen**  |**Och ändra den här parameter filen** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Flera resurs grupper i en prenumeration   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Prenumeration (när du använder ett erbjudande som publicerats på Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Processen som beskrivs här kräver en separat distribution för varje prenumeration som registreras.
> 
> Separata distributioner krävs också om du registrerar flera resurs grupper inom olika prenumerationer. Att registrera flera resurs grupper i en enda prenumeration kan dock göras i en distribution.

I följande exempel visas en modifierad **resourceProjection. Parameters. JSON** -fil som ska användas för att publicera en prenumeration. Resurs gruppens parameter-filer (som finns i mappen [RG-delegerad-resurs hantering](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) ) liknar varandra, men innehåller även en **rgName** -parameter för att identifiera de enskilda resurs grupper som ska publiceras.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
Den senaste auktoriseringen i exemplet ovan lägger till en **principalId** med rollen administratör för användar åtkomst (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). När du tilldelar den här rollen måste du inkludera egenskapen **delegatedRoleDefinitionIds** och en eller flera inbyggda roller. Användaren som skapas i detta tillstånd kommer att kunna tilldela de här inbyggda rollerna till hanterade identiteter. Observera att inga andra behörigheter som normalt är kopplade till rollen administratör för användar åtkomst gäller för den här användaren.

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuera Azure Resource Manager mallar

När du har uppdaterat parameter filen måste kunden distribuera resurs hanterings mal len i kundens klient organisation som en distribution på prenumerations nivå. En separat distribution krävs för varje prenumeration som du vill publicera till Azure delegerad resurs hantering (eller för varje prenumeration som innehåller resurs grupper som du vill publicera).

> [!IMPORTANT]
> Distributionen måste göras av ett konto som inte är gäst i kundens klient organisation som har den [inbyggda rollen ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) för den prenumeration som registreras (eller som innehåller de resurs grupper som registreras).

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Bekräfta lyckad onboarding

När en kund prenumeration har publicerats till Azure-delegerad resurs hantering kommer användare i tjänste leverantörens klient organisation att kunna se prenumerationen och dess resurser (om de har beviljats åtkomst till den genom processen ovan, antingen individuellt eller som medlem i en Azure AD-grupp med rätt behörigheter). Bekräfta detta genom att kontrol lera att prenumerationen visas på något av följande sätt.  

### <a name="azure-portal"></a>Azure Portal

I tjänst leverantörens klient organisation:

1. Gå till [sidan mina kunder](view-manage-customers.md).
2. Välj **kunder**.
3. Bekräfta att du kan se prenumerationerna med det erbjudande namn som du angav i Resource Manager-mallen.

I kundens klient organisation:

1. Gå till [sidan tjänst leverantörer](view-manage-service-providers.md).
2. Välj **Service Provider-erbjudanden**.
3. Bekräfta att du kan se prenumerationerna med det erbjudande namn som du angav i Resource Manager-mallen.

> [!NOTE]
> Det kan ta några minuter efter att distributionen är klar innan uppdateringarna återspeglas i Azure Portal.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
