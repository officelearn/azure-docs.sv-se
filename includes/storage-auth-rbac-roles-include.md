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
ms.openlocfilehash: d97a2350765ac321cf77f8a9f84825c88d0c9185
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824269"
---
Azure tillhandahåller följande inbyggda RBAC-roller för åtkomst till lagringsdata:

- [Storage Blob Data-ägare (förhandsversion)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Använd för att ange ägarskap och hantera POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2 (förhandsversion). Mer information finns i [åtkomstkontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage Blob Data-deltagare (förhandsgranskning)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Använd för att bevilja behörigheter för Läs/Skriv/ta bort till Blob storage-resurser.
- [Storage Blob Data-läsare (förhandsgranskning)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Använd det här alternativet om du vill ge läsbehörighet till Blob storage-resurser.
- [Lagringsködata-deltagare (förhandsgranskning)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Använd för att ge Läs/Skriv/ta bort behörighet till Azure-köer.
- [Lagringsködata-läsare (förhandsgranskning)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Använd det här alternativet om du vill ge läsbehörighet till Azure-köer.

Mer information om hur inbyggda roller definieras för Azure Storage, se [förstå rolldefinitioner](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview).

Azure Storage stöder också anpassad RBAC-roller. Mer information finns i [skapa anpassade roller för rollbaserad åtkomstkontroll i](../articles/role-based-access-control/custom-roles.md). 
