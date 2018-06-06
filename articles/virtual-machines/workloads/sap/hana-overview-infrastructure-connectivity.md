---
title: Infrastruktur och anslutning till SAP HANA i Azure (stora instanser) | Microsoft Docs
description: Konfigurera infrastruktur kräver anslutning om du vill använda SAP HANA i Azure (stora instanser).
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
ms.date: 06/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4741cf306aed1c86be1bc4b54fb961383e2f70bd
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763773"
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infrastruktur för SAP HANA (stora instanser) och anslutningar på Azure 

Vissa definitioner gång innan du läser den här guiden. I [SAP HANA (stora instanser) översikt och arkitektur för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) vi har fört två olika klasser av HANA stora instans enheter med:

- S72, S72m, S144, S144m, S192, S192m och S192xm som vi kallar ”typen I klassen' av SKU: er.
- S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm och S960m som vi kallar ”typ II class-av SKU: er.

Klass-specificerare kommer att användas i hela dokumentationen HANA stora instans att så småningom referera till olika funktioner och krav utifrån HANA stora instans SKU: er.

Övriga definitioner som vi använder ofta är:
- **Stora instans stämpel:** maskinvara infrastruktur-stacken, som är SAP HANA TDI certifierad och avsett för att köra SAP HANA-instanser i Azure.
- **SAP HANA i Azure (stora instanser):** officiellt namn för erbjudandet i Azure att köra HANA instanser i på SAP HANA TDI certifierad maskinvara som distribueras i stora instans tidsstämplar i olika Azure-regioner. Relaterade termen **HANA stora instans** kort för SAP HANA i Azure (stora instanser) och är ofta används den här tekniska distributionsguiden.
 

Efter inköpet av SAP HANA i Azure (stora instanser) är slutförd mellan dig och Microsoft enterprise-kontoteamet, krävs följande information av Microsoft för att distribuera HANA stora instans enheter:

- Kundens namn
- Kontaktinformation (inklusive e-postadress och telefonnummer)
- Tekniska kontaktinformation (inklusive e-postadress och phone tal)
- Tekniska nätverk kontaktinformation (inklusive e-postadress och phone tal)
- Distribution av Azure-region (västra USA, östra USA, östra, Australien, sydost, västra Europa och Norra Europa från och med juli 2017)
- Bekräfta SAP HANA på Azure (stora instanser) SKU (konfiguration)
- Redan detaljerad i dokumentet översikt och arkitektur för stora HANA-instanser för varje Azure-Region som distribueras på:
    - En /29 IP-adressintervall för ER P2P-anslutningar som ansluter Azure Vnet till HANA stora instanser
    - Ett/24 CIDR-Block som används för HANA stora instanser IP-poolen Server
- Intervallvärden för IP-adress används i attributet VNet-adressutrymmet för varje virtuellt Azure-nätverk som ansluter till HANA stora instanser
- Data för varje HANA stora instanser system:
  - Önskade värdnamnet - helst med fullständigt kvalificerade domännamnet.
  - Önskad IP-adress för HANA stora instans enheten out-of Server Pool för IP-adressintervallet - Kom ihåg att 30 första IP-adresser i Serverpoolen för IP-adressintervallet är reserverade för intern användning i HANA stora instanser
  - SAP HANA SID namn för SAP HANA-instansen (krävs för att skapa de nödvändiga SAP HANA-relaterade diskvolymer). HANA SID krävs för att skapa behörigheter för sidadm på NFS-volymer, som hämtar bifogas HANA stora instans-enhet. Det används också för en namn-komponenter av volymerna som hämta monterade. Om du vill köra flera instanser av HANA på enheten måste du lista över flera HANA SID. Var och en hämtar en separat uppsättning volymer som har tilldelats.
  - Groupid sidadm användaren har i Linux-operativsystem krävs för att skapa de nödvändiga SAP HANA-relaterade diskvolymer. SAP HANA-installationen skapar vanligtvis gruppen sapsys med grupp-id 1001. Sidadm användaren tillhör gruppen
  - Användar-ID som användaren sidadm har i Linux-operativsystem krävs för att skapa de nödvändiga SAP HANA-relaterade diskvolymer. Om du kör flera instanser av HANA på enheten, måste du lista alla de <sid>adm-användare 
- Azure prenumerations-ID för Azure-prenumerationen för vilken SAP HANA på Azure HANA stora instanser ska anslutas direkt. Prenumerations-ID refererar till Azure-prenumeration, som kommer att debiteras med HANA stora instans-enheter.

När du har angett informationen Microsoft etablerar SAP HANA i Azure (stora instanser) och returnerar informationen som krävs för att länka ditt Azure Vnet till HANA stora instanser och komma åt HANA stora instans-enheter.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Ansluta virtuella Azure-datorer till HANA stora instanser

Som nämnts i [SAP HANA (stora instanser) översikt och arkitektur för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) minimal distributionen av HANA stora instanser med lagret för SAP-program i Azure ser ut som:

![Azure-VNet som är anslutna till SAP HANA på Azure (stora instanser) och lokalt](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Titta närmare på virtuella Azure-sida, vi är medvetna om behovet av att:

- Definition av ett virtuellt Azure-nätverk som ska användas för att distribuera de virtuella datorerna på programnivå SAP i.
- Att automatiskt innebär att ett standardundernät i Azure Vnet definieras som är den som används för att distribuera de virtuella datorerna till.
- Azure-VNet som skapas måste ha minst ett VM-undernät och en ExpressRoute-Gateway-undernätet. Dessa undernät ska tilldelas IP-adressintervall som anges och beskrivs i följande avsnitt.

Därför ska vi titta lite närmare i Azure VNet skapas för HANA stora instanser

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Skapa Azure VNet för HANA stora instanser

>[!Note]
>Azure VNet för HANA stora instansen måste skapas med hjälp av Azure Resource Manager-distributionsmodellen. Den äldre modellen för Azure-distribution, ofta kallade klassiska distributionsmodellen stöds inte med lösningen för stora HANA-instans.

VNet kan skapas med hjälp av Azure-portalen, PowerShell, Azure-mall eller Azure CLI (se [skapa ett virtuellt nätverk med Azure-portalen](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). I följande exempel titta i ett virtuellt nätverk som skapats via Azure-portalen.

Om vi tittar i definitionerna av ett virtuellt Azure-nätverk via Azure portal nu ska vi titta i några av definitionerna och hur de relaterar till vi lista av olika IP-adressintervall. När vi pratar om den **adressutrymme**, vi menar adressutrymme som du kan använda Azure VNet. Det här adressutrymmet är också adressintervallet som VNet använder för BGP-väg spridning. Detta **adressutrymme** här:

![Adress utrymme för Azure VNet visas i Azure-portalen](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

I det fallet föregående med 10.16.0.0/16, har Azure VNet fått ett ganska stort och wide IP-adressintervall ska användas. Innebär att alla IP-adressintervallen i efterföljande undernät i detta virtuella nätverk kan ha sina områden inom den-adressutrymme. Vanligtvis rekommenderar inte vi sådana stora adressintervall för enkel VNet i Azure. Men vi hämta ytterligare ett steg och titta i undernät som definierats i Azure VNet:

![Azure VNet-undernät och deras IP-adressintervall](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Som du ser tittar vi på ett virtuellt nätverk för med en första VM-undernät (kallas här 'default') och ett undernät som kallas ”GatewaySubnet'.
I följande avsnitt kommer vi refererar till IP-adressintervallet i undernätet som anropades 'default' i grafik som **Azure VM-undernät IP-adressintervall**. I följande avsnitt som vi refererar till Gateway-undernätet som IP-adressintervall **VNet Gateway-undernätet IP-adressintervall**. 

I det fallet genom två bilderna ovan kan du se det i **VNet-adressutrymmet** omfattar både den **Azure VM-undernät IP-adressintervall** och **VNet Gateway undernät IP-adressintervall**. 

I annat fall där du måste spara eller vara specifika med IP-adressintervall kan du begränsa den **VNet-adressutrymmet** av ett VNet till specifika intervall som används av varje undernät. I det här fallet kan du definiera den **VNet-adressutrymmet** av ett VNet som flera specifika intervall som visas här:

![Azure VNet-adressutrymmet med två blanksteg](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

I det här fallet den **VNet-adressutrymmet** har två blanksteg som definierats. Dessa två blanksteg är identiska med de IP-adressintervall som definierats för **Azure VM-undernät IP-adressintervall** och **VNet Gateway-undernätet IP-adressintervall.**

Du kan använda alla namngivningsstandarden som du vill för dessa klient undernät (VM-undernät). Dock **måste alltid vara en och bara en gateway-undernät för varje VNet** som ansluter till SAP HANA i Azure (stora instanser) ExpressRoute-kretsen. Och **gateway-undernätet måste alltid ha namnet ”GatewaySubnet”** att säkerställa rätt placering av ExpressRoute-gatewayen.

> [!WARNING] 
> Det är viktigt att gateway-undernätet alltid har namnet ”GatewaySubnet”.

Flera Virtuella undernät kan även använda icke sammanhängande adressintervall användas. Men som tidigare nämnts dessa adressintervall måste omfattas av den **VNet-adressutrymmet** för VNet i sammansatt form eller i en lista över exakt intervall för VM-undernät och gateway-undernätet.

Sammanfatta viktiga fakta om ett virtuellt Azure-nätverk som ansluter till HANA stora instanser:

- Du måste skicka till Microsoft i **VNet-adressutrymmet** när du utför en inledande distribution till HANA stora instanser. 
- Den **VNet-adressutrymmet** kan vara ett större område som täcker intervallet för Azure VM-undernät IP-adress range(s) och VNet Gateway-undernätet IP-adressintervall.
- Eller kan du skicka som **VNet-adressutrymmet** flera områden som omfattar olika IP-adressintervall för VM-undernät IP-adress range(s) och VNet Gateway-undernätet IP-adressintervall.
- Det definierade **VNet-adressutrymmet** är använda BGP-routning spridningen.
- Namnet på Gateway-undernätet måste vara: **”GatewaySubnet”.**
- Den **VNet-adressutrymmet** används som ett filter på HANA stora instans sida för att tillåta eller neka trafik till HANA stora instans enheter från Azure. Om BGP routningsinformation Azure VNet och IP-adressintervall som konfigurerats för att filtrera på sida HANA stora instans inte matchar, kan det uppstå problem i anslutningen.
- Det finns vissa uppgifter om Gateway-undernätet som beskrivs längre ned i avsnittet ”ansluta ett virtuellt nätverk till HANA stora instans ExpressRoute'



### <a name="different-ip-address-ranges-to-be-defined"></a>Olika IP-adressintervall som ska definieras 

Vi introducerade redan vissa IP-adressintervall som är nödvändiga för att distribuera HANA stora instanser i tidigare avsnitt. Men det finns vissa flera IP-adressintervall, som är viktiga. Vi gå igenom några ytterligare information. Följande IP-adresser som inte alla måste skickas till Microsoft måste definieras innan du skickar en begäran om inledande distributionen:

- **VNet-adressutrymmet:** introduceras redan tidigare, eller är IP-adressen range(s) du har tilldelat (eller planerar att tilldela) till din adressutrymme parameter i Azure virtuella nätverk (VNet) som ansluter till stora instans för SAP HANA-miljö. Du rekommenderas att parametern adressutrymme är ett värde för flerradiga som består av range(s) för Azure VM-undernät och intervall för Azure Gateway-undernät som visas i grafiken tidigare. Det här intervallet får inte överlappa med din lokala eller Server IP-Pool eller ER P2P-adressintervall. Hur du hämtar det eller de här IP-adressen range(s)? Leverantören företagsnätverket team eller tjänst bör ge en eller flera IP-adress Range(s), som är eller som inte används i nätverket. Exempel: Om ditt Azure VM-undernät (se ovan) är 10.0.1.0/24 och Azure-Gatewayundernät (se följande) är 10.0.2.0/28, sedan ditt Azure VNet-adressutrymmet bör vara två rader. 10.0.1.0/24 och 10.0.2.0/28. Adressutrymmet värdena kan aggregeras, men vi rekommenderar matchar dem till undernät adressintervall att undvika oavsiktliga återanvändning av oanvända IP-adressintervall inom större adressutrymmen i framtiden någon annanstans i nätverket. **VNET-adressutrymmet är ett IP-adressintervall som måste skickas till Microsoft vid begäran om en inledande distribution**

- **Azure VM-undernät IP-adressintervall:** den här IP-adressintervall som diskuterats tidigare redan är den som du har tilldelat (eller planerar att tilldela) att parametern virtuella Azure-undernät i ditt Azure VNET anslutning till SAP HANA stora instans-miljö. Den här IP-adressintervall används för att tilldela IP-adresser till din virtuella Azure-datorer. IP-adresser utanför det här intervallet tillåts att ansluta till SAP HANA stora instans-servrar. Om det behövs kan flera Virtuella Azure-undernät användas. Ett/24 CIDR-block rekommenderas av Microsoft för varje Azure VM-undernät. Detta adressintervall måste vara en del av de värden som används i Azure VNet-adressutrymmet. Hur du hämtar den här IP-adressintervall? Leverantören företagsnätverket team eller tjänst bör ge ett IP-adressintervall som inte redan används i nätverket.

- **VNet Gateway-undernätet IP-adressintervall:** beroende på de funktioner som du planerar att använda den rekommenderade storleken är:
   - Ultrahöga prestanda ExpressRoute-gateway: / 26 Adressblock - krävs för klass som typ II SKU: er
   - Existerar med VPN- och ExpressRoute med ett högpresterande ExpressRoute-Gateway (eller mindre): / 27-Adressblock
   - Alla andra situationer: / 28 Adressblock. Detta adressintervall måste vara en del av de värden som används i ”VNet-adressutrymmet”-värden. Detta adressintervall måste vara en del av de värden som används i Azure VNet-adressutrymmet värden som behövs för att skicka till Microsoft. Hur du hämtar den här IP-adressintervall? Leverantören företagsnätverket team eller tjänst bör ge ett IP-adressintervall som inte redan används i nätverket. 

- **Adressintervall för ER P2P anslutning:** detta intervall är IP-adressintervall för SAP HANA stora instans ExpressRoute (ER) P2P-anslutning. Det här intervallet av IP-adresser måste vara en /29 CIDR IP-adressintervall. Det här intervallet får inte överlappa med din lokala eller andra Azure-IP-adressintervall. Den här IP-adressintervall används för att ställa in ER anslutningen från Azure VNet ExpressRoute-Gateway för stora instans för SAP HANA-servrarna. Hur du hämtar den här IP-adressintervall? Leverantören företagsnätverket team eller tjänst bör ge ett IP-adressintervall som inte redan används i nätverket. **Det här intervallet är ett IP-adressintervall som måste skickas till Microsoft vid begäran om en inledande distribution**
  
- **Servern IP-adresspoolintervall:** den här IP-adressintervall som används för att tilldela enskilda IP-adressen till HANA stora instans servrar. Rekommenderade undernätets storlek är ett/24 CIDR blockera - men om behövs det kan vara mindre till minst tillhandahåller 64 IP-adresser. I detta intervall är de 30 första IP-adresserna reserverade för användning av Microsoft. Se till att detta tas med i beräkningen när du väljer storleken på intervallet. Det här intervallet får inte överlappa med din lokala eller andra Azure-IP-adresser. Hur du hämtar den här IP-adressintervall? Leverantören företagsnätverket team eller tjänst bör ge ett IP-adressintervall som inte redan används i nätverket. Ett/24 (rekommenderas) unika CIDR blockera ska användas för att tilldela specifika IP-adresser som behövs för SAP HANA i Azure (stora instanser). **Det här intervallet är ett IP-adressintervall som måste skickas till Microsoft vid begäran om en inledande distribution**
 
Om du behöver definiera och planera IP-adressintervall ovan behöver inte alla dem överföras till Microsoft. Om du vill sammanfatta ovan är de IP-adressintervall som krävs för att namnge till Microsoft

- Azure VNet adress Space(s)
- Adressintervall för ER P2P-anslutning
- Servern IP-adressintervall i poolen

Lägger till ytterligare Vnet som ska ansluta till HANA stora instanser, måste du skicka nya Azure VNet-adressutrymmet du lägger till till Microsoft. 

Följande är ett exempel på olika områden och några exempel intervall som du vill konfigurera och ange slutligen till Microsoft. Som du kan se värdet för Azure VNet-adressutrymmet sammanställs inte i det första exemplet, men har definierats från intervall för det första Virtuella Azure-undernät IP-adressintervallet och VNet Gateway-undernätet IP-adressintervall. Med hjälp av flera Virtuella undernät i Azure VNet fungerar därför av hur du konfigurerar och skickar ytterligare IP-adressintervall för ytterligare VM-adressundernät som en del av Azure VNet-adressutrymmet.

![IP-adressintervall som krävs i SAP HANA på minimal distribution av Azure (stora instanser)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Du har också möjlighet att sammanställa data som du skickar till Microsoft. I så fall skulle adressutrymmet för det Azure-VNet bara innehålla ett blanksteg. Med hjälp av IP-adressintervall som används i exemplet ovan. Den här aggregerade adressutrymmet för VNet kan se ut så:

![Andra risken för IP-adressintervall som krävs i SAP HANA på minimal distribution av Azure (stora instanser)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Som du ser ovan, i stället för två mindre områden som definierats i Azure-VNet adressutrymme har vi ett större område som täcker 4096 IP-adresser. En stor definition av adressutrymmet lämnar det oanvända vissa snarare stora intervall. Eftersom adressutrymmet för VNet-värden används för BGP-väg spridningen, kan användning av den oanvända adressintervall lokalt eller någon annanstans i nätverket orsaka routning problem. Därför har vi rekommenderar för att hålla adressutrymmet nära justerad med faktiska undernätsadressutrymmet som används. Om det behövs, utan att det medför driftstopp för virtuella nätverk, du kan alltid lägga till nya adressutrymme värden senare.
 
> [!IMPORTANT] 
> Varje IP-adress intervallet på ER-P2P, IP-Serverpoolen, Azure VNet-adressutrymmet måste **inte** överlappar varandra eller andra intervall används någon annanstans i nätverket; varje måste vara diskret och som två grafik tidigare visa får inte vara en undernät för alla andra intervall. Om överlappningar uppstår mellan dataområden kan Azure VNet inte ansluta till ExpressRoute-kretsen.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Nästa steg efter adressintervall har definierats
När IP-adressintervall har definierats måste inträffa följande aktiviteter:

1. Skicka IP-adressintervall för Azure VNet-adressutrymmet, ER P2P anslutning och servern IP-adresspoolintervall, tillsammans med andra data som har tagits med i början av dokumentet. Vid denna tidpunkt, kan du också börja skapa VNet och undernät för Virtuella datorer. 
2. Express Route-krets skapas mellan din Azure-prenumeration och stämpeln HANA stora instans av Microsoft.
3. En klientnätverket skapas på stämpel stora instans av Microsoft.
4. Microsoft konfigurerar nätverk i SAP HANA i Azure (stora instanser) infrastruktur att acceptera IP-adresser i ditt Azure VNet-adressutrymmet som kommunicerar med HANA stora instanser.
5. Beroende på den specifika SAP HANA på Azure (stora instanser) SKU köpt, Microsoft tilldelar en beräknings-enhet i en klientnätverket, allokera montera lagring och installera operativsystem (SUSE eller Red Hat Linux). IP-adresser för dessa enheter tas ut ur Server Pool för IP-adressen intervall som du skickade till Microsoft.

I slutet av distributionsprocessen ger Microsoft följande data till dig:
- Information som behövs för att ansluta din Azure-VNet(s) till ExpressRoute-kretsen som ansluter Azure Vnet till HANA stora instanser:
     - Auktorisering nycklar
     - ExpressRoute PeerID
- Data till HANA stora instanser när du har upprättat ExpressRoute-krets och Azure VNet.

Du kan också hitta sekvensen av ansluta HANA stora instanser i dokumentet [slutpunkt till slutpunkt installationsprogrammet för SAP HANA stora instanser](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Många av de följande stegen som visas i ett exempel på distribution i detta dokument. 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Ansluta ett virtuellt nätverk till HANA stora instans ExpressRoute

Du kan starta ansluter det VNet som du skapade innan till HANA stora instanser som du definierat alla IP-adressintervall och nu fått tillbaka data från Microsoft. När Azure VNet skapas måste en ExpressRoute-gateway skapas på VNet länka VNet till ExpressRoute-kretsen som ansluter till kund-klient på stora instans stämpel.

> [!NOTE] 
> Det här steget kan ta upp till 30 minuter att slutföra, som den nya gatewayen skapas i den avsedda Azure-prenumerationen och sedan ansluts till det angivna Azure VNet.

Om det finns redan en gateway, måste du kontrollera om det är en ExpressRoute-gateway eller inte. Om inte gatewayen måste tas bort och återskapas som en ExpressRoute-gateway. Om en ExpressRoute-gateway är redan skapat eftersom Azure VNet är redan ansluten till ExpressRoute-kretsen för lokal anslutning, fortsätter du till avsnittet länka Vnet.

- Använd antingen den (nya) [Azure-portalen](https://portal.azure.com/), eller PowerShell för att skapa en ExpressRoute-VPN-gateway är ansluten till ditt VNet.
  - Om du använder Azure portal, lägger du till en ny **virtuell nätverksgateway** och välj sedan **ExpressRoute** som gateway-typen.
  - Om du väljer PowerShell i stället först hämtar och använder senast [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) att säkerställa en optimal upplevelse. Följande kommandon skapar en ExpressRoute-gateway. Texterna föregås av en _$_ är användardefinierade variabler som behöver uppdateras med din specifika information.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

I det här exemplet används HighPerformance gateway SKU. Alternativen är HighPerformance eller UltraPerformance som den enda gatewayen-SKU: er som stöds för SAP HANA i Azure (stora instanser).

> [!IMPORTANT]
> För HANA stora instanser av typen II classs SKU är användning av Gateway-SKU UltraPerformance obligatoriskt.

### <a name="linking-vnets"></a>Länka Vnet

Nu när Azure VNet har en ExpressRoute-gateway kan använda du den auktoriseringsinformation som tillhandahålls av Microsoft för att ansluta ExpressRoute-gatewayen till SAP HANA på Azure (stora instanser) ExpressRoute-krets som skapats för den här anslutningen. Det här steget kan utföras med hjälp av Azure-portalen eller PowerShell. Portalen rekommenderas, men PowerShell instruktioner är som följer. 

- Du kan köra följande kommandon för varje gateway för virtuellt nätverk med hjälp av en annan AuthGUID för varje anslutning. De första två posterna visas i följande skript kommer från information som tillhandahålls av Microsoft. Dessutom är AuthGUID specifika för varje virtuellt nätverk och dess gateway. Sätt om du vill lägga till en annan Azure-VNet, måste du hämta en annan AuthID för ExpressRoute-krets som ansluter HANA stora instanser till Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Om du vill ansluta en gateway för flera ExpressRoute-kretsar som är associerade med din prenumeration kan du behöva köra det här steget mer än en gång. Till exempel kommer du förmodligen att ansluta samma Gateway för virtuellt nätverk till ExpressRoute-kretsen som ansluter VNet till ditt lokala nätverk.

## <a name="adding-more-ip-addresses-or-subnets"></a>Lägga till flera IP-adresser och undernät

Använd Azure-portalen, PowerShell eller CLI när du lägger till flera IP-adresser eller undernät.

I detta fall vi rekommenderar lägga till det nya IP-adressintervallet som nya adressintervall i VNet-adressutrymmet i stället för att generera ett nytt aggregerade intervall. I båda fallen måste du skicka den här ändringen till Microsoft för att tillåta anslutning utanför det nya IP-adressintervallet till HANA stora instans-enheter i din klient. Du kan öppna en Azure-supportförfrågan om att hämta det nya VNet-adressutrymmet lagts till. När du har tagit emot bekräftelse kan utföra nästa steg.

Om du vill skapa ett ytterligare undernät från Azure portal finns i artikeln [skapa ett virtuellt nätverk med Azure-portalen](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network), och för att skapa från PowerShell, se [skapa ett virtuellt nätverk med PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Lägga till Vnet

När du först ansluta en eller flera virtuella Azure-nätverk, kanske du vill lägga till ytterligare mallar som har åtkomst till SAP HANA i Azure (stora instanser). Först begära en Azure-supporten, i din begäran omfatta både specifik information som identifierar den specifika Azure-distributionen och IP-adressutrymme range(s) av Azure VNet-adressutrymmet. SAP HANA på Azure Service Management ger sen nödvändig information du behöver ansluta ytterligare Vnet och ExpressRoute. För varje VNet behöver du en unik nyckel tillstånd att ansluta till ExpressRoute-kretsen till HANA stora instanser.

Steg för att lägga till ett nytt Azure VNet:

1. Fullständig det första steget i onboarding-processen finns på _skapar Azure VNet_ avsnitt.
2. Fullständig finns det andra steget i onboarding-processen på _skapar gateway-undernätet_ avsnitt.
3. För att ansluta din ytterligare Vnet till HANA stora instans ExpressRoute-krets, öppna ett Azure supportförfrågan med information om det nya VNet och begär en ny nyckel för auktorisering.
4. När ett meddelande om att tillståndet har slutförts, används av Microsoft autentiseringsinformationen för att slutföra tredje steget i onboarding-processen finns på _länka Vnet_ avsnitt.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Ökad bandbredd för ExpressRoute-krets

Kontakta SAP HANA på Azure Service Management. Skapa en Azure-supporten om du bör öka bandbredden för SAP HANA i Azure (stora instanser) ExpressRoute-kretsen. (Du kan begära en ökad för en enskild kretsbandbredden högst 10 Gbit/s). Sedan visas meddelandet när åtgärden har slutförts; Ingen ytterligare åtgärd krävs för att aktivera den här snabbare i Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Lägga till en ytterligare ExpressRoute-krets

Kontakta SAP HANA på Azure Service Management om du rekommenderas att en ytterligare ExpressRoute-krets krävs, gör en Azure som stöd för begäran att skapa en ny ExpressRoute-krets (och hämta information om tillstånd att ansluta till den). Adressutrymmet som ska användas på Vnet måste definieras innan begäran, i ordning för SAP HANA på Azure-tjänsthantering för auktorisering.

När den nya kretsen har skapats och SAP HANA på Azure Service Management-konfigurationen är klar kan ska du få ett meddelande med information om du vill fortsätta. Följ stegen ovan för att skapa och ansluta det nya VNet till den här ytterligare kretsen. Det går inte att ansluta virtuella Azure-nätverk till denna ytterligare krets om de redan är ansluten till en annan SAP HANA i Azure (stora instans) ExpressRoute-kretsen i samma Azure-Region.

## <a name="deleting-a-subnet"></a>Om du tar bort ett undernät

Azure-portalen, PowerShell eller CLI kan användas för att ta bort ett undernät för virtuellt nätverk. Om ditt Azure VNet IP-adress intervallet-/ Azure VNet-adressutrymmet var ett sammansatt intervall, finns det inga Följ för du med Microsoft. Förutom att VNet fortfarande sprider adressutrymmet för BGP-väg innehåller som det borttagna undernätet. Om du har definierat i Azure VNet IP-adress intervall/Azure VNet-adressutrymmet som flera IP-adressintervall som har en tilldelad till undernätet för borttagna, bör du ta bort som out-of-VNet-adressutrymmet och därefter meddela SAP HANA på Azure-tjänsthantering till ta bort den från de områden som SAP HANA i Azure (stora instanser) kan kommunicera med.

Om du vill ta bort ett undernät, se [tar bort ett undernät](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) för mer information om hur du skapar undernät.

## <a name="deleting-a-vnet"></a>Om du tar bort ett virtuellt nätverk

Om du vill ta bort ett virtuellt nätverk, se [ta bort ett virtuellt nätverk](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). SAP HANA på Azure-tjänsthantering tar bort de befintliga tillstånd på SAP HANA i Azure (stora instanser) ExpressRoute-kretsen och ta bort Azure VNet IP-adress intervall/Azure VNet-adressutrymmet för kommunikation med HANA stora instanser.

När det virtuella nätverket har tagits bort, öppna ett Azure-supporten om begäran om att tillhandahålla IP-adressutrymme range(s) ska tas bort.

Ta bort följande objekt för att säkerställa allt tas bort:

- ExpressRoute-anslutningen VNet Gateway offentlig IP för Gateway för virtuellt nätverk och virtuella nätverk

## <a name="deleting-an-expressroute-circuit"></a>Om du tar bort en ExpressRoute-krets

Öppna en Azure-supportbegäran med SAP HANA på Azure-tjänsthantering och begära att kretsen ska tas bort för att ta bort en ytterligare SAP HANA i Azure (stora instanser) ExpressRoute-kretsen. I Azure-prenumeration kan du ta bort eller behålla VNet som behövs. Dock måste du ta bort anslutningen mellan HANA stora instanser ExpressRoute-kretsen och länkade VNet-gateway.

Om du vill ta bort ett virtuellt nätverk, följer du anvisningarna om hur du raderar ett VNet i avsnittet ovan.


