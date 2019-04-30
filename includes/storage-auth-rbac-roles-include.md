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
ms.openlocfilehash: 026717dff2b6883eb643497dec91226e4afe8133
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483609"
---
Azure tillhandahåller följande inbyggda RBAC-roller för att auktorisera åtkomst till blob och kö med hjälp av Azure AD och OAuth:

- [Storage Blob Data ägare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Använd för att ange ägarskap och hantera POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2 (förhandsversion). Mer information finns i [åtkomstkontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage Blob Data-deltagare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Använd för att bevilja behörigheter för Läs/Skriv/ta bort till Blob storage-resurser.
- [Storage Blob Data-läsare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Använd det här alternativet om du vill ge läsbehörighet till Blob storage-resurser.
- [Lagringsködata-deltagare](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Använd för att ge Läs/Skriv/ta bort behörighet till Azure-köer.
- [Lagringsködata-läsare](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Använd det här alternativet om du vill ge läsbehörighet till Azure-köer.
- [Storage-kö Data meddelande Processor](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Använd för att bevilja peek, hämta och ta bort behörigheter till meddelanden i Azure Storage-köer.
- [Kön lagringsdata meddelande avsändaren](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Använd för att bevilja lägga till behörigheter till meddelanden i Azure Storage-köer.

> [!NOTE]
> Tänk på att RBAC-rolltilldelningar kan ta upp till fem minuter att sprida.
