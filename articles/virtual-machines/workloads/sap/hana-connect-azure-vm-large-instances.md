---
title: Anslutningskonfiguration från virtuella datorer till SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Anslutningskonfiguration från virtuella datorer för att använda SAP HANA på Azure (stora instanser).
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
ms.openlocfilehash: 9116dd8a27b268b656f688083032a127177d2d51
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754568"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Ansluta virtuella Azure-datorer till stora HANA-instanser

Artikeln [vad är SAP HANA på Azure (stora instanser)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Hedersomnämnande som minimal distribution av stora HANA-instanser med SAP-programnivån i Azure som ser ut som följande:

![Azure virtuellt nätverk är anslutna till SAP HANA på Azure (stora instanser) och lokala](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Titta närmare på Azure-nätverk sida, vi är medvetna om behovet av:

- Definition av ett Azure-nätverk som du kommer att distribuera de virtuella datorerna för SAP-programnivån.
- Definition av ett standardundernät i Azure-nätverk som är verkligen det som de virtuella datorerna distribueras.
- Azure-nätverk som skapas måste ha minst ett VM-undernät och en Azure ExpressRoute virtuella gateway-undernät. Dessa undernät ska tilldelas de IP-adressintervall som anges och beskrivs i följande avsnitt.

Nu ska vi titta lite närmare på Azure-nätverk skapas för stora HANA-instanser.

## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>Skapa Azure-nätverk för HANA stora instanser

>[!Note]
>Azure-nätverket för stora HANA-instanser måste skapas med hjälp av Azure Resource Manager-distributionsmodellen. Den gamla Azure-distribution-modellen, som kallas den klassiska distributionsmodellen stöds inte av lösningen för stora HANA-instansen.

Du kan använda Azure portal, PowerShell, en Azure-mall eller Azure CLI för att skapa det virtuella nätverket. (Mer information finns i [skapa ett virtuellt nätverk med Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). I följande exempel tittar vi på ett virtuellt nätverk som har skapats med hjälp av Azure portal.

Vi ska se hur definitioner av de virtuella nätverken är relaterade till vad vi lista som olika IP-adressintervall. När vi talar om den **adressutrymme**, menar vi adressutrymmet som du kan använda Azure-nätverket. Det här adressutrymmet är också det adressintervall som det virtuella nätverket använder för BGP-spridning. Detta **adressutrymme** finns här:

![Adressutrymmet för ett Azure-nätverk som visas i Azure portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

I föregående exempel, med 10.16.0.0/16, har Azure-nätverket fått ett ganska stora och många IP-adressintervall du använder. Alla IP-adressintervallen i efterföljande undernät i det här virtuella nätverket kan därför ha sina adressintervall inom den adressutrymmen. Vi rekommenderar inte vanligtvis sådana ett stort adressintervall för ett enda virtuellt nätverk i Azure. Men nu ska vi titta i undernät som definierats i Azure-nätverket:

![Azure-nätverk undernät och deras IP-adressintervall](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Vi tittar på ett virtuellt nätverk med ett första VM-undernät (kallas ”standard” här) och ett undernät med namnet ”GatewaySubnet”.

I de två föregående grafik på **virtuella nätverkets adressutrymme** omfattar både den **undernätets IP-adressintervall för Azure VM** och att den virtuella nätverksgatewayen.

Du kan begränsa den **virtuella nätverkets adressutrymme** till de specifika adressintervall som används av varje undernät. Du kan också definiera den **virtuella nätverkets adressutrymme** för ett virtuellt nätverk som flera specifika intervall, som visas här:

![Adressutrymme för virtuellt Azure-nätverk med två blanksteg](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

I det här fallet den **virtuella nätverkets adressutrymme** har två blanksteg som definierats. De är samma som IP-adressintervall som har definierats för undernätets IP-adressintervall för den virtuella Azure-datorn och den virtuella nätverksgatewayen. 

Du kan använda valfri namngivningsstandarden som du vill för de här undernäten för klient (undernät för virtuella datorer). Dock **måste alltid vara en och endast en gateway-undernät för varje virtuellt nätverk** som ansluter till SAP HANA på Azure (stora instanser) ExpressRoute-krets. Och **gateway-undernätet måste ha namnet ”GatewaySubnet”** att se till att ExpressRoute-gatewayen är korrekt placerad.

> [!WARNING] 
> Det är viktigt att gateway-undernätet alltid ha namnet ”GatewaySubnet”.

Du kan använda flera undernät för virtuella datorer och icke-sammanhängande adressintervall. Dessa adressintervall måste omfattas av den **virtuella nätverkets adressutrymme** för det virtuella nätverket. De kan vara aggregerad form. De kan även vara i en lista över de exakta intervall för VM-undernät och gateway-undernätet.

Nedan följer en sammanfattning av viktiga fakta om Azure-nätverk som ansluter till HANA stora instanser:

- Du måste skicka in den **virtuella nätverkets adressutrymme** till Microsoft när du gör en inledande distribution av stora HANA-instanser. 
- Den **virtuella nätverkets adressutrymme** kan vara ett större intervall som täcker intervallen för både det IP-adressintervallet för undernätet för den virtuella Azure-datorn och den virtuella nätverksgatewayen.
- Eller du kan skicka flera adressintervall som täcker de olika IP-adressintervall för VM-undernät IP-adressintervall och virtuella nätverkets gateway IP-adressintervall.
- Det definierade **virtuella nätverkets adressutrymme** används för routning BGP-spridning.
- Namnet på gateway-undernätet måste vara: **”GatewaySubnet”**.
- Adressutrymmet används som ett filter på stora HANA-instansen sida för att tillåta eller neka trafik till stora HANA-instansen enheter från Azure. BGP routningsinformationen av Azure-nätverk och IP-adressintervall som är konfigurerade för filtrering på stora HANA-instansen sida ska matcha. I annat fall kan det uppstå problem med nätverksanslutningen.
- Det finns vissa information om gateway-undernätet som beskrivs senare i avsnittet **ansluter ett virtuellt nätverk till HANA stora instans ExpressRoute.**



## <a name="different-ip-address-ranges-to-be-defined"></a>Olika IP-adressintervall som ska definieras 

De introducerade vi redan några av de IP-adressintervall som är nödvändiga för att distribuera stora HANA-instanser. Men det finns flera IP-adressintervall som också är viktiga. Vi ska gå igenom några mer information. Följande IP-adresser måste inte skickas till Microsoft. Men behöver du definiera dem innan du skickar en begäran om den första distributionen:

- **Virtuella nätverkets adressutrymme**: Den **virtuella nätverkets adressutrymme** är de IP-adressintervall som du tilldelar till din adress utrymme parameter i Azure-nätverk. Dessa nätverk ansluta till stor SAP HANA-instans-miljö.

 Vi rekommenderar att den här adressen utrymme parametern är ett värde för flera rader. Det bör bestå av undernätsintervallet för den virtuella Azure-datorer och undernät range(s) av Azure-gatewayen. Den här undernätsintervall som visas i föregående bilderna. Det får inte överlappa med din lokala eller server IP-pool eller ER P2P-adressintervall. 
 
Hur får du de här IP-adressintervall? 

Leverantören företagsnätverket team eller din tjänst bör ge en eller flera IP-adress adressintervall som inte används i ditt nätverk. Exempel: Anta undernätet för den virtuella Azure-datorn är 10.0.1.0/24 och undernätet för Azure gateway-undernätet är 10.0.2.0/28.  Vi rekommenderar att ditt adressutrymme för virtuellt Azure-nätverk är följande två rader: 10.0.1.0/24 och 10.0.2.0/28. 

Även om adressen utrymme värden kan aggregeras, rekommenderar vi matchar dem med adressintervall för undernätet. Det här sättet du av misstag undvika återanvända oanvända IP-adressintervall inom större adressutrymmen i nätverket. **Virtuella nätverkets adressutrymme är ett IP-adressintervall. Den behöver skickas till Microsoft när du söker efter en inledande distribution**.

- **Azure VM IP-adressintervall för undernätet:** Den här IP-adressintervall är det som du tilldelar till Azure-nätverk undernät-parameter. Den här parametern är i Azure-nätverk och ansluter till stor SAP HANA-instans-miljö. Den här IP-adressintervall används för att tilldela IP-adresser till virtuella datorer i Azure. IP-adresser utanför det här intervallet ska kunna ansluta till dina servrar för stora SAP HANA-instansen. Du kan använda flera Virtuella Azure-undernät om det behövs. Vi rekommenderar ett/24 CIDR-block för varje Azure VM-undernät. Den här adressintervallet måste vara en del av de värden som används i Azure virtuella nätverkets adressutrymme. 

Hur får du den här IP-adressintervall? 

Leverantören företagsnätverket team eller din tjänst bör ge en IP-adressintervall som inte används i ditt nätverk.

- **Virtuella IP-adressintervall för gatewayundernät:** Beroende på vilka funktioner som du planerar att använda är den rekommenderade storleken:
   - ExpressRoute-gateway med ultrahöga prestanda: / 26 Adressblock – krävs för Type II-klassen för SKU: er.
   - Samverkan med VPN och ExpressRoute med en högpresterande ExpressRoute-gateway för virtuellt nätverk (eller mindre): / 27-Adressblock.
   - Alla andra situationer: / 28-Adressblock. Den här adressintervallet måste vara en del av de värden som används i ”adressutrymme för virtuellt nätverk”-värden. Den här adressintervallet måste vara en del av de värden som används i Azure-nätverk adress utrymme värdena som du skickar till Microsoft. Hur får du den här IP-adressintervall? Din företagsnätverket team eller service provider ska ge ett IP-adressintervall som används för närvarande inte i ditt nätverk. 

- **Adressintervall för ER P2P anslutning:** Det här intervallet är IP-adressintervall till SAP HANA stora instanser ExpressRoute (ER) P2P-anslutningen. Den här IP-adressintervall måste vara ett/29 CIDR IP-adressintervall. Det här intervallet får inte överlappa med din lokala eller andra Azure-IP-adressintervall. Den här IP-adressintervall används för att konfigurera ER-anslutning från din virtuella ExpressRoute-gateway till servrar för stora SAP HANA-instansen. Hur får du den här IP-adressintervall? Din företagsnätverket team eller service provider ska ge ett IP-adressintervall som används för närvarande inte i ditt nätverk. **Det här intervallet är ett IP-adressintervall. Den behöver skickas till Microsoft när du söker efter en inledande distribution**.
  
- **Servern IP-adresspoolintervall:** Den här IP-adressintervall används för att tilldela enskilda IP-adressen till HANA stora instansservrar. Den rekommenderade undernät är ett/24 CIDR-block. Det kan vara mindre, med så lite som 64 IP-adresser om det behövs. Från det här intervallet är de 30 första IP-adresserna reserverade för användning av Microsoft. Se till att du ta hänsyn till detta när du väljer storleken på intervallet. Det här intervallet får inte överlappa med din lokala eller andra Azure-IP-adresser. Hur får du den här IP-adressintervall? Din företagsnätverket team eller service provider ska ge ett IP-adressintervall som används för närvarande inte i ditt nätverk. 

 **Det här intervallet är ett IP-adressintervall som måste skickas till Microsoft vid begäran om en inledande distribution**.
 
Du måste definiera och planera IP-adressintervall som tidigare beskrivits. Du behöver dock inte överföra dem till Microsoft. IP-adressintervall som du måste namnge till Microsoft är:

- Azure-nätverk adress space(s)
- Adressintervall för ER P2P-anslutning
- Servern IP-adressintervall i poolen

Om du lägger till ytterligare virtuella nätverk som behöver ansluta till stora HANA-instanser måste du skicka adressutrymmet för nya Azure-nätverk som du lägger till Microsoft. 

Följande är ett exempel på de olika intervall och några exempel-intervall som du behöver att konfigurera och slutligen tillhandahåller Microsoft. Värdet för Azure virtuella nätverkets adressutrymme är inte samman i det första exemplet. Den definieras dock från intervallen för det första virtuella Azure-datorn undernätets IP-adressintervallet och det virtuella IP-adressintervallet för gatewayundernät. 

Du kan använda flera undernät för virtuella datorer i Azure-nätverket när du konfigurerar och skickar ytterligare IP-adressintervall i ytterligare VM-undernät som en del av Azure virtuella nätverkets adressutrymme.

![IP-adressintervall som krävs i SAP HANA på Azure (stora instanser) minimal distribution](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Du kan också aggregera data som du skickar till Microsoft. Adressutrymmet för virtuella Azure-nätverket i så fall innehåller bara ett blanksteg. Med hjälp av IP-adressintervall från det tidigare exemplet, aggregerade virtuella nätverkets adressutrymme bör se ut som på följande bild:

![Andra alternativet med IP-adressintervall som krävs i SAP HANA på Azure (stora instanser) minimal distribution](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

I det här exemplet i stället för två mindre adressintervall som definierats i adressutrymmet för det Azure-nätverket har vi ett större intervall som omfattar 4096 IP-adresser. En stor definition av adressutrymmet innebär några ganska stort intervall som inte används. Eftersom vnet-adress utrymme värdena som används för BGP-spridning kan av oanvända lokala platser eller någon annanstans i ditt nätverk orsaka routning problem. 

Så vi rekommenderar att du behåller det adressutrymme som är anpassad efter faktiska undernätsadressutrymmet som du använder. Om det behövs utan att orsaka driftstopp på det virtuella nätverket, du kan alltid lägga till nya adress utrymme värden senare.
 
> [!IMPORTANT] 
> Varje IP-adressintervall i ER P2P och serverpoolen för IP-adressutrymme för virtuellt Azure-nätverk måste **inte** överlappar varandra eller med andra intervall som används i nätverket. Var och en måste vara diskret. De två föregående grafik visas de också får inte vara ett undernät för alla andra intervall. Om överlappningar ske mellan adressintervall, kan Azure-nätverket inte ansluta till ExpressRoute-kretsen.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Nästa steg efter adressintervall har definierats
När IP-adressintervall har definierats, måste följande saker händer:

1. Skicka IP-adressintervall för Azure virtuella nätverkets adressutrymme, ER P2P anslutning och server IP-adressintervall i poolen, tillsammans med andra data som har varit visas i början av dokumentet. Nu kan börja du också att skapa virtuella nätverk och undernät för virtuella datorer. 
2. En ExpressRoute-krets har skapats av Microsoft mellan din Azure-prenumeration och stämpel för stora HANA-instansen.
3. Ett klientnätverk skapas på stämpel för stora instanser av Microsoft.
4. Microsoft konfigurerar nätverk i SAP HANA på Azure (stora instanser)-infrastruktur att godkänna IP-adresser från ditt adressutrymme för virtuellt Azure-nätverk som kommunicerar med stora HANA-instanser.
5. Beroende på den specifika SAP-HANA på Azure (stora instanser)-SKU som du har köpt tilldelar Microsoft en beräkningsenhet i ett klientnätverk. Den också allokerar monterar lagring och installerar operativsystemet (SUSE eller Red Hat Linux). IP-adresser för dessa enheter tas ut ur Serverpoolen för IP-adressintervall som du skickade till Microsoft.

I slutet av distributionsprocessen levererar Microsoft följande data till dig:
- Information som behövs för att ansluta din Azure virtuella nätverk till ExpressRoute-krets som ansluter Azure-nätverk till stora HANA-instanser:
     - Auktorisering nycklar
     - ExpressRoute PeerID
- Data för åtkomst till stora HANA-instanser när du har skapat ExpressRoute-krets och Azure-nätverket.

Du kan också hitta sekvens för att ansluta stora HANA-instanser i dokumentet [SAP HANA på Azure (stora instanser) konfiguration](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Många av följande steg visas i ett exempel på distribution i detta dokument. 

## <a name="next-steps"></a>Nästa steg

- Referera till [ansluter ett virtuellt nätverk till HANA stora instans ExpressRoute](hana-connect-vnet-express-route.md).
