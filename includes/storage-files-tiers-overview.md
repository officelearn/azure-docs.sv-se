---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597860"
---
Azure Files erbjuder två olika nivåer av lagring, premium och standard, så att du kan anpassa dina resurser till prestanda- och priskraven i ditt scenario:

- **Premium-filresurser:** Premium-filresurser backas upp av SSD-enheter (Solid State Drives) och distribueras i **lagringskontotypen FileStorage.** Premium-filresurser ger konsekvent hög prestanda och låg latens, inom ensiffriga millisekunder för de flesta IO-åtgärder, för IO-intensiva arbetsbelastningar. Detta gör dem lämpliga för en mängd olika arbetsbelastningar som databaser, webbhotell och utvecklingsmiljöer. Premium-filresurser är endast tillgängliga i en etablerad faktureringsmodell. Mer information om den etablerade faktureringsmodellen för premiumfilresurser finns i [Förstå etablering för premiumfilresurser](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Standardfilresurser:** Standardfilresurser backas upp av hårddiskar (HDDs) och distribueras i den **allmänna versionen 2 (GPv2) lagringskontotyp.** Standardfilresurser ger tillförlitlig prestanda för IO-arbetsbelastningar som är mindre känsliga för prestandavariationer, till exempel filresurser för allmänna ändamål och utvecklings-/testmiljöer. Standardfilresurser är endast tillgängliga i en faktureringsmodell för betalning per betalning.