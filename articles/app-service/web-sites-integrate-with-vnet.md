---
title: Integrera en app med Azure-nätverk
description: Visar hur du ansluter en app i Azure App Service till en ny eller befintlig Azure-nätverk
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: ccompy
ms.openlocfilehash: 5e7a2150757afeecb6b0b100f3259f1628f42074
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262088"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med Azure-nätverk
Det här dokumentet beskriver funktionen Azure App Service-integrering för virtuellt nätverk och visar hur du konfigurerar den med appar i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Om du inte känner till Azure-nätverk (Vnet) är en funktion som gör att du kan placera många av dina Azure-resurser i ett icke-internet-dirigerbara nätverk som du styr åtkomst till. Dessa nätverk kan sedan anslutas till ditt lokala nätverk med hjälp av VPN-teknik. Om du vill veta mer om Azure-nätverk kan du börja med informationen här: [Azure översikt över Virtual Network][VNETOverview]. 

Azure App Service har två typer. 

1. De system för flera innehavare som har stöd för en fullständig uppsättning prissättningsplaner
2. App Service Environment (ASE) premiumfunktion, som distribuerar i ditt virtuella nätverk. 

Det här dokumentet går igenom VNet-integrering och inte App Service Environment. Om du vill veta mer om ASE-funktionen, börjar du med informationen här: [App Service Environment introduktion][ASEintro].

VNet-integrering ger dina webbprogram åtkomst till resurser i ditt virtuella nätverk, men ger inte privat åtkomst till din webbapp från det virtuella nätverket. Åtkomst till privata webbplatsen refererar till att appen endast kan nås från ett privat nätverk som från inom en Azure-nätverk. Privat plats åtkomsten är endast tillgänglig med en ASE som konfigurerats med en intern belastningsutjämnaren (ILB). Mer information om hur du använder en ILB ASE som börjar med den här artikeln: [skapa och använda en ILB ASE][ILBASE]. 

Ett vanligt scenario där du skulle använda VNet-integrering är att aktivera åtkomst från din webbapp till en databas eller en webbtjänst som körs på en virtuell dator i Azure-nätverk. Med VNet-integrering behöver du inte Exponerar en offentlig slutpunkt för program på den virtuella datorn men kan använda privata adresser för andra program än internet-dirigerbara i stället. 

Funktionen VNet-integrering:

* kräver en Standard, Premium eller isolerad prisavtal 
* fungerar med klassisk eller Resource Manager-VNet 
* stöder TCP och UDP
* fungerar med webb-, mobil, API-appar, och funktionsappar
* gör att en app för att ansluta till endast 1 virtuellt nätverk på en gång
* gör att upp till fem virtuella nätverk kan integreras med i en App Service Plan 
* tillåter samma virtuella nätverk som ska användas av flera appar i en App Service Plan
* har stöd för ett serviceavtal på 99,9% på grund av serviceavtalet på VNet-Gateway

Det finns några saker som VNet-integrering inte har stöd för bland annat:

* montera en enhet
* AD-integrering 
* NetBios
* privat Platsåtkomst

### <a name="getting-started"></a>Komma igång
Här följer några saker att tänka på innan du ansluter din webbapp till ett virtuellt nätverk:

* VNet-Integration fungerar bara med appar i en **Standard**, **Premium**, eller **isolerad** prisavtal. Om du aktiverar funktionen och sedan skala App Service-planen till ett som inte stöds prisplanen dina appar förlora sina anslutningar till de virtuella nätverken som de använder. 
* Om ditt virtuella nätverk för målet redan finns, måste den ha punkt-till-plats-VPN aktiveras med en dynamisk routningsgateway innan den kan anslutas till en app. Om din gateway har konfigurerats med statisk routning, kan du inte aktivera punkt-till-plats virtuellt privat nätverk (VPN).
* Det virtuella nätverket måste finnas i samma prenumeration som din App Service-Plan(ASP).
* Om din gateway finns redan med punkt-till-plats aktiverat och den inte är i en grundläggande SKU, måste IKEV2 inaktiveras i punkt-till-plats-konfiguration.
* Appar som integreras med ett virtuellt nätverk använder DNS som har angetts för det virtuella nätverket.
* Som standard dirigerar apparnas integrerande endast trafik i ditt virtuella nätverk baserat på vägar som definieras i ditt virtuella nätverk. 

## <a name="enabling-vnet-integration"></a>Aktiverar VNet-integrering

Du har möjlighet att ansluta din app till ett nytt eller befintligt virtuellt nätverk. Om du skapar ett nytt nätverk som en del av din integrering, förutom att bara skapa det virtuella nätverket kan en dynamisk routningsgateway är förkonfigurerad för dig och punkt-till-plats-VPN är aktiverad. 

> [!NOTE]
> Konfigurera en ny virtual network-integration kan ta flera minuter. 
> 
> 

Öppna inställningarna för din app och välj nätverk för att aktivera VNet-integrering. Användargränssnittet som öppnas erbjuder tre alternativ för nätverk. Den här guiden bara kommer in till VNet-integrering om Hybridanslutningar och App Service-miljöer beskrivs senare i det här dokumentet. 

Om din app inte är i rätt prisavtal, kan du skala för att en högre prisplanen valfri i Användargränssnittet.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Aktiverar VNet-integrering med befintliga virtuella nätverk
Gränssnittet för VNet-integrering kan du välja från en lista över dina virtuella nätverk. Klassiska virtuella nätverk tyda på att de är, till exempel med ordet ”klassisk” inom parentes bredvid virtuella nätverkets namn. Listan sorteras så att Resource Manager-VNets visas först. I bilden nedan ser du att endast ett virtuellt nätverk kan väljas. Det finns flera orsaker till att ett virtuellt nätverk kan vara nedtonad, inklusive:

* det virtuella nätverket är i en annan prenumeration som ditt konto har åtkomst till
* det virtuella nätverket har inte aktiverats punkt-till-plats
* det virtuella nätverket har inte en dynamisk routningsgateway

![][2]

Om du vill aktivera integrering, klickar du på det virtuella nätverket som du vill integrera med. När du har valt det virtuella nätverket startas automatiskt din app för att ändringarna ska börja gälla. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Aktivera punkt-till-plats i ett klassiskt virtuellt nätverk

Om ditt virtuella nätverk har punkt-till-plats eller inte har en gateway, måste du ange detta första. Konfigurera en gateway för ett klassiskt virtuellt nätverk, gå till portalen och ta fram listan över virtuella Networks(classic). Välj det nätverk som du vill integrera med. Välj VPN-anslutningar. Härifrån kan skapa du din point site VPN och även skapa en gateway. Gatewayen tar ungefär 30 minuter att få fram.

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Aktivera punkt-till-plats i en Resource Manager-nätverk
Om du vill konfigurera ett Resource Manager-VNet med en gateway och en punkt-till-plats, du kan använda antingen PowerShell enligt beskrivningen här [konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av PowerShell] [ V2VNETP2S] eller använda Azure portal enligt beskrivningen här [konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med Azure-portalen][V2VNETPortal]. Gränssnittet för att utföra den här funktionen är inte tillgänglig ännu. Du behöver inte skapa certifikat för punkt-till-plats-konfiguration. Certifikat skapas automatiskt när du ansluter din WebApp till det virtuella nätverket med hjälp av portalen. 

### <a name="creating-a-pre-configured-vnet"></a>Skapa en förkonfigurerad VNet
Om du vill skapa ett nytt virtuellt nätverk som har konfigurerats med en gateway och punkt-till-plats har möjlighet att göra det, men endast för en Resource Manager-VNet med App Service-nätverk Användargränssnittet. Om du vill skapa ett klassiskt virtuellt nätverk med en gateway och en punkt-till-plats måste du göra detta manuellt via användargränssnittet för nätverk. 

För att skapa en Resource Manager-VNet via VNet-integrering Användargränssnittet, Välj **Skapa nytt virtuellt nätverk** och ge den:

* Namn på virtuellt nätverk
* Adressblock för virtuellt nätverk
* Undernätsnamn
* Adressbok för undernät
* Adressblock för gateway
* Adressblock för punkt-till-plats

Om du vill att den här virtuella nätverket kan ansluta till andra nätverk, bör du undvika välja IP-adressutrymmen som överlappar med dessa nätverk. 

> [!NOTE]
> Resource Manager-VNet har skapats med en gateway tar cirka 30 minuter och för närvarande integrerar inte det virtuella nätverket med din app. När ditt VNet skapas med en gateway, måste du gå tillbaka till din app UI för VNet-integrering och välj ditt nya VNet.
> 
> 

![][3]

Virtuella Azure-nätverk skapas vanligtvis inom adresser i det privata nätverket. VNet-integrering som standard dirigerar funktionen all trafik som är avsedd för dessa IP-adressintervall i ditt virtuella nätverk. De privata IP-adressintervall är:

* 10.0.0.0/8 – det här är samma som 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 – det här är samma som 172.16.0.0 - 172.31.255.255 
* 192.168.0.0/16 – det här är samma som 192.168.0.0 - 192.168.255.255

VNet-adressutrymmet måste anges i CIDR-notation. Om du är bekant med CIDR-notation, är det en metod för att ange-Adressblock med en IP-adress och ett heltal som representerar nätverksmasken. Överväg att 10.1.0.0/24 är 256 adresser och 10.1.0.0/25 är 128 adresser som en snabbreferens. En IPv4-adress med en /32 är bara 1 adress. 

Om du anger DNS-serverinformation här har som angetts för det virtuella nätverket. Du kan redigera den här informationen från användarupplevelser VNet när VNet har skapats. Om du ändrar DNS för det virtuella nätverket, måste du utföra en åtgärd för synkronisera nätverk.

När du skapar ett klassiskt virtuellt nätverk med hjälp av Användargränssnittet för VNet-integrering, skapar ett virtuellt nätverk i samma resursgrupp som din app. 

## <a name="how-the-system-works"></a>Hur systemet fungerar
Under försättsbladen bygger den här funktionen på punkt-till-plats-VPN-teknik för att ansluta din app till ditt VNet. Appar i Azure App Service har en arkitektur för system med flera innehavare, vilket utesluter etablering av en app direkt i ett virtuellt nätverk som görs med virtuella datorer. Vi begränsa nätverksåtkomsten till bara den virtuella datorn som är värd för programmet som bygger på teknik för punkt-till-plats. Åtkomst till nätverket är också begränsad på app-värdarna så att dina appar kan bara komma åt de nätverk som du konfigurerar dem att komma åt. 

![][4]

Om du inte har konfigurerat en DNS-server med det virtuella nätverket, måste din app att använda IP-adresser för att nå resursen i det virtuella nätverket. Kom ihåg att stor fördel med den här funktionen är att den kan du använda privata adresser i ditt privata nätverk när du använder IP-adresser. Om du ställer in din app använda offentliga IP-adresser för en av dina virtuella datorer och du inte använder funktionen VNet-integrering och kommunicerar via internet.

## <a name="managing-the-vnet-integrations"></a>Hantera VNet-integrering
Möjligheten att ansluta och koppla till ett virtuellt nätverk är en app-nivå. Åtgärder som kan påverka VNet-integrering i flera appar är en ASP-nivå. I användargränssnittet som visas på appnivå kan du få information om ditt virtuella nätverk. De flesta av samma information visas också på ASP-nivå. 

![][5]

Du kan se om din app är ansluten till ditt virtuella nätverk från sidan Nätverksfunktionsstatus. Om din VNet-gateway är igång oavsett orsak, skulle det här visas som inte ansluten. 

Den information som du har nu tillgängliga för dig i appen på VNet-integrering Användargränssnittet är samma som detaljerad information som du får från ASP. Här är de objekt:

* Namn på virtuellt nätverk – den här länken öppnas Azure-nätverket UI
* Plats – detta visar platsen för ditt virtuella nätverk. Det är möjligt att integrera med ett virtuellt nätverk i en annan plats.
* Certifikatstatus - är det certifikat som används för att säkra VPN-anslutning mellan appen och det virtuella nätverket. Detta visar ett test för att säkerställa att de är synkroniserade.
* Status för gateway - bör din gateway inte är tillgänglig av någon anledning sedan din app inte åtkomst till resurser i det virtuella nätverket. 
* Adressutrymme för virtuellt nätverk – det här är IP-adressutrymmet för det virtuella nätverket. 
* Adressutrymme för punkt-till-plats – det är nu till platsen IP-adressutrymme för ditt virtuella nätverk. Din app visar kommunikation som kommer från en av IP-adresser i det här adressutrymmet. 
* Plats till plats adressutrymmet - du kan använda VPN för plats till plats för att ansluta ditt VNet till dina lokala resurser eller till andra virtuella nätverket. Du om har som konfigurerats och sedan IP-adressintervall som definierats med att VPN-anslutningen visar här.
* DNS-servrar – om du har DNS-servrar som konfigurerats med ditt VNet, de listas här.
* IP-adresser cirkulerade till virtuellt nätverk – där finns en lista över IP-adresser som ditt virtuella nätverk har definierats för-Routning och de adresser som visas här. 

Den enda åtgärden som du kan vidta i app-vy av dina VNet-integrering är att din app kopplas bort från det virtuella nätverket som den är ansluten till. Koppla från högst upp om du vill koppla från din app från ett virtuellt nätverk. Den här åtgärden ändras inte ditt virtuella nätverk. Det virtuella nätverket och dess konfiguration, inklusive gateway förblir oförändrat. Om du sedan vill ta bort det virtuella nätverket måste du först ta bort resurser i den, inklusive gateway. 

Visa App Service-Plan har ett antal ytterligare åtgärder. Det är också åt annorlunda än från appen. Öppna ASP-Användargränssnittet och rulla ned för att nå ASP nätverk Användargränssnittet. Välj ”Nätverksfunktionsstatus” öppna nätverk funktionen Status Användargränssnittet. Välj ”Klicka här om du vill hantera” för att lista VNet-integreringar i den här ASP.

![][6]

Platsen för ASP är bra att tänka på när du tittar på platserna för de virtuella nätverken du integrerar med. När det virtuella nätverket finns i en annan plats, du mycket mer troligt att problem med nätverkssvarstiden. 

De virtuella nätverken integrerat med är en påminnelse om hur många virtuella nätverk som dina appar är integrerade med i den här ASP och hur många kan du använda. 

Om du vill visa har lagts till information om varje virtuellt nätverk, klicka bara på det virtuella nätverket som du är intresserad av. Förutom den information som har beskrivits tidigare, kan du också se en lista över appar i den här ASP som använder det virtuella nätverket. 

Det finns två primära åtgärder avseende åtgärder. Först är möjligheten att lägga till vägar som driver trafik som lämnar appen i ditt virtuella nätverk. Den andra åtgärden som är möjligheten att synkronisera certifikat och nätverksinformation.

![][7]

**Routning** som tidigare nämnts vägarna som definieras i ditt virtuella nätverk är vad som används för att dirigera trafik till ditt virtuella nätverk från din app. Det finns några användningsområden men där kunderna vill skicka ytterligare utgående trafik från en app till det virtuella nätverket och för dem med den här funktionen tillhandahålls. Vad händer med trafiken när det är upp till hur kunden konfigurerar sina virtuella nätverk. 

**Certifikat** The certifikatstatus återspeglar en kontroll som utförs av den App Service för att verifiera att de certifikat som vi använder för VPN-anslutningen är fortfarande bra. När VNet-integrering aktiverat om det här är den första integreringen till det virtuella nätverket från alla appar i den här ASP är det ett obligatoriskt utbyte av certifikat för att säkerställa säkerheten för anslutningen. Tillsammans med certifikat som hämta vi DNS-konfigurationen, vägar och andra liknande element som beskrivs i nätverket.
Om dessa certifikat eller information om nätverk ändras måste du klicka på ”Synkronisera nätverk”. **Obs**: när du klickar på ”Synkronisera nätverk” och sedan du orsakar ett kort avbrott i anslutningen mellan din app och ditt virtuella nätverk. När din app inte startas om, leda förlust av anslutning till din webbplats inte fungerar korrekt. 

## <a name="accessing-on-premises-resources"></a>Åtkomst till lokala resurser
En av fördelarna med funktionen för VNet-integrering är som om ditt virtuella nätverk är anslutet till ditt lokala nätverk med VPN för plats till plats och sedan dina appar kan ha åtkomst till lokala resurser från din app. Intervall för det här ska fungera även om du kan behöva uppdatera din lokala VPN-gateway med vägar för punkt-till-plats-IP. När VPN för plats till plats först ställer in bör de skript som används för att konfigurera den ställa in vägar, inklusive punkt-till-plats-VPN. Om du lägger till punkt-till-plats-VPN när du har skapat din plats till plats-VPN, måste du uppdatera vägar manuellt. Information om hur du gör variera per gateway och beskrivs inte här. 

> [!NOTE]
> VNet-integrationsfunktionen integreras inte en app med ett virtuellt nätverk som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har konfigurerats i [samexistens läge] [ VPNERCoex] VNet-Integration fungerar inte. Om du behöver komma åt resurser via en ExpressRoute-anslutning så kan du använda en [App Service Environment][ASE], som körs i ditt virtuella nätverk.
> 
> 

## <a name="pricing-details"></a>Prisinformation
Det finns några priser olika delarna som du bör känna till när du använder funktionen för VNet-integrering. Det finns 3 relaterade avgifter för användning av den här funktionen:

* ASP priser nivå krav
* Kostnader för överföring av data
* Kostnader för VPN-Gateway.

De måste vara i Standard- eller Premium App Service-Plan för dina appar för att kunna använda den här funktionen. Du kan få mer information om dessa kostnader här: [priser för Apptjänst][ASPricing]. 

På grund av punkt-till-plats VPN hantera, har du alltid en avgift för utgående data via VNet-integrering-anslutning även om det virtuella nätverket är i samma datacenter. Om du vill se dessa ändringar är att ta en titt här: [Data Transfer prisinformation om][DataPricing]. 

Det sista objektet är kostnaden för VNet-gateway. Om du inte behöver gateway för något annat, t.ex VPN för plats till plats, du betalar för gatewayer stöd för VNet-integrering-funktionen. Det finns information om dessa kostnader här: [prissättning för VPN Gateway][VNETPricing]. 

## <a name="troubleshooting"></a>Felsökning
När funktionen är enkla att konfigurera det betyder inte att problemet kostnadsfria blir din upplevelse. Bör du stöter på är problem med åtkomst till din önskade slutpunkt vissa verktyg som du kan använda för att testa anslutningen från app-konsolen. Det finns två konsolen upplevelser du kan använda. En från Kudu-konsolen och den andra är den konsol som du kan nå dem i Azure-portalen. Gå till verktyg för att komma till Kudu-konsolen från din app -> Kudu. Det här är samma som kommer att [sitename]. scm.azurewebsites.net. När som öppnas, går du till fliken för felsökning av konsolen. Gå till verktyg för att få till Azure portal-värdbaserade konsolen sedan från din app -> konsolen. 

#### <a name="tools"></a>Verktyg
Verktygen **ping**, **nslookup** och **tracert** fungerar inte via konsolen på grund av säkerhetsbegränsningar. Om du vill fylla void där har två olika verktyg som har lagts till. Vi lagt till ett verktyg med namnet nameresolver.exe för att testa DNS-funktioner. Syntax:

    nameresolver.exe hostname [optional: DNS Server]

Du kan använda **nameresolver** att kontrollera värdnamn som din app är beroende av. På så sätt kan du testa om du har något stavades konfigurerats med din DNS eller kanske inte har tillgång till din DNS-server.

Nästa verktyget kan du testa TCP-anslutning till en värd och port-kombination. Det här verktyget kallas **tcpping** och syntaxen är:

    tcpping.exe hostname [optional: port]

Den **tcpping** verktyget berättar om du når en viss värd och port. Det kan bara visa lyckades om: det finns ett program som lyssnar på en värd och port-kombination det finns nätverksåtkomst från din app till den angivna värd och port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Felsöka åtkomst till VNet värdbaserade resurser
Det finns ett antal saker som kan förhindra att din app når en viss värd och port. I de flesta fall är det en av tre saker:

* **Det finns en brandvägg på sätt** om du har en brandvägg på sätt når du TCP-tidsgräns. Det är 21 sekunder i det här fallet. Använd den **tcpping** verktyg för att testa anslutningen. TCP-tidsgränser kan bero på många saker utanför brandväggar men börjar där. 
* **DNS är inte tillgänglig** DNS-timeout-värdet är tre sekunder per DNS-server. Om du har två DNS-servrar, är tidsgränsen 6 sekunder. Använda nameresolver för att se om DNS fungerar. Kom ihåg att du inte kan använda nslookup som som inte använder ditt VNet är konfigurerat med DNS.
* **Ogiltigt P2S IP-adressintervall** punkt till plats IP-intervall måste finnas i RFC 1918 privata IP-intervall (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Om intervallet använder IP-adresser utanför som kan sedan fungerar saker inte. 

Om ditt problem inte svara på objekten, leta efter de enkla saker som: 

* Visar gatewayen som tas upp i portalen?
* Visar certifikat är synkroniserat?
* Vem som helst ändra nätverkskonfigurationen för utan att behöva göra ett ”Sync nätverk” i de berörda ASP? 

Om din gateway är igång kan sedan konfigurera det igen. Om dina certifikat är inte synkroniserade, gå till ASP-vy av dina VNet-integrering och tryck på ”Synkronisera nätverk”. Om du misstänker att det har gjorts en ändring som gjorts i din konfiguration av virtuellt nätverk och det inte att synkronisera skulle gjort med din ASP går du till vyn ASP för VNet-integrering och tryck på ”Synkronisera nätverk” precis som en påminnelse, detta orsakar ett kort avbrott med din VNet-anslutning och dina appar. 

Om alla som är bra, måste du gå lite djupare:

* Finns det några andra appar som använder VNet-integrering för att nå resurser i samma virtuella nätverk? 
* Kan du gå till app-konsolen och använda tcpping för att nå andra resurser i ditt virtuella nätverk? 

Om inget av ovanstående stämmer sedan din VNet-integrering är bra och problemet är någon annanstans. Det här är där den får vara av en utmaning eftersom det finns inget enkelt sätt att se varför du inte kan nå en värd: port. Några av orsakerna är:

* du har en brandvägg på din värd neka åtkomst till programporten från din punkt till plats IP-adressintervall. Ofta passerande undernät kräver offentlig åtkomst.
* målvärddatorn är inte tillgänglig
* ditt program är nere
* du har fel IP-Adressen eller värdnamnet
* programmet lyssnar på en annan port än vad du förväntade dig. Du kan kontrollera detta genom att gå till värden och med hjälp av ”netstat - aon” från Kommandotolken. Detta visar vilken process som ID lyssnar på vilken port. 
* dina nätverkssäkerhetsgrupper är konfigurerade på ett sådant sätt att de förhindrar åtkomst till programmet värddator och port från din plats till plats IP-adressintervall

Kom ihåg att du inte vet vilka IP i ditt punkt-till-plats-IP-adressintervall som din app ska använda så att du behöver att tillåta åtkomst från hela intervallet. 

Ytterligare felsökning stegen omfattar:

* Anslut till en virtuell dator i ditt virtuella nätverk och som försöker komma åt din resurs värd: port därifrån. För att testa för TCP åtkomst använda PowerShell-kommandot **test-netconnection**. Syntax:

      test-netconnection hostname [optional: -Port]

* Ta fram ett program på en virtuell dator och testa åtkomst till denna värd och port från konsolen från din app

#### <a name="on-premises-resources"></a>Lokala resurser ####

Om din app inte kan nå en resurs på plats, kontrollerar du om du når resursen från ditt virtuella nätverk. Använd den **test-netconnection** PowerShell-kommando för att göra detta. Om den virtuella datorn inte kan nå dina lokala resurser, se till att din plats till plats-VPN-anslutning fungerar. Om det fungerar, kontrollerar du samma saker som beskrivits tidigare samt lokala gateway-konfiguration och status. 

Om ditt virtuella nätverk finns VM kan nå den lokala datorn, men din app kan orsaken är förmodligen en av följande:

* vägarna har inte konfigurerats med din punkt till plats IP-adressintervall i en lokal gateway
* dina nätverkssäkerhetsgrupper blockerar åtkomst för punkt-till-plats-IP-adressintervall
* din lokala brandväggar blockerar trafik från ditt punkt-till-plats-IP-adressintervall
* du har en användare definieras väg (udr) i ditt virtuella nätverk som förhindrar baserade punkt-till-plats-trafik från att nå det lokala nätverket

## <a name="powershell-automation"></a>PowerShell-automation

Du kan integrera App Service med Azure Virtual Network med hjälp av PowerShell. Ett är klara att köra skript, se [anslutit en app i Azure App Service till ett Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Hybridanslutningar och App Service-miljöer
Det finns tre funktioner som ger åtkomst till virtuella nätverk finns resurser. De är:

* VNET-integration
* Hybridanslutningar
* Apptjänstmiljöer

Hybridanslutningar måste du installera en relay agent kallas Hybrid anslutning Manager(HCM) i nätverket. Den HCM-system måste kunna ansluta till Azure och i ditt program. Den här lösningen är särskilt bra från ett fjärrnätverk, till exempel ditt lokala nätverk eller till och med ett annat moln värd nätverk eftersom det inte kräver en internet-tillgänglig slutpunkt. HCM körs bara på Windows och du kan ha upp till fem instanser som körs för att tillhandahålla hög tillgänglighet. Hybridanslutningar stöder endast TCP om och varje HC-slutpunkten måste matcha till en viss värd: port-kombination. 

App Service Environment-funktionen kan du köra en instans av Azure App Service i ditt virtuella nätverk. På så sätt kan dina appar åtkomst till resurser i ditt virtuella nätverk utan några extra steg. Vissa av de andra fördelarna med en App Service Environment är att du kan använda Dv2 baserat arbetare med upp till 14 GB RAM. En annan fördel är att du kan skala systemet efter dina behov. I en ASE till skillnad från flera innehavare miljöer där ASP är begränsat till 20 instanser, kan du skala upp till 100 ASP-instanser. En av de saker som du får med en ASE som du inte med VNet-integrering är att en App Service Environment kan arbeta med en ExpressRoute VPN. 

Det finns vissa använder fallet överlappar varandra, kan ingen av dessa funktioner ersätta någon av de andra. Att veta vilken funktion du ska använda är knuten till dina behov. Exempel:

* Om du är utvecklare och vill köra en plats i Azure och har åtkomst till databasen på arbetsstationen under skrivbordet, är det enklast att använda Hybridanslutningar. 
* Om du är en stor organisation som vill placera ett stort antal webbegenskaper i offentligt molnet och hantera dem i egna nätverk, så du vill gå med App Service Environment. 
* Om du har flera appar som behöver komma åt resurser i ditt virtuella nätverk är det bättre att i VNet-integrering. 

Om ditt virtuella nätverk är redan ansluten till ditt lokala nätverk, är sedan med hjälp av VNet-integrering eller en App Service Environment ett enkelt sätt att använda lokala resurser. Om ditt virtuella nätverk inte är ansluten till ditt lokala nätverk, är det mycket mer kostnader för att konfigurera VPN för plats till plats med det virtuella nätverket jämfört med att installera den HCM-system. 

Utöver de funktionella skillnaderna det också prisskillnaderna. App Service Environment-funktionen är en Premium tjänsterbjudande men erbjuder mest network configuration möjligheter utöver andra fantastiska funktioner. VNet-integrering kan användas med Standard eller Premium ASP och är perfekt för att på ett säkert sätt förbrukar resurser i ditt virtuella nätverk från App Service med flera innehavare. Hybridanslutningar är för närvarande beror på en BizTalk-konto, som har prissättning nivåer som börja kostnadsfritt och få progressivt dyrare baserat på den datamängd som du behöver. När det gäller att arbeta i många nätverk men finns det inga andra funktionen som Hybridanslutningar som gör det möjligt att komma åt resurser i och över 100 separata nätverk. 

## <a name="new-vnet-integration"></a>Nya VNet-integrering ##

Det finns en ny version av VNet-integrering-funktion som inte är beroende av punkt-till-plats VPN-teknik. Den här nya versionen finns i förhandsversion. Den nya funktionen för VNet-integrering har följande egenskaper.

- Den nya funktionen kräver en oanvända undernät i ditt VNet i Resource Manager
- En adress används för varje instans för App Service-plan. Eftersom undernätets storlek inte kan ändras efter tilldelning, Använd ett undernät som kan mer än att täcka storleken på din maximal skala. En/27 med 32 adresser är den rekommenderade storleken som som vill hantera en App Service-plan som skalas till 20 instanser.
- Du kan använda tjänstslutpunkt skyddade resurser med hjälp av den nya funktionen för VNet-integrering. Aktivera åtkomst från undernätet på din app tilldelats att konfigurera tjänstslutpunkter med din app
- Du kan komma åt resurser över ExpressRoute-anslutningar utan någon ytterligare konfiguration
- Ingen gateway krävs för att använda den nya funktionen för VNet-integrering
- App Service-planen måste vara en Standard, Premium eller PremiumV2-plan
- Den nya funktionen är endast tillgänglig från nyare Azure App Service-skalningsenheter. Portalen meddelar dig om din app kan använda den nya funktionen för VNet-integrering. 
- Appen och det virtuella nätverket måste finnas i samma region

Den nya funktionen för VNet-integrering är inledningsvis endast tillgänglig i regionerna Europa, norra och USA, östra.


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
