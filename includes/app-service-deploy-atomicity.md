---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945161"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Vad händer med min app under distributionen?

Alla distributions metoder som stöds officiellt gör ändringar i filerna i appens `/home/site/wwwroot` mapp. De här filerna används för att köra din app. Distributionen kan därför inte utföras på grund av låsta filer. Appen kan också uppstå oförutsägbart under distributionen, eftersom inte alla filer uppdateras samtidigt. Detta är olämpligt för en kundriktad app. Det finns flera olika sätt att undvika dessa problem:

- [Kör appen från zip-paketet direkt](../articles/app-service/deploy-run-package.md) utan att packa upp den.
- Stoppa appen eller Aktivera offline-läge för appen under distributionen. Mer information finns i [Hantera låsta filer under distributionen](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Distribuera till en [mellanlagringsplats](../articles/app-service/deploy-staging-slots.md) med [Automatisk växling](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) aktiverat. 
