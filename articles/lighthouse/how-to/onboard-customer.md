---
title: Publicera delegerad en kund till Azure resource Manager – Azure fyr
description: Lär dig hur att publicera en kund till Azure delegerad resurshantering, så att deras resurser att nås och hanteras via din egen klientorganisation.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 1885a6220f14de234710b6980b5d3b6a6172bb7e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809864"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Publicera en kund till Azure delegerad resurshantering

Den här artikeln förklarar hur du, som en tjänsteleverantör kan registrera en kund att Azure delegerade resource Manager, så att deras delegerade resurser (prenumerationer eller resursgrupper) för att nås och hanteras via din egen Azure Active Directory ( Azure AD)-klient. Vi ska referera till leverantörer och kunder här, kan företag hantera flera klienter använda samma process för att konsolidera sina hanteringsmiljö.

Du kan upprepa den här processen om du hanterar resurser för flera kunder. Sedan, när en behörig användare loggar in på din klient, som användaren kan auktoriseras i kundens innehavare omfattningar att utföra hanteringsåtgärder utan att behöva logga in på varje kunds klient.

Du kan koppla ditt Microsoft Partner Network (MPN)-ID med dina integrerats prenumerationer och spåra din inverkan i arbetet med kunder. Mer information finns i [länka ett partner-ID till dina Azure-konton](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

> [!NOTE]
> Kunder kan ska publiceras automatiskt när de köper ett hanterade tjänster-erbjudande (offentliga eller privata) som du har publicerat på Azure Marketplace. Mer information finns i [publicera hanterade tjänster som erbjuder Azure Marketplace](publish-managed-services-offers.md). Du kan också använda onboarding-processen som beskrivs här med ett erbjudande som publicerats på Azure Marketplace.

Onboarding-processen kräver åtgärder vidtas från båda tjänstleverantörens klient och från kundens klient. Alla dessa steg beskrivs i den här artikeln.

> [!IMPORTANT]
> För närvarande kan du inte publicera en prenumeration eller resursgrupp i en prenumeration för Azure delegerad resurshantering om prenumerationen använder Azure Databricks. På samma sätt, om en prenumeration har registrerats för integrering med den **Microsoft.ManagedServices** resursprovider, du kan inte skapa en Databricks-arbetsyta för den aktuella prenumerationen just nu.

## <a name="gather-tenant-and-subscription-details"></a>Samla in information om klient och prenumeration

Att publicera en kunds klient måste ha en aktiv Azure-prenumeration. Du behöver känna till följande:

- Klient-ID för tjänstleverantörens klient (där du ska hantera kundens resurser)
- Klient-ID för kundens klient (som har resurser som hanteras av leverantören)
- Prenumerations-ID för varje specifik prenumeration i kundens klient som ska hanteras av tjänstleverantören (eller som innehåller resursgrupperna som ska hanteras av leverantören)

Om du inte redan har den här informationen kan hämta du den på något av följande sätt.

### <a name="azure-portal"></a>Azure Portal

Ditt klient-ID kan ses genom att hovra över namnet på ditt konto längst upp till höger i Azure portal eller genom att välja **växla katalog**. Markera och kopiera ditt klient-ID, söker du efter ”Azure Active Directory” från i portalen och välj **egenskaper** och kopiera värdet som visas i den **katalog-ID** fält. För att hitta ID för en prenumeration, Sök efter ”prenumerationer” och väljer sedan rätt prenumerations-ID.

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


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>Se till att kundens prenumeration har registrerats för registrering

Varje prenumeration måste auktoriseras för onboarding genom att manuellt registrera den **Microsoft.ManagedServices** resursprovidern. Kunden kan registrera en prenumeration genom att följa anvisningarna i [Azure resursproviders och resurstyper](../../azure-resource-manager/resource-manager-supported-services.md).

Kunden kan bekräfta att prenumerationen är redo för integrering på något av följande sätt.

### <a name="azure-portal"></a>Azure Portal

1. Välj prenumerationen i Azure-portalen.
1. Välj **resursprovidrar**.
1. Bekräfta att **Microsoft.ManagedServices** som **registrerad**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Detta bör returnera resultat liknar följande:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show –namespace "Microsoft.ManagedServices" –-output table
```

Detta bör returnera resultat liknar följande:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>Definiera roller och behörigheter

En tjänstleverantör, kan du använda flera erbjudanden med en enda kund, kräver olika åtkomst för olika omfång.

För att underlätta hantering, bör du använda Azure AD-användargrupper för varje roll, så att du kan lägga till eller ta bort enskilda användare i gruppen i stället för att tilldela behörigheter direkt till den användaren. Du kanske också vill tilldela roller till ett huvudnamn för tjänsten. Var noga med att följa principen om lägsta behörighet så att användarna bara har de behörigheter som krävs för att slutföra arbetet, vilket minskar risken för oavsiktliga fel. Mer information finns i [rekommenderade säkerhetsmetoder för](../concepts/recommended-security-practices.md).

> [!NOTE]
> Rolltilldelningar måste använda rollbaserad åtkomstkontroll (RBAC) [inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Alla inbyggda roller stöds för närvarande med Azure delegerade resource Manager förutom ägare och alla inbyggda roller med [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) behörighet. Den inbyggda rollen Administratör för användaråtkomst har stöd för begränsad användning enligt beskrivningen nedan. Anpassade roller och [administratörsroller för klassiska prenumerationer](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) stöds också inte.

För att definiera auktoriseringar måste veta ID-värden för varje användare, grupp eller tjänstens huvudnamn som du vill bevilja åtkomst. Du måste också rolldefinitions-ID för varje inbyggd roll som du vill tilldela. Om du inte redan har dem, kan du hämta dem på något av följande sätt.



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
az ad group list –-query "[?displayName == '<yourGroupName>'].objectId" –-output tsv

# To retrieve the objectId for an Azure AD user
az ad user show –-upn-or-object-id "<yourUPN>" –-query "objectId" –-output tsv

# To retrieve the objectId for an SPN
az ad sp list –-query "[?displayName == '<spDisplayName>'].objectId" –-output tsv

# To retrieve role definition IDs
az role definition list –-name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall

Att publicera din kund måste du skapa en [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) mall som innehåller följande:

|Fält  |Definition  |
|---------|---------|
|**mspName**     |Providernamn för tjänsten         |
|**mspOfferDescription**     |En kort beskrivning av ditt erbjudande (till exempel ”Contoso VM management erbjudandet”)      |
|**managedByTenantId**     |Ditt klient-ID         |
|**Auktoriseringar**     |Den **principalId** värden för användare/grupper/SPN från din klient, var och en med en **principalIdDisplayName** att hjälpa kunderna att förstå syftet med auktoriseringen och mappas till en inbyggd **roleDefinitionId** värde som anger vilken åtkomstnivå         |

Att publicera en kunds prenumeration använder du lämplig Azure Resource Manager-mall som vi tillhandahåller i vår [exempel lagringsplatsen](https://github.com/Azure/Azure-Lighthouse-samples/), tillsammans med en motsvarande parameterfil som du kan ändra för att matcha din konfiguration och definiera din auktoriseringar. Separata mallar tillhandahålls beroende på om du är en hela prenumerationen, en resursgrupp eller flera resursgrupper inom en prenumeration. Vi ger även en mall som kan användas för kunder som köpte ett erbjudande med hanterad tjänst som du har publicerat på Azure Marketplace om du föredrar att publicera sina prenumerationer på så sätt.

|**Att publicera det här**  |**Använd den här Azure Resource Manager-mallen**  |**Och ändra den här parameterfilen** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Flera resursgrupper inom en prenumeration   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Prenumeration (om med ett erbjudande som publicerats till Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Processen som beskrivs här kräver en separat distribution för varje prenumeration som det gäller.
> 
> Separata distributioner krävs också om du är flera resursgrupperna i olika prenumerationer. Dock onboarding flera resursgrupper i en enskild prenumeration kan göras i en distribution.

I följande exempel visar en ändrad **resourceProjection.parameters.json** fil som ska användas för att publicera en prenumeration. Resursfiler för grupp-parametern (finns i den [rg-delegerad--resurshantering](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) mapp) liknar varandra, men också innehålla en **rgName** parametern för att identifiera den specifika resursgrupper som värdar för att vara har integrerats.

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
Senaste auktorisering i exemplet ovan lägger till en **principalId** med rollen Administratör för användaråtkomst (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). När du tilldelar den här rollen måste du inkludera den **delegatedRoleDefinitionIds** egenskap och en eller flera inbyggda roller. Användaren som skapades i detta tillstånd kommer att kunna tilldela dessa inbyggda roller till hanterade identiteter. Observera att inga andra behörigheter som normalt förknippas med rollen Administratör för användaråtkomst gäller för den här användaren.

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuera Azure Resource Manager-mallar

När du har uppdaterat parameterfilen, måste kunden distribuera Resource Manager-mall i kundens klient som en prenumerationsnivå-distribution. En separat distribution krävs för varje prenumeration som du vill publicera till Azure delegerade resource Manager (eller för varje prenumeration som innehåller resursgrupper som du vill komma igång).

> [!IMPORTANT]
> Distributionen måste utföras av en icke-gästkonto i kundens klient som har den [inbyggda ägarrollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) för den prenumeration som det gäller (eller som innehåller resursgrupper som det gäller).

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

När en kundprenumeration har integrerats på Azure delegerade resurshantering, kommer användare i tjänsteleverantörens klient att kunna se prenumerationen och dess resurser (om de har beviljats åtkomst till den via processen ovan, antingen individuellt eller som en medlem i en Azure AD-grupp med rätt behörighet). För att bekräfta detta, kontrollera att prenumerationen som visas i något av följande sätt.  

### <a name="azure-portal"></a>Azure Portal

I tjänsteleverantörens klient:

1. Navigera till den [sidan Mina kunder](view-manage-customers.md).
2. Välj **kunder**.
3. Bekräfta att du kan se prenumerationerna med erbjudandet namnet du angav i Resource Manager-mallen.

I kundens klient:

1. Navigera till den [providers Tjänstesida](view-manage-service-providers.md).
2. Välj **tjänsten tjänstleverantören erbjuder**.
3. Bekräfta att du kan se prenumerationerna med erbjudandet namnet du angav i Resource Manager-mallen.

> [!NOTE]
> Det kan ta några minuter när distributionen är klar innan uppdateringarna syns i Azure-portalen.

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

- Lär dig mer om [mellan klientorganisationer hanteringsupplevelser](../concepts/cross-tenant-management-experience.md).
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure-portalen.
