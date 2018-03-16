---
title: "Apptjänst Azure Stack Update en | Microsoft Docs"
description: "Lär dig mer om vad som finns i en uppdatering för Apptjänst Azure stacken kända problem och var du kan hämta uppdateringen."
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 0c33c8fdefbb27ba8414e58bed1b42ee7aaba88a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="app-service-on-azure-stack-update-one-release-notes"></a>Apptjänst Azure stacken uppdatera en viktig information

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Följande versionsinformation innehåller förbättringar och korrigeringar i Azure App Service på Azure-stacken uppdatering 1 och kända problem. Kända problem är indelade i problem som är direkt relaterade till distribution, uppdateringsprocessen och problem med build (efter installationen).

> [!IMPORTANT]
> Uppdateringen är 1802 Azure Stack integrerade systemet eller distribuera den senaste Azure Stack development kit innan du distribuerar Azure App Service.
>
>

## <a name="build-reference"></a>Skapa referens

Tjänsten App på Azure-stacken uppdatering 1 build-nummer är **69.0.13698.9**

### <a name="prerequisites"></a>Förutsättningar

> [!IMPORTANT]
> Azure Apptjänst Azure stacken kräver nu en [tre ämne jokerteckencertifikat](azure-stack-app-service-before-you-get-started.md#get-certificates) på grund av förbättringar i hanteringen av där enkel inloggning för Kudu är nu i Azure App Service.  Det nya ämnet är ** *. sso.appservice.<region>. <domainname>.<extension>**
>
>

Referera till den [innan du börjar dokumentationen](azure-stack-app-service-before-you-get-started.md) innan du påbörjar distributionen.

### <a name="new-features-and-fixes"></a>Nya funktioner och korrigeringar

Azure App Service på Azure-stacken uppdatering 1 innehåller följande förbättringar och korrigeringar:

- **Hög tillgänglighet i Azure App Service** -det Azure-stacken 1802 update har aktiverats arbetsbelastningar som ska distribueras över fault-domäner.  Därför kan Apptjänst infrastruktur vara feltolerant som kommer att distribueras över feldomäner.  Som standard har alla nya distributioner av Azure App Service funktionen men för distributioner som har slutförts innan Azure Stack 1802 uppdateringen tillämpas avser den [Feldomän för App Service-dokumentation](azure-stack-app-service-fault-domain-update.md)

- **Distribuera i befintligt virtuellt nätverk** -kunder kan nu distribuera Apptjänst Azure stacken inom ett befintligt virtuellt nätverk.  Distribution i ett befintligt virtuellt nätverk ger kunder möjlighet att ansluta till SQL Server och filserver, krävs för Azure App Service via privata portar.  Under distributionen av kunder kan välja för att distribuera i ett befintligt virtuellt nätverk, men [skapa undernät för användning av App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) före distributionen.

- Uppdateringar för **App Service-klient, Admin funktioner portaler och Kudu verktyg**.  Konsekvent med Azure-stacken Portal SDK-version.

- **Uppdateringar för följande ramverk för programmet och verktyg**:
    - Lägga till **.Net Core 2.0** stöd
    - Lägga till **Node.JS** versioner:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Lägga till **NPM** versioner:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Lägga till **PHP** uppdateringar:
        - 5.6.32
        - 7.0.26 (x86 och x64)
        - 7.1.12 (x86 och x64)
    - Uppdatera **Git för Windows** v 2.14.1
    - Uppdatera **ett** till v4.5.0

  - Tillagt stöd för **endast HTTPS** funktion inom anpassad domän funktion i App Service-klient-portalen. 

  - Tillagda verifiering av anslutning till lagringsplatsen i Väljaren anpassad lagring för Azure Functions 

#### <a name="fixes"></a>Korrigeringar

- När du skapar ett offline distributionspaket får kunder inte längre ett åtkomst nekad när öppna mappen från installationsprogrammet för App Service

- Lösa problem när du arbetar i funktionen anpassade domäner i App Service-klient-portalen.

- Förhindra att kunder som använder reserverade administratör namn under installationen

- Aktiverad App Service-distributionen med **domänanslutna** filserver

- Förbättrad hämtning av Azure-stacken root certifikat i skript och nu Validera cert rot i installationsprogrammet för App Service.

- Fast felaktig status som returneras till Azure Resource Manager när en prenumeration är ta bort de befintliga resurserna i namnområdet Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Kända problem med distributionen

- Det finns inga kända problem för distribution av Azure App Service på Azure-stacken uppdatering 1.

### <a name="known-issues-with-the-update-process"></a>Kända problem med uppdateringen

- Det finns inga kända problem för uppdatering av Azure App Service på Azure-stacken uppdatering 1.

### <a name="known-issues-post-installation"></a>Kända problem (efter installationen)

- Det finns inga kända problem för installation av Azure App Service på Azure-stacken uppdatering 1.

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Kända problem för molnet administratörer fungerar Azure App Service på Azure-stacken

Dokumentationen i det [Azure Stack 1802 viktig information](azure-stack-update-1802.md)

## <a name="see-also"></a>Se också

- En översikt över Azure App Service finns [Azure App Service på Azure-stacken översikt](azure-stack-app-service-overview.md).
- Mer information om hur du distribuerar Apptjänst Azure stacken finns [innan du börjar med App Service på Azure-stacken](azure-stack-app-service-before-you-get-started.md).
