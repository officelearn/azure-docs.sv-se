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
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a5187ed299f77c11892c6e34c8dfd3f904c7e075
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067716"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med Azure-nätverk
Det här dokumentet beskriver integreringsfunktionen för Azure App Service-virtuellt nätverk och hur du konfigurerar den med appar i den [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Azure-nätverk] [ VNETOverview] (Vnet) gör att du kan placera många av dina Azure-resurser i ett icke-internet-dirigerbara nätverk.  

Azure App Service har två typer. 

1. Flera innehavare-system som har stöd för en fullständig uppsättning prissättningsplaner utom isolerad
2. App Service Environment (ASE) som distribuerar i ditt virtuella nätverk och stöder isolerad prissättning plan appar

Det här dokumentet går igenom de två VNet-integrering-funktionerna, vilket är för användning i App Service med flera innehavare. Om appen är i [App Service Environment][ASEintro], och sedan den redan är i ett virtuellt nätverk och kräver inte användning av funktionen VNet-integrering för att nå resurser i samma virtuella nätverk. Mer information om alla nätverksfunktioner för App Service finns [App Service nätverksfunktioner](networking-features.md)

Det finns två sätt att funktionen VNet-integrering

1. En version möjliggör integrering med virtuella nätverk i samma region. Den här typen av funktionen kräver ett undernät i ett virtuellt nätverk i samma region. Den här funktionen är fortfarande i förhandsversion, men stöds för produktionsarbetsbelastningar för Windows-app med vissa varningar anges nedan.
2. Den andra versionen kan du integrera med virtuella nätverk i andra regioner eller klassiska virtuella nätverk. Den här versionen av funktionen kräver distribution av en virtuell nätverksgateway i ditt virtuella nätverk. Detta är punkt-till-plats VPN-baserad funktion.

En app kan bara använda en form av funktionen för VNet-integrering i taget. Frågan sedan är vilken funktion du ska du använda. Du kan använda antingen för många saker. Rensa skillnaderna är dock:

| Problem  | Lösning | 
|----------|----------|
| Vill nå en RFC 1918 adress (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) i samma region | regional VNet-integrering |
| Om du vill nå ett klassiskt virtuellt nätverk eller ett virtuellt nätverk i en annan region | Gateway krävs för VNet-integrering |
| Om du vill nå RFC 1918 slutpunkter via ExpressRoute | regional VNet-integrering |
| Om du vill nå resurser via tjänstslutpunkter | regional VNet-integrering |

Varken funktionen gör att du kan nå RFC 1918 adresser via ExpressRoute. Gör att du måste använda en ase-miljö för tillfället.

Med hjälp av regional VNet-integrering inte ansluta ditt VNet till lokalt och konfigurera tjänstslutpunkter. Det är separata nätverk konfiguration. Regional VNet-integrering kan helt enkelt din app för att göra anrop mellan dessa anslutningstyper.

Oavsett vilken version som används för VNet-integrering ger dina webbprogram åtkomst till resurser i ditt virtuella nätverk, men bevilja inte inkommande privat åtkomst till din webbapp från det virtuella nätverket. Åtkomst till privata webbplatsen refererar till att appen endast kan nås från ett privat nätverk som från inom en Azure-nätverk. VNet-integrering är endast för utgående samtal från din app till ditt virtuella nätverk. 

Funktionen VNet-integrering:

* kräver en Standard, Premium eller PremiumV2 prisplanen 
* stöder TCP och UDP
* fungerar med App Service-appar och funktionsappar

Det finns några saker som VNet-integrering har inte stöd för bland annat:

* montera en enhet
* AD-integrering 
* NetBios

## <a name="regional-vnet-integration"></a>regional VNet-integrering 

När VNet-integrering används med virtuella nätverk i samma region som din app, kräver användning av en delegerad undernätverk med minst 32 adresser i den. Undernätet kan inte användas för något annat. Utgående anrop som görs från din app kommer att göras från adresser i delegerade undernätet. När du använder den här versionen av VNet-integrering, görs anrop från adresser i ditt virtuella nätverk. Med adresser i ditt virtuella nätverk kan appen:

* göra anrop till tjänsteslutpunkt skyddade tjänster
* komma åt resurser i ExpressRoute-anslutningar
* komma åt resurser i det virtuella nätverket som du är ansluten till
* komma åt resurser i peer-kopplade anslutningar, inklusive ExpressRoute-anslutningar

Den här funktionen är i förhandsversion, men det finns stöd för Windows app produktionsarbetsbelastningar med följande begränsningar:

* Du kan endast nå adresser som finns i RFC 1918 intervallet. De är adresser i 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16-Adressblock.
* Du kan inte nå resurser över global peering-anslutningar
* Du kan inte ange vägar på den trafik som kommer från din app till ditt virtuella nätverk
* funktionen är endast tillgänglig från nyare App Service-skalningsenheter som stöder PremiumV2 App Service-planer.
* funktionen kan inte användas av isolerad appar som finns i en App Service Environment
* funktionen kräver en oanvända undernät med minst 32 adresser i ditt VNet i Resource Manager.
* Appen och det virtuella nätverket måste finnas i samma region
* En adress används för varje instans för App Service-plan. Eftersom undernätets storlek inte kan ändras efter tilldelning, Använd ett undernät som kan mer än att täcka storleken på din maximal skala. En/27 med 32 adresser är den rekommenderade storleken som som vill hantera en App Service-plan som skalas till 20 instanser.
* Du kan inte ta bort ett virtuellt nätverk med en integrerad app. Du måste ta bort integrationen först 
* Du kan ha endast en regional VNet-integrering för per App Service-plan. Flera appar i samma App Service-planen kan använda samma virtuella nätverk. 

Funktionen är i förhandsversion för Linux. Använda funktionen för VNet-integrering med en Resource Manager-VNet i samma region:

1. Gå till nätverk Användargränssnittet i portal. Om din app kan använda den nya funktionen, ser du ett alternativ för att lägga till VNet (förhandsversion).  

   ![Välj VNet-integrering][6]

1. Välj **Lägg till virtuellt nätverk (förhandsversion)** .  

1. Välj Resource Manager-VNet som du vill integrera med och skapa sedan ett nytt undernät eller välj ett befintligt tomt undernät. Integrationen tar mindre än en minut att slutföra. Din app under integrationen har startats om.  När integration är klar visas information på det virtuella nätverket som du är integrerade med och en banderoll överst som talar om funktionen är i förhandsversion.

   ![Välj det virtuella nätverk och undernät][7]

När din app är integrerad med ditt VNet, använder samma DNS-servern som ditt virtuella nätverk har konfigurerats med. 

Om du vill koppla från din app från det virtuella nätverket, Välj **Disconnect**. Detta startar om din webbapp. 

Den nya funktionen för VNet-integrering kan du använda tjänstslutpunkter.  Använda den nya VNet-integrering för att använda Tjänsteslutpunkter med din app, att ansluta till en valda virtuella nätverket och sedan konfigurera tjänstslutpunkter på undernät som du använde för att integrationen. 

### <a name="how-vnet-integration-works"></a>Så här fungerar VNet-integrering

Appar i App Service finns på worker-roller. Grundläggande och högre prissättningsplaner dedikerade värdplaner där det finns inga andra kunder arbetsbelastningar som körs på samma arbetare. VNet-Integration fungerar genom att montera virtuella gränssnitt med adresser i delegerade undernätet. Eftersom den från-adressen är i ditt virtuella nätverk, med åtkomst till de flesta apprelaterade sakerna i eller via ditt virtuella nätverk precis som en virtuell dator i ditt virtuella nätverk skulle. Nätverk implementeringen skiljer sig från att köra en virtuell dator i ditt virtuella nätverk och det vill säga varför vissa funktioner är ännu inte tillgängliga när du använder den här funktionen.

![VNET-integration](media/web-sites-integrate-with-vnet/vnet-integration.png)

När VNet-integrering är aktiverad blir fortfarande utgående samtal till internet via samma kanaler som vanligt i din app. Utgående adresser som anges i portalen för app-egenskaper är fortfarande de adresser som används av din app. Vilka ändringar för din app är att anrop till tjänsteslutpunkt skyddade tjänster eller RFC 1918 adresser hamnar i ditt virtuella nätverk. 

Funktionen stöder bara ett virtuellt gränssnitt per person.  Ett virtuellt gränssnitt per worker innebär en regional VNet-integrering för per App Service-plan. Alla appar i samma App Service-planen kan använda samma VNet-integrering, men om du behöver en app för att ansluta till en ytterligare VNet kan du behöver du skapa en annan App Service-plan. Det virtuella gränssnitt som används är inte en resurs som kunder har direkt åtkomst till.

På grund av hur den här tekniken fungerar, visar den trafik som används med VNet-integrering inte i Network Watcher eller NSG-flödesloggar.  

## <a name="gateway-required-vnet-integration"></a>Gateway krävs för VNet-integrering 

Gatewayen krävs VNet-integrationsfunktionen:

* kan användas för att ansluta till virtuella nätverk i valfri region, oavsett om de Resource Manager eller klassiska virtuella nätverk
* gör att en app för att ansluta till endast 1 virtuellt nätverk på en gång
* gör att upp till fem virtuella nätverk kan integreras med i en App Service Plan 
* tillåter samma virtuella nätverk som ska användas av flera appar i en App Service-Plan utan att påverka hur många som kan användas av en App Service plan.  Om du har en 6-appar med hjälp av samma virtuella nätverk i samma App Service-planen som räknas som 1 virtuellt nätverk som används. 
* kräver en virtuell nätverksgateway som är konfigurerad med punkt till plats-VPN
* Stöds inte för användning med Linux-appar
* Har stöd för ett serviceavtal på 99,9% på grund av serviceavtalet på gatewayen

Den här funktionen stöder inte:

* Åtkomst till resurser via ExpressRoute 
* Åtkomst till resurser via tjänstslutpunkter 

### <a name="getting-started"></a>Komma igång

Här följer några saker att tänka på innan du ansluter din webbapp till ett virtuellt nätverk:

* Virtuellt Målnätverk måste ha punkt-till-plats-VPN aktiveras med en routningsbaserad gateway innan den kan anslutas till appen. 
* Det virtuella nätverket måste finnas i samma prenumeration som din App Service-Plan(ASP).
* Appar som integreras med ett virtuellt nätverk använder DNS som har angetts för det virtuella nätverket.

### <a name="set-up-a-gateway-in-your-vnet"></a>Konfigurera en gateway i ditt virtuella nätverk ###

Om du redan har en gateway som konfigurerats med punkt-till-plats-adresser kan du hoppa över att konfigurera VNet-integrering med din app.  
Skapa en gateway:

1. [Skapa ett gatewayundernät] [ creategatewaysubnet] i ditt virtuella nätverk.  

1. [Skapa VPN-gatewayen][creategateway]. Välj en ruttbaserad VPN-typ.

1. [För plats-adresser][setp2saddresses]. Om gatewayen inte är på grundläggande nivå, sedan IKEV2 måste inaktiveras i punkt-till-plats-konfiguration och SSTP måste väljas. Adressutrymmet måste vara i RFC 1918-Adressblock, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Om du bara skapa gatewayen för använda med VNet-integrering för App Service, sedan du behöver inte ladda upp ett certifikat. Kan ta 30 minuter att skapa gatewayen. Du kommer inte att kunna integrera din app med det virtuella nätverket förrän gatewayen är upprättad. 

### <a name="configure-vnet-integration-with-your-app"></a>Konfigurera VNet-integrering med din app 

Så här aktiverar du VNet-integrering i din app: 

1. Gå till din app i Azure-portalen och öppna appen inställningar och välj nätverk > VNet-integrering. ASP måste finnas i en Standard-SKU eller bättre för att använda antingen VNet-integrering-funktionen. 
 ![VNet-integrering UI][1]

1. Välj **lägga till VNet**. 
 ![Lägg till VNet-integrering][2]

1. Välj ditt virtuella nätverk. 
  ![Välj ditt virtuella nätverk][8]
  
Din app kommer att startas om efter det sista steget.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Hur gatewayen krävs VNet-integrationsfunktionen fungerar

Gatewayen måste VNet-integrationsfunktionen bygger på teknik för punkt-till-plats-VPN. Punkt-till-plats-teknik begränsar åtkomst till bara den virtuella datorn som är värd för appen. Appar är begränsade till bara skickar trafik till internet, via Hybridanslutningar eller VNet-integrering. 

![Så här fungerar VNet-integrering][3]

## <a name="managing-vnet-integration"></a>Hantera VNet-integrering
Möjligheten att ansluta och koppla till ett virtuellt nätverk är en app-nivå. Åtgärder som kan påverka VNet-integrering i flera appar är på nivån för App Service-plan. Från appen > nätverk > VNet-integrering-portalen kan du få information om ditt virtuella nätverk. Du kan se liknande information på ASP-nivå i ASP > nätverk > VNet-integrering-portalen, inklusive vilka appar i den här App Service-planen använder en specifik integration.

 ![Information om virtuellt nätverk][4]

Informationen som finns tillgängliga att du i Användargränssnittet för VNet-integrering är samma mellan appen och ASP portaler.

* Namn på virtuellt nätverk - länkar till det virtuella nätverket UI
* Plats – visar platsen för ditt virtuella nätverk. Integrera med ett virtuellt nätverk i en annan plats kan det orsaka problem med nätverkssvarstiden för din app. 
* Certifikatsstatus – visar om dina certifikat är synkroniserade mellan din App Service-plan och ditt virtuella nätverk.
* Status för gateway - bör du använda en gateway krävs för VNet-integrering kan du se statusen för gatewayen.
* VNet-adressutrymmet - visar IP-adressutrymmet för det virtuella nätverket. 
* Adressutrymme för punkt-till-plats - visar punkten till plats IP-adressutrymme för ditt virtuella nätverk. Vid anrop till ditt VNet när du använder funktionen gateway krävs måste kommer din app från-adressen att någon av dessa adresser. 
* Adressutrymme för plats-till-plats – du kan använda plats-till-plats-VPN: er för att ansluta ditt VNet till dina lokala resurser eller till andra virtuella nätverket. IP-adressintervall som definierats med VPN-anslutningen visas här.
* DNS-servrar – visar DNS-servrar som konfigurerats med ditt virtuella nätverk.
* IP-adresser cirkulerade till virtuellt nätverk – visar-Adressblock dirigeras används på enheten trafik i ditt virtuella nätverk 

Den enda åtgärden som du kan vidta i app-vy av dina VNet-integrering är att din app kopplas bort från det virtuella nätverket som den är ansluten till. Om du vill koppla från din app från ett virtuellt nätverk, Välj **Disconnect**. Din app kommer att startas om när du kopplar från ett virtuellt nätverk. Kopplar från ändras inte ditt virtuella nätverk. Undernät eller gateway tas inte bort. Om du sedan vill ta bort det virtuella nätverket måste du först din app kopplas bort från det virtuella nätverket och ta bort resurser i den, till exempel gatewayer. 

Öppna ASP-Användargränssnittet för att nå ASP VNet-integrering Användargränssnittet, och välj **nätverk**.  Välj under VNet-integrering, **Klicka här om du vill konfigurera** att öppna Gränssnittet nätverk funktionen Status.

![Information för ASP-VNet-integrering][5]

ASP VNet-integrering Användargränssnittet visas alla de virtuella nätverken som används av appar i ASP. Om du vill visa information om varje virtuellt nätverk, klickar du på det virtuella nätverket som du är intresserad av. Det finns två åtgärder som du kan utföra här.

* **Synkronisera nätverk**. Synkroniseringsåtgärden för nätverk är endast för funktionen gateway beroende VNet-integrering. Utför en synkronisering nätverk ser du till att ditt certifikat och nätverksinformation är synkroniserade. Om du lägger till eller ändra DNS för ditt VNet, måste du utföra en **synkronisera nätverk** igen. Den här åtgärden startar om alla appar som använder det här virtuella nätverket.
* **Lägga till vägar** lägga till vägar kommer att öka utgående trafik i ditt virtuella nätverk.

**Routning** vägarna som definieras i ditt virtuella nätverk som används för att dirigera trafik till ditt virtuella nätverk från din app. Om du vill skicka ytterligare utgående trafik till det virtuella nätverket kan du lägga till dessa Adressblock. Den här funktionen endast fungerar med gateway krävs för VNet-integrering.

**Certifikat** när gatewayen krävs VNet-integrering aktiverat, det är ett obligatoriskt utbyte av certifikat för att säkerställa säkerheten för anslutningen. Tillsammans med certifikat som är DNS-konfigurationen, vägar och andra liknande element som beskrivs i nätverket.
Om certifikat eller information om nätverk ändras måste du klicka på ”Synkronisera nätverk”. När du klickar på ”Synkronisera nätverk” orsaka ett kort avbrott i anslutningen mellan din app och ditt virtuella nätverk. När din app inte startas om, leda förlust av anslutning till din webbplats inte fungerar korrekt. 

## <a name="accessing-on-premises-resources"></a>Åtkomst till lokala resurser
Appar kan komma åt lokala resurser genom att integrera med virtuella nätverk som har anslutningar för plats-till-plats. Om du använder VNet-integrering krävs för gatewayen, måste du uppdatera din lokala VPN-gateway vägar med din punkt-till-plats-Adressblock. När plats-till-plats-VPN är först ställer in, bör de skript som används för att konfigurera den ställa in vägar korrekt. Om du lägger till punkt-till-plats-adresser när du har skapat din plats-till-plats-VPN, måste du uppdatera vägar manuellt. Information om hur du gör variera per gateway och beskrivs inte här. Du kan inte ha konfigurerats med plats-till-plats VPN-anslutning för BGP.

Det finns ingen ytterligare konfiguration krävs för funktionen regional VNet-integrering att nå via ditt virtuella nätverk och till den lokala. Du behöver bara ansluta ditt VNet till lokalt med hjälp av ExpressRoute eller VPN för plats-till-plats. 

> [!NOTE]
> Gatewayen måste VNet-integrationsfunktionen inte integrerar en app med ett virtuellt nätverk som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har konfigurerats i [samexistens läge] [ VPNERCoex] VNet-Integration fungerar inte. Om du vill komma åt resurser via en ExpressRoute-anslutning kan du använda funktionen regional VNet-integrering eller ett [App Service Environment][ASE], som körs i ditt virtuella nätverk. 
> 
> 

## <a name="peering"></a>Peering
Om du använder integreringen regional VNet-peering, behöver du inte göra någon ytterligare konfiguration. 

Om du använder gatewayen krävs VNet-integrering med peering, måste du konfigurera några ytterligare objekt. Konfigurera peering för att arbeta med din app:

1. Lägg till en peeranslutning på det virtuella nätverket appen ansluter till. När du lägger till peering-anslutningen, aktivera **Tillåt åtkomst till virtuellt nätverk** och kontrollera **Tillåt vidarebefordrad trafik** och **Tillåt gatewayöverföring**.
1. Lägg till en peering-anslutningen på det virtuella nätverk som är att peer-kopplat till det virtuella nätverket som du är ansluten till. När du lägger till peering-anslutningen på målet VNet, aktivera **Tillåt åtkomst till virtuellt nätverk** och kontrollera **Tillåt vidarebefordrad trafik** och **Tillåt fjärrgateway**.
1. Gå till App Service-planen > nätverk > virtuellt nätverk Integration Användargränssnittet i portal.  Välj det virtuella nätverket som din app ansluter till. Lägg till adressintervallet för det virtuella nätverk som peer-kopplas med det virtuella nätverk som din app är ansluten till under avsnittet routning.  


## <a name="pricing-details"></a>Prisinformation
Regional VNet-integrering-funktionen har utan extra kostnad för användning utöver ASP priser nivån avgifter.

Det finns tre relaterade avgifter för användning av funktionen gateway krävs för VNet-integrering:

* ASP prisnivå nivå avgifter - dina appar måste vara i en Standard, Premium eller PremiumV2 App Service-Plan. Du kan se mer information på dessa kostnader här: [Prissättning för App Service][ASPricing]. 
* Dataöverföring – där är en kostnad för datatrafik, även om det virtuella nätverket är i samma datacenter. Dessa ändringar är beskrivs i [Data Transfer prisinformation om][DataPricing]. 
* Kostnader för VPN-Gateway - där är en kostnad för VNet-gateway som krävs för punkt-till-plats-VPN. Informationen finns på den [prissättning för VPN Gateway] [ VNETPricing] sidan.


## <a name="troubleshooting"></a>Felsökning
När funktionen är enkla att konfigurera det betyder inte att problemet kostnadsfria blir din upplevelse. Bör du stöter på är problem med åtkomst till din önskade slutpunkt vissa verktyg som du kan använda för att testa anslutningen från app-konsolen. Det finns två konsoler som du kan använda. En är Kudu-konsolen och den andra är konsolen i Azure-portalen. För att nå Kudu-konsolen från din app, gå till Verktyg -> Kudu. Det här är samma som kommer att [sitename]. scm.azurewebsites.net. När som öppnas, går du till fliken för felsökning av konsolen. Gå till verktyg för att få till Azure portal-värdbaserade konsolen sedan från din app -> konsolen. 

#### <a name="tools"></a>Verktyg
Verktygen **ping**, **nslookup** och **tracert** fungerar inte via konsolen på grund av säkerhetsbegränsningar. Om du vill fylla annulleringen två separata verktyg som har lagts till. För att kunna testa DNS-funktioner, vi har lagt till ett verktyg som heter nameresolver.exe. Syntax:

    nameresolver.exe hostname [optional: DNS Server]

Du kan använda **nameresolver** att kontrollera värdnamn som din app är beroende av. På så sätt kan du testa om du har något stavades konfigurerats med din DNS eller kanske inte har tillgång till din DNS-server. Du kan se DNS-servern som din app ska använda i-konsolen genom att titta på miljövariabler WEBSITE_DNS_SERVER och WEBSITE_DNS_ALT_SERVER.

Nästa verktyget kan du testa TCP-anslutning till en värd och port-kombination. Det här verktyget kallas **tcpping** och syntaxen är:

    tcpping.exe hostname [optional: port]

Den **tcpping** verktyget berättar om du når en viss värd och port. Det kan bara visa lyckades om: det finns ett program som lyssnar på en värd och port-kombination det finns nätverksåtkomst från din app till den angivna värd och port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Felsöka åtkomst till VNet värdbaserade resurser
Det finns ett antal saker som kan förhindra att din app når en viss värd och port. I de flesta fall är det en av tre saker:

* **Det är en brandvägg i vägen.** Om du har en brandvägg på sätt överskrids TCP-tidsgräns. TCP-tidsgränsen är 21 sekunder i det här fallet. Använd den **tcpping** verktyg för att testa anslutningen. TCP-tidsgränser kan bero på många saker utanför brandväggar men börjar där. 
* **DNS är inte tillgänglig.** DNS-tidsgränsen är tre sekunder per DNS-server. Om du har två DNS-servrar, är tidsgränsen 6 sekunder. Använda nameresolver för att se om DNS fungerar. Kom ihåg att du inte kan använda nslookup som som inte använder ditt VNet är konfigurerat med DNS. Om det är otillgänglig, du kan ha en brandvägg eller NSG blockera åtkomst till DNS- eller den kan ligga nere.

Om ditt problem inte svara på objekten, leta efter saker som: 

**regional VNet-integrering**
* är ditt mål för en RFC 1918 adress
* finns det en NSG blockerar utgående från integration-undernät
* Om du går över ExpressRoute eller VPN, lokal gateway konfigureras för att dirigera trafik säkerhetskopiera till Azure? Om du kan nå slutpunkter i ditt virtuella nätverk men inte lokalt, är det bra att kontrollera.

**Gateway krävs för VNet-integrering**
* är punkt-till-plats-adressintervall i RFC 1918 intervall (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)?
* Visar gatewayen som tas upp i portalen? Om din gateway är igång kan sedan konfigurera det igen.
* Visa certifikat är synkroniserade eller du misstänker att nätverkskonfigurationen har ändrats?  Om dina certifikat är inte synkroniserade eller om du misstänker att det har skett en ändring som gjorts i din konfiguration av virtuellt nätverk som inte har synkroniserats med din ASP, och tryck sedan på ”Synkronisera nätverk”.
* Om du går över ExpressRoute eller VPN, lokal gateway konfigureras för att dirigera trafik säkerhetskopiera till Azure? Om du kan nå slutpunkter i ditt virtuella nätverk men inte lokalt, är det bra att kontrollera.

Felsökning av problem med nätverk är en utmaning eftersom det inte kan du se vad blockerar åtkomst till en viss värd: port-kombination. Några av orsakerna är:

* du har en brandvägg på din värd neka åtkomst till programporten från din punkt till plats IP-adressintervall. Ofta passerande undernät kräver offentlig åtkomst.
* målvärddatorn är inte tillgänglig
* ditt program är nere
* du har fel IP-Adressen eller värdnamnet
* programmet lyssnar på en annan port än vad du förväntade dig. Du kan matcha process-ID med lyssningsporten med hjälp av ”netstat - aon” på slutpunkten värden. 
* dina nätverkssäkerhetsgrupper är konfigurerade på ett sådant sätt att de förhindrar åtkomst till programmet värddator och port från din plats till plats IP-adressintervall

Kom ihåg att du inte vet vilken adress som din app kommer faktiskt använder. Det kan vara en adress i integration undernät eller en punkt-till-plats-adressintervall, så du måste tillåta åtkomst från hela adressintervallet. 

Ytterligare felsökning stegen omfattar:

* Anslut till en virtuell dator i ditt virtuella nätverk och som försöker komma åt din resurs värd: port därifrån. Använd PowerShell-kommando för att testa för TCP-åtkomst, **test-netconnection**. Syntax:

      test-netconnection hostname [optional: -Port]

* Ta fram ett program på en virtuell dator och testa åtkomst till den värden och port från konsolen från din app med **tcpping**

#### <a name="on-premises-resources"></a>Lokala resurser ####

Om din app inte kan nå en resurs på plats, kontrollerar du om du når resursen från ditt virtuella nätverk. Använd den **test-netconnection** PowerShell-kommando för att kontrollera för TCP-åtkomst. Om den virtuella datorn inte kan nå dina lokala resurser, ditt VPN eller ExpressRoute-anslutning är kanske inte korrekt konfigurerad.

Om ditt virtuella nätverk finns VM kan nå den lokala datorn men din app kan inte så beror det förmodligen en av följande orsaker:

* vägarna har inte konfigurerats med undernätet eller pekar på platsen adressintervallen i din lokala gateway
* dina nätverkssäkerhetsgrupper blockerar åtkomst för punkt-till-plats-IP-adressintervall
* din lokala brandväggar blockerar trafik från ditt punkt-till-plats-IP-adressintervall
* du försöker nå en RFC 1918-adress med hjälp av funktionen regional VNet-integrering


## <a name="powershell-automation"></a>PowerShell-automation

Du kan integrera App Service med Azure Virtual Network med hjälp av PowerShell. Ett är klara att köra skript, se [anslutit en app i Azure App Service till ett Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
