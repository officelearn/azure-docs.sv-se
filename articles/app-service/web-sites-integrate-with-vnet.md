---
title: Integrera app med Azure virtuellt nätverk – Azure App Service
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
ms.date: 11/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 265dcccf9202d7b0116bba05b016e8967b68c67a
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273365"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med Azure-nätverk
Det här dokumentet beskriver funktionen Azure App Service-integrering för virtuellt nätverk och visar hur du konfigurerar den med appar i [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Azure-nätverk] [ VNETOverview] (Vnet) gör att du kan placera många av dina Azure-resurser i ett icke-internet-dirigerbara nätverk. Dessa nätverk kan sedan anslutas till ditt lokala nätverk med hjälp av VPN-tekniker. 

Azure App Service har två typer. 

1. Flera innehavare-system som har stöd för en fullständig uppsättning prissättningsplaner utom isolerad
2. I App Service Environment (ASE), som distribuerar i ditt virtuella nätverk. 

Det här dokumentet går igenom funktionen VNet-integrering som är avsedd för användning i App Service med flera innehavare.  Om appen är i [App Service Environment][ASEintro], och sedan den redan är i ett virtuellt nätverk och kräver inte användning av funktionen VNet-integrering för att nå resurser i samma virtuella nätverk.

VNet-integrering ger dina webbprogram åtkomst till resurser i det virtuella nätverket men bevilja inte privat åtkomst till din webbapp från det virtuella nätverket. Åtkomst till privata webbplatsen refererar till att appen endast kan nås från ett privat nätverk som från inom en Azure-nätverk. Privat plats åtkomsten är endast tillgänglig med en ASE som konfigurerats med en intern belastningsutjämnaren (ILB). Mer information om hur du använder en ILB ASE, börjar du med den här artikeln: [Skapa och använda en ILB ASE][ILBASE]. 

VNet-integrering används ofta för att aktivera åtkomst från appar till en databaser och webbtjänster som körs i ditt virtuella nätverk. Med VNet-integrering behöver du inte Exponerar en offentlig slutpunkt för program på den virtuella datorn men kan använda privata adresser för andra program än internet-dirigerbara i stället. 

Funktionen VNet-integrering:

* kräver en Standard, Premium eller PremiumV2 prisplanen 
* fungerar med klassisk eller Resource Manager-VNet 
* stöder TCP och UDP
* fungerar med webb-, mobil, API-appar, och funktionsappar
* gör att en app för att ansluta till endast 1 virtuellt nätverk på en gång
* gör att upp till fem virtuella nätverk kan integreras med i en App Service Plan 
* tillåter samma virtuella nätverk som ska användas av flera appar i en App Service Plan
* kräver en virtuell nätverksgateway som är konfigurerad med punkt till plats-VPN
* har stöd för ett serviceavtal på 99,9% på grund av serviceavtalet på gatewayen

Det finns några saker som VNet-integrering har inte stöd för bland annat:

* montera en enhet
* AD-integrering 
* NetBios
* privat Platsåtkomst
* åtkomst till resurser över ExpressRoute 
* åtkomst till resurser över tjänstslutpunkter 

### <a name="getting-started"></a>Komma igång
Här följer några saker att tänka på innan du ansluter din webbapp till ett virtuellt nätverk:

* Virtuellt Målnätverk måste ha punkt-till-plats-VPN aktiveras med en routningsbaserad gateway innan den kan anslutas till appen. 
* Det virtuella nätverket måste finnas i samma prenumeration som din App Service-Plan(ASP).
* Appar som integreras med ett virtuellt nätverk använder DNS som har angetts för det virtuella nätverket.

## <a name="enabling-vnet-integration"></a>Aktiverar VNet-integrering

Det finns en ny version av funktionen för VNet-integrering som finns i en förhandsversion. Den beroende av inte punkt-till-plats VPN-anslutning och stöder också åtkomst till resurser över ExpressRoute eller Tjänsteslutpunkter. Mer information om den nya funktionen för förhandsgranskning, går du till slutet av det här dokumentet. 

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurera en gateway i ditt virtuella nätverk ###

Om du redan har en gateway som konfigurerats med punkt-till-plats-adresser kan du hoppa över att konfigurera VNet-integrering med din app.  
Skapa en gateway:

1. [Skapa ett gatewayundernät] [ creategatewaysubnet] i ditt virtuella nätverk.  

1. [Skapa VPN-gatewayen][creategateway]. Välj en ruttbaserad VPN-typ.

1. [För plats-adresser][setp2saddresses]. Om gatewayen inte är på grundläggande nivå, sedan IKEV2 måste inaktiveras i punkt-till-plats-konfiguration och SSTP måste väljas. Adressutrymmet måste vara i något av följande-Adressblock:

* 10.0.0.0/8 – detta innebär att ett IP-adressintervall från 10.0.0.0 till 10.255.255.255
* 172.16.0.0/12 – detta innebär att ett IP-adressintervall från 172.16.0.0 till 172.31.255.255 
* 192.168.0.0/16 – detta innebär att ett IP-adressintervall från 192.168.0.0 till 192.168.255.255

Om din är bara skapa gatewayen för användning med VNet-integrering för App Service och du inte behöver ladda upp ett certifikat. Kan ta 30 minuter att skapa gatewayen. Du kommer inte att kunna integrera din app med det virtuella nätverket förrän gatewayen är upprättad. 

### <a name="configure-vnet-integration-with-your-app"></a>Konfigurera VNet-integrering med din app ###

Så här aktiverar du VNet-integrering i din app: 

1. Gå till din app i Azure-portalen och öppna appen inställningar och välj nätverk > VNet-integrering. Skala ASP till en Standard-SKU eller bättre innan du kan konfigurera VNet-integrering. 
 ![VNet-integrering UI][1]

1. Välj **lägga till VNet**. 
 ![Lägg till VNet-integrering][2]

1. Välj ditt virtuella nätverk. 
  ![Välj ditt virtuella nätverk][8]
  
Din app kommer att startas om efter det sista steget.  

## <a name="how-the-system-works"></a>Hur systemet fungerar
VNet-integrering-funktionen är byggt på punkt-till-plats VPN-teknik. Appar i Azure App Service finns i ett system med flera innehavare, vilket utesluter etablering av en app direkt i ett virtuellt nätverk. Punkt-till-plats-teknik begränsar åtkomst till bara den virtuella datorn som är värd för appen. Appar är begränsade till bara skickar trafik till internet, via Hybridanslutningar eller VNet-integrering. 

![Så här fungerar VNet-integrering][3]

## <a name="managing-the-vnet-integrations"></a>Hantera VNet-integrering
Möjligheten att ansluta och koppla till ett virtuellt nätverk är en app-nivå. Åtgärder som kan påverka VNet-integrering i flera appar är på nivån för App Service-plan. Från appen UID, kan du få information om ditt virtuella nätverk. Samma information visas också på ASP-nivå. 

 ![Information om virtuellt nätverk][4]

Du kan se om din app är ansluten till ditt virtuella nätverk från sidan Nätverksfunktionsstatus. Om din VNet-gateway är igång oavsett orsak kan visas din status som inte ansluten. 

Den information som du har nu tillgängliga för dig i appen på VNet-integrering Användargränssnittet är samma som detaljerad information som du får från ASP. Här är de objekt:

* Namn på virtuellt nätverk - länkar till det virtuella nätverket UI
* Plats – visar platsen för ditt virtuella nätverk. Integrera med ett virtuellt nätverk i en annan plats kan det orsaka problem med nätverkssvarstiden för din app. 
* Certifikatsstatus – visar om dina certifikat är synkroniserade mellan din App Service-plan och ditt virtuella nätverk.
* Status för gateway - bör din gateway inte är tillgänglig av någon anledning sedan din app inte åtkomst till resurser i det virtuella nätverket. 
* VNet-adressutrymmet - visar IP-adressutrymmet för det virtuella nätverket. 
* Adressutrymme för punkt-till-plats - visar punkten till plats IP-adressutrymme för ditt virtuella nätverk. Att göra anrop till ditt virtuella nätverk, vara din app FROM-adressen någon av dessa adresser. 
* Adressutrymme för plats-till-plats – du kan använda plats-till-plats-VPN: er för att ansluta ditt VNet till dina lokala resurser eller till andra virtuella nätverket. IP-adressintervall som definierats med VPN-anslutningen visas här.
* DNS-servrar – visar DNS-servrar som konfigurerats med ditt virtuella nätverk.
* IP-adresser cirkulerade till virtuellt nätverk – visar-Adressblock dirigeras används på enheten trafik i ditt virtuella nätverk 

Den enda åtgärden som du kan vidta i app-vy av dina VNet-integrering är att din app kopplas bort från det virtuella nätverket som den är ansluten till. Om du vill koppla från din app från ett virtuellt nätverk, Välj **Disconnect**. Din app kommer att startas om när du kopplar från ett virtuellt nätverk. Kopplar från ändras inte ditt virtuella nätverk. Det virtuella nätverket och dess konfiguration, inklusive gateway förblir oförändrat. Om du sedan vill ta bort det virtuella nätverket måste du först ta bort resurser i den, inklusive gateway. 

Öppna ASP-Användargränssnittet för att nå ASP VNet-integrering Användargränssnittet, och välj **nätverk**.  Välj under VNet-integrering, **Klicka här om du vill konfigurera** att öppna Gränssnittet nätverk funktionen Status.

![Information för ASP-VNet-integrering][5]

ASP VNet-integrering Användargränssnittet visas alla de virtuella nätverken som används av appar i ASP. Du kan ha upp till 5 virtuella nätverk som är anslutna till obegränsat antal appar i App Service-planen. Varje app kan ha endast en integration som har konfigurerats. Om du vill visa information om varje virtuellt nätverk, klickar du på det virtuella nätverket som du är intresserad av. Det finns två åtgärder som du kan utföra här.

* **Synkronisera nätverk**. Synkroniseringsåtgärden för nätverk ser till att ditt certifikat och nätverksinformation är synkroniserade. Om du lägger till eller ändra DNS för ditt VNet, måste du utföra en **synkronisera nätverk** igen. Den här åtgärden startar om alla appar som använder det här virtuella nätverket.
* **Lägga till vägar** lägga till vägar kommer att öka utgående trafik i ditt virtuella nätverk.

**Routning** vägarna som definieras i ditt virtuella nätverk som används för att dirigera trafik till ditt virtuella nätverk från din app. Om du vill skicka ytterligare utgående trafik till det virtuella nätverket kan du lägga till dessa Adressblock.   

**Certifikat** när VNet-integrering aktiverat, det är ett obligatoriskt utbyte av certifikat för att säkerställa säkerheten för anslutningen. Tillsammans med certifikat som är DNS-konfigurationen, vägar och andra liknande element som beskrivs i nätverket.
Om certifikat eller information om nätverk ändras måste du klicka på ”Synkronisera nätverk”. När du klickar på ”Synkronisera nätverk” orsaka ett kort avbrott i anslutningen mellan din app och ditt virtuella nätverk. När din app inte startas om, leda förlust av anslutning till din webbplats inte fungerar korrekt. 

## <a name="accessing-on-premises-resources"></a>Åtkomst till lokala resurser
Appar kan komma åt lokala resurser genom att integrera med virtuella nätverk som har anslutningar för plats-till-plats. För att komma åt resurser lokalt, måste du uppdatera din lokala VPN-gateway vägar med din punkt-till-plats-Adressblock. När plats-till-plats-VPN är först ställer in, bör de skript som används för att konfigurera den ställa in vägar korrekt. Om du lägger till punkt-till-plats-adresser när du har skapat din plats-till-plats-VPN, måste du uppdatera vägar manuellt. Information om hur du gör variera per gateway och beskrivs inte här. Du kan dessutom inte BGP som konfigurerats med en plats-till-plats VPN-anslutning.

> [!NOTE]
> Funktionen VNet-integrering integrerar inte en app med ett virtuellt nätverk som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har konfigurerats i [samexistens läge] [ VPNERCoex] VNet-Integration fungerar inte. Om du behöver komma åt resurser via en ExpressRoute-anslutning så kan du använda en [App Service Environment][ASE], som körs i ditt virtuella nätverk. 
> 
> 

## <a name="peering"></a>Peering
Du kan använda VNet-integrering för att komma åt resurser i virtuella nätverk som peer-kopplade till det virtuella nätverket som du är ansluten till. Konfigurera peering för att arbeta med din app:

1. Lägg till en peeranslutning på det virtuella nätverket appen ansluter till. När du lägger till peering-anslutningen, aktivera **Tillåt åtkomst till virtuellt nätverk** och kontrollera **Tillåt vidarebefordrad trafik** och **Tillåt gatewayöverföring**.
1. Lägg till en peering-anslutningen på det virtuella nätverk som är att peer-kopplat till det virtuella nätverket som du är ansluten till. När du lägger till peering-anslutningen på målet VNet, aktivera **Tillåt åtkomst till virtuellt nätverk** och kontrollera **Tillåt vidarebefordrad trafik** och **Tillåt fjärrgateway**.
1. Gå till App Service-planen > nätverk > virtuellt nätverk Integration Användargränssnittet i portal.  Välj det virtuella nätverket som din app ansluter till. Lägg till adressintervallet för det virtuella nätverk som peer-kopplas med det virtuella nätverk som din app är ansluten till under avsnittet routning.  


## <a name="pricing-details"></a>Prisinformation
Det finns tre relaterade avgifter för användning av funktionen för VNet-integrering:

* ASP priser nivå krav
* Kostnader för överföring av data
* Kostnader för VPN-Gateway.

Dina appar måste vara i en Standard, Premium eller PremiumV2 App Service-Plan. Du kan se mer information på dessa kostnader här: [Prissättning för App Service][ASPricing]. 

Det finns en kostnad för datatrafik, även om det virtuella nätverket är i samma datacenter. Dessa ändringar är beskrivs i [Data Transfer prisinformation om][DataPricing]. 

Det finns en kostnad för VNet-gateway som krävs för punkt-till-plats-VPN. Informationen finns på den [prissättning för VPN Gateway] [ VNETPricing] sidan.

## <a name="troubleshooting"></a>Felsökning
När funktionen är enkla att konfigurera det betyder inte att problemet kostnadsfria blir din upplevelse. Bör du stöter på är problem med åtkomst till din önskade slutpunkt vissa verktyg som du kan använda för att testa anslutningen från app-konsolen. Det finns två konsoler som du kan använda. En är Kudu-konsolen och den andra är konsolen i Azure-portalen. För att nå Kudu-konsolen från din app, gå till Verktyg -> Kudu. Det här är samma som kommer att [sitename]. scm.azurewebsites.net. När som öppnas, går du till fliken för felsökning av konsolen. Gå till verktyg för att få till Azure portal-värdbaserade konsolen sedan från din app -> konsolen. 

#### <a name="tools"></a>Verktyg
Verktygen **ping**, **nslookup** och **tracert** fungerar inte via konsolen på grund av säkerhetsbegränsningar. Om du vill fylla annulleringen två separata verktyg som har lagts till. För att kunna testa DNS-funktioner, vi har lagt till ett verktyg som heter nameresolver.exe. Syntax:

    nameresolver.exe hostname [optional: DNS Server]

Du kan använda **nameresolver** att kontrollera värdnamn som din app är beroende av. På så sätt kan du testa om du har något stavades konfigurerats med din DNS eller kanske inte har tillgång till din DNS-server.

Nästa verktyget kan du testa TCP-anslutning till en värd och port-kombination. Det här verktyget kallas **tcpping** och syntaxen är:

    tcpping.exe hostname [optional: port]

Den **tcpping** verktyget berättar om du når en viss värd och port. Det kan bara visa lyckades om: det finns ett program som lyssnar på en värd och port-kombination det finns nätverksåtkomst från din app till den angivna värd och port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Felsöka åtkomst till VNet värdbaserade resurser
Det finns ett antal saker som kan förhindra att din app når en viss värd och port. I de flesta fall är det en av tre saker:

* **Det är en brandvägg i vägen.** Om du har en brandvägg på sätt överskrids TCP-tidsgräns. TCP-tidsgränsen är 21 sekunder i det här fallet. Använd den **tcpping** verktyg för att testa anslutningen. TCP-tidsgränser kan bero på många saker utanför brandväggar men börjar där. 
* **DNS är inte tillgänglig.** DNS-tidsgränsen är tre sekunder per DNS-server. Om du har två DNS-servrar, är tidsgränsen 6 sekunder. Använda nameresolver för att se om DNS fungerar. Kom ihåg att du inte kan använda nslookup som som inte använder ditt VNet är konfigurerat med DNS.
* **Adressintervallet för punkt-till-plats är ogiltig.** IP-adressintervallet för punkt-till-plats måste finnas i RFC 1918 privata IP-intervall (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Om intervallet använder IP-adresser utanför som kan sedan fungerar saker inte. 

Om ditt problem inte svara på objekten, leta efter de enkla saker som: 

* Visar gatewayen som tas upp i portalen?
* Visar certifikat är synkroniserat?
* Vem som helst ändra nätverkskonfigurationen för utan att behöva göra ett ”Sync nätverk” i de berörda ASP? 

Om din gateway är igång kan sedan konfigurera det igen. Om dina certifikat är inte synkroniserade, gå till ASP-vy av dina VNet-integrering och tryck på ”Synkronisera nätverk”. Om du misstänker att det har skett en ändring som gjorts i din konfiguration av virtuellt nätverk som inte har synkroniserats med din ASP, och tryck sedan på ”Synkronisera nätverk”.  En ”synkronisera nätverk”-åtgärden startar om alla appar i ASP som är integrerade med det virtuella nätverket. 

Om alla som är bra, måste du gå lite djupare:

* Finns det några andra appar som använder VNet-integrering för att nå resurser i samma virtuella nätverk? 
* Kan du gå till app-konsolen och använda tcpping för att nå andra resurser i ditt virtuella nätverk? 

Om inget av ovanstående stämmer sedan din VNet-integrering är bra och problemet är någon annanstans. Det här är där den får vara av en utmaning eftersom det finns inget enkelt sätt att se varför du inte kan nå en värd: port. Några av orsakerna är:

* du har en brandvägg på din värd neka åtkomst till programporten från din punkt till plats IP-adressintervall. Ofta passerande undernät kräver offentlig åtkomst.
* målvärddatorn är inte tillgänglig
* ditt program är nere
* du har fel IP-Adressen eller värdnamnet
* programmet lyssnar på en annan port än vad du förväntade dig. Du kan matcha process-ID med lyssningsporten med hjälp av ”netstat - aon” på slutpunkten värden. 
* dina nätverkssäkerhetsgrupper är konfigurerade på ett sådant sätt att de förhindrar åtkomst till programmet värddator och port från din plats till plats IP-adressintervall

Kom ihåg att du inte vet vilka IP i ditt punkt-till-plats-IP-adressintervall som din app ska använda så att du behöver att tillåta åtkomst från hela intervallet. 

Ytterligare felsökning stegen omfattar:

* Anslut till en virtuell dator i ditt virtuella nätverk och som försöker komma åt din resurs värd: port därifrån. Använd PowerShell-kommando för att testa för TCP-åtkomst, **test-netconnection**. Syntax:

      test-netconnection hostname [optional: -Port]

* Ta fram ett program på en virtuell dator och testa åtkomst till denna värd och port från konsolen från din app

#### <a name="on-premises-resources"></a>Lokala resurser ####

Om din app inte kan nå en resurs på plats, kontrollerar du om du når resursen från ditt virtuella nätverk. Använd den **test-netconnection** PowerShell-kommando för att kontrollera för TCP-åtkomst. Om den virtuella datorn inte kan nå dina lokala resurser, se till att din plats till plats-VPN-anslutning fungerar. Om det fungerar, kontrollerar du samma saker som beskrivits tidigare samt lokala gateway-konfiguration och status. 

Om ditt virtuella nätverk finns VM kan nå den lokala datorn men din app kan inte så beror det förmodligen en av följande orsaker:

* vägarna har inte konfigurerats med din punkt till plats IP-adressintervall i en lokal gateway
* dina nätverkssäkerhetsgrupper blockerar åtkomst för punkt-till-plats-IP-adressintervall
* din lokala brandväggar blockerar trafik från ditt punkt-till-plats-IP-adressintervall


## <a name="powershell-automation"></a>PowerShell-automation

Du kan integrera App Service med Azure Virtual Network med hjälp av PowerShell. Ett är klara att köra skript, se [anslutit en app i Azure App Service till ett Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Hybridanslutningar och App Service-miljöer
Det finns tre funktioner som ger åtkomst till virtuella nätverk finns resurser. De är:

* VNET-integration
* Hybridanslutningar
* Apptjänstmiljöer

Hybridanslutningar måste du installera en relay agent kallas Hybrid anslutning Manager(HCM) i nätverket. Den HCM-system måste kunna ansluta till Azure och i ditt program. Hybridanslutningar kräver inte en inkommande internet-tillgänglig slutpunkt för fjärrnätverk, som krävs för en VPN-anslutning. HCM körs bara på Windows och du kan ha upp till fem instanser som körs för att tillhandahålla hög tillgänglighet. Hybridanslutningar stöder endast TCP om och varje HC-slutpunkten måste matcha till en viss värd: port-kombination. 

App Service Environment-funktionen kan du köra en enda klient-instans i Azure App Service i ditt virtuella nätverk. Om dina appar finns i en App Service Environment, komma dina appar åt resurser i ditt virtuella nätverk utan några extra steg. Med och App Service Environment dina appar körs på mer kraftfulla arbetare och kan skalas upp till 100 ASP-instanser. App Service-miljöer fungerar med alla nätverksfunktioner, inklusive ExpressRoute och Tjänsteslutpunkter.  

Det finns vissa använder fallet överlappar varandra, kan ingen av dessa funktioner ersätta någon av de andra. Att veta vilken funktion du ska använda är knuten till dina behov. Exempel:

* Om du är utvecklare och vill köra en plats i Azure som har åtkomst till en databas på arbetsstationen under skrivbordet, är det enklast att använda Hybridanslutningar. 
* Om du är en stor organisation som vill placera ett stort antal webbegenskaper i offentligt molnet och hantera dem i egna nätverk, så du vill gå med App Service Environment. 
* Om du har flera appar som behöver komma åt resurser i ditt virtuella nätverk är det bättre att i VNet-integrering. 

När det virtuella nätverket är redan ansluten till ditt lokala nätverk med VNet-integrering eller en App Service Environment är ett enkelt sätt att använda lokala resurser. Om ditt virtuella nätverk inte är ansluten till ditt lokala nätverk, är det mycket mer kostnader för att konfigurera VPN för plats till plats med det virtuella nätverket jämfört med att installera den HCM-system. 

Utöver de funktionella skillnaderna det också prisskillnaderna. App Service Environment-funktionen är en Premium tjänsterbjudande men erbjuder mest network configuration möjligheter utöver andra fantastiska funktioner. VNet-integrering kan användas med Standard eller Premium ASP och är perfekt för att på ett säkert sätt förbrukar resurser i ditt virtuella nätverk från App Service med flera innehavare. Hybridanslutningar är för närvarande beror på en BizTalk-konto, som har prissättning nivåer som börja kostnadsfritt och få progressivt dyrare baserat på den datamängd som du behöver. När det gäller att arbeta i många nätverk men finns det inga andra funktionen som Hybridanslutningar som gör det möjligt att komma åt resurser i och över 100 separata nätverk. 

## <a name="new-vnet-integration"></a>Nya VNet-integrering ##

Det finns en ny version av VNet-integrering-funktion som inte är beroende punkt-till-plats VPN-teknik. Till skillnad från den befintliga funktionen fungerar ny förhandsgranskningsfunktion med ExpressRoute och Tjänsteslutpunkter. 

Den nya funktionen är endast tillgänglig från nyare Azure App Service-skalningsenheter. Om du kan skala till PremiumV2, finns i en nyare skalningsenhet för App Service. VNet-integrering Användargränssnittet i portal berätta om din app kan använda den nya funktionen för VNet-integrering. 

Den nya versionen är en förhandsversion och har följande egenskaper.

* Ingen gateway krävs för att använda den nya funktionen för VNet-integrering
* Du kan komma åt resurser över ExpressRoute-anslutningar utan någon ytterligare konfiguration utöver att integrera med ExpressRoute anslutna virtuella nätverk.
* Appen och det virtuella nätverket måste finnas i samma region
* Den nya funktionen kräver en oanvända undernät i ditt VNet i Resource Manager.
* App Service-planen måste vara en Standard, Premium eller PremiumV2-plan
* Produktionsarbetsbelastningar stöds inte på den nya funktionen när den är i förhandsversion
* Din app måste vara i en Azure App Service-distribution som kan skala upp till Premium v2.
* Den nya VNet-integrering-funktionen fungerar inte för appar i en App Service Environment.
* Du kan inte ta bort ett virtuellt nätverk med en integrerad app.  
* Routningstabeller och global peering är inte tillgängliga än med den nya VNet-integrering.  
* En adress används för varje instans för App Service-plan. Eftersom undernätets storlek inte kan ändras efter tilldelning, Använd ett undernät som kan mer än att täcka storleken på din maximal skala. En/27 med 32 adresser är den rekommenderade storleken som som vill hantera en App Service-plan som skalas till 20 instanser.  Du kan använda tjänstslutpunkt skyddade resurser med hjälp av den nya funktionen för VNet-integrering. Gör du genom att aktivera tjänstslutpunkter på det undernät som används för VNet-integrering.

Använda den nya funktionen:

1. Gå till nätverk Användargränssnittet i portal. Om din app kan använda den nya funktionen, ser du en funktion för att använda den nya förhandsgranskningsfunktionen.  

 ![Välj den nya förhandsversionen av VNet-integrering][6]

1. Välj **Lägg till virtuellt nätverk (förhandsversion)**.  

1. Välj Resource Manager-VNet som du vill integrera med och skapa sedan ett nytt undernät eller välj ett befintligt tomt undernät. Integrationen tar mindre än en minut att slutföra. Din app under integrationen har startats om.  När integration är klar visas information på det virtuella nätverket som du är integrerade med och en banderoll överst som talar om funktionen är i förhandsversion.

 ![Välj det virtuella nätverk och undernät][7]

Skapa programinställning för din app där namnet är WEBSITE_DNS_SERVER och värdet är IP-adressen för servern för att aktivera din app att använda DNS-servern som ditt virtuella nätverk har konfigurerats med.  Om du har en sekundär DNS-server, skapar du en annan programinställning där namnet är WEBSITE_DNS_ALT_SERVER och värdet är IP-adressen för servern. 

Om du vill koppla från din app från det virtuella nätverket, Välj **Disconnect**. Detta startar om din webbapp. 

Den nya funktionen för VNet-integrering kan du använda tjänstslutpunkter.  Använda den nya VNet-integrering för att använda Tjänsteslutpunkter med din app, att ansluta till en valda virtuella nätverket och sedan konfigurera tjänstslutpunkter på undernät som du använde för att integrationen. 

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
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
