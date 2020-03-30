---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74796201"
---
| Resurs | Mål | Hård gräns |
|----------|--------------|------------|
| Lagringssynkroniseringstjänster per region | 20 synkroniseringstjänster för lagring | Ja |
| Synkronisera grupper per synkroniseringstjänst för lagring | 100 synkroniseringsgrupper | Ja |
| Registrerade servrar per synkroniseringstjänst för lagring | 99 servrar | Ja |
| Molnslutpunkter per synkroniseringsgrupp | 1 slutpunkt i molnet | Ja |
| Serverslutpunkter per synkroniseringsgrupp | 50 serverslutpunkter | Inga |
| Serverslutpunkter per server | 30 serverslutpunkter | Ja |
| Filsystemobjekt (kataloger och filer) per synkroniseringsgrupp | 100 miljoner objekt | Inga |
| Maximalt antal filsystemobjekt (kataloger och filer) i en katalog | 5 miljoner objekt | Ja |
| Maximal objekt (kataloger och filer) säkerhetsbeskrivning storlek | 64 KiB (olika) | Ja |
| Filstorlek | 100 Gib (100 GiB) | Inga |
| Minsta filstorlek för en fil som ska nivåindelad | V9: Baserat på filsystemet klusterstorlek (dubbel fil systemklusterstorlek). Om filsystemets klusterstorlek till exempel är 4kb är den minsta filstorleken 8kb.<br> V8 och äldre: 64 KiB  | Ja |

> [!Note]  
> En Slutpunkt för Azure File Sync kan skala upp till storleken på en Azure-filresurs. Om storleksgränsen för Azure-filresursen har uppnåtts kan synkroniseringen inte fungera.
