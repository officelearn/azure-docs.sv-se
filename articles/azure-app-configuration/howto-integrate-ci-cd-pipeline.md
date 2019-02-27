---
title: Integrera med en pipeline för kontinuerlig integrering och leverans som har med Azure-Appkonfiguration | Microsoft Docs
description: Lär dig hur du skapar en konfigurationsfil med hjälp av data i Azure-Appkonfiguration vid kontinuerlig integrering och leverans
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 0e6b24175ffa28b2a0f361bc46fd6c41e09cae72
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885440"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrera med en CI/CD-pipeline

För att förbättra elasticiteten i ditt program mot remote risken att du inte att kunna nå Azure konfiguration, bör du paketera aktuella konfigurationsdata i en fil som har distribuerats med programmet och läsa in lokalt under Start . Den här metoden garanterar att ditt program har inställningen standardvärden minst. Dessa värden kommer att skrivas över av nya ändringar i en appbutik konfiguration när den är tillgänglig.

## <a name="automate-configuration-data-retrieval"></a>Automatisera konfiguration datahämtning

Med hjälp av den [ **exportera** ](./howto-import-export-data.md#export-data) funktionen för konfiguration av Azure, kan du automatisera processen för att hämta aktuella konfigurationsdata som en enda fil. Du kan sedan inkludera den här filen i ett bygge eller distribution steg i din kontinuerlig integrering och kontinuerlig distributionspipeline.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa en ASP.NET-webbapp](quickstart-aspnet-core-app.md)  
