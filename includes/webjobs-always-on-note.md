---
title: ta med fil
description: ta med fil
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424159"
---
> [!NOTE]
> En webbapp kan ta lång tid efter 20 minuters inaktivitet. Endast begär anden till den faktiska webbappen återställer timern. Om du visar appens konfiguration i Azure Portal eller gör förfrågningar till webbplatsen för avancerade verktyg (`https://<app_name>.scm.azurewebsites.net`) återställs inte timern. Om din app kör kontinuerliga eller schemalagda webbjobber, aktiverar du **Always on** för att säkerställa att webbjobben körs på ett tillförlitligt sätt. Den här funktionen är endast tillgänglig på [pris nivåerna](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Basic, standard och Premium.
