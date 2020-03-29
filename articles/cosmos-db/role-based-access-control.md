---
title: Rollbaserad åtkomstkontroll i Azure Cosmos DB
description: Lär dig mer om hur Azure Cosmos DB tillhandahåller databasskydd med Active Directory-integrering (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445088"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Rollbaserad åtkomstkontroll i Azure Cosmos DB

Azure Cosmos DB tillhandahåller inbyggd rollbaserad åtkomstkontroll (RBAC) för vanliga hanteringsscenarier i Azure Cosmos DB. En person som har en profil i Azure Active Directory kan tilldela dessa RBAC-roller till användare, grupper, tjänsthuvudnamn eller hanterade identiteter för att bevilja eller neka åtkomst till resurser och åtgärder på Azure Cosmos DB-resurser. Rolltilldelningar är begränsade till endast åtkomst på styrplan, vilket inkluderar åtkomst till Azure Cosmos-konton, databaser, behållare och erbjudanden (dataflöde).

## <a name="built-in-roles"></a>Inbyggda roller

Följande är de inbyggda roller som stöds av Azure Cosmos DB:

|**Inbyggd roll**  |**Beskrivning**  |
|---------|---------|
|[Deltagare på DocumentDB-konto](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan hantera Azure Cosmos DB-konton.|
|[Cosmos DB-kontoläsare](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan läsa Azure Cosmos DB-kontodata.|
|[Cosmos backup operatör](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Kan skicka återställningsbegäran för en Azure Cosmos-databas eller en behållare.|
|[Cosmos DB-operatör](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Kan etablera Azure Cosmos-konton, databaser och behållare men kan inte komma åt de nycklar som krävs för att komma åt data.|

> [!IMPORTANT]
> RBAC-stöd i Azure Cosmos DB gäller endast för styrplansåtgärder. Dataplanåtgärder skyddas med hjälp av huvudnycklar eller resurstoken. Mer information finns [i Säker åtkomst till data i Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identitets- och åtkomsthantering (IAM)

**Fönstret Åtkomstkontroll (IAM)** i Azure-portalen används för att konfigurera rollbaserad åtkomstkontroll på Azure Cosmos-resurser. Rollerna tillämpas på användare, grupper, tjänsthuvudnamn och hanterade identiteter i Active Directory. Du kan använda inbyggda roller eller anpassade roller för individer och grupper. Följande skärmbild visar Active Directory-integrering (RBAC) med hjälp av åtkomstkontroll (IAM) i Azure-portalen:

![Åtkomstkontroll (IAM) i Azure-portalen – demonstrerar databassäkerhet](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Anpassade roller

Förutom de inbyggda rollerna kan användare också skapa [anpassade roller](../role-based-access-control/custom-roles.md) i Azure och tillämpa dessa roller på tjänsthuvudnamn för alla prenumerationer i active directory-klienten. Anpassade roller ger användarna ett sätt att skapa RBAC-rolldefinitioner med en anpassad uppsättning resursprovideråtgärder. Om du vill veta vilka åtgärder som är tillgängliga för att skapa anpassade roller för Azure Cosmos DB finns i [Azure Cosmos DB-resursprovideråtgärder](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Förhindra ändringar från Cosmos SDK

Cosmos-resursprovidern kan låsas för att förhindra ändringar av resurser, inklusive Cosmos-konto, databaser, behållare och dataflöde från alla klienter som ansluter via kontonycklar (dvs. program som ansluter via Cosmos SDK). När den är inställd måste ändringar i en resurs komma från en användare med rätt RBAC-roll och autentiseringsuppgifter. Den här funktionen `disableKeyBasedMetadataWriteAccess` anges med egenskapsvärdet i Cosmos-resursprovidern. Ett exempel på en Azure Resource Manager-mall med den här egenskapsinställningen finns nedan.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

- [Vad är rollbaserad åtkomstkontroll (RBAC) för Azure-resurser](../role-based-access-control/overview.md)
- [Anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB-resursprovideråtgärder](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
