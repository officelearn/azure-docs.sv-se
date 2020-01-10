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
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752006"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Vad händer med min app under distributionen?

Alla distributions metoder som stöds officiellt gör ändringar i filerna i appens `/home/site/wwwroot` mapp. De här filerna används för att köra din app. Distributionen kan därför inte utföras på grund av låsta filer. Appen kan också uppstå oförutsägbart under distributionen, eftersom inte alla filer uppdateras samtidigt. Detta är olämpligt för en kundriktad app. Det finns flera olika sätt att undvika dessa problem:

- Stoppa appen eller Aktivera offline-läge för appen under distributionen. Mer information finns i [Hantera låsta filer under distributionen](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Distribuera till en [mellanlagringsplats](../articles/app-service/deploy-staging-slots.md) med [Automatisk växling](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) aktiverat. 
- Använd [Kör från paket](https://github.com/Azure/app-service-announcements/issues/84) i stället för kontinuerlig distribution.
