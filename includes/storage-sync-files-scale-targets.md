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
ms.openlocfilehash: e7aa2b4389fe60eed80b15aff04d6f7fcbc7b013
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968943"
---
| Resource | Mål | Hård gräns |
|----------|--------------|------------|
| Tjänster för synkronisering av lagring per region | 20 lagrings tjänster för synkronisering | Ja |
| Synkronisera grupper per tjänst för synkronisering av lagring | 100 Sync-grupper | Ja |
| Registrerade servrar per tjänst för synkronisering av lagring | 99-servrar | Ja |
| Moln slut punkter per Sync-grupp | 1 moln slut punkt | Ja |
| Server slut punkter per Sync-grupp | 50 Server slut punkter | Nej |
| Server slut punkter per server | 30 Server slut punkter | Ja |
| Fil system objekt (kataloger och filer) per Sync-grupp | 50 000 000 objekt | Nej |
| Maximalt antal fil system objekt (kataloger och filer) i en katalog | 5 000 000 objekt | Ja |
| Säkerhets beskrivnings storlek för maximalt objekt (kataloger och filer) | 64 KiB | Ja |
| Filstorlek | 100 GiB | Nej |
| Minsta fil storlek för en fil i nivå | 64 KiB | Ja |
| Samtidiga synkroniseringsbegäranden | V4-agenten och senare: Gränsen varierar beroende på tillgängliga system resurser. <BR> V3-agent: Två aktiva Sync-sessioner per processor eller högst åtta aktiva Sync-sessioner per server. | Ja

> [!Note]  
> En Azure File Sync-slutpunkt kan skala upp till storleken på en Azure-filresurs. Om storleks gränsen för Azure-filresursen uppnås går det inte att använda synkroniseringen.
