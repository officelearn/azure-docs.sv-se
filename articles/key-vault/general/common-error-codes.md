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
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877003"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Vanliga felkoder för Azure Key Vault

Fel koderna som anges i följande tabell kan returneras av en åtgärd i Azure Key Vault

| Felkod | Användar meddelande |
|--|--|
| VaultAlreadyExists |  Det angivna nyckel valvet finns redan (i läget Soft-Deleted eller en annan prenumeration). |
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
