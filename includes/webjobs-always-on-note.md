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
ms.openlocfilehash: 14f2e663e3db81684a73c4ea093ed0403cbb09ed
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751100"
---
> [!NOTE]
> En webbapp kan timeout efter 20 minuters inaktivitet. Endast begäranden till den faktiska webbappen återställer timern. Visa appens konfiguration i Azure-portalen eller genom att göra begäranden till webbplatsen avancerade verktyg (`https://<app_name>.scm.azurewebsites.net`) inte återställa timern. Om din app körs kontinuerligt eller schemalagt WebJobs, aktivera **alltid på** så att Webbjobb körs på ett tillförlitligt sätt. Den här funktionen är endast tillgänglig i den Basic, Standard och Premium [prisnivåer](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
