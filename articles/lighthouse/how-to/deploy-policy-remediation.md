---
title: Distribuera en princip som kan åtgärdas
description: Lär dig hur du lägger till en kund till Azure-delegerad resurshantering, så att deras resurser kan nås och hanteras via din egen klientorganisation.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: c06ed4ea597808aee18d4a848bcfea7152b9cf8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270646"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Distribuera en princip som kan åtgärdas inom en delegerad prenumeration

[Med Azure Lighthouse](../overview.md) kan tjänstleverantörer skapa och redigera principdefinitioner i en delegerad prenumeration. Om du vill distribuera principer som använder en [reparationsuppgift](../../governance/policy/how-to/remediate-resources.md) (d.v.s. principer med [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) eller [ändra](../../governance/policy/concepts/effects.md#modify) effekt) måste du dock skapa en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) i kundklienten. Den här hanterade identiteten kan användas av Azure Policy för att distribuera mallen i principen. Det finns steg som krävs för att aktivera det här scenariot, både när du ombord på kunden för Azure-delegerad resurshantering och när du distribuerar själva principen.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Skapa en användare som kan tilldela roller till en hanterad identitet i kundklienten

När du ombord på en kund för Azure-delegerad resurshantering använder du en [Azure Resource Manager-mall](onboard-customer.md#create-an-azure-resource-manager-template) tillsammans med en parameterfil som definierar användare, användargrupper och tjänsthuvudnamn i din hantering av klientorganisation som kan komma åt de delegerade resurserna i kundens klientorganisation. I parameterfilen tilldelas var och en av dessa användare (**principalId**) en [inbyggd roll](../../role-based-access-control/built-in-roles.md) (**roleDefinitionId**) som definierar åtkomstnivån.

Om du vill att en **principalId** ska kunna skapa en hanterad identitet i klientklienten måste du ange dess **rollDefinitionId** till **User Access Administrator**. Även om den här rollen i allmänhet inte stöds kan den användas i det här specifika scenariot, vilket gör att användare med den här behörigheten kan tilldela en eller flera specifika inbyggda roller till hanterade identiteter. Dessa roller definieras i egenskapen **delegatedRoleDefinitionIds.** Du kan inkludera alla inbyggda roller här förutom administratör eller ägare av användaråtkomst.

När kunden är ombord kan **principalId som** skapas i den här auktoriseringen tilldela dessa inbyggda roller till hanterade identiteter i kundklienten. De har dock inga andra behörigheter som normalt associeras med rollen administratör för användaråtkomst.

Exemplet nedan visar en **principalId** som har rollen Administratör för användaråtkomst. Den här användaren kan tilldela två inbyggda roller till hanterade identiteter i kundens klientorganisation: Contributor och Log Analytics Contributor.

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

När du har skapat användaren med de behörigheter som krävs enligt beskrivningen ovan kan användaren distribuera principer i kundens klientorganisation som använder reparationsuppgifter.

Anta till exempel att du vill aktivera diagnostik på Azure Key Vault-resurser i kundklienten, vilket illustreras i det här [exemplet](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring). En användare i den hanterande klienten med rätt behörigheter (enligt beskrivningen ovan) skulle distribuera en [Azure Resource Manager-mall](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) för att aktivera det här scenariot.

Observera att skapa principtilldelningen som ska användas med en delegerad prenumeration måste göras för närvarande via API:er, inte i Azure-portalen. När du gör det måste **apiVersion** ställas in på **2019-04-01-preview**, som innehåller den nya **delegatedManagedIdentityResourceId-egenskapen.** Med den här egenskapen kan du inkludera en hanterad identitet som finns i kundklienten (i en prenumeration eller resursgrupp som har lagts ombord på Azure-delegerad resurshantering).

I följande exempel visas en rolltilldelning med en **delegeradManagedIdentityResourceId**.

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
> Ett [liknande exempel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) är tillgängligt för att visa hur du distribuerar en princip som lägger till eller tar bort en tagg (med hjälp av ändringseffekten) till en delegerad prenumeration.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Policy](../../governance/policy/index.yml).
- Lär dig mer om [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).
