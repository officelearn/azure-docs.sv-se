---
title: Hantera Azure blockchain-tjänsten med hjälp av Azure CLI
description: Hantera Azure blockchain-tjänsten med Azure CLI
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 4b41bc44c9efbf71621fcfba06e668f42caa0f8c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076935"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Hantera Azure blockchain-tjänsten med hjälp av Azure CLI

Förutom Azure Portal kan du använda Azure CLI för att hantera blockchain-medlemmar och Transaction-noder för Azure blockchain-tjänsten.

Kontrol lera att du har installerat den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) och loggat in på ett Azure-konto i med `az login` .

I följande exempel ersätter `<parameter names>` du exempel med dina egna värden.

## <a name="create-blockchain-member"></a>Skapa blockchain-medlem

Exempel skapar en blockchain-medlem i Azure blockchain-tjänsten som kör kvorumet redovisnings protokoll i ett nytt konsortium.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. |
| **Namn** | Ett unikt namn som identifierar din Azure blockchain service blockchain-medlem. Namnet används för den offentliga slut punktens adress. Till exempel `myblockchainmember.blockchain.azure.com`. |
| **sökvägen** | Azure-region där blockchain-medlemmen skapas. Till exempel `eastus`. Välj den plats som är närmast dina användare eller dina andra Azure-program. |
| **lösenord** | Medlems kontots lösen ord. Medlems kontots lösen ord används för att autentisera till blockchain-medlemsens offentliga slut punkt med hjälp av grundläggande autentisering. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkla citat tecken (), bindestreck (-) och semicolumn (;)|
| **protokollhanterare** | Den offentliga för hands versionen stöder kvorum. |
| **Consortium** | Namnet på konsortiet att ansluta till eller skapa. |
| **consortiumManagementAccountPassword** | Lösen ordet för konsortiets hantering. Lösen ordet används för att ansluta till ett konsortium. |
| **ruleName** | Regel namn för vit listning av ett IP-adressintervall. Valfri parameter för brand Väggs regler.|
| **startIpAddress** | Början av IP-adressintervallet för vit listning. Valfri parameter för brand Väggs regler. |
| **endIpAddress** | Slutet av IP-adressintervallet för vit listning. Valfri parameter för brand Väggs regler. |
| **skuName** | Nivå typ. Använd S0 för standard och B0 för Basic. |

## <a name="change-blockchain-member-password"></a>Ändra blockchain medlems lösen ord

Exempel ändringar av en blockchain-medlems lösen ord.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. |
| **Namn** | Namn som identifierar din Azure blockchain service-medlem. |
| **lösenord** | Medlems kontots lösen ord. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkelt citat tecken (), bindestreck (-) och semikolon (;). |

## <a name="create-transaction-node"></a>Skapa Transaction-nod

Skapa en Transaction-nod i en befintlig blockchain-medlem. Genom att lägga till Transaction Nodes kan du öka säkerhets isoleringen och distribuera belastningen. Du kan till exempel ha en Transaction Node-slutpunkt för olika klient program.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. |
| **Namn** | Namnet på Azure blockchain-tjänstens blockchain-medlem som också innehåller det nya namnet på Transaction-noden. |
| **sökvägen** | Azure-region där blockchain-medlemmen skapas. Till exempel `eastus`. Välj den plats som är närmast dina användare eller dina andra Azure-program. |
| **lösenord** | Transaktions nodens lösen ord. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkelt citat tecken (), bindestreck (-) och semikolon (;). |
| **ruleName** | Regel namn för vit listning av ett IP-adressintervall. Valfri parameter för brand Väggs regler. |
| **startIpAddress** | Början av IP-adressintervallet för vit listning. Valfri parameter för brand Väggs regler. |
| **endIpAddress** | Slutet av IP-adressintervallet för vit listning. Valfri parameter för brand Väggs regler.|

## <a name="change-transaction-node-password"></a>Ändra lösen ord för Transaction Node

Exempel ändringar av ett transaktions-Node-lösenord.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **Namn** | Namnet på Azure blockchain-tjänstens blockchain-medlem som också innehåller det nya namnet på Transaction-noden. |
| **lösenord** | Transaktions nodens lösen ord. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkelt citat tecken (), bindestreck (-) och semikolon (;). |

## <a name="change-consortium-management-account-password"></a>Ändra lösen ord för hanterings kontot för konsortiet

Konsortiets hanterings konto används för medlemskaps hantering i konsortiet. Varje medlem identifieras unikt av ett konto för hantering av konsortier och du kan ändra lösen ordet för kontot med följande kommando.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser skapas. |
| **Namn** | Namn som identifierar din Azure blockchain service-medlem. |
| **consortiumManagementAccountPassword** | Konto lösen ordet för konsortiets hanterings konto. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkelt citat tecken (), bindestreck (-) och semikolon (;). |
  
## <a name="update-firewall-rules"></a>Uppdatera brand Väggs regler

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **Namn** | Namnet på Azure blockchain-tjänstens blockchain-medlem. |
| **ruleName** | Regel namn för vit listning av ett IP-adressintervall. Valfri parameter för brand Väggs regler.|
| **startIpAddress** | Början av IP-adressintervallet för vit listning. Valfri parameter för brand Väggs regler.|
| **endIpAddress** | Slutet av IP-adressintervallet för vit listning. Valfri parameter för brand Väggs regler.|

## <a name="list-api-keys"></a>Visa lista över API-nycklar

API-nycklar kan användas för Node-åtkomst liknande användar namn och lösen ord. Det finns två API-nycklar som stöder nyckel rotation. Använd följande kommando för att lista dina API-nycklar.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **Namn** | Namnet på Azure blockchain-tjänstens blockchain-medlem som också innehåller det nya namnet på Transaction-noden. |

## <a name="regenerate-api-keys"></a>Återskapa API-nycklar

Använd följande kommando för att återskapa dina API-nycklar.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **Namn** | Namnet på Azure blockchain-tjänstens blockchain-medlem som också innehåller det nya namnet på Transaction-noden. |
| **keyName** | Ersätt \<keyValue\> med antingen KEY1 eller key2. |

## <a name="delete-a-transaction-node"></a>Ta bort en Transaction-nod

Exempel tar bort en blockchain-medlems transaktions nod.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **Namn** | Namnet på Azure blockchain service-blockchain medlem som också innehåller det transaktions nodnamn som ska tas bort. |

## <a name="delete-a-blockchain-member"></a>Ta bort en blockchain-medlem

Exempel tar bort en blockchain-medlem.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beskrivning |
|---------|-------------|
| **resurs grupp** | Resurs grupp namn där Azure blockchain service-resurser finns. |
| **Namn** | Namnet på den Azure blockchain-tjänst blockchain-medlem som ska tas bort. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Bevilja åtkomst för Azure AD-användare

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **rollinnehavaren** | Namnet på Azure AD-rollen. |
| **tilldelad person** | Användar-ID för Azure AD. Till exempel, `user@contoso.com` |
| **utrymme** | Roll tilldelningens omfattning. Kan vara antingen en blockchain-medlem eller Transaction-nod. |

**Exempel:**

Bevilja noden åtkomst för Azure AD-användare till blockchain- **medlemmen**:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exempel:**

Bevilja Node-åtkomst för Azure AD-användare till blockchain- **transaktions nod**:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Bevilja Node-åtkomst för Azure AD-grupp eller-program roll

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parameter | Beskrivning |
|---------|-------------|
| **rollinnehavaren** | Namnet på Azure AD-rollen. |
| **tilldelad person-objekt-ID** | Azure AD-grupp-ID eller program-ID. |
| **utrymme** | Roll tilldelningens omfattning. Kan vara antingen en blockchain-medlem eller Transaction-nod. |

**Exempel:**

Bevilja noden åtkomst för **program rollen**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Ta bort åtkomst till Azure AD-nod

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | Beskrivning |
|---------|-------------|
| **rollinnehavaren** | Namnet på Azure AD-rollen. |
| **tilldelad person** | Användar-ID för Azure AD. Till exempel, `user@contoso.com` |
| **utrymme** | Roll tilldelningens omfattning. Kan vara antingen en blockchain-medlem eller Transaction-nod. |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar noder i Azure blockchain service Transactions med Azure Portal](configure-transaction-nodes.md).
