---
title: 'Självstudie: nätverks check lista'
description: Krav på nätverks krav och information om nätverks anslutning och nätverks portar
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 42bd579a455c7efe3b8f6e4d4154e687726adb1d
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740152"
---
# <a name="networking-checklist-for-azure-vmware-solution-avs"></a>Nätverks check lista för Azure VMware-lösning (AVS)

Azure VMware-lösningen erbjuder en privat VMware-moln miljö, som är tillgänglig för användare och program från lokala och Azure-baserade miljöer eller resurser. Anslutningen levereras via nätverks tjänster som Azure ExpressRoute och VPN-anslutningar och kräver vissa nätverks adress intervall och brand Väggs portar för att aktivera tjänsterna. Den här artikeln ger dig den information du behöver för att kunna konfigurera nätverket så att det fungerar med AVS.

I den här självstudien får du lära dig hur du:

> [!div class="checklist"]
> * Krav för nätverks anslutning
> * DHCP i AVS

## <a name="network-connectivity"></a>Nätverksanslutningar

Det privata AVS-molnet är anslutet till ditt virtuella Azure-nätverk med en Azure ExpressRoute-anslutning. Med den här hög bandbredden kan du få åtkomst till tjänster som körs i din Azure-prenumeration från din privata moln miljö.

AVS-privata moln kräver minst ett `/22` CIDR-adressblock för undernät som visas nedan. Det här nätverket kompletterar dina lokala nätverk. För att kunna ansluta till lokala miljöer och virtuella nätverk måste detta vara ett nätverks adress block som inte överlappar varandra.

Exempel `/22` på CIDR-nätverks adress block:`10.10.0.0/22`

Under näten:

| Nätverksanvändning             | Undernät | Exempel        |
| ------------------------- | ------ | -------------- |
| Hantering av privata moln            | `/24`    | `10.10.0.0/24`   |
| vMotion-nätverk       | `/24`    | `10.10.1.0/24`   |
| Arbetsbelastningar för virtuella datorer | `/24`   | `10.10.2.0/24`   |
| ExpressRoute-peering | `/24`    | `10.10.3.8/30`   |

### <a name="network-ports-required-to-communicate-with-the-service"></a>Nätverks portar som krävs för att kommunicera med tjänsten

Källa|Mål|Protokoll |Port |Beskrivning  |Kommentar
---|-----|:-----:|:-----:|-----|-----
DNS-server för privat moln  |Lokal DNS-Server  |UDP |53|DNS-klient vidarebefordra begär Anden från PC vCenter för alla lokala DNS-frågor (kontrol lera DNS-avsnittet nedan) |
Lokal DNS-Server  |DNS-server för privat moln  |UDP |53|DNS-klient vidarebefordra begär Anden från lokala tjänster till privata moln DNS-servrar (kontrol lera DNS-avsnittet nedan)
Lokalt nätverk  |VCenter-Server för privat moln  |TCP (HTTP)  |80|vCenter Server kräver port 80 för direkta HTTP-anslutningar.Port 80 omdirigerar begär anden till HTTPS-port 443. Den här omdirigeringen hjälper om du `http://server` använder i `https://server`stället för.  <br><br>WS-Management (kräver också att port 443 är öppen) <br><br>Om du använder en anpassad Microsoft SQL-databas och inte den paketerade SQL Server 2008-databasen på vCenter Server, används port 80 av SQL Reporting tjänsterna. När du installerar vCenter Server uppmanas du i installations programmet att ändra HTTP-porten för vCenter Server. Ändra vCenter Server HTTP-port till ett anpassat värde för att säkerställa en lyckad installation.Microsoft Internet Information Services (IIS) använder också port 80. Se konflikt mellan vCenter Server och IIS för port 80.
Nätverk för hantering av privata moln |Lokalt Active Directory  |TCP  |389|Den här porten måste vara öppen på den lokala och alla fjärrinstanser av vCenter Server. Den här porten är LDAP-portnumret för katalog tjänsterna för vCenter Servers gruppen. VCenter Server systemet måste bindas till port 389, även om du inte ansluter den här vCenter Server-instansen till en grupp med länkade lägen. Om en annan tjänst körs på den här porten kan det vara bättre att ta bort den eller ändra dess port till en annan port. Du kan köra LDAP-tjänsten på vilken port som helst från 1025 till 65535.Om den här instansen fungerar som Microsoft Windows Active Directory ändrar du Port numret från 389 till en tillgänglig port från 1025 till 65535. Den här porten är frivillig för konfiguration av lokal AD som identitets källa i det privata molnet vCenter
Lokalt nätverk  |VCenter-Server för privat moln  |TCP (HTTPS)  |443|Med den här porten kan du komma åt vCenter från det lokala nätverket.Standard porten som vCenter Server systemet använder för att lyssna efter anslutningar från vSphere-klienten. Om du vill att vCenter Server systemet ska ta emot data från vSphere-klienten öppnar du port 443 i brand väggen. VCenter Server systemet använder också port 443 för att övervaka data överföring från SDK-klienter.Den här porten används också för följande tjänster: WS-Management (kräver också att port 80 är öppen). vSphere-klient åtkomst till vSphere Update Manager. Klient anslutningar från tredje part för nätverks hantering till vCenter Server. Nätverks hanterings klienter från tredje part får åtkomst till värdar. 
Webbläsare  | Hybrid moln hanterare  | TCP (https) | 9443 | Hybrid Cloud Manager Virtual Management Interface för Hybrid Cloud Manager system konfiguration.
Admin-nätverk  | Hybrid moln hanterare |SSH |22| Administratörs SSH-åtkomst till hybrid moln hanterare.
HCM |   Cloud Gateway|TCP (HTTPS) |8123| Skicka instruktioner för värdbaserade replikeringstjänsten till hybrid molnets Gateway.
HCM |   Cloud Gateway  |    HTTP TCP (HTTPS) |    9443  |  Skicka hanterings instruktioner till den lokala hybrid moln-gatewayen med hjälp av REST API.
Cloud Gateway|      L2C |    TCP (HTTP)  |   443 |   Skicka hanterings instruktioner från Cloud Gateway till L2C när L2C använder samma sökväg som hybrid molnets Gateway.
Cloud Gateway |     ESXi-värdar |     TCP |    80 902  |   Hanterings-och OVF-distribution
Molnbaserad Gateway (lokal)|     Molnbaserad Gateway (fjärran sluten)|     UDP  |   4500 | Krävs för IPSEC<br>   IKEv2 (Internet Key Exchange) för att kapsla in arbets belastningar för dubbelriktad tunnel. Nätverks adress översättning – traversal (NAT-T) stöds också.
Molnbaserad Gateway (lokal)  |   Molnbaserad Gateway (fjärran sluten)  |   UDP  |   500   | Krävs för IPSEC<br> ISAKMP (Internet Key Exchange) för dubbelriktad tunnel.
Lokalt vCenter-nätverk|      Nätverk för hantering av privata moln|      TCP  |    8000    |  vMotion av virtuella datorer från lokala vCenter till privat moln vCenter   |     

## <a name="dhcp"></a>DHCP

Program och arbets belastningar som körs i en privat moln miljö kräver namn matchning och DHCP-tjänster för sökning och tilldelning av IP-adresser. En korrekt DHCP-och DNS-infrastruktur krävs för att tillhandahålla dessa tjänster. Du kan konfigurera en virtuell dator för att tillhandahålla dessa tjänster i din privata moln miljö.  
Vi rekommenderar att du använder den DHCP-tjänst som är inbyggd för att NSX eller använda en lokal DHCP-server i det privata molnet i stället för att dirigera broadcast-DHCP-trafik via WAN tillbaka till lokalt.

I den här självstudien lärde du dig att:

> [!div class="checklist"]
> * Krav för nätverks anslutning
> * DHCP i AVS

## <a name="next-steps"></a>Nästa steg

När du har rätt nätverk på plats kan du fortsätta till nästa självstudie för att skapa ditt AVS-privata moln.

> [!div class="nextstepaction"]
> [Självstudie: skapa ett privat AVS-moln](tutorial-create-private-cloud.md)
