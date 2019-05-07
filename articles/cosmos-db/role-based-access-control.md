---
title: Rollbaserad åtkomstkontroll i Azure Cosmos DB med Azure Active Directory-integrering
description: Lär dig hur Azure Cosmos DB tillhandahåller databasskyddet med Active directory-integrering (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: e43341e59e807fdc20033d909beadb9d582149b5
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078947"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Rollbaserad åtkomstkontroll i Azure Cosmos DB

Azure Cosmos DB tillhandahåller inbyggd rollbaserad åtkomstkontroll (RBAC) för vanliga scenarier för hantering i Azure Cosmos DB. En person som har en profil i Azure Active Directory kan tilldela dessa RBAC-roller till användare, grupper, tjänstens huvudnamn eller hanterade identiteter kan bevilja eller neka åtkomst till resurser och åtgärder på Azure Cosmos DB-resurser. Rolltilldelningar är begränsade till kontrollplanet åtkomst, som ger åtkomst till Azure Cosmos-konton, databaser, behållare och erbjuder (dataflöde).

## <a name="built-in-roles"></a>Inbyggda roller

Följande är de inbyggda roller som stöds av Azure Cosmos DB:

|**Inbyggd roll**  |**Beskrivning**  |
|---------|---------|
|[DocumentDB-konton deltagare](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Hantera Azure Cosmos DB-konton.  |
|[Cosmos DB-konto läsare](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Kan läsa data i Azure Cosmos DB-konto.        |
|[Cosmos ansvarig för säkerhetskopiering](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Kan skicka restore-förfrågan för en Azure Cosmos-databas eller en behållare.       |
|[Cosmos DB-Operator](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Etablera Azure-Cosmos-konton, databaser och behållare men kan inte komma åt de nycklar som krävs för att komma åt data.         |

> [!IMPORTANT]
> RBAC-stöd i Azure Cosmos DB gäller endast kontrollplanåtgärder. Kontrollplansåtgärder data är skyddade med huvudnycklar eller resurstokens. Mer information finns i [säker åtkomst till data i Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identitets- och åtkomsthantering (IAM)

Den **åtkomstkontroll (IAM)** fönstret i Azure portal som används för att konfigurera rollbaserad åtkomstkontroll på Azure Cosmos-resurser. Rollerna som tillämpas på användare, grupper, tjänstens huvudnamn och hanterade identiteter i Active Directory. Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper. I följande skärmbild visas Active Directory-integrering (RBAC) med hjälp av åtkomstkontroll (IAM) i Azure portal:

![Åtkomstkontroll (IAM) i Azure portal – visar databassäkerhet](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Anpassade roller

Förutom de inbyggda rollerna, användare kan också skapa [anpassade roller](../role-based-access-control/custom-roles.md) i Azure och gäller dessa roller för tjänstens huvudnamn i alla prenumerationer i sina Active Directory-klient. Anpassade roller ge användarna ett sätt att skapa RBAC rolldefinitioner med en anpassad uppsättning resource åtgärder för resursprovider. Läs vilka åtgärder som är tillgängliga för att skapa anpassade roller finns i Azure Cosmos DB [Azure Cosmos DB åtgärder för resursprovider](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>Nästa steg

- [Vad är rollbaserad åtkomstkontroll (RBAC för Azure-resurser)](../role-based-access-control/overview.md)
- [Anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB åtgärder för resursprovider](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
