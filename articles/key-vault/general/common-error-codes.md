---
title: Vanliga felkoder för Azure Key Vault | Microsoft Docs
description: Vanliga felkoder för Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: a36e15a56a5a4c8a637120ca730ae1da764d376d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422911"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Vanliga felkoder för Azure Key Vault

Fel koderna som anges i följande tabell kan returneras av en åtgärd i Azure Key Vault

| Felkod | Användar meddelande |
|--|--|
| VaultAlreadyExists |  Ditt försök att skapa ett nytt nyckel valv med det angivna namnet misslyckades eftersom namnet redan används. Om du nyligen har tagit bort ett nyckel valv med det här namnet kan det fortfarande vara i läget Soft Deleted. Du kan kontrol lera om den finns i läget Soft-Deleted [här](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) |
| VaultNameNotValid |  Valv namnet ska vara 24 tecken, alfanumeriskt och börja med ett alfabet |
| AccessDenied |  Du kanske saknar behörigheter i åtkomst principen för att utföra åtgärden. |
| ForbiddenByFirewall |  Klient adressen är inte auktoriserad och anroparen är inte en betrodd tjänst. |
| ConflictError |  Du begär flera åtgärder på samma objekt.  |
| RegionNotSupported |  Den angivna Azure-regionen stöds inte för den här resursen. |
| SkuNotSupported |  Den angivna SKU-typen stöds inte för den här resursen. |
| ResourceNotFound |  Det gick inte att hitta den angivna Azure-resursen. |
| CertificateExpired |  Kontrol lera utgångs datum och giltighets tid för certifikatet. |


## <a name="next-steps"></a>Nästa steg

- Se [guiden för Azure Key Vault utvecklare](developers-guide.md)
- Läs mer om [att autentisera till nyckel valv](authentication.md)
