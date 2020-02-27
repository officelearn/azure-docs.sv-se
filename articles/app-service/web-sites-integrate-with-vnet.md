---
title: Integrera app med Azure Virtual Network
description: Integrera appar i Azure App Service med virtuella Azure-nätverk.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649074"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med en Azure-Virtual Network
Det här dokumentet beskriver den Azure App Service funktionen för integrering av virtuella nätverk och hur du konfigurerar den med appar i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Med [Azure Virtual Networks][VNETOverview] (virtuella nätverk) kan du placera många av dina Azure-resurser i ett dirigerbart nätverk som inte är Internet.  

Azure App Service har två varianter. 

1. Datorer med flera innehavare som stöder alla pris avtal förutom isolerade
2. App Service-miljön (ASE), som distribueras till ditt VNet och stöder isolerade appar för pris plan

Det här dokumentet går igenom funktionen för VNet-integrering som används i App Service för flera innehavare. Om din app är i [App Service-miljön][ASEintro]är den redan i ett VNet och kräver inte att funktionen VNet-integrering används för att komma åt resurser i samma VNet. Mer information om alla funktioner i App Service nätverksfunktioner finns i [App Service nätverksfunktioner](networking-features.md)

VNet-integrering ger din webbapp åtkomst till resurser i det virtuella nätverket, men beviljar inte inkommande privat åtkomst till din webbapp från det virtuella nätverket. Åtkomst till privata webbplatser avser att göra din app endast tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering är bara för att göra utgående samtal från din app till ditt VNet. Funktionen VNet-integrering fungerar annorlunda när den används med virtuella nätverk i samma region och med virtuella nätverk i andra regioner. Funktionen för VNet-integrering har två varianter.

1. Regional VNet-integration – när du ansluter till Resource Manager-virtuella nätverk i samma region måste du ha ett dedikerat undernät i det virtuella nätverk som du integrerar med. 
2. Gateway krävs VNet-integrering – vid anslutning till virtuella nätverk i andra regioner eller till ett klassiskt VNet i samma region måste du ha en Virtual Network Gateway etablerad i mål-VNet.

Funktionerna för VNet-integrering:

* Kräv en pris plan för standard, Premium, PremiumV2 eller elastisk Premium 
* stöd för TCP och UDP
* arbeta med App Service appar och Function-appar

Det finns vissa saker som VNet-integrering inte stöder, inklusive:

* montera en enhet
* AD-integrering 
* NetBios

Gateway krävs endast VNet-integrering ger till gång till resurser i målets virtuella nätverk eller i nätverk som är anslutna till målets virtuella nätverk med peering eller VPN. Gateway krävs VNet-integrering ger inte åtkomst till resurser som är tillgängliga i ExpressRoute-anslutningar eller fungerar med tjänst slut punkter. 

Oavsett vilken version som används ger VNet-integration din webbapp åtkomst till resurser i det virtuella nätverket, men beviljar inte inkommande privat åtkomst till din webbapp från det virtuella nätverket. Åtkomst till privata webbplatser avser att göra din app endast tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering är bara för att göra utgående samtal från din app till ditt VNet. 

## <a name="enable-vnet-integration"></a>Aktivera VNet-integrering 

1. Gå till nätverks gränssnittet i App Service portalen. Under VNet-integrering väljer *du "Klicka här för att konfigurera"* . 

1. Välj **Lägg till VNet**.  

   ![Välj VNet-integrering][1]

1. Den nedrullningsbara listan innehåller alla Resource Manager-virtuella nätverk i din prenumeration i samma region och nedan finns en lista över alla Resource Manager-virtuella nätverk i alla andra regioner. Välj det virtuella nätverk som du vill integrera med.

   ![Välj VNet][2]

   * Om VNet finns i samma region skapar du antingen ett nytt undernät eller väljer ett tomt redan befintligt undernät. 

   * Om du vill välja ett VNet i en annan region måste du ha en Virtual Network Gateway etablerad med peka på plats aktive rad.

   * Om du vill integrera med ett klassiskt VNet i stället för att klicka på VNet-listruta väljer du **Klicka här för att ansluta till ett klassiskt VNet**. Välj önskat klassiskt VNet. Mål-VNet måste redan ha en Virtual Network Gateway etablerad med peka på plats aktive rad.

    ![Välj klassiskt VNet][3]
    
Under integrationen startas appen om.  När integrationen är klar visas information om det virtuella nätverk som du är integrerad med. 

När din app har integrerats med ditt VNet kommer den att använda samma DNS-server som ditt VNet har kon figurer ATS med, såvida det inte är Azure DNS Private Zones. Du kan för närvarande inte använda VNet-integrering med Azure DNS Private Zones.

## <a name="regional-vnet-integration"></a>Regional VNet-integrering

Genom att använda regional VNet-integrering kan din app komma åt:

* resurser i VNet i samma region som du integrerar med 
* resurser i virtuella nätverk som peer-kopplas till ditt VNet i samma region
* tjänst slut punkt säkra tjänster
* resurser över ExpressRoute-anslutningar
* resurser i det virtuella nätverk som du är ansluten till
* resurser mellan peer-anslutningar inklusive ExpressRoute-anslutningar
* Privata slut punkter 

När du använder VNet-integrering med virtuella nätverk i samma region kan du använda följande funktioner i Azure-nätverk:

* Nätverks säkerhets grupper (NSG: er) – du kan blockera utgående trafik med en nätverks säkerhets grupp som placeras i ditt integrations undernät. Reglerna för inkommande trafik gäller inte eftersom du inte kan använda VNet-integrering för att ge inkommande åtkomst till din webbapp.
* Routningstabeller (UDR) – du kan placera en routningstabell i integrations under nätet för att skicka utgående trafik där du vill. 

Som standard dirigerar din app endast RFC1918-trafik till ditt VNet. Om du vill dirigera all utgående trafik till ditt VNet använder du appens inställning WEBSITE_VNET_ROUTE_ALL till din app. Konfigurera appens inställning:

1. Gå till konfigurations gränssnittet på din app-Portal. Välj **ny program inställning**
1. Skriv **WEBSITE_VNET_ROUTE_ALL** i fältet namn och **1** i fältet värde

   ![Ange program inställning][4]

1. Välj **OK**
1. Välj **Spara**

Om du dirigerar all utgående trafik till ditt VNet, kommer den att omfattas av NSG: er och UDR som tillämpas på ditt integrations undernät. När du dirigerar all utgående trafik till ditt VNet är dina utgående adresser fortfarande de utgående adresser som listas i dina app-egenskaper, om du inte tillhandahåller vägar för att skicka trafiken någon annan stans. 

Det finns vissa begränsningar med att använda VNet-integrering med virtuella nätverk i samma region:

* Du kan inte komma åt resurser över global peering anslutningar
* Funktionen är endast tillgänglig från nyare App Service skalnings enheter som stöder PremiumV2 App Service-planer.
* Integrations under nätet kan bara användas av ett App Service plan
* Funktionen kan inte användas av isolerade plan-appar som finns i en App Service-miljön
* Funktionen kräver ett oanvänt undernät som är a/27 med 32 adresser eller större i ett virtuellt Resource Manager-nätverk
* Appen och VNet måste finnas i samma region
* Du kan inte ta bort ett VNet med en integrerad app. Ta bort integrationen innan du tar bort det virtuella nätverket. 
* Du kan bara integrera med virtuella nätverk i samma prenumeration som webbappen
* Du kan bara ha en regional VNet-integration per App Service plan. Flera appar i samma App Service plan kan använda samma VNet. 
* Det går inte att ändra prenumerationen på en app eller en App Service plan medan det finns en app som använder regional VNet-integrering

En adress används för varje App Service plan instans. Om du skalar din app till fem instanser används fem adresser. Eftersom det inte går att ändra under näts storleken efter tilldelningen, måste du använda ett undernät som är tillräckligt stort för att anpassa vilken skala appen kan komma åt. En/26 med 64-adresser är den rekommenderade storleken. A/26 med 64 adresser kommer att ha en Premium-App Service plan med 30 instanser. När du skalar en App Service plan upp eller ned behöver du två gånger så många adresser under en kort tids period. 

Om du vill att dina appar i en annan App Service plan ska komma åt ett VNet som är anslutet till redan av appar i en annan App Service plan, måste du välja ett annat undernät än det som används av den befintliga VNet-integreringen.  

Funktionen är i för hands version för Linux. Linux-formen för funktionen stöder bara anrop till RFC 1918-adresser (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-app-for-containers"></a>Web App for Containers

Om du använder App Service på Linux med inbyggda avbildningar fungerar regional VNet-integrering utan ytterligare ändringar. Om du använder Web App for Containers måste du ändra Docker-avbildningen för att kunna använda VNet-integrering. I Docker-avbildningen använder du PORT miljö variabeln som den huvudsakliga webb serverns lyssnings port, i stället för att använda ett hårdkodad port nummer. PORT miljö variabeln anges automatiskt av App Service plattform vid behållarens start tid. Om du använder SSH måste SSH-daemonen konfigureras för att lyssna på det port nummer som anges av SSH_PORT-miljövariabeln när du använder regional VNet-integrering.  Det finns inget stöd för gateway som kräver VNet-integrering på Linux. 

### <a name="service-endpoints"></a>Tjänst slut punkter

Regional VNet-integrering gör att du kan använda tjänst slut punkter.  Om du vill använda tjänstens slut punkter med din app använder du regional VNet-integrering för att ansluta till ett valt VNet och konfigurerar sedan tjänst slut punkter på det undernät som du använde för integreringen. 

### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Med nätverks säkerhets grupper kan du blockera inkommande och utgående trafik till resurser i ett VNet. En webbapp som använder regional VNet-integrering kan använda [nätverks säkerhets gruppen][VNETnsg] för att blockera utgående trafik till resurser i ditt VNet eller Internet. Om du vill blockera trafik till offentliga adresser måste du ha program inställningen WEBSITE_VNET_ROUTE_ALL inställd på 1. Reglerna för inkommande trafik i en NSG gäller inte för din app eftersom VNet-integration endast påverkar utgående trafik från din app. Om du vill kontrol lera inkommande trafik till din webbapp använder du funktionen begränsningar för åtkomst. En NSG som tillämpas på ditt integrations under nät kommer att gälla oavsett vilka vägar som tillämpas på ditt integrations under nät. Om WEBSITE_VNET_ROUTE_ALL har angetts till 1 och du inte har några vägar som påverkar offentlig adress trafik i ditt integrations under nät, skulle all utgående trafik fortfarande bli föremål för NSG: er som har tilldelats ditt integrations-undernät. Om WEBSITE_VNET_ROUTE_ALL inte har angetts tillämpas NSG: er endast på RFC1918-trafik.

### <a name="routes"></a>Vägar

Med routningstabeller kan du dirigera utgående trafik från din app till var du vill. Som standard kommer routningstabeller bara att påverka din RFC1918-destinations trafik.  Om du anger WEBSITE_VNET_ROUTE_ALL till 1, kommer alla utgående samtal att påverkas. Vägar som anges i ditt integrations undernät påverkar inte svar på inkommande app-begäranden. Vanliga mål kan omfatta brand Väggs enheter eller gatewayer. Om du vill dirigera all utgående trafik lokalt kan du använda en routningstabell för att skicka all utgående trafik till din ExpressRoute-Gateway. Om du dirigerar trafik till en gateway måste du ställa in vägar i det externa nätverket för att skicka svar tillbaka.

BGP-vägar (Border Gateway Protocol) kommer också att påverka din app-trafik. Om du har BGP-vägar från något som liknar en ExpressRoute-gateway kommer din app utgående trafik att påverkas. Som standard kommer BGP-vägar endast att påverka din RFC1918-destinations trafik. Om WEBSITE_VNET_ROUTE_ALL har värdet 1 kan all utgående trafik påverkas av BGP-vägarna. 

### <a name="how-regional-vnet-integration-works"></a>Så här fungerar regional VNet-integrering

Appar i App Service finns i arbets roller. De grundläggande och högre pris planerna är dedikerade värd planer där det inte finns några andra kund arbets belastningar som körs på samma arbetare. Regional VNet-integrering fungerar genom att montera virtuella gränssnitt med adresser i det delegerade under nätet. Eftersom från-adressen är i ditt VNet kan den komma åt de flesta saker i eller via ditt VNet precis som en virtuell dator i ditt VNet. Nätverks implementeringen skiljer sig från att köra en virtuell dator i ditt VNet och det är anledningen till att vissa nätverksfunktioner ännu inte är tillgängliga när du använder den här funktionen.

![Så här fungerar regional VNet-integrering][5]

När regional VNet-integrering är aktive rad kommer din app fortfarande att göra utgående samtal till Internet via samma kanaler som normalt. De utgående adresser som listas i app Properties-portalen är fortfarande de adresser som används av din app. Vilka ändringar av appen är, anrop till tjänstens slut punkts säkra tjänster eller RFC 1918-adresser går till ditt VNet. Om WEBSITE_VNET_ROUTE_ALL är inställt på 1 kan all utgående trafik skickas till ditt VNet. 

Funktionen stöder endast ett virtuellt gränssnitt per arbetare.  Ett virtuellt gränssnitt per arbetare innebär en regional VNet-integration per App Service plan. Alla appar i samma App Service plan kan använda samma VNet-integrering, men om du behöver en app för att ansluta till ytterligare ett VNet måste du skapa en annan App Service plan. Det virtuella gränssnittet som används är inte en resurs som kunder har direkt åtkomst till.

På grund av hur den här tekniken fungerar visas inte den trafik som används med VNet-integrering i Network Watcher-eller NSG flödes loggar.  

## <a name="gateway-required-vnet-integration"></a>Gateway krävs VNet-integrering

Gateway krävs VNet-integration stöder anslutning till ett VNet i en annan region eller till ett klassiskt VNet. Gateway krävs VNet-integrering: 

* Gör det möjligt för en app att ansluta till endast ett VNet i taget
* Gör det möjligt att integrera upp till fem virtuella nätverk i en App Service plan 
* Tillåter att samma VNet används av flera appar i en App Service plan utan att det totala antalet som kan användas av en App Service plan påverkas.  Om du har sex appar som använder samma VNet i samma App Service plan räknas det som 1 VNet som används. 
* Har stöd för 99,9% SLA på grund av SLA på gatewayen
* Gör att dina appar kan använda den DNS som VNet har kon figurer ATS med
* Kräver en Virtual Network Route-baserad gateway som kon figurer ATS med SSTP punkt-till-plats-VPN innan den kan anslutas till appen. 

Du kan inte använda Gateway krävs VNet-integrering:

* Med Linux-appar
* Med ett virtuellt nätverk som är anslutet till ExpressRoute 
* Få åtkomst till tjänst slut punkter säkrade resurser
* Med en gateway för samexistens som stöder både ExpressRoute och pekar på plats-/plats-till-plats-VPN

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurera en gateway i ditt VNet ###

Så här skapar du en gateway:

1. [Skapa ett Gateway-undernät][creategatewaysubnet] i ditt VNet.  

1. [Skapa VPN-gatewayen][creategateway]. Välj en Route-baserad VPN-typ.

1. [Ange platsen för plats adresser][setp2saddresses]. Om gatewayen inte finns i Basic SKU måste IKEV2 vara inaktiverat i plats-till-plats-konfigurationen och SSTP måste väljas. Platsens adress utrymme måste vara i RFC 1918-Adressblock, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Om du bara skapar en gateway för användning med App Service VNet-integrering behöver du inte ladda upp ett certifikat. Det kan ta 30 minuter att skapa en gateway. Du kommer inte att kunna integrera din app med ditt VNet förrän gatewayen har tillhandahållits. 

### <a name="how-gateway-required-vnet-integration-works"></a>Hur Gateway kräver att VNet-integrering fungerar

Gateway krävs VNet-integrering som byggts ovanpå punkt-till-plats-VPN-teknik. Peka på plats-VPN: er begränsar nätverks åtkomsten till bara den virtuella dator som är värd för appen. Appar är begränsade till att bara skicka trafik ut till Internet, via Hybridanslutningar eller genom VNet-integrering. När din app har kon figurer ATS med portalen för att använda Gateway krävs VNet-integrering, hanteras en komplex förhandling för din räkning för att skapa och tilldela certifikat på gatewayen och på program sidan. Slut resultatet är att de anställda som används som värd för dina appar kan ansluta direkt till den virtuella Nätverksgatewayen i det valda virtuella nätverket. 

![Hur Gateway kräver att VNet-integrering fungerar][6]

### <a name="accessing-on-premises-resources"></a>Åtkomst till lokala resurser

Appar kan komma åt lokala resurser genom att integrera med virtuella nätverk som har plats-till-plats-anslutningar. Om du använder gatewayen som krävs VNet-integrering måste du uppdatera dina lokala VPN gateway-vägar med punkt-till-plats-Adressblock. När plats-till-plats-VPN först konfigureras bör de skript som används för att konfigurera den Konfigurera vägar korrekt. Om du lägger till punkt-till-plats-adresser när du har skapat din plats-till-plats-VPN måste du uppdatera vägarna manuellt. Information om hur du gör det varierar per gateway och beskrivs inte här. Du kan inte ha BGP konfigurerat med en plats-till-plats-VPN-anslutning.

Det krävs ingen ytterligare konfiguration för den regionala VNet-integrerings funktionen för att komma ut på ditt VNet och lokalt. Du behöver bara ansluta ditt VNet till lokalt med ExpressRoute eller VPN för plats till plats. 

> [!NOTE]
> Den gateway som krävs för funktionen VNet-integrering integrerar inte en app med ett VNet som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har kon figurer ATS i [läget för samexistens][VPNERCoex] fungerar inte VNet-integreringen. Om du behöver åtkomst till resurser via en ExpressRoute-anslutning kan du använda funktionen regional VNet-integrering eller en [App Service-miljön][ASE]som körs i ditt VNet. 
> 
> 

### <a name="peering"></a>Peering

Om du använder peering med den regionala VNet-integrationen behöver du inte göra någon ytterligare konfiguration. 

Om du använder gatewayen som krävs VNet-integrering med peering måste du konfigurera ytterligare några objekt. Konfigurera peering så att den fungerar med din app:

1. Lägg till en peering-anslutning på det virtuella nätverk som din App ansluter till. När du lägger till peering-anslutningen aktiverar du **Tillåt åtkomst till virtuellt nätverk** och markerar **Tillåt vidarebefordrad trafik** och **Tillåt Gateway-överföring**.
1. Lägg till en peering-anslutning på det virtuella nätverk som peer-kopplas till det virtuella nätverk som du är ansluten till. När du lägger till peering-anslutningen på målets VNet aktiverar du **Tillåt åtkomst till virtuellt nätverk** och markerar **Tillåt vidarebefordrad trafik** och **Tillåt fjärrgatewayer**.
1. Gå till App Service plan > Networking > VNet integration UI i portalen.  Välj det VNet som appen ansluter till. Under avsnittet routning lägger du till adress intervallet för det virtuella nätverk som peer-kopplas med det virtuella nätverk som appen är ansluten till.  

## <a name="managing-vnet-integration"></a>Hantera VNet-integrering 

Anslutning och från koppling med ett VNet finns på en app-nivå. Åtgärder som kan påverka VNet-integration över flera appar finns på App Service plan nivå. Från appen > nätverk > VNet-integrering kan du få information om ditt VNet. Du kan se liknande information på ASP-nivå i ASP-> nätverk > VNet integrations Portal.

Den enda åtgärd du kan utföra i vyn app för din VNet-integrering är att koppla från din app från det virtuella nätverk som den är ansluten till. Om du vill koppla från din app från ett VNet väljer du **Koppla från**. Din app kommer att startas om när du kopplar från ett virtuellt nätverk. Om du kopplar från, ändras inte ditt VNet. Under nätet eller gatewayen tas inte bort. Om du sedan vill ta bort ditt VNet måste du först koppla från appen från VNet och ta bort resurserna i den, till exempel gatewayer. 

ASP-gränssnittet för VNet-integrering visar alla VNet-integreringar som används av apparna i ASP. Om du vill se information om varje VNet klickar du på det virtuella nätverk som du är intresse rad av. Det finns två åtgärder som du kan utföra här för gateway krävs VNet-integrering.

* **Synkronisera nätverk**. Den synkroniserade nätverks åtgärden gäller endast för den gateway-beroende funktionen för VNet-integrering. Att utföra en synkroniserad nätverks åtgärd garanterar att dina certifikat och nätverksinformation är synkroniserade. Om du lägger till eller ändrar DNS för ditt VNet måste du utföra en **synkroniserad nätverks** åtgärd. Den här åtgärden startar om alla appar som använder det här virtuella nätverket.
* **Lägg till vägar** Om du lägger till vägar kommer utgående trafik att bedrivas i ditt VNet. 

**Gateway krävs VNet-integrering routning** Vägarna som definieras i ditt VNet används för att dirigera trafik till ditt VNet från din app. Om du behöver skicka ytterligare utgående trafik till VNet kan du lägga till dessa adress block här. Den här funktionen fungerar bara med Gateway krävs VNet-integrering. Routningstabeller påverkar inte din app-trafik när du använder Gateway krävs VNet-integration på det sätt som de gör med regional VNet-integration.

**Gateway krävs VNet-integrerings certifikat** När gatewayen kräver att VNet-integrering är aktiverat, finns det ett nödvändigt utbyte av certifikat för att säkerställa anslutningens säkerhet. Tillsammans med certifikaten är DNS-konfigurationen, vägarna och andra liknande saker som beskriver nätverket.
Om certifikat eller nätverksinformation har ändrats måste du klicka på synkronisera nätverk. När du klickar på "synkronisera nätverk" orsakar du ett kort avbrott i anslutningen mellan appen och ditt VNet. När din app inte startas om kan det leda till att din webbplats inte fungerar korrekt. 

## <a name="pricing-details"></a>Prisinformation
Funktionen för regional VNet-integrering har ingen extra kostnad för användning utöver pris nivån för ASP.

Det finns tre relaterade kostnader för att använda gatewayen som krävs VNet-integrerings funktion:

* Pris nivå avgifter för ASP – dina appar måste vara i en standard-, Premium-eller PremiumV2 App Service-plan. Du kan se mer information om dessa kostnader här: [App Service prissättning][ASPricing]. 
* Kostnader för data överföring – det finns en avgift för utgående data, även om VNet finns i samma data Center. Dessa avgifter beskrivs i [dataöverföring pris information][DataPricing]. 
* VPN Gateway kostnader – det finns en kostnad för den VNet-gateway som krävs för punkt-till-plats-VPN. Informationen finns på sidan med [VPN gateway priser][VNETPricing] .

## <a name="troubleshooting"></a>Felsökning
Även om funktionen är enkel att konfigurera, innebär det inte att din upplevelse kommer att vara problem fri. Om du stöter på problem med att komma åt den önskade slut punkten finns det några verktyg som du kan använda för att testa anslutningen från App-konsolen. Det finns två konsoler som du kan använda. Det ena är kudu-konsolen och den andra konsolen i Azure Portal. Om du vill komma åt kudu-konsolen från din app går du till Verktyg-> kudu. Du kan också komma åt Kudo-konsolen på [webbplats namn]. scm. azurewebsites. net. När webbplatsen har lästs in går du till fliken fel söknings konsol. För att komma till den Azure Portal värdbaserade konsolen går du till Verktyg->-konsolen från din app. 

#### <a name="tools"></a>Verktyg
Verktygen **ping**, **nslookup**och **tracert** fungerar inte via konsolen på grund av säkerhets begränsningar. Två separata verktyg har lagts till för att fylla i Void. För att testa DNS-funktionen har vi lagt till ett verktyg med namnet nameresolver. exe. Syntax:

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
* är ditt mål en icke-RFC1918 adress och du har inte WEBSITE_VNET_ROUTE_ALL inställt på 1
* finns det en NSG som blockerar utgående från ditt integrations under nät
* Om du går över ExpressRoute eller en VPN-anslutning, är din lokala gateway konfigurerad för att dirigera trafik tillbaka till Azure? Om du kan komma åt slut punkter i ditt VNet men inte lokalt, kontrollerar du dina vägar.
* har du tillräckligt med behörighet för att ställa in delegering i integrations under nätet? Under konfigurationen av regional VNet-integrering delegeras ditt integrations undernät till Microsoft. Web. GRÄNSSNITTET för VNet-integrering kommer att delegera under nätet till Microsoft. Web automatiskt. Om ditt konto inte har tillräckliga nätverks behörigheter för att konfigurera delegering, behöver du någon som kan ange attribut i ditt integrations-undernät för att delegera under nätet. Om du vill delegera integrations under nätet manuellt går du till Azure Virtual Network Subnet UI och anger delegering för Microsoft. Web. 

**Gateway krävs VNet-integrering**
* är adress intervallet för punkt-till-plats i RFC 1918-intervallen (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Visas gatewayen som i portalen? Om din Gateway inte är igång kan du ta den tillbaka.
* Visas certifikaten som synkroniserade eller så misstänker du att nätverks konfigurationen har ändrats?  Om dina certifikat inte är synkroniserade eller om du misstänker att det har skett en ändring av din VNet-konfiguration som inte har synkroniserats med ASP, klickar du på synkronisera nätverk.
* Om du går över en VPN-anslutning är den lokala gatewayen konfigurerad för att dirigera trafik tillbaka till Azure? Om du kan komma åt slut punkter i ditt VNet men inte lokalt, kontrollerar du dina vägar.
* försöker du använda en gateway för samexistens som stöder både punkt-till-plats-och ExpressRoute? Samexistens-gatewayer stöds inte med VNet-integrering 

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


## <a name="automation"></a>Automation

Det finns CLI-stöd för regional VNet-integrering. [Installera Azure CLI][installCLI]för att få åtkomst till följande kommandon. 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

För gateway krävs VNet-integrering, kan du integrera App Service med en Azure-Virtual Network med hjälp av PowerShell. Ett skript som är klart att köra finns i [ansluta en app i Azure App Service till en Azure-Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


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
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
