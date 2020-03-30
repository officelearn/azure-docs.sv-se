---
title: Lär känna villkoren för SAP HANA på Azure (Stora instanser) | Microsoft-dokument
description: Lär känna villkoren för SAP HANA på Azure (Stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6785ae821f701121185f0064c6317c69d50191ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617053"
---
# <a name="know-the-terms"></a>Förstå villkoren

Flera vanliga definitioner används ofta i arkitektur- och teknikdistributionsguiden. Notera följande termer och deras betydelser:

- **IaaS**: Infrastruktur som en tjänst.
- **PaaS**: Plattform som en tjänst.
- **SaaS**: Programvara som en tjänst.
- **SAP-komponent**: Ett enskilt SAP-program, till exempel ERP Central Component (ECC), Business Warehouse (BW), Solution Manager eller Enterprise Portal (EP). SAP-komponenter kan baseras på traditionella ABAP- eller Java-tekniker eller ett icke-NetWeaver-baserat program som affärsobjekt.
- **SAP-miljö**: En eller flera SAP-komponenter grupperade logiskt för att utföra en affärsfunktion, till exempel utveckling, kvalitetssäkring, utbildning, haveriberedskap eller produktion.
- **SAP-landskap**: Refererar till hela SAP-tillgångarna i IT-landskapet. SAP-landskapet omfattar alla produktions- och icke-produktionsmiljöer.
- **SAP-system**: Kombinationen av DBMS-lager och applikationsskikt av till exempel ett SAP ERP-utvecklingssystem, ett SAP BW-testsystem och ett SAP CRM-produktionssystem. Azure-distributioner stöder inte att dela upp dessa två lager mellan lokala och Azure. Ett SAP-system distribueras antingen lokalt eller distribueras i Azure. Du kan distribuera de olika systemen i ett SAP-landskap till antingen Azure eller lokalt. Du kan till exempel distribuera SAP CRM-utvecklings- och testsystemen i Azure medan du distribuerar SAP CRM-produktionssystemet lokalt. För SAP HANA på Azure (Stora instanser) är det tänkt att du ska vara värd för SAP-programskiktet för SAP-system i virtuella datorer och den relaterade SAP HANA-instansen på en enhet i SAP HANA på Azure -stämpeln (Stora instanser).
- **Stor instansstämpel:** En maskinvaruinfrastrukturstack som är SAP HANA TDI-certifierad och dedikerad för att köra SAP HANA-instanser i Azure.
- **SAP HANA på Azure (stora instanser):** Officiellt namn för erbjudandet i Azure för att köra HANA-instanser på SAP HANA TDI-certifierad maskinvara som distribueras i stora instansstämplar i olika Azure-regioner. Den relaterade termen *HANA Large Instance* är en förkortning för *SAP HANA på Azure (Stora instanser)* och används ofta i den här tekniska distributionsguiden.
- **Korslokaler:** Beskriver ett scenario där virtuella datorer distribueras till en Azure-prenumeration som har plats-till-plats-, multi-site- eller Azure ExpressRoute-anslutning mellan lokala datacenter och Azure. I vanlig Azure-dokumentation beskrivs även dessa typer av distributioner som scenarier över flera lokala. Anledningen till anslutningen är att utöka lokala domäner, lokala Azure Active Directory/OpenLDAP och lokal DNS till Azure. Det lokala landskapet utökas till Azure-tillgångarna i Azure-prenumerationerna. Med det här tillägget kan de virtuella datorerna vara en del av den lokala domänen. 

   Domänanvändare av den lokala domänen kan komma åt servrarna och köra tjänster på dessa virtuella datorer (till exempel DBMS-tjänster). Kommunikation och namnmatchning mellan virtuella datorer som distribueras lokalt och Azure-distribuerade virtuella datorer är möjlig. Det här scenariot är typiskt för hur de flesta SAP-tillgångar distribueras. Mer information finns i [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och Skapa ett [virtuellt nätverk med en plats-till-plats-anslutning med hjälp av Azure-portalen](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Klient**: En kund som distribueras i HANA Large Instance-stämpeln isoleras till en *klient.* En klient är isolerad i nätverks-, lagrings- och beräkningslager från andra klienter. Lagrings- och beräkningsenheter som tilldelats de olika klienterna kan inte se varandra eller kommunicera med varandra på stämpelnivån HANA Large Instance. En kund kan välja att ha distributioner till olika klienter. Även då finns det ingen kommunikation mellan klienter på HANA Large Instance stämpelnivå.
- **SKU kategori**: För HANA Large Instance, följande två kategorier av SKU erbjuds:
    - **Typ I-klass**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 och S224m
    - **Typ II-klass**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm och S960m
- **Stämpel**: Definierar Microsofts interna distributionsstorlek för STORA HANA-instanser. Innan HANA Large Instance-enheter kan distribueras måste en HANA-stor instansstämpel som består av beräknings-, nätverks- och lagringsställ distribueras på en datacenterplats. En sådan distribution kallas en HANA Stor instans stämpel eller från Revision 4 (se nedan) på vi använder den alternativa termen **för stora instans rad**
- **Revidering**: Det finns två olika stämpelrevideringar för HANA Large Instance frimärken. Dessa skiljer sig åt i arkitektur och närhet till Azure virtuella dator värdar
    - "Revision 3" (Rev 3): är den ursprungliga designen som sattes in från mitten av året 2016
    - "Revision 4" (Rev 4): är en ny design som kan ge närmare närhet till Azure-värdar för virtuella datorer och med den lägre nätverksfördröjningen mellan Virtuella Azure-datorer och HANA-enheter för stora instanser 

En mängd ytterligare resurser finns tillgängliga om hur du distribuerar en SAP-arbetsbelastning i molnet. Om du planerar att köra en distribution av SAP HANA i Azure måste du ha erfarenhet av och medveten om principerna för Azure IaaS och distributionen av SAP-arbetsbelastningar på Azure IaaS. Innan du fortsätter läser du [Använda SAP-lösningar på virtuella Azure-datorer](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för mer information. 

**Nästa steg**
- Se [HLI-certifiering](hana-certification.md)