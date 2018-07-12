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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38763103"
---
Nej: ta bort en serverslutpunkt är inte som en servern startas om! Ta bort och återskapa Serverslutpunkten är nästan aldrig en lämplig lösning för att åtgärda problem med synkronisering, lagringsnivåer för moln eller andra aspekter av Azure File Sync. Tar bort en serverslutpunkt är en destruktiv åtgärd och kan leda till förlust av data i de fall som nivåindelade filer som finns utanför den slutpunkten namnrymden (se [varför nivåindelade filer finns utanför den slutpunkten namnrymden](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) för Mer information) eller i otillgängliga filer för nivåindelade filer som finns inom namnområdet för server-slutpunkt. De här problemen kommer inte att matcha när Serverslutpunkten återskapas. Nivåindelade filer kan finnas samtidigt i namnområdet för slutpunkten server även om du aldrig haft molnlagringsnivåer aktiverad. Vi rekommenderar därför att du inte tar bort Serverslutpunkten om du vill sluta använda Azure File Sync med den här specifika mappen eller uttryckligen har fått instruktioner att göra detta av en Microsoft-tekniker. Läs mer på Ta bort serverslutpunkter [ta bort en serverslutpunkt](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    