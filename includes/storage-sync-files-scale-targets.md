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
ms.openlocfilehash: 19c570be71583a2933a39780ba948edeb9f68498
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129116"
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
| Maximal (kataloger och filer) security descriptor Objektstorlek | 64 KiB | Ja |
| Filstorlek | 100 giB | Nej |
| Minsta filstorlek för en fil för att vara nivåindelad | 64 KiB | Ja |
| Samtidiga synkroniseringssessioner | V4-agenten och senare: Gränsen varierar beroende på tillgängliga systemresurser. <BR> V3 agent: Två active sync-sessioner per processor eller högst åtta active sync-sessioner per server. | Ja
