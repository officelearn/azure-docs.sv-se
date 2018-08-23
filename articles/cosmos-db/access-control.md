---
title: Ange åtkomstkontroll i Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du ställer in åtkomstkontroll i Azure Cosmos DB.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: rafats
ms.openlocfilehash: 0d4595bcf8a9f009dce928d3f3cbc442328ec39b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42060662"
---
# <a name="access-control-in-azure-cosmos-db"></a>Åtkomstkontroll i Azure Cosmos DB

För att lägga till läsåtkomst i Azure Cosmos DB-konto till ditt användarkonto har du en prenumerant som utför följande steg i Azure-portalen.

1. Öppna Azure portal och välj ditt Azure Cosmos DB-konto.
2. Klicka på den **åtkomstkontroll (IAM)** fliken och klicka sedan på **+ Lägg till**.
3. I den **Lägg till behörigheter** fönstret i den **rollen** väljer **läsarroll för Cosmos DB-konto**.
4. I den **tilldela åtkomst till box**väljer **Azure AD-användare, grupp eller program**.
5. Välj användaren, gruppen eller programmet i din katalog som du vill bevilja åtkomst.  Du kan söka i katalogen efter visningsnamn, e-postadress eller objektidentifierare.
    Den valda användaren, gruppen eller programmet visas i listan valda medlemmar.
6. Klicka på **Spara**.

Entiteten kan nu läsa Azure Cosmos DB-resurser.
