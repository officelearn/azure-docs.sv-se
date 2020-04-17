---
title: Registrera en kund för Azure-delegerad resurshantering
description: Lär dig hur du lägger till en kund till Azure-delegerad resurshantering, så att deras resurser kan nås och hanteras via din egen klientorganisation.
ms.date: 04/16/2020
ms.topic: conceptual
ms.openlocfilehash: 22c96d43f3d5f284c2cba995eb33f5f8cd238659
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481696"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Registrera en kund för Azure-delegerad resurshantering

I den här artikeln beskrivs hur du som tjänsteleverantör kan vara ombord på en kund till Azure-delegerad resurshantering, så att deras delegerade resurser (prenumerationer och/eller resursgrupper) kan nås och hanteras via din egen Azure Active Directory-klientorganisation (Azure AD). Även om vi hänvisar till tjänsteleverantörer och kunder här, [kan företag som hanterar flera klienter](../concepts/enterprise.md) använda samma process för att konsolidera sin hanteringsupplevelse.

Du kan upprepa den här processen om du hanterar resurser för flera kunder. När en behörig användare sedan loggar in på din klientorganisation kan den användaren auktoriseras över kundens hyresutrymme för att utföra hanteringsåtgärder utan att behöva logga in på varje enskild kundklient.

Om du vill spåra din påverkan på kundengagemang och få erkännande associerar du ditt MPN-ID (Microsoft Partner Network) med minst ett användarkonto som har åtkomst till var och en av dina inbyggda prenumerationer. Observera att du måste utföra den här associationen i din tjänsteleverantörsklient. För enkelhetens skull rekommenderar vi att du skapar ett huvudkonto för tjänsten i din klientorganisation som är kopplad till ditt MPN-ID och ger det reader-åtkomst till alla kunder som du är ombord på. Mer information finns i [Länka ett partner-ID till dina Azure-konton](../../billing/billing-partner-admin-link-started.md). 

> [!NOTE]
> Kunder kan också vara ombord när de köper ett erbjudande om hanterade tjänster (offentligt eller privat) som du har publicerat på Azure Marketplace. Mer information finns i [Publicera erbjudanden om hanterade tjänster till Azure Marketplace](publish-managed-services-offers.md). Du kan också använda den introduktionsprocess som beskrivs här tillsammans med ett erbjudande som publiceras på Azure Marketplace.

Introduktionsprocessen kräver åtgärder som ska vidtas både inom tjänsteleverantörens klientorganisation och från kundens klientorganisation. Alla dessa steg beskrivs i den här artikeln.

## <a name="gather-tenant-and-subscription-details"></a>Samla in klient- och prenumerationsinformation

För att kunna registrera en kunds klientorganisation måste den ha en aktiv Azure-prenumeration. Du måste veta följande:

- Klient-ID för tjänsteleverantörens klientorganisation (där du kommer att hantera kundens resurser)
- Klient-ID för kundens klientorganisation (som kommer att ha resurser som hanteras av tjänsteleverantören)
- Prenumerations-ID:t för varje specifik prenumeration i kundens klientorganisation som ska hanteras av tjänsteleverantören (eller som innehåller resursgruppen/resursgrupperna som ska hanteras av tjänsteleverantören).

> [!NOTE]
> Även om du bara vill registrera en eller flera resursgrupper i en prenumeration måste distributionen göras på prenumerationsnivå, så du behöver prenumerations-ID.

Om du inte redan har dessa ID-värden kan du hämta dem på något av följande sätt. Var noga med att använda dessa exakta värden i distributionen.

### <a name="azure-portal"></a>Azure Portal

Ditt klient-ID kan ses genom att hovra över ditt kontonamn på den övre högra sidan av Azure-portalen eller genom att välja **Växla katalog**. Om du vill välja och kopiera ditt klient-ID söker du efter "Azure Active Directory" inifrån portalen och väljer sedan Egenskaper och **kopierar** värdet som visas i fältet **Katalog-ID.** Om du vill hitta ID:t för en prenumeration hos kundens klientorganisation söker du efter "Prenumerationer" och väljer sedan lämpligt prenumerations-ID.

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
> När du tecknar en prenumeration (eller en eller flera resursgrupper inom en prenumeration) med hjälp av den process som beskrivs här registreras resursleverantören **Microsoft.ManagedServices** för den prenumerationen.

## <a name="define-roles-and-permissions"></a>Definiera roller och behörigheter

Som tjänsteleverantör kanske du vill utföra flera uppgifter för en enskild kund, vilket kräver olika åtkomst för olika scope. Du kan definiera så många auktoriseringar som du behöver för att tilldela [rollbaserade åtkomstkontroll (RBAC) inbyggda roller](../../role-based-access-control/built-in-roles.md) till användare i din klientorganisation.

För att underlätta hanteringen rekommenderar vi att du använder Azure AD-användargrupper för varje roll, så att du kan lägga till eller ta bort enskilda användare i gruppen i stället för att tilldela behörigheter direkt till den användaren. Du kanske också vill tilldela roller till ett tjänsthuvudnamn. Var noga med att följa principen om lägsta behörighet så att användarna bara har de behörigheter som behövs för att slutföra sitt jobb. Rekommendationer och information om roller som stöds finns i [Klienter, användare och roller i Azure Lighthouse-scenarier](../concepts/tenants-users-roles.md).

> [!IMPORTANT]
> För att kunna lägga till behörigheter för en Azure AD-grupp måste **grupptypen** vara **Säkerhet** och inte **Office 365**. Det här alternativet väljs när gruppen skapas. Mer information finns i [Skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

För att definiera auktoriseringar måste du känna till ID-värdena för varje användare, användargrupp eller tjänsthuvudnamn i den tjänstproviderklient som du vill bevilja åtkomst till. Du behöver också rolldefinitions-ID för varje inbyggd roll som du vill tilldela. Om du inte redan har dem kan du hämta dem genom att köra kommandona nedanifrån från tjänsteleverantörens klientorganisation.

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
> Vi rekommenderar att du tilldelar [rollen Borttagning](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) av hanterade tjänster när en kund finns ombord, så att användare i klienten kan [ta bort åtkomsten till delegeringen](#remove-access-to-a-delegation) senare om det behövs. Om den här rollen inte tilldelas kan delegerade resurser endast tas bort av en användare i kundens klientorganisation.

## <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall

Om du vill vara ombord på kunden måste du skapa en [Azure Resource Manager-mall](../../azure-resource-manager/index.yml) för ditt erbjudande med följande information. Värdena **mspOfferName** och **mspOfferDescription** visas för kunden när de visar erbjudandeinformation på [sidan Tjänstleverantörer](view-manage-service-providers.md) i Azure-portalen.

|Field  |Definition  |
|---------|---------|
|**mspOfferName**     |Ett namn som beskriver den här definitionen. Det här värdet visas för kunden som erbjudandetitel.         |
|**mspOfferDescription**     |En kort beskrivning av ditt erbjudande (till exempel "Contoso VM management offer").      |
|**managedByTenantId**     |Ditt klient-ID.          |
|**Tillstånd**     |**PrincipalId-värdena** för användare/grupper/SPN från din klient, var och en med ett **principalIdDisplayName** för att hjälpa kunden att förstå syftet med auktoriseringen och mappas till ett inbyggt **rollDefinitionId-värde** för att ange åtkomstnivån.      |

Introduktionsprocessen kräver en Azure Resource Manager-mall (som finns i vår [repo)](https://github.com/Azure/Azure-Lighthouse-samples/)och en motsvarande parameterfil som du ändrar för att matcha din konfiguration och definiera dina auktoriseringar.

Vilken mall du väljer beror på om du tecknar en hel prenumeration, en resursgrupp eller flera resursgrupper i en prenumeration. Vi tillhandahåller också en mall som kan användas för kunder som har köpt ett hanterat tjänsterbjudande som du har publicerat på Azure Marketplace, om du föredrar att gå ombord på deras prenumerationer på det här sättet.

|För att gå ombord på detta  |Använda den här Azure Resource Manager-mallen  |Och ändra den här parameterfilen |
|---------|---------|---------|
|Prenumeration   |[delegeradResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegeradResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resursgrupp   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Flera resursgrupper inom en prenumeration   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Prenumeration (när du använder ett erbjudande som publiceras på Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Processen som beskrivs här kräver en separat distribution på prenumerationsnivå för varje prenumeration som är inskriven, även om du tecknar prenumerationer i samma kundklient. Separata distributioner krävs också om du anlöpar flera resursgrupper inom olika prenumerationer i samma kundklient. Inbyggt flera resursgrupper inom en enda prenumeration kan dock göras i en distribution på prenumerationsnivå.
>
> Separata distributioner krävs också för att flera erbjudanden ska tillämpas på samma prenumeration (eller resursgrupper inom en prenumeration). Varje erbjudande som tillämpas måste använda ett annat **mspOfferName**.

I följande exempel visas en **modifierad delegatedResourceManagement.parameters.json-fil** som kan användas för att lägga till en prenumeration. Parameterfilerna för resursgrupp (som finns i mappen [rg-delegated-resource-management)](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) är likartade, men innehåller också en **rgName-parameter** för att identifiera de specifika resursgrupper som ska finnas ombord.

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

Den senaste auktoriseringen i exemplet ovan lägger till en **principalId** med rollen Administratör för användaråtkomst (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). När du tilldelar den här rollen måste du inkludera egenskapen **delegatedRoleDefinitionIds** och en eller flera inbyggda roller. Användaren som skapas i den här auktoriseringen kan tilldela dessa inbyggda roller till [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) i kundklienten, vilket krävs för att [distribuera principer som kan åtgärdas](deploy-policy-remediation.md). Inga andra behörigheter som normalt associeras med rollen Administratör för användaråtkomst gäller för den här användaren.

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuera Azure Resource Manager-mallarna

När du har uppdaterat parameterfilen måste en användare i kundens klient distribuera Azure Resource Manager-mallen i sin klientorganisation som en distribution på prenumerationsnivå. En separat distribution behövs för varje prenumeration som du vill gå ombord på Azure-delegerad resurshantering (eller för varje prenumeration som innehåller resursgrupper som du vill vara ombord).

Eftersom det här är en distribution på prenumerationsnivå kan den inte initieras i Azure-portalen. Distributionen kan göras med hjälp av PowerShell eller Azure CLI, som visas nedan.

> [!IMPORTANT]
> Den här distributionen på prenumerationsnivå måste utföras av ett icke-gästkonto i kundens klientorganisation som har [rollen Ägare inbyggt](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) för prenumerationen som är inbyggt (eller som innehåller de resursgrupper som finns ombord). Om du vill visa alla användare som kan delegera prenumerationen kan en användare i kundens klientorganisation välja prenumerationen i Azure-portalen, öppna **åtkomstkontroll (IAM)** och [visa alla användare med ägarrollen](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
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

## <a name="confirm-successful-onboarding"></a>Bekräfta lyckad introduktion

När en kundprenumeration har lagts ombord på Azure-delegerad resurshantering kan användare i tjänsteleverantörens klientorganisation se prenumerationen och dess resurser (om de har beviljats åtkomst till den via processen ovan, antingen individuellt eller som medlem i en Azure AD-grupp med rätt behörighet). Kontrollera att prenumerationen visas på något av följande sätt för att bekräfta detta.  

### <a name="azure-portal"></a>Azure Portal

I tjänsteleverantörens klient:

1. Navigera till [sidan Mina kunder](view-manage-customers.md).
2. Välj **kunder**.
3. Bekräfta att du kan se prenumerationerna med det erbjudandenamn som du angav i resource manager-mallen.

> [!IMPORTANT]
> För att kunna se den delegerade prenumerationen i [Mina kunder](view-manage-customers.md)måste användare i tjänsteleverantörens klient ha tilldelats [rollen Reader](../../role-based-access-control/built-in-roles.md#reader) (eller en annan inbyggd roll som inkluderar läsaråtkomst) när prenumerationen var inbyggd för Azure-delegerad resurshantering.

I kundens hyresgäst:

1. Navigera till [sidan Tjänstleverantörer](view-manage-service-providers.md).
2. Välj **Service-leverantör erbjuder**.
3. Bekräfta att du kan se prenumerationerna med det erbjudandenamn som du angav i resource manager-mallen.

> [!NOTE]
> Det kan ta några minuter efter att distributionen är klar innan uppdateringarna återspeglas i Azure-portalen.

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

Som standard kan användare i kundens klientorganisation som har rätt behörighet ta bort åtkomst till tjänstprovidern till delegerade resurser [på sidan Tjänstleverantörer](view-manage-service-providers.md#add-or-remove-service-provider-offers) i Azure-portalen. När de gör det kan inga användare i tjänsteleverantörens klient att komma åt de resurser som tidigare delegerats.

Om du har inbyggda användare med [rollen Borttagning av tilldelning](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) av hanterade tjänster när du registrerar en kund för Azure-delegerad resurshantering, kan dessa användare också ta bort delegeringen.

Exemplet nedan visar en tilldelning som beviljar rollen Borttagning av **borttagning av hanterade tjänsters tilldelning** som kan inkluderas i en parameterfil:

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

1. Navigera till [sidan Mina kunder](view-manage-customers.md).
2. Välj **Delegationer**.
3. Leta reda på den delegering som du vill ta bort och markera sedan papperskorgen som visas på raden.

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

- Lär dig mer om [hanteringsupplevelser mellan klienter.](../concepts/cross-tenant-management-experience.md)
- [Visa och hantera kunder](view-manage-customers.md) genom att gå till **Mina kunder** i Azure-portalen.
