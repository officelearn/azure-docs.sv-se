---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/17/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4b10955a1d3b85acbcae109836bebc03ec04c72c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279318"
---
Azure tillhandahåller följande inbyggda RBAC-roller för att auktorisera åtkomst till blob-och Queue-data med hjälp av Azure AD och OAuth:

- [Storage BLOB data-ägare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Använd för att ange ägarskap och hantera POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2. Mer information finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage BLOB data-deltagare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Använd för att bevilja läs-/skriv-/borttagnings behörigheter till Blob Storage-resurser.
- [Storage BLOB data Reader](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): används för att bevilja Läs behörighet till Blob Storage-resurser.
- [Data deltagare i Storage Queue](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Använd för att bevilja läs-/skriv-/borttagnings behörigheter till Azure-köer.
- [Data läsare för lagrings köer](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Använd för att bevilja Läs behörighet till Azure-köer.
- [Processor för lagrings data meddelande](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Använd för att ge behörighet att granska, hämta och ta bort meddelanden i Azure Storage köer.
- [Avsändare av data meddelande i lagrings](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)köer: Använd för att bevilja Lägg till behörigheter till meddelanden i Azure Storage köer.

> [!NOTE]
> Det kan ta upp till fem minuter att sprida RBAC-roll tilldelningar.
>
> Endast roller som uttryckligen definierats för data åtkomst tillåter ett säkerhets objekt att komma åt BLOB-eller Queue data. Roller som **ägare**, **deltagare**och **lagrings konto deltagare** tillåter ett säkerhets objekt att hantera ett lagrings konto, men ger inte till gång till BLOB-eller Queue-data inom det kontot.
>
> Åtkomst till BLOB-eller Queue-data i Azure Portal kan godkännas med hjälp av antingen ditt Azure AD-konto eller lagrings kontots åtkomst nyckel. Mer information finns i [använda Azure Portal för att få åtkomst till BLOB-eller Queue-data](../articles/storage/common/storage-access-blobs-queues-portal.md).
