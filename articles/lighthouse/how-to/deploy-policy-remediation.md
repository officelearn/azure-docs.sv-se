---
title: Distribuera en princip som kan åtgärdas
description: Lär dig att publicera en kund till Azure-delegerad resurs hantering, så att deras resurser kan nås och hanteras via din egen klient.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: 4522c9ebad741f5ec0cb7e56e68467312ef8f037
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463872"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Distribuera en princip som kan åtgärdas inom en delegerad prenumeration

Med [Azure-Lighthouse](../overview.md) kan tjänst leverantörer skapa och redigera princip definitioner i en delegerad prenumeration. Men om du vill distribuera principer som använder en [reparations uppgift](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) (det vill säga principer med [deployIfNotExists](https://docs.microsoft.com/azure/governance/policy/concepts/effects#deployifnotexists) eller [ändra](https://docs.microsoft.com/azure/governance/policy/concepts/effects#modify) -effekter) måste du skapa en [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) i kund klienten. Den här hanterade identiteten kan användas av Azure Policy för att distribuera mallen i principen. Det finns åtgärder som krävs för att aktivera det här scenariot, både när du registrerar kunden för Azure-delegerad resurs hantering och när du distribuerar själva principen.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Skapa en användare som kan tilldela roller till en hanterad identitet i kund klienten

När du registrerar en kund för Azure-delegerad resurs hantering, använder du en [Azure Resource Manager-mall](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer#create-an-azure-resource-manager-template) tillsammans med en parameter fil som definierar användare, användar grupper och tjänstens huvud namn i den hanterande klienten som kommer att kunna komma åt de delegerade resurserna i kund klienten. I parameter filen tilldelas var och en av dessa användare (**principalId**) en [inbyggd roll](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) (**roleDefinitionId**) som definierar åtkomst nivån.

Om du vill tillåta en **principalId** att skapa en hanterad identitet i kundens klient organisation måste du ange dess **RoleDefinitionId** till **användar åtkomst administratör**. Även om den här rollen inte stöds i allmänhet, kan den användas i det här scenariot, så att användare med den här behörigheten kan tilldela en eller flera angivna inbyggda roller till hanterade identiteter. De här rollerna definieras i egenskapen **delegatedRoleDefinitionIds** . Du kan inkludera valfri inbyggd roll här, förutom administratör för användar åtkomst eller ägare.

När kunden har registrerats kommer **principalId** som skapats i det här tillståndet att kunna tilldela de inbyggda rollerna till hanterade identiteter i kund klienten. De kommer dock inte att ha några andra behörigheter som vanligt vis är associerade med rollen administratör för användar åtkomst.

Exemplet nedan visar en **principalId** som kommer att ha rollen administratör för användar åtkomst. Den här användaren kan tilldela två inbyggda roller till hanterade identiteter i kund klienten: bidrags givare och Log Analytics deltagare.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Distribuera principer som kan åtgärdas

När du har skapat användaren med nödvändiga behörigheter enligt beskrivningen ovan, kan den användaren distribuera principer i kund innehavaren som använder reparations aktiviteter.

Anta till exempel att du vill aktivera diagnostik på Azure Key Vault resurser i kund klienten, som du ser i det här [exemplet](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring). En användare i hanterings klienten med rätt behörigheter (enligt beskrivningen ovan) distribuerar en Azure Resource Manager- [mall](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) för att aktivera det här scenariot.

Observera att när du skapar princip tilldelningen som ska användas med en delegerad prenumeration måste den för närvarande utföras via API: er, inte i Azure Portal. När du gör det måste **API version** anges till **2019-04-01-Preview**, som innehåller den nya **delegatedManagedIdentityResourceId** -egenskapen. Med den här egenskapen kan du ta med en hanterad identitet som finns i kund klient organisationen (i en prenumeration eller resurs grupp som har publicerats till Azure delegerad resurs hantering).

I följande exempel visas en roll tilldelning med en **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> Ett [liknande exempel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) är tillgängligt för att demonstrera hur du distribuerar en princip som lägger till eller tar bort en tagg (med hjälp av ändra-effekter) till en delegerad prenumeration.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure policy](https://docs.microsoft.com/azure/governance/policy/).
- Lär dig mer om [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
