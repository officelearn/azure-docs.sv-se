---
title: Infrastruktur och anslutning till SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Konfigurera nödvändig anslutningsinfrastruktur för att använda SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cea89087742f1987f693b8bfb627bd71038a0c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616926"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA-distribution (stora instanser) 

Den här artikeln förutsätter att du har slutfört ditt köp av SAP HANA på Azure (stora instanser) från Microsoft. Innan du läser den här artikeln, för allmän bakgrund, se [HANA stora instanser vanliga termer](hana-know-terms.md) och [HANA stora instanser SKU .](hana-available-skus.md)


Microsoft kräver följande information för att distribuera STORA HANA-enheter:

- Kundens namn.
- Affärskontaktinformation (inklusive e-postadress och telefonnummer).
- Teknisk kontaktinformation (inklusive e-postadress och telefonnummer).
- Teknisk kontaktinformation för nätverk (inklusive e-postadress och telefonnummer).
- Azure-distributionsregion (till exempel Västra USA, Östra Australien eller Norra Europa).
- SAP HANA på Azure (stora instanser) SKU (konfiguration).
- För alla Azure-distributionsregioner:
    - Ett /29 IP-adressintervall för ER-P2P-anslutningar som ansluter virtuella Azure-nätverk till HANA stora instanser.
    - Ett /24 CIDR-block som används för HANA:s IP-pool för stora instanser.
    - Valfritt när du använder [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) för att aktivera direkt routning från lokalt till HANA-enheter för stora instanser eller routning mellan HANA-enheter för stora instanser i olika Azure-regioner, måste du reservera ett annat /29 IP-adressintervall. Det här intervallet kanske inte överlappar något av de andra IP-adressintervall som du definierade tidigare.
- DE IP-adressintervallvärden som används i attributet virtuellt nätverk adressutrymme för alla virtuella Azure-nätverk som ansluter till DE STORA HANA-instanserna.
- Data för varje HANA stora instanser system:
  - Önskat värdnamn, helst med ett fullständigt kvalificerat domännamn.
  - Önskad IP-adress för den stora HANA-instansenheten utanför server-IP-pooladressintervallet. (De första 30 IP-adresserna i server-IP-pooladressintervallet är reserverade för internt bruk i HANA stora instanser.)
  - SAP HANA SID-namn för SAP HANA-instansen (krävs för att skapa nödvändiga SAP HANA-relaterade diskvolymer). Microsoft behöver HANA SID för att skapa behörigheter för sidadm på NFS-volymerna. Dessa volymer kopplas till den stora HANA-instansenheten. HANA SID används också som en av namnkomponenterna i de diskvolymer som monteras. Om du vill köra mer än en HANA-instans på enheten bör du lista flera HANA-SID. Var och en får en separat uppsättning tilldelade volymer.
  - I Linux OS har sidadm-användaren ett grupp-ID. Det här ID:t krävs för att skapa nödvändiga SAP HANA-relaterade diskvolymer. SAP HANA-installationen skapar vanligtvis sapsys-gruppen med ett grupp-ID på 1001. Sidadm-användaren ingår i den gruppen.
  - I Linux OS har sidadm-användaren ett användar-ID. Det här ID:t krävs för att skapa nödvändiga SAP HANA-relaterade diskvolymer. Om du kör flera HANA-instanser på enheten kan du lista alla sidadm-användare. 
- Azure-prenumerations-ID för Azure-prenumerationen som SAP HANA på Azure HANA stora instanser kommer att vara direkt anslutna till. Det här prenumerations-ID:t refererar till Azure-prenumerationen, som kommer att debiteras med HANA:s stora instansenhet eller enheter.

När du har anger föregående information, microsoft bestämmelser SAP HANA på Azure (stora instanser). Microsoft skickar information om du vill länka dina virtuella Azure-nätverk till STORA HANA-instanser. Du kan också komma åt HANA stora instansenheter.

Använd följande sekvens för att ansluta till stora HANA-instanser efter att Microsoft har distribuerat den:

1. [Ansluta virtuella Azure-datorer till STORA HANA-instanser](hana-connect-azure-vm-large-instances.md)
2. [Ansluta ett VNet till HANA stora instanser ExpressRoute](hana-connect-vnet-express-route.md)
3. [Ytterligare nätverkskrav (valfritt)](hana-additional-network-requirements.md)

