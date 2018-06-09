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
ms.openlocfilehash: ac2cf4d688b1bdc54ed2d7341f0e195d3b2fe42d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236481"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Vad händer med min app under distributionen?

En sak som är gemensamma för alla officiellt stöds distributionsmetoder: de göra ändringar i filerna i den `/site/home/wwwroot` mappen för din app. Detta är samma filer som körs i produktion. Därför distributionen kan misslyckas på grund av låsta filer eller program i produktion har oförutsägbara resultat under distributionen eftersom inte alla filer uppdateras samtidigt. Det finns några olika sätt att undvika de här problemen:

- Stoppa appen eller aktivera offline-läge för din app under distributionen. Mer information finns i [behandlar låsta filer under distributionen av](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Distribuera till en [mellanlagringsplatsen](../articles/app-service/web-sites-staged-publishing.md) med [automatisk växling](../articles/app-service/web-sites-staged-publishing.md#configure-auto-swap) aktiverat. 
- Använd [kör från Zip-](https://github.com/Azure/app-service-announcements/issues/84) i stället.
