---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391805"
---
Nej: att ta bort en serverslutpunkt är inte som att starta om en server! Att ta bort och återskapa serverslutpunkten är nästan aldrig en lämplig lösning för att åtgärda problem med synkronisering, molnnivådelning eller andra aspekter av Azure File Sync. Att ta bort en serverslutpunkt är en destruktiv åtgärd. Det kan leda till dataförlust om nivåindelade filer finns utanför serverslutpunktsnamnområdet. Se [Varför finns nivåindelade filer utanför serverslutpunktsnamnområdet](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) för mer information. Eller det kan resultera i otillgängliga filer för nivåindelade filer som finns inom serverns slutpunktnamnområde. Dessa problem lösers inte när serverslutpunkten återskapas. Nivåindelad filer kan finnas i serverslutpunktsnamnområdet även om du aldrig hade aktiverat molnnivådelning. Därför rekommenderar du att du inte tar bort serverslutpunkten om du inte vill sluta använda Azure File Sync med den här mappen eller uttryckligen har instruerats att göra det av en Microsoft-tekniker. Mer information om hur du tar bort serverslutpunkter finns i [Ta bort en serverslutpunkt](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
