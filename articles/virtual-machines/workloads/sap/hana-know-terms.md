---
title: Vet villkoren i SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Vet villkoren i SAP HANA på Azure (stora instanser).
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
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 902c4c94cc7366af342816e9b4776af603711c8b
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275456"
---
# <a name="know-the-terms"></a>Vet villkoren

Flera gemensamma definitioner används mycket i arkitekturen och tekniska distributionsguiden. Observera följande villkor och deras innebörd:

- **IaaS**: infrastruktur som en tjänst.
- **PaaS**: plattform som en tjänst.
- **SaaS**: programvara som en tjänst.
- **SAP-komponenten**: ett enskilt SAP-program, till exempel ERP Central komponent (ECC), Business Warehouse (BW), lösningen Manager eller Enterprise Portal (EP). SAP-komponenter kan baseras på traditionella ABAP eller Java-tekniker eller ett icke-NetWeaver-baserade program, till exempel företag objekt.
- **SAP-miljön**: en eller flera SAP-komponenter som är logiskt grupperade för att utföra en funktion för företag, utveckling, kvalitetssäkring, utbildning, haveriberedskap och produktionsmiljö.
- **SAP-landskap**: refererar till hela SAP-tillgångarna i din IT-miljön. SAP-landskap innehåller alla produktions- och icke-produktionsmiljöer.
- **SAP-system**: kombinationen av DBMS lager och programnivån av, till exempel ett SAP ERP-system för utveckling, ett testsystem för SAP BW och en SAP CRM-system för produktion. Azure-distributioner stöder inte mellan dessa två lager mellan lokala och Azure. Ett SAP-system är distribueras lokalt eller dess distribueras i Azure. Du kan distribuera olika system i ett SAP-landskap i Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utveckling och testa system i Azure medan du distribuerar SAP CRM produktionsprogrammen system på plats. Den är avsedd att du vara värd för SAP-programnivån av SAP-system i virtuella datorer och relaterade SAP HANA-instans på en enhet i SAP HANA på Azure (stora instanser) stämpel för SAP HANA på Azure (stora instanser).
- **Stor instans stämpel**: en infrastruktur-stack med maskinvara som är SAP HANA TDI-certifierade och dedikerad att köra SAP HANA-instanser i Azure.
- **SAP HANA på Azure (stora instanser):** officiellt namn för erbjudandet i Azure att köra HANA-instanser i på SAP HANA TDI-certifierade maskinvara som är distribuerad i stor instans tidsstämplar i olika Azure-regioner. Relaterade termen *stora HANA-instansen* är kort för *SAP HANA på Azure (stora instanser)* och som ofta används i den här tekniska distributionsguiden.
- **Mellan lokala**: Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration med plats-till-plats, flera platser eller Azure ExpressRoute-anslutning mellan lokala datacenter och Azure. I vanliga Azure-dokumentation, dessa typer av distributioner beskrivs också som mellan lokala scenarier. Orsaken till att anslutningen är att utöka lokala domäner, en lokal Azure Active Directory/OpenLDAP och den lokala DNS i Azure. Lokala liggande utökas till Azure-tillgångar i Azure-prenumerationer. Med det här tillägget kan de virtuella datorerna inte ingå i den lokala domänen. 

   Domänanvändare på den lokala domänen kan komma åt servrarna och köra tjänster på dessa virtuella datorer (till exempel DBMS tjänster). Kommunikation och namnmatchning mellan virtuella datorer distribueras på plats och Azure-distribuerade virtuella datorer är möjligt. Det här scenariot är vanliga på vägen där de flesta SAP-resurser distribueras. Mer information finns i [planering och utformning för Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [skapa ett virtuellt nätverk med en plats-till-plats-anslutning med hjälp av Azure portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Klient**: en kund som distribuerats i stora HANA-instansen stämpel hämtar isolerade i en *klient.* En klient är isolerade i nätverk, lagring och beräkningslager från andra klienter. Lagrings- och enheter som är tilldelade till olika klienter kan inte se varandra eller kommunicera med varandra på stora HANA-instansen stämpel nivå. En kund kan du har distributioner i olika klienter. Det finns även sedan ingen kommunikation mellan klienter på stora HANA-instansen stämpel nivå.
- **SKU-kategori**: för stora HANA-instansen, erbjuds följande två typer av SKU: er:
    - **Typen som jag klassen**: S72, S72m, S96, S144, S144m, S192, S192m och S192xm
    - **Skriv II klass**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm och S960m


Det finns massor av ytterligare resurser om hur du distribuerar en SAP-arbetsbelastning i molnet. Om du planerar att köra en distribution av SAP HANA i Azure måste vara med och medvetna om principerna för Azure IaaS och distribution av SAP-arbetsbelastningar på Azure IaaS. Innan du fortsätter Se [använda SAP-lösningar på Azure virtual machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för mer information. 

**Nästa steg**
- Se [HLI certifiering](hana-certification.md)