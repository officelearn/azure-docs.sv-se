---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765656"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Vad händer med min app under distributionen?

Alla distributionsmetoder som stöds officiellt har en sak gemensamt: de göra ändringar i filerna i den `/home/site/wwwroot` -mappen i appen. Det här är samma filer som körs i produktionsmiljön. Därför distributionen kan misslyckas på grund av låsta filer eller appen i produktion kan ha oväntade funktionssätt under distributionen eftersom inte alla filerna uppdateras samtidigt. Det finns några olika sätt att undvika dessa problem:

- Stoppa din app eller aktivera offline-läge för din app under distributionen. Mer information finns i [hanterar låsta filer under distributionen av](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Distribuera till en [mellanlagringsplatsen](../articles/app-service/deploy-staging-slots.md) med [Autoväxling](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) aktiverat. 
- Använd [kör från paketet](https://github.com/Azure/app-service-announcements/issues/84) i stället.
