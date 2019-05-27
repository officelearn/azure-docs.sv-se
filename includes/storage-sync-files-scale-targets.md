---
title: ta med fil
description: ta med fil
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114528"
---
| Resource | Mål | Hård gräns |
|----------|--------------|------------|
| Lagringstjänster för synkronisering per region | 20 lagringstjänster för synkronisering | Ja |
| Synkroniseringsgrupper per tjänst för Lagringssynkronisering | 100 synkroniseringsgrupper | Ja |
| Registrerade servrar per tjänst för Lagringssynkronisering | 99 servrar | Ja |
| Molnslutpunkter per synkroniseringsgrupp | 1 molnslutpunkt | Ja |
| Serverslutpunkter per synkroniseringsgrupp | 50 serverslutpunkter | Nej |
| Serverslutpunkter per server | 30 serverslutpunkter | Ja |
| Filsystemobjekt (kataloger och filer) per synkroniseringsgrupp | 25 miljoner objekt | Nej |
| Maxantalet filsystemsobjekt (kataloger och filer) i en katalog | 1 miljon objekt | Ja |
| Maximal (kataloger och filer) security descriptor Objektstorlek | 64 KiB | Ja |
| Filstorlek | 100 giB | Nej |
| Minsta filstorlek för en fil för att vara nivåindelad | 64 KiB | Ja |
| Samtidiga synkroniseringssessioner | V4-agenten och senare: Gränsen varierar beroende på tillgängliga systemresurser. <BR> V3 agent: Två active sync-sessioner per processor eller högst åtta active sync-sessioner per server. | Ja

> [!Note]  
> En slutpunkt för Azure File Sync kan skala upp till storleken på en Azure-filresurs. Om storleksgränsen för Azure file-resursen har nåtts synkroniseringen inte fungerar.