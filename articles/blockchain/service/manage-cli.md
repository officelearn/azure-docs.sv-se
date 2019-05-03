---
title: Hantera Azure Blockchain-tjänsten med Azure CLI
description: Hur du skapar och hanterar Azure Blockchain-tjänsten med Azure CLI
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 100d50443c7ed839e57d80ceea3b8b86904e4ba7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027877"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Hantera Azure Blockchain-tjänsten med Azure CLI

Du kan använda Azure CLI att snabbt skapa och hantera blockchain-medlemmar och transaktionen noder för Azure Blockchain Service utöver Azure-portalen.

Se till att du har installerat senast [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och inloggad på ett Azure-konto med `az login`.

I följande exempel har du ersätta exempel `<parameter names>` med dina egna värden.

## <a name="create-blockchain-member"></a>Skapa blockchain medlem

Exemplet skapar en blockchain-medlem i Azure Blockchain-tjänsten som körs kvorum transaktionsregister-protokollet i en ny consortium.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties '{ "location": "<myBlockchainLocation>", "properties": {"password": "<myStrongPassword>", "protocol": "Quorum", "consortium": "<myConsortiumName>", "consortiumManagementAccountPassword": "<myConsortiumManagementAccountPassword>", "firewallRules": [ { "ruleName": "<myRuleName>", "startIpAddress": "<myStartIpAddress>", "endIpAddress": "<myEndIpAddress>" } ] }, "sku": { "name": "<skuName>" } }'
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn var Azure Blockchain-tjänstresurser ska skapas. |
| **Namn** | Ett unikt namn som identifierar din Azure Blockchain Service blockchain medlem. Namnet används för offentlig slutpunkt-adress. Till exempel `myblockchainmember.blockchain.azure.com`. |
| **Plats** | Azure-region där den blockchain-medlemmen har skapats. Till exempel `eastus`. Välj den plats som är närmast dina användare eller dina andra Azure-program. |
| **Lösenord** | Lösenordet för medlem. Lösenordet för medlem används för att autentisera till medlemmen blockchain offentlig slutpunkt som använder grundläggande autentisering. Lösenordet måste uppfylla tre av följande fyra krav: längd måste vara mellan 12 och 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som är inte antalet sign(#), procent (%), cittatecken, star(*), Bakåtlutande citattecken () \`), dubbelklicka quote("), enskild quote('), streck (-) och semikolon (;). |
| **protocol** | Offentlig förhandsversion stöder kvorum. |
| **consortium** | Namnet på consortium att ansluta till eller skapa. |
| **consortiumManagementAccountPassword** | Consortium management lösenordet. Lösenordet används för att ansluta till en consortium. |
| **ruleName** | Regelnamnet för att godkänna ett IP-adressintervall. Valfri parameter för brandväggsregler.|
| **startIpAddress** | Början av IP-adressintervall för listan över tillåtna program. Valfri parameter för brandväggsregler. |
| **endIpAddress** | Slut på IP-adressintervall för listan över tillåtna program. Valfri parameter för brandväggsregler. |
| **skuName** | Nivåtyp. Använd S0 för Standard och B0 för Basic. |

## <a name="change-blockchain-member-password"></a>Ändra blockchain medlem lösenord

Exempel ändras medlem blockchain lösenord.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn var Azure Blockchain-tjänstresurser ska skapas. |
| **Namn** | Namn som identifierar Azure Blockchain Service-medlem. |
| **Lösenord** | Lösenordet för medlem. Lösenordet måste uppfylla tre av följande fyra krav: längd måste vara mellan 12 och 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som är inte antalet sign(#), procent (%), cittatecken, star(*), Bakåtlutande citattecken () \`), dubbelklicka quote("), enskild quote('), streck (-) och semikolon (;). |


## <a name="create-transaction-node"></a>Skapa transaktion nod

Skapa en transaktion nod i en befintlig blockchain-medlem. Genom att lägga till transaktion noder, kan du öka säkerhetsisolering och distribuera belastning. Du kan till exempel ha en transaktion noden slutpunkt för olika program.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties '{ "location": "<myRegion>", "properties": { "password": "<myStrongPassword>", "firewallRules": [ { "ruleName": "<myRuleName>", "startIpAddress": "<myStartIpAddress>", "endIpAddress": "<myEndIpAddress>" } ] } }'
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn var Azure Blockchain-tjänstresurser ska skapas. |
| **Namn** | Namnet på medlemmen Azure Blockchain Service blockchain som även innehåller namnet på nya transaktionen noden. |
| **Plats** | Azure-region där den blockchain-medlemmen har skapats. Till exempel `eastus`. Välj den plats som är närmast dina användare eller dina andra Azure-program. |
| **Lösenord** | Transaktionen noden lösenord. Lösenordet måste uppfylla tre av följande fyra krav: längd måste vara mellan 12 och 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som är inte antalet sign(#), procent (%), cittatecken, star(*), Bakåtlutande citattecken () \`), dubbelklicka quote("), enskild quote('), streck (-) och semikolon (;). |
| **ruleName** | Regelnamnet för att godkänna ett IP-adressintervall. Valfri parameter för brandväggsregler. |
| **startIpAddress** | Början av IP-adressintervall för listan över tillåtna program. Valfri parameter för brandväggsregler. |
| **endIpAddress** | Slut på IP-adressintervall för listan över tillåtna program. Valfri parameter för brandväggsregler.|

## <a name="change-transaction-node-password"></a>Ändra lösenord för noden för transaktion

Exempel ändras en transaktion noden lösenord.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn där Azure Blockchain-tjänstresurser finns. |
| **Namn** | Namnet på medlemmen Azure Blockchain Service blockchain som även innehåller namnet på nya transaktionen noden. |
| **Lösenord** | Transaktionen noden lösenord. Lösenordet måste uppfylla tre av följande fyra krav: längd måste vara mellan 12 och 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som är inte antalet sign(#), procent (%), cittatecken, star(*), Bakåtlutande citattecken () \`), dubbelklicka quote("), enskild quote('), streck (-) och semikolon (;). |

## <a name="change-consortium-management-account-password"></a>Ändra consortium management kontolösenord

Consortium management-kontot används för hantering av consortium medlemskap. Varje medlem identifieras unikt med ett konto för hantering av consortium och du kan ändra lösenordet för kontot med följande kommando.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn var Azure Blockchain-tjänstresurser ska skapas. |
| **Namn** | Namn som identifierar Azure Blockchain Service-medlem. |
| **consortiumManagementAccountPassword** | Kontolösenord för consortium management. Lösenordet måste uppfylla tre av följande fyra krav: längd måste vara mellan 12 och 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som är inte antalet sign(#), procent (%), cittatecken, star(*), Bakåtlutande citattecken () \`), dubbelklicka quote("), enskild quote('), streck (-) och semikolon (;). |
  
## <a name="update-firewall-rules"></a>Uppdatera brandväggsregler

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules='[ { "ruleName": "<myRuleName>", "startIpAddress": "<myStartIpAddress>", "endIpAddress": "<myEndIpAddress>" } ]' --remove properties.consortiumManagementAccountAddress
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn där Azure Blockchain-tjänstresurser finns. |
| **Namn** | Namnet på Azure Blockchain Service blockchain medlemmen. |
| **ruleName** | Regelnamnet för att godkänna ett IP-adressintervall. Valfri parameter för brandväggsregler.|
| **startIpAddress** | Början av IP-adressintervall för listan över tillåtna program. Valfri parameter för brandväggsregler.|
| **endIpAddress** | Slut på IP-adressintervall för listan över tillåtna program. Valfri parameter för brandväggsregler.|

## <a name="list-api-keys"></a>Lista över API-nycklar

API-nycklar kan användas för noden åtkomst liknar användarnamn och lösenord. Det finns två API-nycklar för rotation av. Använd följande kommando för att visa dina API-nycklar.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn där Azure Blockchain-tjänstresurser finns. |
| **Namn** | Namnet på medlemmen Azure Blockchain Service blockchain som även innehåller namnet på nya transaktionen noden. |

## <a name="regenerate-api-keys"></a>Återskapa API-nycklar

Använd följande kommando för att återskapa din API-nycklar.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn där Azure Blockchain-tjänstresurser finns. |
| **Namn** | Namnet på medlemmen Azure Blockchain Service blockchain som även innehåller namnet på nya transaktionen noden. |
| **keyName** | Ersätt \<keyValue\> med key1 och key2. |

## <a name="delete-a-transaction-node"></a>Ta bort en nod för transaktion

Exempel tar bort en nod med blockchain medlem transaktion.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn där Azure Blockchain-tjänstresurser finns. |
| **Namn** | Namnet på medlemmen Azure Blockchain Service blockchain som även innehåller nya transaktion nodnamnet ska tas bort. |

## <a name="delete-a-blockchain-member"></a>Ta bort medlem blockchain

Exempel tar bort en blockchain-medlem.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parameter | Beskrivning |
|---------|-------------|
| **resource-group** | Resursgruppens namn där Azure Blockchain-tjänstresurser finns. |
| **Namn** | Namnet på medlemmen Azure Blockchain Service blockchain som ska tas bort. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Bevilja åtkomst för Azure AD-användare

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parameter | Beskrivning |
|---------|-------------|
| **Rollen** | Namnet på Azure AD-rollen. |
| **Tilldelad person** | Azure AD-användar-ID. Till exempel, `user@contoso.com` |
| **Omfång** | Omfattning rolltilldelningen. Kan vara antingen blockchain medlem i transaktionen noden. |

**Exempel:**

Bevilja noden åtkomst för Azure AD-användare och blockkedja **medlem**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Exempel:**

Bevilja noden åtkomst för Azure AD-användare och blockkedja **transaktion noden**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Bevilja noden åtkomst för Azure AD-grupp eller ett program roll

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parameter | Beskrivning |
|---------|-------------|
| **Rollen** | Namnet på Azure AD-rollen. |
| **assignee-object-id** | Azure AD-grupp-ID eller program-ID. |
| **Omfång** | Omfattning rolltilldelningen. Kan vara antingen blockchain medlem i transaktionen noden. |

**Exempel:**

Bevilja åtkomst för noden för **programroll**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Ta bort åtkomst till Azure AD-nod

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | Beskrivning |
|---------|-------------|
| **Rollen** | Namnet på Azure AD-rollen. |
| **Tilldelad person** | Azure AD-användar-ID. Till exempel, `user@contoso.com` |
| **Omfång** | Omfattning rolltilldelningen. Kan vara antingen blockchain medlem i transaktionen noden. |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera Azure Blockchain Service transaktion noder med Azure portal](configure-transaction-nodes.md)
