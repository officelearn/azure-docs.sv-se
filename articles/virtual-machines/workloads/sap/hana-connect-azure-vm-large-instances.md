---
title: Anslutningsinställningar från virtuella datorer till SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Anslutningsinställningar från virtuella datorer för att använda SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb6f88fbfcbd539603e435b11661c428d54f3c34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74224728"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Ansluta virtuella Azure-datorer till HANA – stora instanser

Artikeln [Vad är SAP HANA på Azure (stora instanser)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) nämner att den minimala distributionen av STORA HANA-instanser med SAP-programlagret i Azure ser ut så här:

![Azure VNet ansluten till SAP HANA på Azure (stora instanser) och lokalt](./media/hana-overview-architecture/image1-architecture.png)

Om du tittar närmare på Azure virtuella nätverk sidan, det finns ett behov av:

- Definitionen av ett virtuellt Azure-nätverk som du ska distribuera de virtuella datorerna i SAP-programlagret.
- Definitionen av ett standardundernät i det virtuella Azure-nätverket som egentligen är det som de virtuella datorerna distribueras till.
- Det virtuella Azure-nätverk som har skapats måste ha minst ett VM-undernät och ett Azure ExpressRoute-undernät för virtuell nätverksgateway. Dessa undernät bör tilldelas IP-adressintervallen som anges och diskuteras i följande avsnitt.


## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Skapa det virtuella Azure-nätverket för STORA HANA-instanser

>[!Note]
>Azure virtuella nätverk för HANA stora instanser måste skapas med hjälp av Azure Resource Manager distributionsmodell. Den äldre Azure-distributionsmodellen, allmänt känd som den klassiska distributionsmodellen, stöds inte av HANA Large Instance-lösningen.

Du kan använda Azure-portalen, PowerShell, en Azure-mall eller Azure CLI för att skapa det virtuella nätverket. (Mer information finns i [Skapa ett virtuellt nätverk med Azure-portalen](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). I följande exempel tittar vi på ett virtuellt nätverk som skapas med hjälp av Azure-portalen.

När du refererar till **adressutrymmet** i den här dokumentationen, till adressutrymmet som azure-virtuella nätverket tillåts använda. Det här adressutrymmet är också det adressintervall som det virtuella nätverket använder för BGP-vägspridning. Detta **adressutrymme** kan ses här:

![Adressutrymme för ett virtuellt Azure-nätverk som visas i Azure-portalen](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

I föregående exempel, med 10.16.0.0/16, fick det virtuella Azure-nätverket ett ganska stort och brett IP-adressintervall att använda. Därför kan alla IP-adressintervall för efterföljande undernät i det här virtuella nätverket ha sina intervall inom det adressutrymmet. Vi brukar inte rekommendera ett så stort adressintervall för ett enda virtuellt nätverk i Azure. Men låt oss titta på de undernät som definieras i det virtuella Azure-nätverket:

![Azure virtuella nätverksundernät och deras IP-adressintervall](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Vi tittar på ett virtuellt nätverk med ett första VM-undernät (här kallas "standard") och ett undernät som heter "GatewaySubnet".

I de två föregående grafikerna täcker det **virtuella nätverksadressutrymmet** både **IP-adressintervallet för azure-datorn** och för den virtuella nätverksgatewayen.

Du kan begränsa det **virtuella nätverksadressutrymmet** till de specifika områden som används av varje undernät. Du kan också definiera det **virtuella nätverksadressutrymmet** för ett virtuellt nätverk som flera specifika områden, som visas här:

![Azure virtuellt nätverksadressutrymme med två blanksteg](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

I det här fallet har det **virtuella nätverksadressutrymmet** två blanksteg definierade. De är samma som IP-adressintervall som har definierats för ip-adressintervallet för undernätet för Azure VM och den virtuella nätverksgatewayen. 

Du kan använda valfri namngivningsstandard som du vill för dessa undernät för klient (VM).You can use any naming standard you like for these tenant subnets (VM subnets). Det måste dock **alltid finnas ett, och endast ett, gateway-undernät för varje virtuellt nätverk** som ansluter till SAP HANA på Azure (Stora instanser) ExpressRoute-krets. **Det här gateway-undernätet måste heta "GatewaySubnet"** för att se till att ExpressRoute-gatewayen är korrekt placerad.

> [!WARNING] 
> Det är viktigt att gateway-undernätet alltid heter "GatewaySubnet".

Du kan använda flera vm-undernät och icke-sammanhängande adressintervall. Dessa adressintervall måste täckas av det **virtuella nätverksadressutrymmet** i det virtuella nätverket. De kan vara i aggregerad form. De kan också finnas i en lista över de exakta intervallen för vm-undernäten och gateway-undernätet.

Nedan följer en sammanfattning av viktiga fakta om ett virtuellt Azure-nätverk som ansluter till STORA HANA-instanser:

- Du måste skicka **adressutrymmet** för det virtuella nätverket till Microsoft när du utför en första distribution av STORA HANA-instanser. 
- Det **virtuella nätverksadressutrymmet** kan vara ett större intervall som täcker intervallen för både ip-adressintervallet för undernätet för Azure VM och den virtuella nätverksgatewayen.
- Du kan också skicka in flera intervall som täcker de olika IP-adressintervallen för VM-undernäts-IP-adressintervall och IP-adressintervallet för virtuell nätverksgateway.
- Det definierade **virtuella nätverksadressutrymmet** används för BGP-routningsspridning.
- Namnet på gateway-undernätet måste vara: **"GatewaySubnet".**
- Adressutrymmet används som ett filter på HANA-sidan för stor instans för att tillåta eller inte tillåta trafik till HANA-stora instansenheter från Azure. BGP-routningsinformationen för det virtuella Azure-nätverket och IP-adressintervallen som har konfigurerats för filtrering på HANA-sidan för stor instans bör matchas. Annars kan anslutningsproblem uppstå.
- Det finns några detaljer om gateway-undernätet som diskuteras senare, i avsnittet **Ansluta ett virtuellt nätverk till HANA Large Instance ExpressRoute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Olika IP-adressintervall som ska definieras 

Några av de IP-adressintervall som är nödvändiga för att distribuera STORA HANA-instanser har redan introducerats. Men det finns fler IP-adressintervall som också är viktiga. Alla följande IP-adressintervall behöver inte skickas till Microsoft. Du måste dock definiera dem innan du skickar en begäran om första distribution:

- **Virtuellt nätverksadressutrymme:** Det **virtuella nätverksadressutrymmet** är de IP-adressintervall som du tilldelar din adressutrymmesparameter i virtuella Azure-nätverk. Dessa nätverk ansluter till SAP HANA Large Instance-miljön. Vi rekommenderar att den här adressutrymmesparametern är ett flerradsvärde. Den bör bestå av undernätsintervallet för Den virtuella Azure-datorn och undernätsintervallen för Azure-gatewayen. Det här undernätet visas i föregående grafik. Den får INTE överlappa med din lokala IP-pool eller ER-P2P-adressintervall. Hur får du dessa IP-adressintervall(er)? Företagets nätverk eller tjänsteleverantör bör tillhandahålla ett eller flera IP-adressintervall som inte används i nätverket. Till exempel är undernätet för din Virtuella Azure-dator 10.0.1.0/24 och undernätet i ditt Azure gateway-undernät är 10.0.2.0/28.  Vi rekommenderar att ditt azure-virtuella nätverksadressutrymme definieras som: 10.0.1.0/24 och 10.0.2.0/28. Även om adressutrymmesvärdena kan aggregeras rekommenderar vi att du matchar dem med undernätsområdena. På så sätt kan du av misstag undvika att återanvända oanvända IP-adressintervall inom större adressutrymmen någon annanstans i nätverket. **Det virtuella nätverksadressutrymmet är ett IP-adressintervall. Den måste skickas till Microsoft när du ber om en första distribution**.
- **IP-adressintervall för Azure VM:s undernät:** Det här IP-adressintervallet är det som du tilldelar parametern Azure virtual network subnet. Den här parametern finns i ditt virtuella Azure-nätverk och ansluter till MILJÖN FÖR SAP HANA-stor instans. Det här IP-adressintervallet används för att tilldela IP-adresser till dina virtuella Azure-datorer. IP-adresserna utanför det här intervallet tillåts ansluta till dina SAP HANA Large Instance-servrar. Om det behövs kan du använda flera Azure VM-undernät. Vi rekommenderar ett /24 CIDR-block för varje Azure VM-undernät. Det här adressintervallet måste vara en del av de värden som används i Azure-adressutrymmet för virtuella nätverk. Hur får du detta IP-adressintervall? Företagets nätverk eller tjänsteleverantör bör tillhandahålla ett IP-adressintervall som inte används i nätverket.
- **IP-adressintervall för virtuell nätverksgateway:** Beroende på vilka funktioner du planerar att använda är den rekommenderade storleken:
   - Ultra-prestanda ExpressRoute gateway: /26 adress block - krävs för typ II klass av SKU: er.
   - Samexistens med VPN och ExpressRoute med en högpresterande ExpressRoute virtuell nätverksgateway (eller mindre): /27-adressblock.
   - Alla andra situationer: /28 adressblock. Det här adressintervallet måste vara en del av de värden som används i värdena "VNet-adressutrymme". Det här adressintervallet måste vara en del av de värden som används i azure-värdet för virtuellt nätverk adressutrymme som du skickar till Microsoft. Hur får du detta IP-adressintervall? Företagets nätverk eller tjänsteleverantör bör tillhandahålla ett IP-adressintervall som för närvarande inte används i nätverket. 
- **Adressintervall för ER-P2P-anslutning:** Det här intervallet är IP-intervallet för din SAP HANA Large Instance ExpressRoute (ER) P2P-anslutning. Det här intervallet med IP-adresser måste vara ett /29 CIDR IP-adressintervall. Det här intervallet får INTE överlappa med dina lokala eller andra Azure IP-adressintervall. Det här IP-adressintervallet används för att konfigurera ER-anslutningen från din Virtuella ExpressRoute-gateway till SAP HANA Large Instance-servrarna. Hur får du detta IP-adressintervall? Företagets nätverk eller tjänsteleverantör bör tillhandahålla ett IP-adressintervall som för närvarande inte används i nätverket. **Det här intervallet är ett IP-adressintervall. Den måste skickas till Microsoft när du ber om en första distribution**.  
- **Adressintervall för server-IP-pool:** Det här IP-adressintervallet används för att tilldela den enskilda IP-adressen till HANA-servrar med stora instanser. Den rekommenderade undernätsstorleken är ett /24 CIDR-block. Om det behövs kan den vara mindre, med så få som 64 IP-adresser. Från det här intervallet är de första 30 IP-adresserna reserverade för användning av Microsoft. Se till att du tar hänsyn till detta faktum när du väljer storleken på intervallet. Det här intervallet får INTE överlappa med dina lokala eller andra Azure IP-adresser. Hur får du detta IP-adressintervall? Företagets nätverk eller tjänsteleverantör bör tillhandahålla ett IP-adressintervall som för närvarande inte används i nätverket.  **Det här intervallet är ett IP-adressintervall som måste skickas till Microsoft när du ber om en första distribution**.

Valfria IP-adressintervall som så småningom måste skickas till Microsoft:

- Om du väljer att använda [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) för att aktivera direkt routning från lokalt till HANA-enheter för stora instanser måste du reservera ett annat /29 IP-adressintervall. Det här intervallet kanske inte överlappar något av de andra IP-adressintervall som du definierade tidigare.
- Om du väljer att använda [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) för att aktivera direkt routning från en HANA-stor instansklientorganisation i en Azure-region till en annan HANA Large Instance-klient i en annan Azure-region, måste du reservera ett annat /29 IP-adressintervall. Det här intervallet kanske inte överlappar något av de andra IP-adressintervall som du definierade tidigare.

Mer information om ExpressRoute Global Reach och användning kring stora HANA-instanser finns i dokumenten:

- [SAP HANA-nätverksarkitektur (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)
- [Ansluta ett virtuellt nätverk till STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)
 
Du måste definiera och planera IP-adressintervall som beskrevs tidigare. Du behöver dock inte överföra dem alla till Microsoft. De IP-adressintervall som du måste namnge till Microsoft är:

- Azure virtuella nätverksadressutrymmen
- Adressintervall för ER-P2P-anslutning
- Adressintervall för server-IP-pool

Om du lägger till ytterligare virtuella nätverk som behöver ansluta till STORA HANA-instanser måste du skicka det nya Azure-adressutrymmet för virtuella nätverk som du lägger till i Microsoft. 

Följande är ett exempel på de olika intervallen och några exempelintervall som du behöver konfigurera och så småningom ge till Microsoft. Värdet för Azure-adressutrymmet för virtuella nätverk aggregeras inte i det första exemplet. Det definieras dock från intervallen för det första AZURE VM-undernätets IP-adressintervall och IP-adressintervallet för virtuell nätverksgateway. 

Du kan använda flera vm-undernät i det virtuella Azure-nätverket när du konfigurerar och skickar ytterligare IP-adressintervall för ytterligare VM-undernät som en del av Azures virtuella nätverksadressutrymme.

![IP-adressintervall som krävs i SAP HANA på Azure (stora instanser) minimal distribution](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Bilden visar inte de ytterligare IP-adressintervall som krävs för valfri användning av ExpressRoute Global Reach.

Du kan också aggregera de data som du skickar till Microsoft. I så fall innehåller adressutrymmet för det virtuella Azure-nätverket bara ett utrymme. Med hjälp av IP-adressen sträcker sig från det tidigare exemplet kan det aggregerade virtuella nätverksadressutrymmet se ut så här:

![Andra möjligheten till IP-adressintervall som krävs i SAP HANA på Azure (Stora instanser) minimal distribution](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

I exemplet har vi i stället för två mindre intervall som definierade adressutrymmet för det virtuella Azure-nätverket ett större intervall som täcker 4096 IP-adresser. En sådan stor definition av adressutrymmet lämnar några ganska stora intervall oanvända. Eftersom värdet för det virtuella nätverksadressutrymmet används för BGP-flödesspridning kan användning av oanvända områden lokalt eller någon annanstans i nätverket orsaka routningsproblem. Bilden visar inte de ytterligare IP-adressintervall som krävs för valfri användning av ExpressRoute Global Reach.

Vi rekommenderar att du håller adressutrymmet tätt i linje med det faktiska undernätsadressutrymmet som du använder. Om det behövs, utan att drabbas av driftstopp i det virtuella nätverket, kan du alltid lägga till nya värden för adressutrymme senare.
 
> [!IMPORTANT] 
> Varje IP-adressintervall i ER-P2P, serverns IP-pool och Azure-adressutrymmet för virtuella nätverk får **INTE** överlappa varandra eller med något annat intervall som används i nätverket. Var och en måste vara diskret. Som de två tidigare grafiken visar kan de inte heller vara ett undernät till något annat område. Om överlappningar uppstår mellan intervallen kanske det virtuella Azure-nätverket inte ansluter till ExpressRoute-kretsen.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Nästa steg efter att adressintervall har definierats
När IP-adressintervallen har definierats måste följande saker hända:

1. Skicka IP-adressintervallen för Azure-adressutrymmet för virtuella nätverk, ER-P2P-anslutningen och server-IP-pooladressintervallet, tillsammans med andra data som har listats i början av dokumentet. Nu kan du också börja skapa det virtuella nätverket och undernäten för den virtuella datorn. 
2. En ExpressRoute-krets skapas av Microsoft mellan din Azure-prenumeration och stämpeln HANA Large Instance.
3. Ett klientnätverk skapas på stämpeln Stor instans av Microsoft.
4. Microsoft konfigurerar nätverk i SAP HANA-infrastrukturen (Stora instanser) för att acceptera IP-adresser från ditt azure-virtuella nätverksadressutrymme som kommunicerar med STORA HANA-instanser.
5. Beroende på den specifika SAP HANA på Azure (Stora instanser) SKU som du har köpt, tilldelar Microsoft en beräkningsenhet i ett klientnätverk. Det allokerar också och monterar lagring, och installerar operativsystemet (SUSE eller Red Hat Linux). IP-adresser för dessa enheter tas bort från server-IP-pooladressintervallet som du skickade till Microsoft.

I slutet av distributionsprocessen levererar Microsoft följande data till dig:
- Information som behövs för att ansluta dina virtuella Azure-nätverk till ExpressRoute-kretsen som ansluter virtuella Azure-nätverk till STORA HANA-instanser:
     - Auktoriseringsnyckel(er)
     - ExpressRoute PeerID
- Data för åtkomst till STORA HANA-instanser när du har upprättat ExpressRoute-krets och virtuellt Azure-nätverk.

Du kan också hitta sekvensen för att ansluta HANA Stora instanser i dokumentet [SAP HANA på Azure (Stora instanser) Setup](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Många av följande steg visas i en exempeldistribution i det dokumentet. 

## <a name="next-steps"></a>Nästa steg

- Referera till [Ansluta ett virtuellt nätverk till HANA Large Instance ExpressRoute](hana-connect-vnet-express-route.md).
