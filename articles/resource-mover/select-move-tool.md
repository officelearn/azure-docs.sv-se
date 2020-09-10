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
ms.openlocfilehash: 8af5cd4f961a2138f09bb3e56fccf7aa8f726dd9
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653617"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Välj ett verktyg för att flytta Azure-resurser

Du kan flytta resurser i Azure på följande sätt:

- **Flytta resurser över flera regioner**: flytta resurser från resurs förflyttnings hubben eller inom en resurs grupp. 
- **Flytta resurser över resurs grupper/prenumerationer**: flytta från en resurs grupp. 
- **Flytta resurser mellan Azure-moln**: använd tjänsten Azure Site Recovery för att flytta resurser mellan offentliga och offentliga moln.
- **Flytta resurser mellan tillgänglighets zoner i samma region**: Använd Azure Site Recovery tjänsten för att flytta resurser mellan tillgänglighets zoner i samma Azure-region.


## <a name="compare-move-tools"></a>Jämför flytt verktyg

**Verktyg** | **När du ska använda detta**
--- | --- 
**Flytta i resurs gruppen** | Flytta resurser till en annan resurs grupp/prenumeration, eller mellan regioner.<br/><br/> Om du flyttar mellan regioner i resurs gruppen väljer du de resurser som du vill flytta och flyttar sedan till resurs förflyttnings hubben för att kontrol lera beroenden och flytta resurserna till mål regionen.
**Flytta från resurs förflyttnings hubben** | Flytta resurser mellan regioner. <br/><br/> Du kan flytta till en mål region eller till en bestämd tillgänglighets zon, eller tillgänglighets uppsättning inom mål regionen.
**Flytta virtuella datorer med Site Recovery** | Används för att flytta virtuella Azure-datorer mellan myndigheter och privata moln.<br/><br/> Använd om du vill flytta virtuella datorer mellan tillgänglighets zoner i samma region.

## <a name="next-steps"></a>Nästa steg

[Läs mer](about-move-process.md) om resurs förflyttnings komponenter och flyttnings processen.
