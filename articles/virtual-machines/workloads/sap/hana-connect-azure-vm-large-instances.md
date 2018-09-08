---
title: Anslutningskonfiguration från virtuella datorer till SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Anslutningskonfiguration från virtuella datorer att använda SAP HANA på Azure (stora instanser).
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
ms.openlocfilehash: f0e09e6dfce5d0ede525f461193866219b7f9429
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164780"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Ansluta virtuella Azure-datorer till stora HANA-instanser

Som redan anges i [SAP HANA (stora instanser) översikt och arkitektur på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) minimal distribution av stora HANA-instanser med SAP-programnivån i Azure ser ut som:

![Azure virtuellt nätverk är anslutna till SAP HANA på Azure (stora instanser) och lokala](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Titta närmare på virtuella Azure-nätverket sida, vi är medvetna om behovet av:

- Definition av ett virtuellt Azure nätverk som ska användas för att distribuera de virtuella datorerna för SAP-programnivån till.
- Att automatiskt innebär att ett standardundernät i det virtuella Azure-nätverket är definierade som är den som används för att distribuera de virtuella datorerna till.
- Azure VNet som skapas måste ha minst ett VM-undernät och en ExpressRoute-Gateway-undernät. Dessa undernät ska tilldelas de IP-adressintervall som anges och beskrivs i följande avsnitt.

Därför ska vi titta lite närmare på det Azure VNet har skapandet för HANA stora instanser

## <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Skapa Azure VNet för stora HANA-instanser

>[!Note]
>Azure virtuellt nätverk för stora HANA-instansen måste skapas med hjälp av Azure Resource Manager-distributionsmodellen. Den gamla modellen för Azure-distribution, ofta kallade klassiska distributionsmodellen stöds inte med lösningen för stora HANA-instansen.

Det virtuella nätverket kan skapas med hjälp av Azure portal, PowerShell, Azure-mall eller Azure CLI (se [skapa ett virtuellt nätverk med Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). I följande exempel kan vi titta på ett virtuellt nätverk som skapats via Azure-portalen.

Om vi tittar i definitionerna av ett virtuellt Azure-nätverk via Azure-portalen nu ska vi titta in några av definitionerna och hur de förhåller sig till vad vi lista med olika IP-adressintervall. När vi pratar om den **adressutrymme**, menar vi adressutrymmet som du kan använda Azure VNet. Det här adressutrymmet är också det adressintervall som det virtuella nätverket använder för BGP-spridning. Detta **adressutrymme** finns här:

![Adress utrymme för virtuella Azure-nätverket visas i Azure portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Om föregående med 10.16.0.0/16, har Azure VNet fått ett ganska stora och många IP-adressintervall du använder. Innebär att alla IP-adressintervallen i efterföljande undernät i det här virtuella nätverket kan ha sina intervall inom den-adressutrymme. Vanligtvis rekommenderar inte vi sådana stora adressintervallet för enskilt virtuellt nätverk i Azure. Men vi hämta ett steg längre och titta i undernät som definierats i Azure VNet:

![Azure VNet-undernät och deras IP-adressintervall](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Som du ser tittar vi på ett virtuellt nätverk med ett första VM-undernät (kallas ”standard” här) och ett undernät med namnet ”GatewaySubnet”.
I följande avsnitt som vi refererar till IP-adressintervall för undernätet, som har namnet ”standard” i bilderna som **Azure VM undernätets IP-adressintervall**. I följande avsnitt kommer vi att referera till IP-adressintervall för Gateway-undernätet som **VNet-Gateway-undernätet IP-adressintervall**. 

I det fallet som visas av två bilderna ovan kan du se att den **VNet-adressutrymmet** omfattar både, den **Azure VM undernätets IP-adressintervall** och **VNet-Gateway-undernätet IP-adressintervall**. 

I annat fall där du måste spara eller specifik med dina IP-adressintervall som du kan begränsa den **VNet-adressutrymmet** i ett virtuellt nätverk till specifika adressintervall som används av varje undernät. I det här fallet kan du definiera den **VNet-adressutrymmet** i ett virtuellt nätverk som flera specifika intervall som visas här:

![Azure VNet-adressutrymmet med två blanksteg](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

I det här fallet den **VNet-adressutrymmet** har två blanksteg som definierats. Dessa två blanksteg är identiska med de IP-adressintervall som definierats för **Azure VM undernätets IP-adressintervall** och **VNet-Gateway-undernätet IP-adressintervall.**

Du kan använda valfri namngivningsstandarden som du vill för de här undernäten för klient (undernät för virtuella datorer). Dock **måste alltid vara en och endast en gateway-undernätet för varje virtuellt nätverk** som ansluter till SAP HANA på Azure (stora instanser) ExpressRoute-krets. Och **gateway-undernätet måste alltid ha namnet ”GatewaySubnet”** att se till att rätt placering av ExpressRoute-gatewayen.

> [!WARNING] 
> Det är viktigt att gateway-undernätet alltid har namnet ”GatewaySubnet”.

Flera undernät för virtuella datorer kan användas, även med icke-sammanhängande adressintervall. Men som tidigare nämnts är dessa adressintervall måste omfattas av den **VNet-adressutrymmet** för det virtuella nätverket i sammansatt form eller i en lista över de exakta intervall för VM-undernät och gateway-undernätet.

Sammanfatta viktiga fakta kring ett virtuellt Azure nätverk som ansluter till HANA stora instanser:

- Du måste skicka till Microsoft i **VNet-adressutrymmet** när du utför en inledande distribution av stora HANA-instanser. 
- Den **VNet-adressutrymmet** kan vara ett större intervall som omfattar intervall för Azure VM-undernät IP-adressintervall och VNet-Gateway-undernätet IP-adressintervall.
- Eller du kan skicka som **VNet-adressutrymmet** flera adressintervall som täcker de olika IP-adressintervall för VM-undernät IP-adressintervall och VNet-Gateway-undernätet IP-adressintervall.
- Det definierade **VNet-adressutrymmet** är använda BGP-routning spridning.
- Namnet på Gateway-undernätet måste vara: **”GatewaySubnet”.**
- Den **VNet-adressutrymmet** används som ett filter på stora HANA-instansen sida för att tillåta eller neka trafik till stora HANA-instansen enheter från Azure. Om inte överensstämmer med BGP routningsinformationen av den virtuella Azure-nätverket och IP-adressintervall som konfigurerats för filtrering på stora HANA-instansen sida, kan det uppstå problem med anslutningen.
- Det finns information om Gateway-undernätet som beskrivs längre ned i avsnittet ”ansluta ett virtuellt nätverk till HANA stora instans ExpressRoute”



## <a name="different-ip-address-ranges-to-be-defined"></a>Olika IP-adressintervall som ska definieras 

De introducerade vi redan några av de IP-adressintervall behövs för att distribuera stora HANA-instanser i tidigare avsnitt. Men det finns några fler IP-adressintervall som är viktiga. Vi ska gå igenom några ytterligare information. Följande IP-adresser som inte alla måste skickas till Microsoft måste definieras innan du skickar en begäran om den första distributionen:

- **VNet-adressutrymmet:** redan introducerade tidigare, eller är IP-adress som adressintervall som tilldelats (eller planerar att tilldela) till din adress utrymme parameter i Azure virtuella nätverk (VNet) som ansluter till stor SAP HANA-instans-miljö. Vi rekommenderar att den här parametern adressutrymme är ett värde för med flera rader som består av range(s) för Azure VM-undernät och Gateway för Azure-intervall för undernät enligt bilderna tidigare. Det här intervallet får inte överlappa med din lokala eller Server IP-Pool eller ER P2P-adressintervall. Hur du hämtar det eller de här IP-adressintervall? Leverantören företagsnätverket team eller din tjänst bör ge en eller flera IP-adressintervall, som är eller används inte i ditt nätverk. Exempel: Om ditt Azure VM-undernät (se tidigare) är 10.0.1.0/24 och Azure Gateway-undernätet (se följande) är 10.0.2.0/28, sedan din Azure VNet-adressutrymmet bör vara två rader; 10.0.1.0/24 och 10.0.2.0/28. Även om adressutrymme-värden kan aggregeras, rekommenderas matchar dem med intervall för undernät att undvika att oavsiktligt återanvändning av oanvända IP-adressintervall inom större adressutrymmen i framtiden någon annanstans i nätverket. **Det virtuella Nätverkets adressutrymme är ett IP-adressintervall som måste skickas till Microsoft vid begäran om en inledande distribution**

- **Azure VM undernätets IP-adressintervall:** följande IP-adressintervall, enligt beskrivningen tidigare redan är den som du har tilldelat (eller planerar att tilldela) till parametern Azure VNet-undernät i ditt Azure virtuella nätverk som ansluter till stor SAP HANA-instans-miljö. Den här IP-adressintervall används för att tilldela IP-adresser till virtuella datorer i Azure. IP-adresser utanför det här intervallet ska kunna ansluta till dina servrar för stora SAP HANA-instansen. Om det behövs kan flera Virtuella Azure-undernät användas. Ett/24 CIDR-block som rekommenderas av Microsoft för varje Azure VM-undernät. Den här adressintervallet måste vara en del av de värden som används i Azure VNet-adressutrymmet. Hur du får den här IP-adressintervall? Din företagsnätverket team eller service provider ska ge ett IP-adressintervall som inte används för närvarande i ditt nätverk.

- **VNet-Gateway-undernätet IP-adressintervall:** beroende på vilka funktioner som du planerar att använda den rekommenderade storleken är:
   - ExpressRoute-gateway med ultrahöga prestanda: / 26 Adressblock - krävs för Type II-klassen för SKU: er
   - Samtidig med VPN och ExpressRoute med en ExpressRoute-Gateway med höga prestanda (eller mindre): / 27-Adressblock
   - Alla andra situationer: / 28-Adressblock. Den här adressintervallet måste vara en del av de värden som används i ”adressutrymme för virtuellt nätverk”-värden. Den här adressintervallet måste vara en del av de värden som används i Azure VNet-adressutrymmet värden som du måste skicka till Microsoft. Hur du får den här IP-adressintervall? Din företagsnätverket team eller service provider ska ge ett IP-adressintervall som inte används för närvarande i ditt nätverk. 

- **Adressintervall för ER P2P anslutning:** det här intervallet är IP-adressintervall till SAP HANA stora instanser ExpressRoute (ER) P2P-anslutningen. Den här IP-adressintervall måste vara ett/29 CIDR IP-adressintervall. Det här intervallet får inte överlappa med din lokala eller andra Azure-IP-adressintervall. Den här IP-adressintervall används för att ställa in ER-anslutning från din Azure VNet ExpressRoute-Gateway till servrar för stora SAP HANA-instansen. Hur du får den här IP-adressintervall? Din företagsnätverket team eller service provider ska ge ett IP-adressintervall som inte används för närvarande i ditt nätverk. **Det här intervallet är ett IP-adressintervall som måste skickas till Microsoft vid begäran om en inledande distribution**
  
- **Servern IP-adresspoolintervall:** den här IP-adressintervall som används för att tilldela enskilda IP-adressen till HANA stora instansservrar. Den rekommenderade undernät är ett/24 CIDR blockera - men om behövs det kan vara mindre till minst tillhandahåller 64 IP-adresser. Från det här intervallet är de 30 första IP-adresserna reserverade för användning av Microsoft. Se till att detta tas med i beräkningen när du väljer storleken på intervallet. Det här intervallet får inte överlappa med din lokala eller andra Azure-IP-adresser. Hur du får den här IP-adressintervall? Din företagsnätverket team eller service provider ska ge ett IP-adressintervall som inte används för närvarande i ditt nätverk. Ett/24 (rekommenderas) unika CIDR blockera ska användas för att tilldela de specifika IP-adresser som behövs för SAP HANA på Azure (stora instanser). **Det här intervallet är ett IP-adressintervall som måste skickas till Microsoft vid begäran om en inledande distribution**
 
Även om du vill definiera och planera IP-adressintervall ovan kan behöver inte alla dem överföras till Microsoft. Sammanfattningsvis ovan är de IP-adressintervall som du behöver ge ett namn till Microsoft:

- Azure VNet-adress Space(s)
- Adressintervall för ER P2P-anslutning
- Servern IP-adressintervall i poolen

Lägger till ytterligare virtuella nätverk som behöver ansluta till HANA stora instanser, måste du skicka in nya Azure VNet-adressutrymmet du lägger till Microsoft. 

Följande är ett exempel på de olika intervall och några exempel-intervall som du behöver att konfigurera och slutligen tillhandahåller Microsoft. Som du kan se värdet för Azure VNet-adressutrymmet sammanställs inte i det första exemplet, men har definierats från intervallen för det första virtuella Azure-datorn undernätets IP-adressintervallet och VNet-Gateway-undernätet IP-adressintervall. Med flera undernät för virtuella datorer i Azure VNet skulle fungera i enlighet med detta av konfigurerar och skickar ytterligare IP-adressintervall av ytterligare VM-undernät som en del av Azure VNet-adressutrymmet.

![IP-adressintervall som krävs i SAP HANA på Azure (stora instanser) minimal distribution](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Du har också möjlighet att sammanställa data du skickar in till Microsoft. I så fall skulle adressutrymmet för det virtuella Azure-nätverket bara innehålla ett blanksteg. Med de IP-adressintervall som tidigare användes i exemplet. Den här aggregerade VNet-adressutrymmet kan se ut:

![Andra alternativet med IP-adressintervall som krävs i SAP HANA på Azure (stora instanser) minimal distribution](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Som du ser ovan, i stället för två mindre adressintervall som definierats i adressutrymmet för det virtuella nätverket i Azure, har vi ett större intervall som omfattar 4096 IP-adresser. En stor definition av adressutrymmet innebär några ganska stort intervall som inte används. Eftersom VNet-adressutrymmet värdena som används för BGP-spridning kan av oanvända lokala platser eller någon annanstans i ditt nätverk orsaka routning problem. Så vi rekommenderar för att hålla det adressutrymme som är anpassad efter faktiska undernätsadressutrymmet används. Om det behövs utan att orsaka driftstopp på det virtuella nätverket, du kan alltid lägga till nya adressutrymme värden senare.
 
> [!IMPORTANT] 
> Varje IP-adress intervallet av ER-P2P, IP-Serverpoolen, Azure VNet-adressutrymmet måste **inte** överlappar varandra eller andra intervall används någon annanstans i ditt nätverk, var och en måste vara diskret och som två grafik tidigare show kanske inte är en undernät för alla andra intervall. Om överlappningar uppstår mellan adressintervall kan inte ansluta till Azure VNet till ExpressRoute-kretsen.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>Nästa steg efter adressintervall har definierats
När IP-adressintervall har definierats, måste inträffa följande aktiviteter:

1. Skicka in IP-adressintervall för Azure VNet-adressutrymmet, ER P2P anslutning och Server adressintervall för IP-Pool, tillsammans med andra data som har varit visas i början av dokumentet. Vid denna tidpunkt, börja du också att skapa det virtuella nätverket och undernät för virtuella datorer. 
2. En Express Route-krets har skapats av Microsoft mellan din Azure-prenumeration och stämpel för stora HANA-instansen.
3. Ett klientnätverk skapas på stämpel för stora instanser av Microsoft.
4. Microsoft konfigurerar nätverk i SAP HANA på Azure (stora instanser)-infrastruktur att godkänna IP-adresser från ditt Azure VNet-adressutrymmet som kommunicerar med stora HANA-instanser.
5. Beroende på specifika SAP HANA på Azure (stora instanser)-SKU som har köpt, Microsoft tilldelar en beräkningsenhet i ett klientnätverk, allokera och montera lagring och installera operativsystemet (SUSE eller Red Hat Linux). IP-adresser för dessa enheter tas ut ur Serverpoolen för IP-adress intervall som du skickade till Microsoft.

I slutet av distributionsprocessen levererar Microsoft följande data till dig:
- Information som behövs för att ansluta dina virtuella Azure-nätverket till ExpressRoute-krets som ansluter virtuella Azure-nätverk till stora HANA-instanser:
     - Auktorisering nycklar
     - ExpressRoute PeerID
- Data för åtkomst till stora HANA-instanser när du har upprättat ExpressRoute-kretsen och virtuella Azure-nätverket.

Du kan också hitta sekvens för att ansluta stora HANA-instanser i dokumentet [från slutpunkt till slutpunkt-konfigurationen för SAP HANA stora instanser](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Många av följande steg visas i ett exempel på distribution i detta dokument. 

**Nästa steg**

- Se [ansluter ett virtuellt nätverk till HANA stora instanser ExpressRoute](hana-connect-vnet-express-route.md).