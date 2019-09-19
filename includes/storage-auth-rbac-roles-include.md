---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c2b409f0eefe5efa389432cbb007cc08e0c6ae1e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71078284"
---
Azure tillhandahåller följande inbyggda RBAC-roller för att auktorisera åtkomst till blob-och Queue-data med hjälp av Azure AD och OAuth:

- [Ägare till Storage BLOB-data](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Använd för att ange ägarskap och hantera POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2. Mer information finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Data deltagare i Storage BLOB](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Används för att bevilja läs-/skriv-/borttagnings behörigheter till Blob Storage-resurser.
- [Storage BLOB data Reader](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Används för att bevilja Läs behörighet till Blob Storage-resurser.
- [Deltagare i Storage Queue-data](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Används för att bevilja läs-/skriv-/borttagnings behörigheter till Azure-köer.
- [Data läsare för lagrings köer](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Använd för att ge Läs behörighet till Azure-köer.
- [Processor för data meddelande i lagrings köer](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Används för att ge behörighet att granska, hämta och ta bort meddelanden i Azure Storage köer.
- [Avsändare av data meddelande i lagrings köer](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Används för att bevilja Lägg till behörigheter till meddelanden i Azure Storage köer.

> [!NOTE]
> Det kan ta upp till fem minuter att sprida RBAC-roll tilldelningar.
>
> Endast roller som uttryckligen definierats för data åtkomst tillåter ett säkerhets objekt att komma åt BLOB-eller Queue data. Roller som **ägare**, **deltagare**och **lagrings konto deltagare** tillåter ett säkerhets objekt att hantera ett lagrings konto, men ger inte till gång till BLOB-eller Queue-data inom det kontot.
