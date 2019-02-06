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
ms.openlocfilehash: fea31bf1c5869164dde1eb7014b2aacc8fcc5e9c
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55757559"
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
| Maximal (kataloger och filer) security descriptor Objektstorlek | 4 KiB | Ja |
| Filstorlek | 100 giB | Nej |
| Minsta filstorlek för en fil för att vara nivåindelad | 64 KiB | Ja |
| Samtidiga synkroniseringssessioner | V4-agenten: Filgränsen varierar beroende på tillgängliga systemresurser. <BR> V3 agent: 2 active sync-sessioner per processor- eller högst 8 active sync-sessioner per server | Ja
