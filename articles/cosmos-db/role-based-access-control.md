---
title: Rollbaserad åtkomst kontroll i Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB tillhandahåller databas skydd med RBAC (Active Directory integration).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445088"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Rollbaserad åtkomst kontroll i Azure Cosmos DB

Azure Cosmos DB tillhandahåller inbyggd rollbaserad åtkomst kontroll (RBAC) för vanliga hanterings scenarier i Azure Cosmos DB. En person som har en profil i Azure Active Directory kan tilldela de här RBAC-rollerna till användare, grupper, tjänstens huvud namn eller hanterade identiteter för att bevilja eller neka åtkomst till resurser och åtgärder på Azure Cosmos DB resurser. Roll tilldelningar är begränsade till kontroll – endast plan åtkomst, vilket omfattar åtkomst till Azure Cosmos-konton, databaser, behållare och erbjudanden (data flöde).

## <a name="built-in-roles"></a>Inbyggda roller

Följande är de inbyggda roller som stöds av Azure Cosmos DB:

|**Inbyggd roll**  |**Beskrivning**  |
|---------|---------|
|[DocumentDB-konto deltagare](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan hantera Azure Cosmos DB-konton.|
|[Cosmos DB konto läsare](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan läsa Azure Cosmos DB konto data.|
|[Ansvarig för Cosmos-säkerhetskopiering](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Kan skicka en Restore-begäran för en Azure Cosmos-databas eller en behållare.|
|[Cosmos DB operatör](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Kan etablera Azure Cosmos-konton, databaser och behållare, men har inte åtkomst till de nycklar som krävs för att komma åt data.|

> [!IMPORTANT]
> RBAC-stöd i Azure Cosmos DB gäller endast för kontroll Plans åtgärder. Data Plans åtgärder skyddas med hjälp av huvud nycklar eller resurs-token. Mer information finns i [säker åtkomst till data i Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identitets- och åtkomsthantering (IAM)

Fönstret **åtkomst kontroll (IAM)** i Azure Portal används för att konfigurera rollbaserad åtkomst kontroll på Azure Cosmos-resurser. Rollerna tillämpas på användare, grupper, tjänstens huvud namn och hanterade identiteter i Active Directory. Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper. Följande skärm bild visar Active Directory integration (RBAC) med åtkomst kontroll (IAM) i Azure Portal:

![Åtkomst kontroll (IAM) i Azure Portal – demonstrera databas säkerhet](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Anpassade roller

Förutom de inbyggda rollerna kan användare också skapa [anpassade roller](../role-based-access-control/custom-roles.md) i Azure och tillämpa dessa roller för tjänstens huvud namn i alla prenumerationer inom sin Active Directory-klient. Anpassade roller ger användare ett sätt att skapa RBAC-roll definitioner med en anpassad uppsättning av resurs leverantörs åtgärder. Om du vill veta vilka åtgärder som är tillgängliga för att skapa anpassade roller för Azure Cosmos DB, se [Azure Cosmos DB Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Förhindra ändringar från Cosmos SDK

Cosmos Resource Provider kan låsas för att förhindra ändringar av resurser, inklusive Cosmos-konto, databaser, behållare och data flöde från klienter som ansluter via konto nycklar (d.v.s. program som ansluter via Cosmos SDK). När det är inställt måste ändringar av en resurs vara från en användare med rätt RBAC-roll och autentiseringsuppgifter. Den här funktionen anges med `disableKeyBasedMetadataWriteAccess` egenskaps värde i Cosmos-resurs leverantören. Ett exempel på en Azure Resource Manager-mall med denna egenskaps inställning finns nedan.

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

- [Vad är rollbaserad åtkomst kontroll (RBAC) för Azure-resurser](../role-based-access-control/overview.md)
- [Anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md)
- [Åtgärder för Azure Cosmos DB Resource Provider](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
