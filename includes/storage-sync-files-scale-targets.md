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
ms.openlocfilehash: 3f70a8cf2df25f487de7cd1a8c8cbdf9431839f0
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285629"
---
| Resurs | Mål | Hård gräns |
|----------|--------------|------------|
| Lagringstjänster för synkronisering per prenumeration | 15 lagringstjänster för synkronisering | Nej |
| Synkroniseringsgrupper per tjänst för Lagringssynkronisering | 100 synkroniseringsgrupper | Ja |
| Registrerade servrar per tjänst för Lagringssynkronisering | 99 servrar | Ja |
| Molnslutpunkter per Synkroniseringsgrupp | 1 molnslutpunkt | Ja |
| Serverslutpunkter per Synkroniseringsgrupp | 50 serverslutpunkter | Nej |
| Serverslutpunkter per server | 33-99 serverslutpunkter | Ja, men varierar beroende på konfigurationen (processor, minne, volymer, filen omsättning, antal filer, osv.) |
| Slutpunkten storlek | 4 TiB | Nej |
| Filsystemobjekt (kataloger och filer) per synkroniseringsgrupp | 25 miljoner objekt | Nej |
| Maxantalet filsystemsobjekt (kataloger och filer) i en katalog | 200 000 objekt | Ja |
| Namnlängd för maximal objekt (kataloger och filer) | 255 tecken | Ja |
| Maximal (kataloger och filer) security descriptor Objektstorlek | 4 KiB | Ja |
| Filstorlek | 100 giB | Nej |
| Minsta filstorlek för en fil för att vara nivåindelad | 64 KiB | Ja |
