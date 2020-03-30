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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424159"
---
> [!NOTE]
> En webbapp kan ta time out efter 20 minuters inaktivitet. Endast begäranden till den faktiska webbappen återställer timern. Visa appens konfiguration i Azure-portalen eller göra förfrågningar`https://<app_name>.scm.azurewebsites.net`till den avancerade verktygswebbplatsen ( ) återställ inte timern. Om din app körs kontinuerligt eller schemalagt (Timer-utlösare) WebJobs aktiverar du **Alltid på** för att säkerställa att WebJobs körs på ett tillförlitligt sätt. Den här funktionen är endast tillgänglig på [prisnivåerna](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Basic, Standard och Premium .
