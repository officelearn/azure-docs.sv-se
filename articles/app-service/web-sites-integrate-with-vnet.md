---
title: Integrera app med Azure Virtual Network
description: Lär dig hur Azure App Service integreras med Azure Virtual Network och hur du ansluter en app till ett virtuellt nätverk.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 71dc37fc000b2f195478e06f7e755fa8df926444
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688297"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med en Azure-Virtual Network
Det här dokumentet beskriver den Azure App Service funktionen för integrering av virtuella nätverk och hur du konfigurerar den med appar i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Med [Azure Virtual Networks][VNETOverview] (virtuella nätverk) kan du placera många av dina Azure-resurser i ett dirigerbart nätverk som inte är Internet.  

Azure App Service har två varianter. 

1. Datorer med flera innehavare som stöder alla pris avtal förutom isolerade
2. App Service-miljön (ASE), som distribueras till ditt VNet och stöder isolerade appar för pris plan

Det här dokumentet går igenom de två funktionerna för VNet-integrering som används i App Service för flera innehavare. Om din app är i [App Service-miljön][ASEintro]är den redan i ett VNet och kräver inte att funktionen VNet-integrering används för att komma åt resurser i samma VNet. Mer information om alla funktioner i App Service nätverksfunktioner finns i [App Service nätverksfunktioner](networking-features.md)

Det finns två formulär för funktionen VNet-integrering

1. En version möjliggör integrering med virtuella nätverk i samma region. Den här typen av funktion kräver ett undernät i ett VNet i samma region. Den här funktionen är fortfarande i för hands version men stöds för arbets belastningar för Windows-programproduktioner med vissa villkor som anges nedan.
2. Den andra versionen möjliggör integrering med virtuella nätverk i andra regioner eller med klassiska virtuella nätverk. Den här versionen av funktionen kräver distribution av en Virtual Network Gateway till ditt VNet. Det här är den punkt-till-plats-baserade VPN-baserade funktionen och stöds bara med Windows-appar.

En app kan bara använda en form av funktionen VNet-integrering i taget. Frågan är sedan vilken funktion du ska använda. Du kan använda antingen för många saker. De tydliga differentieringarna är:

| Problem  | Lösning | 
|----------|----------|
| Vill komma åt en RFC 1918-adress (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) i samma region | Regional VNet-integrering |
| Vill du komma åt resurser i ett klassiskt VNet eller ett VNet i en annan region | Gateway krävs VNet-integrering |
| Vill komma åt RFC 1918-slutpunkter över ExpressRoute | Regional VNet-integrering |
| Vill du uppnå resurser över tjänst slut punkter | Regional VNet-integrering |

Ingen av funktionerna gör att du kan komma åt icke-RFC 1918-adresser i ExpressRoute. Om du vill göra det måste du använda en ASE för tillfället.

Att använda regional VNet-integrering ansluter inte ditt VNet till lokala eller konfigurera tjänst slut punkter. Det är en separat nätverks konfiguration. Den regionala VNet-integrationen gör det enkelt för din app att ringa upp samtal mellan dessa anslutnings typer.

Oavsett vilken version som används ger VNet-integration din webbapp åtkomst till resurser i det virtuella nätverket, men beviljar inte inkommande privat åtkomst till din webbapp från det virtuella nätverket. Åtkomst till privata webbplatser avser att göra din app endast tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering är bara för att göra utgående samtal från din app till ditt VNet. 

Funktionen för VNet-integrering:

* kräver en pris plan för standard, Premium eller PremiumV2 
* Stöder TCP och UDP
* fungerar med App Service appar och Function-appar

Det finns vissa saker som VNet-integrering inte stöder, inklusive:

* montera en enhet
* AD-integrering 
* NetBios

## <a name="regional-vnet-integration"></a>Regional VNet-integrering 

> [!NOTE]
> Peering är inte tillgängligt ännu för Linux-baserade App Service.
>

När VNet-integrering används med virtuella nätverk i samma region som din app, kräver det att ett delegerat undernät används med minst 32 adresser. Det går inte att använda under nätet för något annat. Utgående anrop som görs från din app görs från adresserna i det delegerade under nätet. När du använder den här versionen av VNet-integrering görs anropen från adresser i ditt VNet. Genom att använda adresser i ditt VNet kan din app:

* Gör anrop till tjänstens slut punkt säkrade tjänster
* Åtkomst till resurser över ExpressRoute-anslutningar
* Få åtkomst till resurser i det virtuella nätverk som du är ansluten till
* Få åtkomst till resurser mellan peer-anslutningar inklusive ExpressRoute-anslutningar

Den här funktionen är i för hands version men stöds för Windows-appens arbets belastningar med följande begränsningar:

* Du kan bara komma åt adresser i RFC 1918-intervallet. De är adresser i adress blocken 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
* Du kan inte komma åt resurser över global peering anslutningar
* Du kan inte ange vägar för trafik som kommer från din app till ditt VNet
* Funktionen är endast tillgänglig från nyare App Service skalnings enheter som stöder PremiumV2 App Service-planer.
* Integrations under nätet kan bara användas av ett App Service plan
* Funktionen kan inte användas av isolerade plan-appar som finns i en App Service-miljön
* Funktionen kräver ett oanvänt undernät som är a/27 med 32 adresser eller större i ditt Resource Manager VNet
* Appen och VNet måste finnas i samma region
* Du kan inte ta bort ett VNet med en integrerad app. Du måste ta bort integrationen först 
* Du kan bara ha en regional VNet-integration per App Service plan. Flera appar i samma App Service plan kan använda samma VNet. 
* Det går inte att ändra prenumerationen på en app eller en App Service plan medan det finns en app som använder regional VNet-integrering

En adress används för varje App Service plan instans. Om du skalade appen till 5 instanser används 5 adresser. Eftersom det inte går att ändra under näts storleken efter tilldelningen, måste du använda ett undernät som är tillräckligt stort för att anpassa vilken skala appen kan komma åt. En/26 med 64-adresser är den rekommenderade storleken. En/27 med 32-adresser skulle ge en Premium-App Service plan 20 instanser om du inte ändrade App Service plan storlek. När du skalar en App Service plan upp eller ned behöver du två gånger så många adresser under en kort tids period. 

Om du vill att dina appar i en annan App Service plan ska komma åt ett VNet som är anslutet till redan av appar i en annan App Service plan, måste du välja ett annat undernät än det som används av den befintliga VNet-integreringen.  

Funktionen är också i för hands version för Linux. Använda funktionen VNet-integrering med ett Resource Manager VNet i samma region:

1. Gå till nätverks gränssnittet i portalen. Om din app kan använda den nya funktionen visas ett alternativ för att lägga till VNet (för hands version).  

   ![Välj VNet-integrering][6]

1. Välj **Lägg till VNet (för hands version)** .  

1. Välj det virtuella Resource Manager-nätverk som du vill integrera med och skapa sedan antingen ett nytt undernät eller Välj ett tomt redan befintligt undernät. Integrationen tar mindre än en minut att slutföra. Under integrationen startas appen om.  När integrationen är klar visas information om det virtuella nätverk som du är integrerad med och en banderoll överst som visar att funktionen är i för hands version.

   ![Välj VNet och undernät][7]

När din app har integrerats med ditt VNet kommer den att använda samma DNS-server som ditt VNet har kon figurer ATS med. 

Regional VNet-integrering kräver att ditt integrations-undernät delegeras till Microsoft. Web.  GRÄNSSNITTET för VNet-integrering kommer att delegera under nätet till Microsoft. Web automatiskt. Om ditt konto inte har tillräcklig nätverks behörighet för att ange detta, behöver du någon som kan ange attribut i ditt integrations-undernät för att delegera under nätet. Om du vill delegera integrations under nätet manuellt går du till Azure Virtual Network Subnet UI och anger delegering för Microsoft. Web.

Om du vill koppla från din app från VNet väljer du **Koppla från**. Då startas din webbapp om. 


#### <a name="web-app-for-containers"></a>Web App for Containers

Om du använder App Service på Linux med de inbyggda avbildningarna fungerar funktionen regional VNet-integrering utan ytterligare ändringar. Om du använder Web App for Containers måste du ändra Docker-avbildningen för att kunna använda VNet-integrering. I Docker-avbildningen använder du PORT miljö variabeln som den huvudsakliga webb serverns lyssnings port, i stället för att använda ett hårdkodad port nummer. PORT miljö variabeln anges automatiskt av App Service plattform vid behållarens start tid. Om du använder SSH måste SSH-daemonen konfigureras för att lyssna på det port nummer som anges av SSH_PORT-miljövariabeln när du använder regional VNet-integrering.

### <a name="service-endpoints"></a>Serviceslutpunkter

Med den nya funktionen för VNet-integrering kan du använda tjänst slut punkter.  Om du vill använda tjänst slut punkter med din app använder du den nya VNet-integreringen för att ansluta till ett valt VNet och konfigurerar sedan tjänstens slut punkter på det undernät som du använde för integreringen. 


### <a name="how-vnet-integration-works"></a>Så här fungerar VNet-integrering

Appar i App Service finns i arbets roller. De grundläggande och högre pris planerna är dedikerade värd planer där det inte finns några andra kund arbets belastningar som körs på samma arbetare. VNet-integreringen fungerar genom att montera virtuella gränssnitt med adresser i det delegerade under nätet. Eftersom från-adressen är i ditt VNet har den åtkomst till de flesta saker i eller via ditt VNet precis som en virtuell dator i ditt VNet. Nätverks implementeringen skiljer sig från att köra en virtuell dator i ditt VNet och det är anledningen till att vissa nätverksfunktioner ännu inte är tillgängliga när du använder den här funktionen.

![VNET-integration](media/web-sites-integrate-with-vnet/vnet-integration.png)

När VNet-integrering är aktive rad kommer din app fortfarande att göra utgående samtal till Internet via samma kanaler som normalt. De utgående adresser som listas i app Properties-portalen är fortfarande de adresser som används av din app. Vilka ändringar av appen är, anrop till tjänstens slut punkts säkra tjänster eller RFC 1918-adresser går till ditt VNet. 

Funktionen stöder endast ett virtuellt gränssnitt per arbetare.  Ett virtuellt gränssnitt per arbetare innebär en regional VNet-integration per App Service plan. Alla appar i samma App Service plan kan använda samma VNet-integrering, men om du behöver en app för att ansluta till ytterligare ett VNet måste du skapa en annan App Service plan. Det virtuella gränssnittet som används är inte en resurs som kunder har direkt åtkomst till.

På grund av hur den här tekniken fungerar visas inte den trafik som används med VNet-integrering i Network Watcher-eller NSG flödes loggar.  

## <a name="gateway-required-vnet-integration"></a>Gateway krävs VNet-integrering 

Gateway krävs VNet-integrerings funktion:

* Kan användas för att ansluta till virtuella nätverk i valfri region som de är Resource Manager eller klassisk virtuella nätverk
* Gör det möjligt för en app att ansluta till endast ett VNet i taget
* Gör det möjligt att integrera upp till fem virtuella nätverk med i ett App Service plan 
* Tillåter att samma VNet används av flera appar i en App Service plan utan att det totala antalet som kan användas av en App Service plan påverkas.  Om du har 6 appar som använder samma VNet i samma App Service plan räknas det som 1 VNet som används. 
* Kräver en Virtual Network gateway som är konfigurerad med punkt-till-plats-VPN
* Har stöd för 99,9% SLA på grund av SLA på gatewayen

Den här funktionen stöder inte:
* Använd med Linux-appar
* Åtkomst till resurser via ExpressRoute 
* Åtkomst till resurser via tjänstslutpunkter 

### <a name="getting-started"></a>Komma igång

Här är några saker att tänka på innan du ansluter din webbapp till ett virtuellt nätverk:

* Ett virtuellt mål nätverk måste ha punkt-till-plats-VPN aktiverat med en väg-baserad Gateway innan det kan anslutas till appen. 
* Det virtuella nätverket måste finnas i samma prenumeration som din App Service-plan (ASP).
* De appar som integreras med ett VNet använder den DNS som anges för det virtuella nätverket.

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurera en gateway i ditt VNet ###

Om du redan har en gateway som kon figurer ATS med punkt-till-plats-adresser kan du gå vidare till konfigurera VNet-integrering med din app.  
Så här skapar du en gateway:

1. [Skapa ett Gateway-undernät][creategatewaysubnet] i ditt VNet.  

1. [Skapa VPN-gatewayen][creategateway]. Välj en Route-baserad VPN-typ.

1. [Ange platsen för plats adresser][setp2saddresses]. Om gatewayen inte finns i Basic SKU måste IKEV2 vara inaktiverat i punkt-till-plats-konfigurationen och SSTP måste väljas. Adress utrymmet måste finnas i RFC 1918-Adressblock, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Om du bara skapar en gateway för användning med App Service VNet-integrering behöver du inte ladda upp ett certifikat. Det kan ta 30 minuter att skapa en gateway. Du kommer inte att kunna integrera din app med ditt VNet förrän gatewayen har tillhandahållits. 

### <a name="configure-vnet-integration-with-your-app"></a>Konfigurera VNet-integrering med din app 

Så här aktiverar du VNet-integrering i appen: 

1. Gå till din app i Azure Portal och öppna appinställningar och välj nätverk > VNet-integrering. Din ASP måste vara en standard-SKU eller bättre för att kunna använda funktionen VNet-integrering. 
 ![VNet-integrations gränssnitt][1]

1. Välj **Lägg till VNet**. 
 ![lägga till VNet-integrering][2]

1. Välj ditt VNet. 
  ![Välj ditt VNet-][8]
  
Din app kommer att startas om efter det här sista steget.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Så här fungerar gatewayen som krävs för VNet-integrering

Den gateway som krävs för VNet-integrering är byggd ovanpå punkt-till-plats-VPN-teknik. Punkt-till-plats-tekniken begränsar nätverks åtkomsten till bara den virtuella dator som är värd för appen. Appar är begränsade till att bara skicka trafik ut till Internet, via Hybridanslutningar eller genom VNet-integrering. 

![Så här fungerar VNet-integrering][3]

## <a name="managing-vnet-integration"></a>Hantera VNet-integrering
Möjligheten att ansluta och koppla från ett VNet finns på en app-nivå. Åtgärder som kan påverka VNet-integration över flera appar finns på App Service plan nivå. Från appen > nätverk > VNet-integrering kan du få information om ditt VNet. Du kan se liknande information på ASP-nivå i ASP-> nätverk > VNet integrations Portal, inklusive vilka appar i som App Service plan använder en specifik integrering.

 ![VNet-information][4]

Den information du har till gång till i gränssnittet för VNet-integrering är densamma mellan appen och ASP-portalerna.

* VNet-namn – länkar till det virtuella nätverkets användar gränssnitt
* Plats – visar platsen för ditt VNet. Att integrera med ett VNet på en annan plats kan orsaka svars tids problem för din app. 
* Certifikat status – visar om dina certifikat är synkroniserade mellan din App Service plan och ditt VNet.
* Gateway-status – om du använder gatewayen som krävs för VNet-integrering kan du se Gateway-statusen.
* VNet-adress utrymme – visar IP-adressutrymmet för ditt VNet. 
* Adress utrymme för punkt-till-plats – visar punkt-till-plats-IP-adressutrymmet för ditt VNet. När du gör anrop till ditt VNet när du använder den gateway-obligatoriska funktionen, är din app från adress en av dessa adresser. 
* Plats-till-plats-adress utrymme – du kan använda plats-till-plats-VPN för att ansluta ditt VNet till dina lokala resurser eller till ett annat VNet. De IP-intervall som definieras med VPN-anslutningen visas här.
* DNS-servrar – visar de DNS-servrar som kon figurer ATS med ditt VNet.
* IP-adresser som dirigeras till VNet – visar de adress block som dirigeras som används för att driva trafik till ditt VNet 

Den enda åtgärd du kan utföra i vyn app för din VNet-integrering är att koppla från din app från det virtuella nätverk som den är ansluten till. Om du vill koppla från din app från ett VNet väljer du **Koppla från**. Din app kommer att startas om när du kopplar från ett virtuellt nätverk. Om du kopplar från, ändras inte ditt VNet. Under nätet eller gatewayen tas inte bort. Om du sedan vill ta bort ditt VNet måste du först koppla från appen från VNet och ta bort resurserna i den, till exempel gatewayer. 

Öppna ASP-ANVÄNDARGRÄNSSNITTET och välj **nätverk**för att komma till ASP-gränssnittet för VNet-integrering.  Under VNet-integrering väljer du **Klicka här för att konfigurera** för att öppna användar gränssnittet för nätverks funktions status.

![Integrerings information för ASP VNet][5]

ASP-gränssnittet för VNet-integrering visar alla virtuella nätverk som används av apparna i ASP. Om du vill se information om varje VNet klickar du på det virtuella nätverk som du är intresse rad av. Det finns två åtgärder som du kan utföra här.

* **Synkronisera nätverk**. Den synkroniserade nätverks åtgärden gäller endast för den gateway-beroende funktionen för VNet-integrering. Att utföra en synkroniserad nätverks åtgärd garanterar att dina certifikat och nätverksinformation är synkroniserade. Om du lägger till eller ändrar DNS för ditt VNet måste du utföra en **synkroniserad nätverks** åtgärd. Den här åtgärden startar om alla appar som använder det här virtuella nätverket.
* **Lägg till vägar** Om du lägger till vägar kommer utgående trafik att bedrivas i ditt VNet.

**Routning** Vägarna som definieras i ditt VNet används för att dirigera trafik till ditt VNet från din app. Om du behöver skicka ytterligare utgående trafik till VNet kan du lägga till dessa adress block här. Den här funktionen fungerar bara med Gateway krävs VNet-integrering.

**Certifikat** När gatewayen kräver att VNet-integrering är aktiverat, finns det ett nödvändigt utbyte av certifikat för att säkerställa anslutningens säkerhet. Tillsammans med certifikaten är DNS-konfigurationen, vägarna och andra liknande saker som beskriver nätverket.
Om certifikat eller nätverksinformation har ändrats måste du klicka på synkronisera nätverk. När du klickar på "synkronisera nätverk" orsakar du ett kort avbrott i anslutningen mellan appen och ditt VNet. När din app inte startas om kan det leda till att din webbplats inte fungerar korrekt. 

## <a name="accessing-on-premises-resources"></a>Åtkomst till lokala resurser
Appar kan komma åt lokala resurser genom att integrera med virtuella nätverk som har plats-till-plats-anslutningar. Om du använder gatewayen som krävs VNet-integrering måste du uppdatera dina lokala VPN gateway-vägar med punkt-till-plats-Adressblock. När plats-till-plats-VPN först konfigureras bör de skript som används för att konfigurera den Konfigurera vägar korrekt. Om du lägger till punkt-till-plats-adresser när du har skapat din plats-till-plats-VPN måste du uppdatera vägarna manuellt. Information om hur du gör det varierar per gateway och beskrivs inte här. Du kan inte ha BGP konfigurerat med en plats-till-plats-VPN-anslutning.

Det krävs ingen ytterligare konfiguration för den regionala VNet-integrerings funktionen för att komma ut på ditt VNet och lokalt. Du behöver bara ansluta ditt VNet till lokalt med ExpressRoute eller VPN för plats till plats. 

> [!NOTE]
> Den gateway som krävs för funktionen VNet-integrering integrerar inte en app med ett VNet som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har kon figurer ATS i [läget för samexistens][VPNERCoex] fungerar inte VNet-integreringen. Om du behöver åtkomst till resurser via en ExpressRoute-anslutning kan du använda funktionen regional VNet-integrering eller en [App Service-miljön][ASE]som körs i ditt VNet. 
> 
> 

## <a name="peering"></a>Peering
Om du använder peering med den regionala VNet-integrationen behöver du inte göra någon ytterligare konfiguration. 

Om du använder gatewayen som krävs VNet-integrering med peering måste du konfigurera ytterligare några objekt. Konfigurera peering så att den fungerar med din app:

1. Lägg till en peering-anslutning på det virtuella nätverk som din App ansluter till. När du lägger till peering-anslutningen aktiverar du **Tillåt åtkomst till virtuellt nätverk** och markerar **Tillåt vidarebefordrad trafik** och **Tillåt Gateway-överföring**.
1. Lägg till en peering-anslutning på det virtuella nätverk som peer-kopplas till det virtuella nätverk som du är ansluten till. När du lägger till peering-anslutningen på målets VNet aktiverar du **Tillåt åtkomst till virtuellt nätverk** och markerar **Tillåt vidarebefordrad trafik** och **Tillåt fjärrgatewayer**.
1. Gå till App Service plan > Networking > VNet integration UI i portalen.  Välj det VNet som appen ansluter till. Under avsnittet routning lägger du till adress intervallet för det virtuella nätverk som peer-kopplas med det virtuella nätverk som appen är ansluten till.  


## <a name="pricing-details"></a>Prisinformation
Funktionen för regional VNet-integrering har ingen extra kostnad för användning utöver pris nivån för ASP.

Det finns tre relaterade kostnader för att använda gatewayen som krävs VNet-integrerings funktion:

* Pris nivå avgifter för ASP – dina appar måste vara i en standard-, Premium-eller PremiumV2 App Service-plan. Du kan se mer information om dessa kostnader här: [App Service prissättning][ASPricing]. 
* Kostnader för data överföring – det finns en avgift för utgående data, även om VNet finns i samma data Center. Dessa avgifter beskrivs i [dataöverföring pris information][DataPricing]. 
* VPN Gateway kostnader – det finns en kostnad för den VNet-gateway som krävs för punkt-till-plats-VPN. Informationen finns på sidan med [VPN gateway priser][VNETPricing] .


## <a name="troubleshooting"></a>Felsöka
Även om funktionen är enkel att konfigurera, innebär det inte att din upplevelse kommer att vara problem fri. Om du stöter på problem med att komma åt den önskade slut punkten finns det några verktyg som du kan använda för att testa anslutningen från App-konsolen. Det finns två konsoler som du kan använda. Det ena är kudu-konsolen och den andra konsolen i Azure Portal. Om du vill komma åt kudu-konsolen från din app går du till Verktyg-> kudu. Du kan också komma åt Kudo-konsolen på [webbplats namn]. scm. azurewebsites. net. När webbplatsen har lästs in går du till fliken fel söknings konsol. För att komma till den Azure Portal värdbaserade konsolen går du till Verktyg->-konsolen från din app. 

#### <a name="tools"></a>Verktyg
Verktygen **ping**, **nslookup** och **tracert** fungerar inte via konsolen på grund av säkerhets begränsningar. Två separata verktyg har lagts till för att fylla i Void. För att testa DNS-funktionen har vi lagt till ett verktyg med namnet nameresolver. exe. Syntax:

    nameresolver.exe hostname [optional: DNS Server]

Du kan använda **nameresolver** för att kontrol lera de värdnamn som appen är beroende av. På så sätt kan du testa om du har något som är mis-konfigurerat med din DNS eller kanske inte har åtkomst till din DNS-server. Du kan se den DNS-server som appen kommer att använda i-konsolen genom att titta på miljövariabler WEBSITE_DNS_SERVER och WEBSITE_DNS_ALT_SERVER.

Med nästa verktyg kan du testa TCP-anslutningar till en kombination av värd och port. Det här verktyget kallas **tcpping** och syntaxen är:

    tcpping.exe hostname [optional: port]

**Tcpping** -verktyget visar om du kan komma åt en speciell värd och port. Den kan bara visas om: det finns ett program som lyssnar på värd-och port kombinationen och det finns en nätverks åtkomst från din app till den angivna värden och porten.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Fel sökning av åtkomst till VNet-baserade resurser
Det finns ett antal saker som kan förhindra att appen når en speciell värd och port. Merparten av tiden är ett av tre saker:

* **En brand vägg är på väg.** Om du har en brand vägg i vägen kommer du att trycka på TCP-timeout. TCP-tidsgräns är 21 sekunder i det här fallet. Använd verktyget **tcpping** för att testa anslutningen. TCP-tidsgräns kan bero på många saker bortom brand väggarna, men starta där. 
* **DNS är inte tillgängligt.** DNS-timeoutvärdet är tre sekunder per DNS-server. Om du har två DNS-servrar är tids gränsen 6 sekunder. Använd nameresolver för att se om DNS fungerar. Kom ihåg att du inte kan använda nslookup eftersom det inte använder DNS ditt VNet har kon figurer ATS med. Om det inte går att komma åt kan du ha en brand vägg eller NSG som blockerar åtkomsten till DNS, eller så kan den vara avstängd.

Om dessa objekt inte besvarar dina problem, se först till exempel: 

**regional VNet-integrering**
* är ditt mål en RFC 1918-adress
* finns det en NSG som blockerar utgående från ditt integrations under nät
* Om du går över ExpressRoute eller en VPN-anslutning, är din lokala gateway konfigurerad för att dirigera trafik tillbaka till Azure? Om du kan komma åt slut punkter i ditt VNet men inte lokalt, är det bättre att kontrol lera.

**Gateway krävs VNet-integrering**
* är adress intervallet för punkt-till-plats i RFC 1918-intervallen (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Visas gatewayen som i portalen? Om din Gateway inte är igång kan du ta den tillbaka.
* Visas certifikaten som synkroniserade eller så misstänker du att nätverks konfigurationen har ändrats?  Om dina certifikat inte är synkroniserade eller om du misstänker att det har skett en ändring av din VNet-konfiguration som inte har synkroniserats med ASP, klickar du på synkronisera nätverk.
* Om du går över ExpressRoute eller en VPN-anslutning, är din lokala gateway konfigurerad för att dirigera trafik tillbaka till Azure? Om du kan komma åt slut punkter i ditt VNet men inte lokalt, är det bättre att kontrol lera.

Fel sökning av nätverks problem är en utmaning eftersom det inte går att se vad som blockerar åtkomst till en speciell värd: port kombination. Några av orsakerna är:

* du har en brand vägg på din värd som förhindrar åtkomst till program porten från platsens IP-intervall. Korsande undernät kräver ofta offentlig åtkomst.
* mål värden är avstängd
* programmet är inte tillgängligt
* du har fel IP-adress eller värdnamn
* ditt program lyssnar på en annan port än det du förväntade dig. Du kan matcha ditt process-ID med lyssnings porten med hjälp av "netstat-Aon" på slut punkts värden. 
* dina nätverks säkerhets grupper konfigureras på ett sådant sätt att de förhindrar åtkomst till din program värd och port från ditt plats-till-plats-IP-intervall

Kom ihåg att du inte vet vilken adress appen faktiskt kommer att använda. Det kan vara vilken adress som helst i integrations under nätet eller från punkt-till-plats-adressintervallet, så du måste tillåta åtkomst från hela adress intervallet. 

Ytterligare fel söknings steg är:

* Anslut till en virtuell dator i ditt VNet och försök att ansluta till resurs värden: port därifrån. Om du vill testa TCP-åtkomst använder du PowerShell **-kommandot test-NetConnection**. Syntax:

      test-netconnection hostname [optional: -Port]

* Hämta ett program på en virtuell dator och testa åtkomsten till denna värd och port från-konsolen från din app med hjälp av **tcpping**

#### <a name="on-premises-resources"></a>Lokala resurser ####

Om din app inte kan komma åt en resurs lokalt, kontrollerar du om du kan komma åt resursen från ditt VNet. Använd PowerShell **-kommandot test-NetConnection** för att kontrol lera TCP-åtkomst. Om din virtuella dator inte kan komma åt din lokala resurs, kanske inte VPN-eller ExpressRoute-anslutningen är korrekt konfigurerad.

Om det virtuella nätverket som finns på den virtuella datorn kan komma åt ditt lokala system, men din app inte kan, beror orsaken förmodligen på något av följande orsaker:

* dina vägar har inte kon figurer ATS med ditt undernät eller pekar på plats adress intervall i din lokala gateway
* dina nätverks säkerhets grupper blockerar åtkomsten för ditt punkt-till-plats-IP-intervall
* dina lokala brand väggar blockerar trafik från ditt punkt-till-plats-IP-intervall
* du försöker komma åt en icke-RFC 1918-adress med hjälp av funktionen för regional VNet-integrering


## <a name="powershell-automation"></a>PowerShell-Automation

Du kan integrera App Service med en Azure-Virtual Network med hjälp av PowerShell. Ett skript som är klart att köra finns i [ansluta en app i Azure App Service till en Azure-Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
