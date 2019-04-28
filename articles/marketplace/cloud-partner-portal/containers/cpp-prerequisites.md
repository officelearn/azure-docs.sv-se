---
title: Azure-behållare erbjuder krav | Microsoft Docs
description: Förutsättningar för att publicera en Azure-behållare.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 09c9afcf3126f7f84c20f5514bc038a65b2ea1b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472795"
---
# <a name="container-publishing-prerequisites"></a>Behållaren publishing krav

Den här artikeln beskrivs kraven för att publicera ett erbjudande med behållare på Azure Marketplace.  Om du inte redan har gjort det, bör du granska den [behållare erbjuder publiceringsguide](../../marketplace-containers.md).

## <a name="publishing-prerequisites"></a>Krav för publicering

Om du vill publicera en ny behållaravbildning som du behöver uppfylla följande krav:

- Åtkomst till partnerportalen i molnet. Mer information finns i publiceringsguide för Azure Marketplace och AppSource.
- Avtal för Azure Marketplace-villkor
- Vara värd för behållaren teknisk tillgången i ett Azure Container Registry.
- Har din metadata för behållaren som är redo att användas. Till exempel följande icke uttömmande lista:
  - En rubrik
  - En beskrivning (i HTML-format)
  - En logotyp (i PNG-format) och fasta bildstorleken i den här: 40 x 40 px, 90 x 90 px, 115 x 115 bildpunkter och 255 x 115 bildpunkter.
- En *användningsvillkor* och en *sekretesspolicy* uttryck
- Dokumentation om container-lösning
- Supportkontakter

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, måste du [och Förbered din behållare tekniska resurser](./cpp-create-technical-assets.md) innan du [skapa erbjudandet behållare](./cpp-create-offer.md).  