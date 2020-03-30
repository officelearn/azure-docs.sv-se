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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279318"
---
Azure tillhandahåller följande inbyggda RBAC-roller för att auktorisera åtkomst till blob- och ködata med Azure AD och OAuth:

- [Lagring blob dataägare:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner)Används för att ange ägarskap och hantera POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2. Mer information finns [i Åtkomstkontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage Blob Data Contributor](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Används för att bevilja läs-/skriv-/borttagningsbehörigheter till Blob-lagringsresurser.
- [Lagring blob dataläsare:](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader)Används för att bevilja skrivskyddade behörigheter till Blob lagringsresurser.
- [Storage Queue Data Contributor](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Används för att bevilja läs-/skriv-/borttagningsbehörigheter till Azure-köer.
- [Dataläsare för lagringskö:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader)Används för att bevilja skrivskyddade behörigheter till Azure-köer.
- [Lagringsködatameddelandebehandlare:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor)Används för att bevilja peek, retrieve och ta bort behörigheter till meddelanden i Azure Storage-köer.
- [Meddelandeavsändare för lagringsködata:](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)Används för att bevilja tilläggsbehörigheter för meddelanden i Azure Storage-köer.

> [!NOTE]
> RBAC-rolltilldelningar kan ta upp till fem minuter att sprida.
>
> Endast roller som uttryckligen definierats för dataåtkomst gör det möjligt för ett säkerhetsobjekt att komma åt blob- eller ködata. Roller som **Ägare,** **Deltagare**och **Storage Account Contributor** tillåter ett säkerhetsobjekt att hantera ett lagringskonto, men ger inte åtkomst till blob- eller ködata i det kontot.
>
> Åtkomst till blob- eller ködata i Azure-portalen kan auktoriseras med antingen ditt Azure AD-konto eller åtkomstnyckeln för lagringskontot. Mer information finns i [Använda Azure-portalen för att komma åt blob- eller ködata](../articles/storage/common/storage-access-blobs-queues-portal.md).
