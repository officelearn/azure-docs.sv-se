---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4c66f35537cff87eb53a6cfe9bc0389f038f1a10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449761"
---
Azure tillhandahåller följande inbyggda RBAC-roller för åtkomst till lagringsdata:

- [Storage Blob Data ägare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Använd för att ange ägarskap och hantera POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2 (förhandsversion). Mer information finns i [åtkomstkontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage Blob Data-deltagare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Använd för att bevilja behörigheter för Läs/Skriv/ta bort till Blob storage-resurser.
- [Storage Blob Data-läsare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Använd det här alternativet om du vill ge läsbehörighet till Blob storage-resurser.
- [Lagringsködata-deltagare](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Använd för att ge Läs/Skriv/ta bort behörighet till Azure-köer.
- [Lagringsködata-läsare](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Använd det här alternativet om du vill ge läsbehörighet till Azure-köer.
- [Storage-kö Data meddelande Processor](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor-preview): Använd för att bevilja peek, hämta och ta bort behörigheter till meddelanden i Azure Storage-köer.
- [Kön lagringsdata meddelande avsändaren](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender-preview): Använd för att bevilja lägga till behörigheter till meddelanden i Azure Storage-köer.

> [!IMPORTANT]
> RBAC-rolltilldelningar kan ta upp till fem minuter att sprida.

Mer information om hur inbyggda roller definieras för Azure Storage, se [förstå rolldefinitioner](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Information om hur du skapar anpassade RBAC-roller finns i [skapa anpassade roller för rollbaserad åtkomstkontroll i](../articles/role-based-access-control/custom-roles.md). 
