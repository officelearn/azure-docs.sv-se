---
title: inkludera fil
description: inkludera fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597860"
---
Azure Files erbjuder två olika nivåer av lagring, Premium och standard, så att du kan skräddarsy dina resurser efter prestanda-och pris krav för ditt scenario:

- **Premium-fil resurser**: Premium fil resurser backas upp av solid state-hårddiskar (SSD) och distribueras i **lagrings konto typen FileStorage** . Premium-filresurser ger konsekvent höga prestanda och låg latens i ensiffriga millisekunder för de flesta i/o-åtgärder för i/o-intensiva arbets belastningar. Detta gör dem lämpliga för en mängd olika arbets belastningar, t. ex. databaser, webbplats värdar och utvecklings miljöer. Premium-filresurser är bara tillgängliga i en etablerad fakturerings modell. Mer information om den etablerade fakturerings modellen för Premium-filresurser finns i [förstå etablering för Premium-filresurser](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Standard fil resurser**: standard fil resurser backas upp av hård diskar (HDD) och distribueras i **GPv2-lagrings konto typen (General Purpose version 2)** . Standard fil resurser ger tillförlitlig prestanda för i/o-arbetsbelastningar som är mindre känsliga för prestanda variationer som generella fil resurser och utvecklings-och test miljöer. Standard fil resurser är bara tillgängliga i en fakturerings modell enligt principen betala per användning.