---
title: App Service i Azure Stack uppdatera 2 viktig information | Microsoft Docs
description: Läs mer om nyheter i uppdateringen två för App Service i Azure Stack, kända problem och var du kan hämta uppdateringen.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: ec0c80925770c75ee1f23df29f1724444c1e7337
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249671"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>App Service i Azure Stack uppdatering 2 viktig information

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Följande versionsinformation innehåller förbättringar och korrigeringar i Azure App Service på Azure Stack Update 2 och kända problem. Kända problem är indelade i problem som är direkt relaterade till distribution, uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]
> Uppdateringen är 1804 integrerade Azure Stack-system eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service 1.2.
>
>

## <a name="build-reference"></a>Skapa referens

App Service på Azure Stack Update 2 build-nummer är **72.0.13698.10**

### <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> Nya distributioner av Azure App Service i Azure Stack nu kräver en [tre ämne jokerteckencertifikat](azure-stack-app-service-before-you-get-started.md#get-certificates) tack vare förbättringar i hanteringen av där enkel inloggning för Kudu är nu i Azure App Service. Det nya ämnet är  **\*. sso.appservice.\< region\>.\< DomainName\>.\< tillägget\>**
>
>

Referera till den [innan du börjar dokumentation](azure-stack-app-service-before-you-get-started.md) innan du påbörjar distributionen.

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Azure App Service i Azure Stack Update 2 innehåller följande förbättringar och korrigeringar:

- Uppdaterar till **App Service-klient, Admin, portaler för funktioner och verktyg för Kudu**. Konsekvent med Azure Stack Portal SDK-version.

- Core-tjänst för att förbättra tillförlitlighet och felmeddelanden aktiverar enklare undersöka vanliga problem med uppdateringar.

- **Uppdateringar till följande ramverk för programmet och verktyg**:
  - Har lagts till .net Framework 4.7.1
  - Lagt till **Node.JS** versioner:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Lagt till **NPM** versioner:
    - 5.6.0
  - Uppdatera .net viktiga komponenter för att överensstämma med Azure App Service i offentliga moln.
  - Uppdaterade Kudu

- Autoväxling av distribution platser funktionen aktiverad - [konfigurera automatisk växling](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- Testa i produktion-funktionen aktiverad - [introduktion till testning i produktion](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Azure Functions Proxies aktiverat - [arbeta med Azure Functions Proxies](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- App Service-admininistrationstillägg UX stöd har lagts till för:
  - Hemlig rotation
  - Certifikatrotering
  - System credential rotation
  - Sträng anslutningsrotationen

### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

- Arbetare är inte nå filserver när App Service har distribuerats i ett befintligt virtuellt nätverk och servern är endast tillgänglig i det privata nätverket.

Om du väljer att distribuera till ett befintligt virtuellt nätverk och en intern IP-adress för att ansluta till filservern, du måste lägga till en utgående säkerhetsregel att aktivera SMB-trafik mellan worker-undernät och filservern. Gör detta genom att gå till WorkersNsg i Admin Portal och Lägg till en utgående säkerhetsregel med följande egenskaper:
 * Källa: Alla
 * Käll-portintervall: *
 * Mål: IP-adresser
 * Mål-IP-adressintervall: Intervall av IP-adresser för din filserver
 * Målportintervall: 445
 * Protokoll: TCP
 * Åtgärd: Tillåt
 * Prioritet: 700
 * Namn: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Kända problem med Cloud administratörerna fungerar Azure App Service i Azure Stack

Finns i dokumentationen i den [viktig information om Azure Stack 1804](azure-stack-update-1804.md)

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure App Service finns i [Azure App Service i Azure Stack-översikt](azure-stack-app-service-overview.md).
- Mer information om hur du förbereder att distribuera App Service i Azure Stack finns i [innan du sätter igång med App Service i Azure Stack](azure-stack-app-service-before-you-get-started.md).
