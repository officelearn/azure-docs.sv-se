---
title: ta med fil
description: ta med fil
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: dfba8db87dab12f856fbd97d578321477e9f92b5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554088"
---
| Resurs | Mål | Hård gräns |
|----------|--------------|------------|
| Lagringstjänster för synkronisering per region | 15 lagringstjänster för synkronisering | Ja |
| Synkroniseringsgrupper per tjänst för Lagringssynkronisering | 100 synkroniseringsgrupper | Ja |
| Registrerade servrar per tjänst för Lagringssynkronisering | 99 servrar | Ja |
| Molnslutpunkter per synkroniseringsgrupp | 1 molnslutpunkt | Ja |
| Serverslutpunkter per synkroniseringsgrupp | 50 serverslutpunkter | Nej |
| Serverslutpunkter per server | 30 serverslutpunkter | Ja |
| Slutpunkten storlek | 4 TiB | Nej |
| Filsystemobjekt (kataloger och filer) per synkroniseringsgrupp | 25 miljoner objekt | Nej |
| Maxantalet filsystemsobjekt (kataloger och filer) i en katalog | 1 miljon objekt | Ja |
| Maximal (kataloger och filer) security descriptor Objektstorlek | 4 KiB | Ja |
| Filstorlek | 100 giB | Nej |
| Minsta filstorlek för en fil för att vara nivåindelad | 64 KiB | Ja |
| Samtidiga synkroniseringssessioner | V4-agenten och senare: Gränsen varierar beroende på tillgängliga systemresurser. <BR> V3 agent: Två active sync-sessioner per processor eller högst åtta active sync-sessioner per server. | Ja
