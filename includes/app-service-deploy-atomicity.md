---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836752"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Vad händer med min app under distributionen?

Alla distributionsmetoder som stöds officiellt gör ändringar i filerna i den `/home/site/wwwroot` -mappen i appen. De här filerna är samma som körs i produktion. Därför kan distributionen misslyckas på grund av låsta filer. Appen i produktion kan också fungera oförutsägbart under distributionen, eftersom inte alla filerna som uppdateras samtidigt. Det finns några olika sätt att undvika dessa problem:

- Stoppa din app eller aktivera offline-läge för din app under distributionen. Mer information finns i [utan låsta filer under distributionen av](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Distribuera till en [mellanlagringsplatsen](../articles/app-service/deploy-staging-slots.md) med [Autoväxling](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) aktiverat. 
- Använd [kör från paketet](https://github.com/Azure/app-service-announcements/issues/84) i stället för kontinuerlig distribution.
