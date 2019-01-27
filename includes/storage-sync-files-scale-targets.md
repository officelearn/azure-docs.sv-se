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
ms.openlocfilehash: 6d9370ce6a031377e37c4e830400337f3e57a62e
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55084832"
---
| Resurs | Mål | Hård gräns |
|----------|--------------|------------|
| Lagringstjänster för synkronisering per region | 15 lagringstjänster för synkronisering | Ja |
| Synkroniseringsgrupper per tjänst för Lagringssynkronisering | 100 synkroniseringsgrupper | Ja |
| Registrerade servrar per tjänst för Lagringssynkronisering | 99 servrar | Ja |
| Molnslutpunkter per Synkroniseringsgrupp | 1 molnslutpunkt | Ja |
| Serverslutpunkter per Synkroniseringsgrupp | 50 serverslutpunkter | Nej |
| Serverslutpunkter per server | 30 serverslutpunkter | Ja |
| Slutpunkten storlek | 4 TiB | Nej |
| Filsystemobjekt (kataloger och filer) per synkroniseringsgrupp | 25 miljoner objekt | Nej |
| Maxantalet filsystemsobjekt (kataloger och filer) i en katalog | 200 000 objekt | Ja |
| Namnlängd för maximal objekt (kataloger och filer) | 255 tecken | Ja |
| Maximal (kataloger och filer) security descriptor Objektstorlek | 4 KiB | Ja |
| Filstorlek | 100 giB | Nej |
| Minsta filstorlek för en fil för att vara nivåindelad | 64 KiB | Ja |
| Samtidiga synkroniseringssessioner | V4-agenten: Filgränsen varierar beroende på tillgängliga systemresurser. <BR> V3 agent: 2 active sync-sessioner per processor- eller högst 8 active sync-sessioner per server | Ja
