---
title: ta med fil
description: ta med fil
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738691"
---
Nej: tar bort en serverslutpunkt är inte som en servern startas om! Ta bort och återskapa Serverslutpunkten är nästan aldrig en lämplig lösning för att åtgärda problem med synkronisering, molnet skiktning eller andra aspekter av Azure filsynkronisering. Ta bort en serverslutpunkt för är en destruktiva åtgärd och kan resultera i förlust av data i de fall som nivåer filer som finns utanför servern endpoint namnområde (finns [varför nivåindelade filer finnas utanför servern endpoint namnområdet](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) för Mer information) eller i otillgängliga filer för nivåindelade filer som finns inom namnområdet server slutpunkt. Dessa problem kommer inte att matcha när Serverslutpunkten återskapas. Nivåindelad filer kan finnas inom namnområdet server endpoint även om du aldrig hade molnet skiktning aktiverad. Vi rekommenderar därför att du inte tar bort Serverslutpunkten om du vill sluta använda Azure filsynkronisering med den här viss mapp eller uttryckligen har fått instruktioner att göra det av en Microsoft-tekniker. Läs mer på Ta bort server slutpunkter [ta bort en serverslutpunkt för](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    