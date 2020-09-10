---
title: Välj ett verktyg för att flytta Azure-resurser mellan regioner
description: Granska alternativ och verktyg för att flytta Azure-resurser mellan regioner
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 36f4bb0c10e8346caa0c4ef8fd1ed69e4b2878a0
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669376"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Välj ett verktyg för att flytta Azure-resurser

Du kan flytta resurser i Azure på följande sätt:

- **Flytta resurser över flera regioner**: flytta resurser från resurs förflyttnings hubben eller inom en resurs grupp. 
- **Flytta resurser över resurs grupper/prenumerationer**: flytta från en resurs grupp. 
- **Flytta resurser mellan Azure-moln**: använd tjänsten Azure Site Recovery för att flytta resurser mellan offentliga och offentliga moln.
- **Flytta resurser mellan tillgänglighets zoner i samma region**: Använd Azure Site Recovery tjänsten för att flytta resurser mellan tillgänglighets zoner i samma Azure-region.


## <a name="compare-move-tools"></a>Jämför flytt verktyg

**Verktyg** | **När du ska använda detta** | **Läs mer**
--- | --- 
**Flytta i resurs gruppen** | Flytta resurser till en annan resurs grupp/prenumeration, eller mellan regioner.<br/><br/> Om du flyttar mellan regioner i resurs gruppen väljer du de resurser som du vill flytta och flyttar sedan till resurs förflyttnings hubben för att kontrol lera beroenden och flytta resurserna till mål regionen. | [Flytta resurser till en annan resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).<br/><br/> [Flytta resurser till en annan region från en resurs grupp](../azure-resource-manager/management/move-region.md).
**Flytta från resurs förflyttnings hubben** | Flytta resurser mellan regioner. <br/><br/> Du kan flytta till en mål region eller till en bestämd tillgänglighets zon, eller tillgänglighets uppsättning inom mål regionen. | [Flytta resurser mellan regioner i resurs förflyttnings hubben]().
**Flytta virtuella datorer med Site Recovery** | Används för att flytta virtuella Azure-datorer mellan myndigheter och offentliga moln.<br/><br/> Använd om du vill flytta virtuella datorer mellan tillgänglighets zoner i samma region. |[Flytta resurser mellan myndigheter/offentliga moln](../site-recovery/region-move-cross-geos.md), [Flytta resurser till tillgänglighets zoner i samma region](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="next-steps"></a>Nästa steg

[Läs mer](about-move-process.md) om resurs förflyttnings komponenter och flyttnings processen.
