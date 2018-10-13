---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/08/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 91a4a9ae1d3d84f1396adad07d1cda73ee3747c9
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49312486"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Vad händer med min app under distributionen?

Alla distributionsmetoder som stöds officiellt har en sak gemensamt: de göra ändringar i filerna i den `/site/home/wwwroot` -mappen i appen. Det här är samma filer som körs i produktionsmiljön. Därför distributionen kan misslyckas på grund av låsta filer eller appen i produktion kan ha oväntade funktionssätt under distributionen eftersom inte alla filerna uppdateras samtidigt. Det finns några olika sätt att undvika dessa problem:

- Stoppa din app eller aktivera offline-läge för din app under distributionen. Mer information finns i [hanterar låsta filer under distributionen av](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Distribuera till en [mellanlagringsplatsen](../articles/app-service/web-sites-staged-publishing.md) med [Autoväxling](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) aktiverat. 
- Använd [kör från paketet](https://github.com/Azure/app-service-announcements/issues/84) i stället.
