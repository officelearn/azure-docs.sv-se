---
title: inkludera fil
description: inkludera fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "72391805"
---
Nej: att ta bort en server slut punkt är inte lika med att starta om en server! Att ta bort och återskapa Server slut punkten är nästan aldrig en lämplig lösning för att åtgärda problem med synkronisering, moln nivåer eller andra aspekter av Azure File Sync. Att ta bort en server slut punkt är en destruktiv åtgärd. Det kan leda till data förlust om det finns skiktade filer utanför Server slut punktens namn område. Mer information finns i [Varför finns det skiktade filer utanför Server slut punktens namnrymd](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) . Eller så kan det leda till otillgängliga filer för nivåbaserade filer som finns i Server slut punktens namn område. De här problemen löses inte när Server slut punkten återskapas. Skiktade filer kan finnas i Server slut punktens namn område även om du aldrig har aktiverat moln nivåer. Därför rekommenderar vi att du inte tar bort Server slut punkten om du inte vill sluta använda Azure File Sync med den här mappen eller uttryckligen har instruerats att göra det av en Microsoft-tekniker. Mer information om hur du tar bort Server slut punkter finns i [ta bort en server slut punkt](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
