---
title: Apptjänst Azure stacken uppdatera 2 viktig information | Microsoft Docs
description: Lär dig mer om nyheterna i uppdateringen två för användning på Azure-stacken, kända problem och var du kan hämta uppdateringen.
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
ms.reviewer: brenduns
ms.openlocfilehash: 8e1790b7d0b3a210a9142fc8580ff8ed4d64311c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360407"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Apptjänst på Azure-stacken uppdatering 2 viktig information

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Följande versionsinformation innehåller förbättringar och korrigeringar i Azure App Service på Azure-stacken uppdatering 2 och kända problem. Kända problem är indelade i problem som är direkt relaterade till distribution, uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]
> Uppdateringen är 1804 Azure Stack integrerade systemet eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service 1.2.
>
>

## <a name="build-reference"></a>Skapa referens

Tjänsten App på Azure-stacken uppdatering 2 build-nummer är **72.0.13698.10**

### <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> Nya distributioner av Azure App Service på Azure-stacken kräver en [tre ämne jokerteckencertifikat](azure-stack-app-service-before-you-get-started.md#get-certificates) på grund av förbättringar i hanteringen av där enkel inloggning för Kudu är nu i Azure App Service. Det nya ämnet är  **\*. sso.appservice.\< region\>.\< DomainName\>.\< tillägg\>**
>
>

Referera till den [innan du börjar dokumentationen](azure-stack-app-service-before-you-get-started.md) innan du påbörjar distributionen.

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Azure App Service på Azure-stacken uppdatering 2 innehåller följande förbättringar och korrigeringar:

- Uppdateringar för **App Service-klient, Admin funktioner portaler och Kudu verktyg**. Konsekvent med Azure-stacken Portal SDK-version.

- Uppdateringar för kärntjänsten för att förbättra tillförlitligheten och fel messaging aktiverar enklare diagnos av vanliga problem.

- **Uppdateringar för följande ramverk för programmet och verktyg**:
  - Tillagda .net Framework 4.7.1
  - Lägga till **Node.JS** versioner:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Lägga till **NPM** versioner:
    - 5.6.0
  - Uppdatera .net kärnkomponenter överensstämmer med Azure App Service i offentliga moln.
  - Uppdaterade Kudu

- Automatisk växling av distribution fack-funktionen aktiverad - [konfigurerar automatiskt växla](https://docs.microsoft.com/azure/app-service/web-sites-staged-publishing#configure-auto-swap)

- Testa i produktion-funktionen aktiverad - [introduktion till testa i produktion](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Azure Functions proxyservrar aktiverat – [arbeta med Azure Functions proxyservrar](https://docs.microsoft.com/en-us/azure/azure-functions/functions-proxies)

- Apptjänst Admin tillägget UX stöd lagts till för:
  - Hemlig rotation
  - Certifikatet rotation
  - System autentiseringsuppgifter rotation
  - Anslutningen sträng rotation

### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

- Anställda kan inte nå filserver när Apptjänst distribueras i ett befintligt virtuellt nätverk och filservern är endast tillgängligt i det privata nätverket.

Om du vill distribuera till ett befintligt virtuellt nätverk och en intern IP-adress för att ansluta till servern, du måste lägga till utgående säkerhetsregel aktivera SMB-trafik mellan worker-undernät och filservern. Gör detta genom att gå till WorkersNsg i Admin Portal och Lägg till utgående säkerhetsregel med följande egenskaper:
 * Källa: alla
 * Datakällan portintervall: *
 * Mål: IP-adresser
 * Mål-IP-adressintervall: intervall av IP-adresser för din filserver
 * Målportintervall: 445
 * Protokoll: TCP
 * Åtgärd: Tillåt
 * Prioritet: 700
 * Namn: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Kända problem för molnet administratörer fungerar Azure App Service på Azure-stacken

Dokumentationen i det [Azure Stack 1804 viktig information](azure-stack-update-1804.md)

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure App Service finns [Azure App Service på Azure-stacken översikt](azure-stack-app-service-overview.md).
- Mer information om hur du distribuerar Apptjänst Azure stacken finns [innan du börjar med App Service på Azure-stacken](azure-stack-app-service-before-you-get-started.md).
