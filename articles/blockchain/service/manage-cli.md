---
title: Hantera Azure Blockchain-tjänst med Azure CLI
description: Hantera Azure Blockchain-tjänsten med Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455576"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Hantera Azure Blockchain-tjänst med Azure CLI

Förutom Azure-portalen kan du använda Azure CLI för att hantera blockchain-medlemmar och transaktionsnoder för din Azure Blockchain-tjänst.

Kontrollera att du har installerat den senaste [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och `az login`loggat in på ett Azure-konto med .

I följande exempel ersätter du exempel `<parameter names>` med dina egna värderingar.

## <a name="create-blockchain-member"></a>Skapa blockchain-medlem

Exempel skapar en blockchain-medlem i Azure Blockchain Service som kör kvorumsredovisningsprotokollet i ett nytt konsortium.

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
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser skapas. |
| **Namn** | Ett unikt namn som identifierar din Azure Blockchain Service blockchain-medlem. Namnet används för den offentliga slutpunktsadressen. Till exempel `myblockchainmember.blockchain.azure.com`. |
| **Plats** | Azure-region där blockchain-medlemmen skapas. Till exempel `eastus`. Välj den plats som är närmast dina användare eller dina andra Azure-program. |
| **lösenord** | Lösenordet för medlemskontot. Medlemskontots lösenord används för att autentisera till blockchain-medlemmens offentliga slutpunkt med hjälp av grundläggande autentisering. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemener, 1 versaltecken, 1 tal och 1 specialtecken som inte är\`taltecken(#), procent(%), komma(,), stjärntecken(*), dubbel citattecken("), enkel offert('), dash(-) och semikolon(;)|
| **Protokollet** | Offentlig förhandsversion stöder kvorum. |
| **Konsortium** | Namn på konsortiet att gå med i eller skapa. |
| **konsortiumManagementAccountPassword** | Konsortiets hanteringslösenord. Lösenordet används för att gå med i ett konsortium. |
| **ruleName (härst)** | Regelnamn för vitlistning av ett IP-adressintervall. Valfri parameter för brandväggsregler.|
| **startIAddress** | Start av IP-adressintervallet för vitlistning. Valfri parameter för brandväggsregler. |
| **endIpAddress** | på IP-adressintervallet för vitlistning. Valfri parameter för brandväggsregler. |
| **skuNamn** | Nivåtyp. Använd S0 för Standard och B0 för Basic. |

## <a name="change-blockchain-member-password"></a>Ändra lösenord för blockchain-medlemmar

Exempel ändrar en blockchain-medlems lösenord.

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
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser skapas. |
| **Namn** | Namn som identifierar din Azure Blockchain Service-medlem. |
| **lösenord** | Lösenordet för medlemskontot. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemener, 1 versaltecken, 1 tal och 1 specialtecken som inte är\`taltecken(#), procent(%), komma(,), stjärntecken(*), dubbel citattecken("), enkel offert('), dash(-) och semikolon(;). |

## <a name="create-transaction-node"></a>Skapa transaktionsnod

Skapa en transaktionsnod i en befintlig blockchain-medlem. Genom att lägga till transaktionsnoder kan du öka säkerhetsisolering och distribuera belastning. Du kan till exempel ha en slutpunkt för transaktionsnod för olika klientprogram.

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
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser skapas. |
| **Namn** | Namn på Azure Blockchain Service blockchain-medlemmen som också innehåller det nya transaktionsnodnamnet. |
| **Plats** | Azure-region där blockchain-medlemmen skapas. Till exempel `eastus`. Välj den plats som är närmast dina användare eller dina andra Azure-program. |
| **lösenord** | Lösenordet för transaktionsnoden. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemener, 1 versaltecken, 1 tal och 1 specialtecken som inte är\`taltecken(#), procent(%), komma(,), stjärntecken(*), dubbel citattecken("), enkel offert('), dash(-) och semikolon(;). |
| **ruleName (härst)** | Regelnamn för vitlistning av ett IP-adressintervall. Valfri parameter för brandväggsregler. |
| **startIAddress** | Start av IP-adressintervallet för vitlistning. Valfri parameter för brandväggsregler. |
| **endIpAddress** | på IP-adressintervallet för vitlistning. Valfri parameter för brandväggsregler.|

## <a name="change-transaction-node-password"></a>Ändra lösenord för transaktionsnod

Exempel ändrar ett lösenord för transaktionsnoder.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parameter | Beskrivning |
|---------|-------------|
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser finns. |
| **Namn** | Namn på Azure Blockchain Service blockchain-medlemmen som också innehåller det nya transaktionsnodnamnet. |
| **lösenord** | Lösenordet för transaktionsnoden. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemener, 1 versaltecken, 1 tal och 1 specialtecken som inte är\`taltecken(#), procent(%), komma(,), stjärntecken(*), dubbel citattecken("), enkel offert('), dash(-) och semikolon(;). |

## <a name="change-consortium-management-account-password"></a>Ändra lösenord för konsortiehanteringskonto

Konsortiets förvaltningskonto används för konsortiemedlemskap. Varje medlem identifieras unikt av ett konsortiehanteringskonto och du kan ändra lösenordet för det här kontot med följande kommando.

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
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser skapas. |
| **Namn** | Namn som identifierar din Azure Blockchain Service-medlem. |
| **konsortiumManagementAccountPassword** | Lösenordet för konsortiets hanteringskonto. Lösenordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemener, 1 versaltecken, 1 tal och 1 specialtecken som inte är\`taltecken(#), procent(%), komma(,), stjärntecken(*), dubbel citattecken("), enkel offert('), dash(-) och semikolon(;). |
  
## <a name="update-firewall-rules"></a>Uppdatera brandväggsregler

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
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser finns. |
| **Namn** | Namn på blockchain-medlemmen i Azure Blockchain-tjänsten. |
| **ruleName (härst)** | Regelnamn för vitlistning av ett IP-adressintervall. Valfri parameter för brandväggsregler.|
| **startIAddress** | Start av IP-adressintervallet för vitlistning. Valfri parameter för brandväggsregler.|
| **endIpAddress** | på IP-adressintervallet för vitlistning. Valfri parameter för brandväggsregler.|

## <a name="list-api-keys"></a>Lista API-nycklar

API-nycklar kan användas för nodåtkomst som liknar användarnamn och lösenord. Det finns två API-nycklar som stöder nyckelrotation. Använd följande kommando för att lista dina API-nycklar.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beskrivning |
|---------|-------------|
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser finns. |
| **Namn** | Namn på Azure Blockchain Service blockchain-medlemmen som också innehåller det nya transaktionsnodnamnet. |

## <a name="regenerate-api-keys"></a>Återskapa API-nycklar

Använd följande kommando för att återskapa API-nycklarna.

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
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser finns. |
| **Namn** | Namn på Azure Blockchain Service blockchain-medlemmen som också innehåller det nya transaktionsnodnamnet. |
| **Nyckelnamn** | Ersätt \<keyValue\> med antingen key1 eller key2. |

## <a name="delete-a-transaction-node"></a>Ta bort en transaktionsnod

Exempel tar bort en blockchain-medlemstransaktionsnod.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beskrivning |
|---------|-------------|
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser finns. |
| **Namn** | Namn på Azure Blockchain Service blockchain-medlemmen som också innehåller transaktionsnodnamnet som ska tas bort. |

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
| **resursgrupp** | Resursgruppsnamn där Azure Blockchain Service-resurser finns. |
| **Namn** | Namn på den Azure Blockchain Service blockchain-medlem som ska tas bort. |

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
| **Roll** | Namn på Azure AD-rollen. |
| **förvärvaren** | Azure AD-användar-ID. Till exempel, `user@contoso.com` |
| **Omfattning** | Rolltilldelningens omfattning. Kan vara antingen en blockchain-medlem eller transaktionsnod. |

**Exempel:**

Bevilja nodåtkomst för Azure AD-användare till **blockchain-medlem:**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exempel:**

Bevilja nodåtkomst för Azure AD-användare till **blockchain-transaktionsnod:**

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Bevilja nodåtkomst för Azure AD-grupp eller programroll

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parameter | Beskrivning |
|---------|-------------|
| **Roll** | Namn på Azure AD-rollen. |
| **assignee-object-id** | Azure AD-grupp-ID eller program-ID. |
| **Omfattning** | Rolltilldelningens omfattning. Kan vara antingen en blockchain-medlem eller transaktionsnod. |

**Exempel:**

Bevilja nodåtkomst för **programroll**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Ta bort Azure AD-nodåtkomst

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | Beskrivning |
|---------|-------------|
| **Roll** | Namn på Azure AD-rollen. |
| **förvärvaren** | Azure AD-användar-ID. Till exempel, `user@contoso.com` |
| **Omfattning** | Rolltilldelningens omfattning. Kan vara antingen en blockchain-medlem eller transaktionsnod. |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar Azure Blockchain Service-transaktionsnoder med Azure-portalen](configure-transaction-nodes.md).
