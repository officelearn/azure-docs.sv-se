---
title: Registrera en kund för Azure-delegerad resurshantering
description: Lär dig att publicera en kund till Azure-delegerad resurs hantering, så att deras resurser kan nås och hanteras via din egen klient.
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 7cf0ff1d64603215a9607f5a25ebc4077f9fa9da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270685"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Registrera en kund för Azure-delegerad resurshantering

Den här artikeln förklarar hur du, som en tjänst leverantör, kan publicera en kund till Azure-delegerad resurs hantering, så att deras delegerade resurser (prenumerationer och/eller resurs grupper) kan nås och hanteras via din egen Azure Active Directory (Azure AD)-klient. Vi kommer att hänvisa till tjänst leverantörer och kunder här, men [företag som hanterar flera klienter](../concepts/enterprise.md) kan använda samma process för att konsolidera sin hanterings upplevelse.

Du kan upprepa den här processen om du hanterar resurser för flera kunder. Sedan, när en behörig användare loggar in till din klient, kan den användaren auktoriseras mellan kundens hyres omfång för att utföra hanterings åtgärder utan att behöva logga in på varje enskild kund klient.

Om du vill spåra din påverkan på kund engagemang och få erkännande, associerar du ditt Microsoft Partner Network (MPN) ID med minst ett användar konto som har åtkomst till var och en av dina inbyggda prenumerationer. Observera att du måste utföra den här associationen i din tjänst leverantörs klient. För enkelhetens skull rekommenderar vi att du skapar ett tjänst huvud namns konto i din klient som är associerat med ditt MPN-ID och ger åtkomst till den till alla kunder du registrerar. Mer information finns i [Länka ett partner-ID till dina Azure-konton](../../billing/billing-partner-admin-link-started.md). 

> [!NOTE]
> Kunder kan också registreras när de köper ett erbjudande för hanterade tjänster (offentligt eller privat) som du har publicerat på Azure Marketplace. Mer information finns i [publicera Managed Services-erbjudanden på Azure Marketplace](publish-managed-services-offers.md). Du kan också använda onboarding-processen som beskrivs här tillsammans med ett erbjudande som publicerats på Azure Marketplace.

Onboarding-processen kräver att åtgärder tas från både tjänst leverantörens klient organisation och kundens klient organisation. Alla dessa steg beskrivs i den här artikeln.

## <a name="gather-tenant-and-subscription-details"></a>Samla in klient-och prenumerations information

För att kunna publicera en kunds klient måste den ha en aktiv Azure-prenumeration. Du måste känna till följande:

- Klient-ID för tjänst leverantörens klient organisation (där du kommer att hantera kundens resurser)
- Klient-ID för kundens klient organisation (som kommer att ha resurser som hanteras av tjänst leverantören)
- Prenumerations-ID: na för varje specifik prenumeration i kundens klient organisation som ska hanteras av tjänst leverantören (eller som innehåller de resurs grupper som ska hanteras av tjänst leverantören).

> [!NOTE]
> Även om du bara vill publicera en eller flera resurs grupper i en prenumeration, måste du utföra distributionen på prenumerations nivå, så att du behöver prenumerations-ID: t.

Om du inte redan har dessa ID-värden kan du hämta dem på något av följande sätt. Se till att du använder de här exakta värdena i distributionen.

### <a name="azure-portal"></a>Azure Portal

Du kan se ditt klient-ID genom att hovra över ditt konto namn längst upp till höger i Azure Portal, eller genom att välja **Växla katalog**. Om du vill välja och kopiera ditt klient-ID söker du efter "Azure Active Directory" i portalen och väljer sedan **Egenskaper** och kopierar värdet som visas i fältet **katalog-ID** . Om du vill hitta ID för en prenumeration i kundens klientorganisation söker du efter ”Prenumerationer” och väljer sedan lämpligt prenumerations-ID.

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

Som tjänst leverantör kanske du vill utföra flera uppgifter för en enskild kund, vilket kräver olika åtkomst för olika omfång. Du kan definiera så många auktoriseringar som du behöver för att tilldela [inbyggda roller för rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/built-in-roles.md) till användare i din klient organisation.

För att förenkla hanteringen rekommenderar vi att du använder Azure AD-användargrupper för varje roll, så att du kan lägga till eller ta bort enskilda användare i gruppen i stället för att tilldela behörigheter direkt till den användaren. Du kanske också vill tilldela roller till ett huvud namn för tjänsten. Se till att du följer principen om minsta behörighet så att användarna bara har de behörigheter som krävs för att utföra jobbet. Rekommendationer och information om vilka roller som stöds finns i [klienter, användare och roller i Azure Lighthouse-scenarier](../concepts/tenants-users-roles.md).

För att kunna definiera auktoriseringar måste du känna till ID-värdena för varje användare, användar grupp eller tjänstens huvud namn i den tjänst leverantörs klient som du vill bevilja åtkomst till. Du behöver också roll Definitions-ID: t för varje inbyggd roll som du vill tilldela. Om du inte redan har gjort det kan du hämta dem genom att köra kommandona nedan inifrån tjänst leverantörens klient organisation.

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
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Vi rekommenderar att du tilldelar [borttagnings rollen för hanterade tjänster för registrering av hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) när du registrerar en kund, så att användare i din klient kan [ta bort åtkomst till delegeringen](#remove-access-to-a-delegation) senare om det behövs. Om den här rollen inte är tilldelad kan delegerade resurser bara tas bort av en användare i kundens klient organisation.

## <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall

För att registrera kunden behöver du skapa en [Azure Resource Manager](../../azure-resource-manager/index.yml)-mall för ditt erbjudande med följande information. Värdena för **mspOfferName** och **mspOfferDescription** visas för kunden när du visar erbjudande information på [sidan tjänst leverantörer](view-manage-service-providers.md) i Azure Portal.

|Field  |Definition  |
|---------|---------|
|**mspOfferName**     |Ett namn som beskriver den här definitionen. Det här värdet visas för kunden som titeln på erbjudandet.         |
|**mspOfferDescription**     |En kort beskrivning av ditt erbjudande (till exempel "contoso VM Management-erbjudande").      |
|**managedByTenantId**     |Ditt klient-ID.          |
|**auktoriseringar**     |**PrincipalId** -värdena för användare/grupper/SPN från din klient, var och en med en **principalIdDisplayName** för att hjälpa kunden att förstå syftet med auktoriseringen och mappas till ett inbyggt **roleDefinitionId** -värde för att ange åtkomst nivå.      |

Onboarding-processen kräver en Azure Resource Manager-mall (tillhandahålls i våra [exempel lagrings platsen](https://github.com/Azure/Azure-Lighthouse-samples/)) och en motsvarande parameter fil som du ändrar för att matcha konfigurationen och definiera dina auktoriseringar.

Vilken mall du väljer beror på om du registrerar en hel prenumeration, en resurs grupp eller flera resurs grupper i en prenumeration. Vi tillhandahåller också en mall som kan användas för kunder som har köpt ett hanterat tjänst erbjudande som du har publicerat på Azure Marketplace, om du föredrar att publicera deras prenumerationer på det här sättet.

|För att publicera detta  |Använd den här Azure Resource Manager mallen  |Och ändra den här parameter filen |
|---------|---------|---------|
|Prenumeration   |[delegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resursgrupp   |[rgDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Flera resurs grupper i en prenumeration   |[multipleRgDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Prenumeration (när du använder ett erbjudande som publicerats på Azure Marketplace)   |[marketplaceDelegatedResourceManagement. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement. Parameters. JSON](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Processen som beskrivs här kräver en separat distribution av prenumerations nivå för varje prenumeration som registreras, även om du registrerar prenumerationer i samma kund klient organisation. Separata distributioner krävs också om du registrerar flera resurs grupper inom olika prenumerationer i samma kund klient organisation. Att registrera flera resurs grupper i en enda prenumeration kan dock göras i en distribution på prenumerations nivå.
>
> Separata distributioner krävs också för att flera erbjudanden ska tillämpas på samma prenumeration (eller resurs grupper inom en prenumeration). Varje erbjudande som tillämpas måste använda en annan **mspOfferName**.

I följande exempel visas en modifierad **delegatedResourceManagement. Parameters. JSON** -fil som kan användas för att publicera en prenumeration. Resurs gruppens parameter-filer (som finns i mappen [RG-delegerad-resurs hantering](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) ) liknar varandra, men innehåller även en **rgName** -parameter för att identifiera de enskilda resurs grupper som ska publiceras.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
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

Den senaste auktoriseringen i exemplet ovan lägger till en **principalId** med rollen administratör för användar åtkomst (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). När du tilldelar den här rollen måste du inkludera egenskapen **delegatedRoleDefinitionIds** och en eller flera inbyggda roller. Användaren som skapas i detta tillstånd kommer att kunna tilldela de här inbyggda rollerna till [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) i kund klienten, vilket krävs för att [distribuera principer som kan åtgärdas](deploy-policy-remediation.md). Inga andra behörigheter som vanligt vis är kopplade till rollen administratör för användar åtkomst gäller för den här användaren.

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuera Azure Resource Manager mallar

När du har uppdaterat parameter filen måste en användare i kundens klient organisation Distribuera Azure Resource Manager-mallen i sin klient organisation som en distribution på prenumerations nivå. En separat distribution krävs för varje prenumeration som du vill publicera till Azure delegerad resurs hantering (eller för varje prenumeration som innehåller resurs grupper som du vill publicera).

Eftersom det här är en distribution på prenumerations nivå kan den inte initieras i Azure Portal. Distributionen kan göras med hjälp av PowerShell eller Azure CLI, som du ser nedan.

> [!IMPORTANT]
> Den här distributionen på prenumerations nivå måste utföras av ett konto som inte är gäst i kundens klient organisation som har den [inbyggda rollen som ägarens inbyggda roll](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) för den prenumeration som registreras (eller som innehåller de resurs grupper som har publicerats). Om du vill se alla användare som kan delegera prenumerationen kan en användare i kundens klient välja prenumerationen i Azure Portal, öppna **åtkomst kontroll (IAM)** och [Visa alla användare med ägar rollen](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

### <a name="powershell"></a>PowerShell

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
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
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

> [!IMPORTANT]
> För att kunna se den delegerade prenumerationen i [Mina kunder](view-manage-customers.md)måste användare i tjänste leverantörens klient organisation ha beviljats rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsar åtkomst) när prenumerationen har registrerats för Azure-delegerad resurs hantering.

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

## <a name="remove-access-to-a-delegation"></a>Ta bort åtkomst till en delegering

Som standard kan användare i kundens klient organisation som har rätt behörigheter ta bort tjänst leverantörens åtkomst till delegerade resurser på [sidan tjänst leverantörer](view-manage-service-providers.md#add-or-remove-service-provider-offers) i Azure Portal. När de gör det kommer inga användare i tjänste leverantörens klient organisation att kunna komma åt de resurser som tidigare har delegerats.

Om du har registrerat användare med [tilldelnings rollen för hanterade tjänster](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) för att ta bort en kund för Azure-delegerad resurs hantering kan användarna även ta bort delegeringen.

Exemplet nedan visar en tilldelning som beviljar **borttagnings rollen för hanterade tjänster** som kan tas med i en parameter fil:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

En användare med den här behörigheten kan ta bort en delegering på något av följande sätt.

### <a name="azure-portal"></a>Azure Portal

1. Gå till [sidan mina kunder](view-manage-customers.md).
2. Välj **delegeringar**.
3. Hitta den delegering som du vill ta bort och välj sedan pappers korgs ikonen som visas på raden.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
