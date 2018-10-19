---
title: Infrastruktur och anslutning till SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Konfigurera kräver anslutning infrastruktur för att använda SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d8bbe2fc218004116177c4c9d95777d9ec57503
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426093"
---
# <a name="sap-hana-large-instances-deployment"></a>Distribution av SAP HANA (stora instanser) 

Den här artikeln förutsätter att du har slutfört köpet av SAP HANA på Azure (stora instanser) från Microsoft. Innan du läser den här artikeln för grundläggande, se [HANA stora instanser vanliga termer](hana-know-terms.md) och [HANA stora instanser SKU: er](hana-available-skus.md).


Microsoft kräver följande information för att distribuera HANA stora instanser enheter:

- Kundens namn.
- Kontaktinformation (inklusive e-postadress och telefonnummer nummer).
- Teknisk kontaktinformation (inklusive e-postadress och telefonnummer nummer).
- Teknisk nätverk kontaktinformation (inklusive e-postadress och telefonnummer nummer).
- Azure-distribution område (till exempel västra USA, Östra Australien eller Nordeuropa).
- SAP HANA på Azure (stora instanser) SKU (konfiguration).
- För varje region för Azure-distribution:
    - Ett/29 IP-adressintervall för ER P2P-anslutningar som ansluter Azure-nätverk till stora HANA-instanser.
    - Ett/24 CIDR-Block som används för IP-adresspoolen HANA stora instanser av servern.
- IP-adressintervallet värdena som används i attributet virtuellt nätverk adress utrymme för varje Azure-nätverk som ansluter till HANA stora instanser.
- Data för varje HANA stora instanser system:
  - Önskade värdnamnet, helst med ett fullständigt kvalificerat domännamn.
  - Önskad IP-adress för HANA stora instanser enhet utanför de Server-IP-adressintervallet i poolen. (30 första IP-adresser i adressintervallet för server IP-pool är reserverade för internt bruk inom HANA stora instanser).
  - SAP HANA SID-namn för SAP HANA-instansen (krävs för att skapa de nödvändiga SAP HANA-relaterade diskvolymer). Microsoft behöver HANA SID för att skapa behörigheter för sidadm på NFS-volymer. Dessa volymer ansluta till HANA stora instanser enhet. HANA SID används också som en av komponenterna i diskvolymer som monteras namn. Om du vill köra flera HANA-instansen på enheten ska du visa flera HANA-SID. Var och en får en separat uppsättning volymer som har tilldelats.
  - I Linux-operativsystem har sidadm användaren ett grupp-ID. Detta ID krävs för att skapa de nödvändiga SAP HANA-relaterade diskvolymer. SAP HANA-installationen skapar vanligtvis sapsys-grupp med ett grupp-ID 1001. Sidadm-användare är medlem i gruppen.
  - I Linux-operativsystem har sidadm användaren ett användar-ID. Detta ID krävs för att skapa de nödvändiga SAP HANA-relaterade diskvolymer. Om du använder flera HANA-instanser på enheten kan du lista alla sidadm-användare. 
- Azure prenumerations-ID för Azure-prenumerationen till vilken SAP HANA på Azure HANA stora instanser kommer att anslutas direkt. Den här prenumerations-ID refererar till den Azure-prenumeration som kommer att debiteras med HANA stora instanser enhet eller enheter.

När du har angett informationen etablerar Microsoft SAP HANA på Azure (stora instanser). Microsoft skickar information för att länka ditt Azure-nätverk till stora HANA-instanser. Du kan också komma åt HANA stora instanser enheter.

Använd följande sekvens för att ansluta till HANA stora instanser när Microsoft har distribuerat den:

1. [Ansluta virtuella Azure-datorer till stora HANA-instanser](hana-connect-azure-vm-large-instances.md)
2. [Ansluta ett virtuellt nätverk till stora HANA-instanser ExpressRoute](hana-connect-vnet-express-route.md)
3. [Ytterligare krav (valfritt)](hana-additional-network-requirements.md)

