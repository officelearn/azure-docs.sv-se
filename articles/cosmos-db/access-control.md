---
title: Ställa in åtkomstkontroll i Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om att ställa in åtkomstkontroll i Azure Cosmos-databasen.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: fae3af3f-4d6e-46d8-9d9b-b80a4218add9
ms.service: cosmos-db
ms.topic: article
ms.date: 02/06/2018
ms.author: sngun
ms.openlocfilehash: 740f1ca560207ada95dd03fbecc7f9940ee7b2a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="access-control-in-azure-cosmos-db"></a>Åtkomstkontroll i Azure Cosmos DB

För att lägga till Azure Cosmos DB reader åtkomst till ditt användarkonto har en prenumeration ägare utför följande steg i Azure-portalen.

1. Öppna Azure-portalen och välj Azure DB som Cosmos-konto.
2. Klicka på den **åtkomstkontroll (IAM)** fliken och klicka sedan på **+ Lägg till**.
3. I den **lägga till behörigheter** rutan i den **rollen** väljer **Cosmos DB konto Reader rollen**.
4. I den **tilldelar åtkomst till rutan**väljer **Azure AD-användare, grupp eller programmet**.
5. Välj användare, grupp eller program i din katalog som du vill bevilja åtkomst.  Du kan söka i katalogen med visningsnamn, e-postadress eller objektidentifierare.
    Den valda användaren, gruppen eller programmet visas i listan valda medlemmar.
6. Klicka på **Spara**.

Entiteten kan nu läsa Azure Cosmos DB resurser.
