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
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161364"
---
# <a name="sap-hana-large-instances-deployment"></a>Distribution av SAP HANA (stora instanser) 

Efter inköpet av SAP HANA på Azure (stora instanser) har slutförts mellan dig och Microsoft enterprise-kontoteamet, krävs följande information av Microsoft för att distribuera enheter för stora HANA-instans:

- Kundens namn
- Kontaktinformation (inklusive e-postadress och telefonnummer)
- Teknisk kontaktinformation (inklusive e-postadress och telefonnummer tal)
- Teknisk nätverk kontaktinformation (inklusive e-postadress och telefonnummer tal)
- Av Azure-distributionsregion (USA, västra, USA, östra, Australien, östra, Australien, sydöstra, Europa, västra och Norra Europa från och med juli 2017)
- Bekräfta SAP HANA på Azure (stora instanser) SKU (konfiguration)
- Som redan beskrivs i dokumentet översikt och arkitektur för stora HANA-instanser för varje Azure-Region som distribueras på:
    - Ett/29 IP-adressintervall för ER P2P-anslutningar som ansluta virtuella Azure-nätverk till stora HANA-instanser
    - Ett/24 CIDR-Block som används för HANA stora instanser Server IP-poolen
- IP-adressintervallet värdena som används i attributet VNet-adressutrymmet för varje Azure virtuellt nätverk som ansluter till HANA stora instanser
- Data för var och en av HANA stora instanser system:
  - Önskade värdnamnet - helst med fullständigt kvalificerade domännamnet.
  - Önskad IP-adress för den stora HANA-instansen enheten från Serverpoolen för IP-adressintervall - Tänk på att de 30 första IP-adresserna i Serverpoolen för IP-adressintervall är reserverade för intern användning i stora HANA-instanser
  - SAP HANA SID-namn för SAP HANA-instansen (krävs för att skapa de nödvändiga SAP HANA-relaterade diskvolymer). HANA SID krävs för att skapa behörigheter för sidadm på NFS-volymer som är komma ansluten till en enhet för stora HANA-instansen. Den används också för som en av komponenterna i diskvolymer som monteras namn. Om du vill köra flera HANA-instansen på enheten måste visa en lista över flera HANA-SID. Var och en får en separat uppsättning volymer som har tilldelats.
  - Groupid sidadm användaren har i Linux-operativsystem krävs för att skapa de nödvändiga SAP HANA-relaterade diskvolymer. SAP HANA-installationen skapar vanligtvis gruppen sapsys med ett grupp-id 1001. Sidadm användare är medlem i gruppen
  - Användar-ID som användaren sidadm har i Linux-operativsystem krävs för att skapa de nödvändiga SAP HANA-relaterade diskvolymer. Om du kör flera HANA-instanser på enheten, måste du lista alla de <sid>adm-användare 
- Azure-prenumerations-ID för Azure-prenumerationen till vilken SAP HANA på Azure HANA stora instanser kommer att anslutas direkt. Den här prenumerations-ID refererar till den Azure-prenumeration som kommer att debiteras med stora HANA-instansen enhet(er).

När du har angett informationen Microsoft etablerar SAP HANA på Azure (stora instanser) och returnerar informationen som krävs för att länka ditt virtuella Azure-nätverk till stora HANA-instanser och få åtkomst till stora HANA-instansen-enheter.

Innan du läser den här artikeln, bekanta dig med [vanliga termer som HANA stora instanser](hana-know-terms.md) och [HANA stora instanser SKU: er](hana-available-skus.md).

Du kan använda följande sekvens för att ansluta till HANA stora instanser när den har distribuerats av Microsofts:

1. [Ansluta virtuella Azure-datorer till stora HANA-instanser](hana-connect-azure-vm-large-instances.md)
2. [Ansluta ett virtuellt nätverk till HANA stora instanser ExpressRoute](hana-connect-vnet-express-route.md)
3. [Ytterligare krav (valfritt)](hana-additional-network-requirements.md)

**Nästa steg**

- Se [ansluta virtuella Azure-datorer till stora HANA-instanser](hana-connect-azure-vm-large-instances.md).
- Se [ansluter ett virtuellt nätverk till HANA stora instanser ExpressRoute](hana-connect-vnet-express-route.md).