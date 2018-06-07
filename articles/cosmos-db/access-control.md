---
title: Ställa in åtkomstkontroll i Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om att ställa in åtkomstkontroll i Azure Cosmos-databasen.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: sngun
ms.openlocfilehash: 2dae2b6291aa7ce18cc6f612b25cd5bdcc1ba753
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611151"
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
