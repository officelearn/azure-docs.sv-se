---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218098"
---
Azure tillhandahåller följande inbyggda RBAC-roller för åtkomst till lagringsdata:

- [Storage Blob Data-ägare (förhandsversion)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Använd för att ange ägarskap och hantera POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2 (förhandsversion). Mer information finns i [åtkomstkontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage Blob Data-deltagare (förhandsgranskning)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Använd för att bevilja behörigheter för Läs/Skriv/ta bort till Blob storage-resurser.
- [Storage Blob Data-läsare (förhandsgranskning)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Använd det här alternativet om du vill ge läsbehörighet till Blob storage-resurser.
- [Lagringsködata-deltagare (förhandsgranskning)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Använd för att ge Läs/Skriv/ta bort behörighet till Azure-köer.
- [Lagringsködata-läsare (förhandsgranskning)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Använd det här alternativet om du vill ge läsbehörighet till Azure-köer.

Mer information om hur inbyggda roller definieras för Azure Storage, se [förstå rolldefinitioner](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Information om hur du skapar anpassade RBAC-roller finns i [skapa anpassade roller för rollbaserad åtkomstkontroll i](../articles/role-based-access-control/custom-roles.md). 
