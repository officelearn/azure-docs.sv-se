---
title: "Integrera en app med Azure-nätverk"
description: "Visar hur du ansluter en app i Azure App Service till en ny eller befintlig Azure-nätverk"
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: d285e63e64d8f4a260c45143f0ae3f7fddd4a2b6
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrera din app med Azure-nätverk
Det här dokumentet beskriver funktionen Azure App Service virtuellt nätverk integrering och visar hur du ställer in den med appar i [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Om du inte känner till virtuella Azure-nätverk (Vnet) är en funktion som gör att du kan placera många av dina Azure-resurser i ett routeable-internet-nätverk som du styr åtkomst till. Dessa nätverk kan sedan vara ansluten till ditt lokala nätverk med hjälp av en mängd olika tekniker för VPN. Mer information om virtuella Azure-nätverk som startar med den här informationen: [Azure översikt över virtuella nätverk][VNETOverview]. 

Azure App Service har två typer. 

1. Flera innehavare-system som stöder en fullständig uppsättning prissättning
2. App Service miljö (ASE) premiumfunktionen som distribuerar i ditt VNet. 

Det här dokumentet går igenom VNet-integrering och inte Apptjänstmiljö. Om du vill veta mer om funktionen ASE startar med den här informationen: [Apptjänstmiljö introduktion][ASEintro].

VNet-integrering ger din web appåtkomst till resurser i ditt virtuella nätverk, men ger inte privat åtkomst till ditt webbprogram från det virtuella nätverket. Åtkomst till privata webbplatsen avser att göra din app endast tillgänglig från ett privat nätverk som från Azure-nätverk. Åtkomst till privata webbplatsen är bara tillgängligt med en ASE som konfigurerats med en intern belastning belastningsutjämnare (ILB). Börja med den här artikeln för information om hur du använder en ILB ASE: [skapa och använda en ILB ASE][ILBASE]. 

Ett vanligt scenario där du vill använda för VNet-integrering är att aktivera åtkomst från ditt webbprogram till en databas eller en webbtjänst som körs på en virtuell dator i ditt virtuella Azure-nätverket. VNet-integration behöver du inte exponera en offentlig slutpunkt för program på din VM, men kan använda de privata icke internet-dirigerbara adresserna istället. 

Funktionen VNet-integrering:

* kräver en Standard, Premium eller isolerad priser plan 
* fungerar med klassiska eller Resource Manager VNet 
* stöder TCP och UDP
* fungerar med webb-, mobil, API apps och funktionen appar
* gör att en app för att ansluta till endast 1 virtuellt nätverk i taget
* Gör upp till fem Vnet integreras med i en Apptjänstplan 
* Gör samma virtuella nätverk som ska användas av flera appar i en Apptjänstplan
* stöder ett SLA för 99,9% på grund av SLA på Gateway för virtuellt nätverk

Det finns vissa saker som VNet-Integration inte stöder inklusive:

* montera en enhet
* AD-integrering 
* NetBios
* åtkomst till privata webbplatsen

### <a name="getting-started"></a>Komma igång
Här följer några saker att tänka på innan du ansluter ditt webbprogram till ett virtuellt nätverk:

* VNet-Integration fungerar endast med appar i en **Standard**, **Premium**, eller **isolerad** priser plan. Om du aktiverar funktionen och skala din Apptjänstplan till ett som inte stöds prisavtal dina appar förlora sina anslutningar till Vnet som de använder. 
* Om det virtuella nätverket målet redan finns måste den ha punkt-till-plats VPN aktiverad med en dynamisk routning gateway innan den kan anslutas till en app. Om din gateway har konfigurerats med statisk routning, kan du inte aktivera punkt-till-plats virtuella privata nätverk (VPN).
* Det virtuella nätverket måste finnas i samma prenumeration som din App Service Plan(ASP). 
* Appar som integreras med ett virtuellt nätverk använder DNS som har angetts för det virtuella nätverket.
* Som standard dirigerar apparna integrerande endast trafik i ditt VNet baserat på de vägar som definieras i ditt VNet. 

## <a name="enabling-vnet-integration"></a>Aktivera integrering av virtuellt nätverk

Du har möjlighet att ansluta din app till en ny eller befintlig virtuellt nätverk. Om du skapar ett nytt nätverk som en del av din integrering, förutom att bara skapa VNet, en dynamisk routning gateway är förkonfigurerad för dig och punkt till plats-VPN är aktiverat. 

> [!NOTE]
> Konfigurera en ny virtuell nätverksintegration kan ta flera minuter. 
> 
> 

Öppna appen inställningar för att aktivera integrering av virtuella nätverk, och välj sedan nätverk. Användargränssnittet som öppnas erbjuder tre alternativ för nätverksfunktioner. Den här guiden är bara i VNet integrering men Hybridanslutningar och Apptjänstmiljöer beskrivs senare i det här dokumentet. 

Om din app inte är i rätt priser plan, kan du skala din plan till en högre prisnivå plan önskat i Användargränssnittet.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Aktivera VNet-integrering med ett befintligt virtuellt nätverk
VNet-integrering Användargränssnittet kan du välja från en lista över ditt Vnet. Klassiska Vnet tyda på att de är exempel med ordet ”klassisk” inom parentes bredvid namnet på VNet. Listan sorteras så att Resource Manager VNets visas först. Du kan se att endast en VNet kan väljas i bilden nedan. Det finns flera orsaker till att ett virtuellt nätverk kan vara nedtonad inklusive:

* VNet är i en annan prenumeration som ditt konto har åtkomst till
* VNet saknar punkt till plats aktiverat
* det virtuella nätverket har inte en dynamisk routning gateway

![][2]

Om du vill aktivera integrering Klicka på det VNet som du vill integrera med. När du har valt VNet startas automatiskt din app för att ändringarna ska börja gälla. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Aktivera punkt till plats i ett klassiskt virtuellt nätverk
Om ditt VNet har punkt till plats eller inte har en gateway, har att ställa in som först. Om du vill göra detta för klassiska VNet, gå till den [Azure-portalen] [ AzurePortal] och visa listan över virtuella Networks(classic). Klicka på det nätverk som du vill integrera med och klicka på rutan stort under Essentials kallas VPN-anslutningar från den här. Här kan skapa du din plats för att platsen VPN och har även den skapa en gateway. När du går igenom punkten till plats gateway skapa upplevelse är ungefär 30 minuter innan den är klar. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Aktivera punkt till plats i en Resource Manager-VNet
Om du vill konfigurera en Resource Manager-VNet med en gateway och pekar på platsen, kan du använda antingen PowerShell enligt beskrivningen här, [konfigurerar en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell] [ V2VNETP2S] eller använda Azure portal som beskrivs här [konfigurerar en punkt-till-plats-anslutning till ett virtuellt nätverk med Azure-portalen][V2VNETPortal]. Användargränssnittet för att utföra den här funktionen är inte tillgänglig ännu. Observera att du inte behöver skapa certifikat för peka platskonfiguration. Detta konfigureras automatiskt när du ansluter din WebApp till VNet. 

### <a name="creating-a-pre-configured-vnet"></a>Skapa ett förkonfigurerade virtuellt nätverk
Om du vill skapa ett nytt virtuellt nätverk som har konfigurerats med en gateway och punkt-till-plats har möjlighet att göra det men endast för en Resource Manager-VNet med App Service nätverk Användargränssnittet. Om du vill skapa ett klassiskt virtuellt nätverk med en gateway och punkt-till-plats måste du göra det manuellt via användargränssnittet för nätverk. 

Om du vill skapa en Resource Manager VNet i användargränssnittet för integrering av virtuellt nätverk väljer du helt enkelt **Skapa nytt virtuellt nätverk** och ange den:

* Namn för virtuellt nätverk
* Adressblock för virtuellt nätverk
* Undernätsnamn
* Adressbok för undernät
* Adressblock för gateway
* Adressblock för punkt-till-plats

Om du vill att detta virtuella nätverk att ansluta till andra nätverk, bör du inte välja IP-adressutrymmen som överlappar med dessa nätverk. 

> [!NOTE]
> Hanteraren för filserverresurser VNet har skapats med en gateway tar cirka 30 minuter och för närvarande är inte integrerat VNet med din app. När ditt VNet har skapats med en gateway måste du gå tillbaka till din app VNet integrering UI och välj ditt nya VNet.
> 
> 

![][3]

Azure Vnet skapas normalt i privata nätverksadresser. Som standard VNet-Integration dirigerar funktionen alla trafiken för de IP-adressintervall i ditt VNet. De privata IP-adressintervall är:

* -Detta är samma som 10.0.0.0 - 10.0.0.0/8 10.255.255.255
* -Detta är samma som 172.16.0.0 - 172.16.0.0/12 172.31.255.255 
* -Detta är samma som 192.168.0.0 - 192.168.0.0/16 192.168.255.255

VNet-adressutrymmet måste anges i CIDR-notation. Om du är bekant med CIDR-notering, är en metod för att ange Adressblock med en IP-adress och ett heltal som representerar nätmasken. Överväg att 10.1.0.0/24 är 256 adresser och 10.1.0.0/25 skulle vara 128 adresser som en snabbreferens. En IPv4-adress med en /32 är bara 1 adress. 

Om du anger DNS-serverinformation här har som angetts för din VNet. Du kan redigera den här informationen från användarupplevelser VNet efter att VNet har skapats. Om du ändrar namn för det virtuella nätverket måste att utföra en synkronisering nätverk.

När du skapar ett klassiskt virtuellt nätverk med hjälp av Användargränssnittet för integrering av virtuella nätverk, skapar ett VNet i samma resursgrupp som din app. 

## <a name="how-the-system-works"></a>Så här fungerar systemet
Under försättsbladen skapar den här funktionen ovanpå punkt-till-plats VPN-teknik för att ansluta din app till ditt VNet. Appar i Azure App Service har en flera innehavare systemarkitektur, vilket utesluter etablerar en app direkt i ett VNet som görs med virtuella datorer. Vi begränsa nätverksåtkomsten till bara den virtuella datorn som är värd för appen genom att bygga på punkt-till-plats-teknik. Åtkomst till nätverket är också begränsad på värdarna app så att dina appar endast kan komma åt de nätverk som du konfigurerar dem tillgång till. 

![][4]

Om du inte har konfigurerat en DNS-server med ditt virtuella nätverk, behöver din app använda IP-adresser för att nå resursen i virtuella nätverk. Kom ihåg att större fördelen med den här funktionen är att den kan du använda privata adresser i ditt privata nätverk när du använder IP-adresser. Om du ställer in din app använda offentliga IP-adresser för en av dina virtuella datorer och du inte använder funktionen VNet-integrering och kommunicerar via internet.

## <a name="managing-the-vnet-integrations"></a>Hantera virtuella nätverk integreringar
Möjligheten att ansluta och koppla till ett virtuellt nätverk är på en appnivå. Åtgärder som kan påverka VNet-Integration över flera appar är på en ASP-nivå. Du kan visa information på ditt VNet i användargränssnittet som visas på app-nivå. De flesta av samma information visas också på ASP-nivå. 

![][5]

Du kan se om din app är ansluten till ditt VNet från sidan funktionen nätverksstatus. Om din gateway för virtuellt nätverk avser oavsett orsak, skulle det här visas som inte ansluten. 

Den information som du har nu tillgängliga för dig i appen nivå VNet integrering Användargränssnittet är samma som detaljerad information som du får från ASP. Här följer dessa objekt:

* VNet - namn i den här länken öppnar virtuella Azure-nätverket UI
* Plats - det motsvarar platsen för ditt VNet. Det är möjligt att integrera med ett VNet i en annan plats.
* Certifikatstatus - är det certifikat som används för att säkra VPN-anslutning mellan appen och VNet. Det motsvarar ett test för att säkerställa att de är synkroniserade.
* Status för gateway - bör din gateway inte är tillgänglig oavsett orsak sedan din app kan inte komma åt resurser i VNet. 
* VNet-adressutrymmet - detta är IP-adressutrymmet för din VNet. 
* Peka på platsen adressutrymme - detta är punkten till plats IP-adressutrymme för din VNet. Din app visar kommunikation som kommer från en av IP-adresser i det här adressutrymmet. 
* Plats till plats-adressutrymme - du kan använda plats-till-plats VPN för att ansluta ditt VNet till ditt lokala resurser eller andra VNet. Du om har som konfigurerats sedan IP-adressintervall som definierats med att VPN-anslutning visas här.
* DNS-servrar – om du har DNS-servrar som konfigurerats med ditt VNet anges här.
* IP-adresser cirkulerade till virtuellt nätverk – där finns en lista över IP-adresser som ditt VNet har definierats för, och dessa adresser visas här. 

Den enda åtgärd som du kan vidta i vyn appen på din VNet-integrering är att koppla appen från den virtuella nätverk som den är ansluten till. Om du vill göra detta helt enkelt klicka på Koppla längst upp. Den här åtgärden ändras inte ditt VNet. VNet och dess konfiguration inklusive gateway förblir oförändrad. Om du sedan vill ta bort ditt VNet, måste du först ta bort resurser i den inklusive gateway. 

Apptjänstplan-vyn innehåller ett antal ytterligare åtgärder. Det är också komma åt annorlunda än från appen. För att nå öppnar ASP nätverk Användargränssnittet din ASP UI och rulla ned. Det finns ett UI-element som kallas funktionen nätverksstatus. Den ger vissa mindre information kring VNet-Integration. Klicka på det här Gränssnittet öppnar gränssnitt för nätverket funktionen Status. Om du sedan klickar på ”Klicka här för att hantera” öppnas som visar VNet integreringar i den här ASP Användargränssnittet.

![][6]

Platsen för ASP är bra att tänka på när du tittar på platserna för Vnet som du integrerar med. Du är mycket mer troligt att problem med nätverkssvarstiden när det virtuella nätverket är på en annan plats. 

Vnet integrerad med är en påminnelse på hur många Vnet dina appar är integrerade med i den här ASP och hur många du kan ha. 

Har lagt till information för varje virtuella nätverk precis Klicka för att visa för det virtuella nätverk som du är intresserad av. Förutom den information som har beskrivits tidigare kan du också se en lista över appar i den här ASP som använder det virtuella nätverket. 

Det finns två primära åtgärder med avseende på åtgärder. Först är möjligheten att lägga till vägar som enheten trafik som lämnar din app i ditt VNet. Den andra åtgärden är möjligheten att synkronisera certifikat och nätverksinformation.

![][7]

**Routning** som tidigare nämnts vägar som definieras i ditt virtuella nätverk är vad som ska användas för att dirigera trafik i ditt virtuella nätverk från din app. Det finns några användningsområden men där kunder vill skicka ytterligare utgående trafik från en app till VNet och för den här funktionen tillhandahålls. Vad händer med trafiken är upp till hur kunden konfigurerar sina virtuella nätverk. 

**Certifikat** i certifikatstatus visar en kontroll som utförs av Apptjänst för att verifiera att de certifikat som vi använder för VPN-anslutningen är fortfarande bra. När VNet-Integration aktiverad sedan finns om det här är den första integrationen till det virtuella nätverket från alla appar i den här ASP en obligatorisk utbyte av certifikat för att säkerställa säkerheten för anslutningen. Tillsammans med certifikat som hämta vi DNS-konfigurationen, vägar och andra liknande saker som beskrivs i nätverket.
Om dessa certifikat eller nätverksinformation ändras, måste du klicka på ”Sync nätverk”. **Obs**: när du klickar på ”Sync nätverk” och sedan du orsaka ett kort avbrott i anslutningen mellan din app och ditt VNet. När appen inte startas leda går att ansluta till din webbplats inte fungerar korrekt. 

## <a name="accessing-on-premises-resources"></a>Åtkomst till lokala resurser
En av fördelarna med funktionen VNet-integrering är att om ditt virtuella nätverk som är ansluten till ditt lokala nätverk med en plats till plats-VPN och sedan dina appar kan ha åtkomst till lokala resurser från din app. För att detta ska fungera även om du kan behöva uppdatera din lokala VPN-gatewayen med vägar för din plats till plats IP-intervallet. När plats till plats-VPN först ställa in bör de skript som används för att konfigurera den ställa in vägar inklusive din plats till plats-VPN. Om du lägger till punkten till plats-VPN när du har skapat din plats till plats-VPN, måste du uppdatera vägar manuellt. Information om hur du gör variera per gateway och beskrivs inte här. 

> [!NOTE]
> Funktionen VNet Integration integrerar inte en app med ett virtuellt nätverk som har en ExpressRoute-Gateway. Även om ExpressRoute-Gateway har konfigurerats i [samexistens läge] [ VPNERCoex] VNet-integrering inte fungerar. Om du behöver komma åt resurser via en ExpressRoute-anslutning så att du kan använda en [Apptjänstmiljö][ASE], som körs i ditt VNet.
> 
> 

## <a name="pricing-details"></a>Prisinformation
Det finns några priser olika delarna som du bör vara medveten om när du använder funktionen VNet-Integration. Det finns 3 relaterade avgifter för användning av den här funktionen:

* ASP priser nivå krav
* Kostnader för överföring av data
* VPN-Gateway kostnader.

De måste vara i en Standard eller Premium App Service-Plan för dina appar för att kunna använda den här funktionen. Du kan visa mer information om de här kostnaderna: [priser för Apptjänst][ASPricing]. 

På grund av sättet punkt till plats-VPN är hanteras du alltid har en avgift för utgående data via VNet integrering anslutningen även om VNet i samma datacenter. Om du vill se dessa ändringar är att ta en titt här: [Data Transfer prisinformation][DataPricing]. 

Det sista objektet är kostnaden för gateway för virtuellt nätverk. Om du inte behöver gateway för något annat som plats-till-plats-VPN, som du betalar för gateways att stödja funktionen VNet-Integration. Det finns information om de här kostnaderna: [priser för VPN-Gateway][VNETPricing]. 

## <a name="troubleshooting"></a>Felsökning
När funktionen är enkla att ställa in det betyder inte din upplevelse att problem som är ledigt. Bör du stöter på är problem med åtkomst till det önskade slutpunkten vissa verktyg som du kan använda för att testa anslutningen från konsolen app. Det finns två konsolen upplevelser som du kan använda. En är från Kudu-konsolen och den andra är den konsol som du kan nå i Azure-portalen. Gå till Verktyg -> Kudu för att hämta Kudu-konsolen från din app. Det här är samma som ska [sitename]. scm.azurewebsites.net. När du öppnar som bara gå till fliken Debug-konsolen. Gå till Verktyg -> konsolen för att få till Azure portal värdbaserade konsolen och sedan från din app. 

#### <a name="tools"></a>Verktyg
Verktyg ping, nslookup och tracert fungerar inte via konsolen på grund av säkerhetsbegränsningar. Om du vill fylla void där har två separata verktyg som lagts till. Vi lagt till ett verktyg med namnet nameresolver.exe för att testa DNS-funktioner. Syntax:

    nameresolver.exe hostname [optional: DNS Server]

Du kan använda nameresolver för att kontrollera värdnamn som din app är beroende av. Det här sättet kan du testa om du har något felaktigt konfigurerad med din DNS eller kanske inte har åtkomst till DNS-servern.

Nästa verktyget kan du testa för TCP-anslutning till en värd och port-kombination. Det här verktyget kallas tcpping.exe och syntaxen är:

    tcpping.exe hostname [optional: port]

Verktyget tcpping får du om du till en specifik värd och port. Det kan bara visa lyckas om: det finns ett program som lyssnar på en värd och port-kombination och det finns nätverksåtkomst från din app till den angivna värd och port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Åtkomst till VNet-felsökning värdbaserade resurser
Det finns ett antal saker som kan förhindra att din app når en viss värd och port. I de flesta fall är det en av tre saker:

* **Det finns en brandvägg på sätt** om du har en brandvägg i hur du kommer till TCP-tidsgränsen. Som är i det här fallet 21 sekunder. Verktyget tcpping används för att testa anslutningen. TCP-tidsgränser kan bero på många saker utanför brandväggar men det startas. 
* **DNS är inte tillgänglig** DNS för timeout är tre sekunder per DNS-server. Om du har två DNS-servrar, är tidsgränsen 6 sekunder. Använd nameresolver om du vill att DNS fungerar. Kom ihåg att du inte kan använda nslookup som som inte använder ditt virtuella nätverk är konfigurerat med DNS.
* **Ogiltig P2S IP-adressintervall** pekar på platsen IP-intervallet måste vara i RFC 1918 privata IP-intervall (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Om intervallet använder IP-adresser utanför som, sedan fungerar saker inte. 

Om ditt problem inte svara på dessa objekt, leta efter de enkla t.ex.: 

* Visar gatewayen som tas upp i portal?
* Visa certifikat som synkroniserade?
* Vem som helst ändra nätverkskonfigurationen utan att göra en ”Sync nätverk” i berörda ASP? 

Om din gateway är igång kan sedan ta den säkerhetskopiera. Om ditt certifikat är inte synkroniserade, gå till vyn ASP VNet-integrering och träffar ”Sync nätverk”. Om du misstänker att det har gjorts en ändring som gjorts i konfigurationen av virtuellt nätverk och det inte sync skulle med din ASP går du till vyn ASP VNet-integrering och träffar ”Sync nätverk” precis som en påminnelse, detta orsakar ett kort avbrott med din VNet-anslutning och dina appar. 

Om alla som är bra, måste du går lite djupare:

* Finns det några andra appar som använder VNet-integrering för att nå resurser i samma virtuella nätverk? 
* Kan du gå till app-konsolen och använda tcpping för att nå andra resurser i ditt VNet? 

Om något av ovanstående stämmer sedan din VNet-integrering är bra och problemet är någon annanstans. Detta är där hämtar den som en utmaning eftersom det inte finns något enkelt sätt att se varför du inte kan nå värdnamn: port. Orsaker är:

* du har en brandvägg på värddatorn neka åtkomst till porten för programmet från din plats till plats IP-adressintervall. Ofta passerande undernät kräver offentlig åtkomst.
* mål-värden är inte tillgänglig
* programmet är inte tillgänglig
* du har fel IP-Adressen eller värdnamnet
* ditt program lyssnar på en annan port än vad du förväntade dig. Du kan kontrollera detta genom att gå till värden och använder ”netstat – aon” från cmd-prompt. Detta visar vilken process som ID lyssnar på vad port. 
* din nätverkssäkerhetsgrupper har konfigurerats på ett sådant sätt att de förhindrar åtkomst till ditt programvärd och port från din plats till plats IP-adressintervall

Kom ihåg att du inte vet vilken IP i din punkt till plats IP-adressintervall som din app ska använda så du måste tillåta åtkomst från hela intervallet. 

Ytterligare stegen innefattar:

* logga in på en annan virtuell dator på ditt VNet och försöker nå din resurs värdnamn: port därifrån. Det finns vissa TCP ping verktyg du kan använda för detta ändamål eller kan även använda telnet om behöver vara. Syftet här är bara för att avgöra om anslutningen är det från den här andra VM. 
* Ta fram ett program på en annan virtuell dator och testa åtkomst till värd och port från konsolen från din app

#### <a name="on-premises-resources"></a>Lokala resurser ####
Om din app inte kan nå resurser lokalt, då är det första bör du kontrollera om du kan nå en resurs i ditt VNet. Om det fungerar försök komma åt lokala program från en virtuell dator i virtuella nätverk. Du kan använda telnet eller en TCP ping-verktyget. Om den virtuella datorn inte kan nå din lokal resurs, kontrollera plats-till-plats VPN-anslutningen fungerar. Om den fungerar kontrollerar du samma saker som beskrivits tidigare samt lokala gateway-konfiguration och status. 

Nu ditt VNet finns VM kan nå datorn lokalt men appen kan sedan orsaken är förmodligen en av följande:

* din vägar har inte konfigurerats med din plats till plats IP-adressintervall i din lokala gateway
* din nätverkssäkerhetsgrupper blockerar åtkomst för din plats till plats IP-intervall
* din lokala brandväggar blockerar trafik från din plats till plats IP-intervall
* du har definierat Route(UDR) en användare på ditt VNet som förhindrar din plats till plats baserat trafik från att nå det lokala nätverket

## <a name="hybrid-connections-and-app-service-environments"></a>Hybridanslutningar och Apptjänstmiljöer
Det finns tre funktioner som ger åtkomst till virtuella nätverk finns resurser. De är:

* VNet-integrering
* Hybridanslutningar
* Apptjänstmiljöer

Hybridanslutningar måste du installera en relay agent kallas Manager(HCM) för Hybrid-anslutning i nätverket. HCM måste kunna ansluta till Azure och ditt program. Den här lösningen är särskilt stor från nätverket, till exempel ditt lokala nätverk eller även ett annat moln värdbaserad nätverk eftersom det inte kräver en tillgänglig internet-slutpunkt. HCM körs bara på Windows och du kan ha upp till fem instanser som körs för att tillhandahålla hög tillgänglighet. Hybridanslutningar stöder endast TCP men och varje HC slutpunkt måste matcha till en specifik värd: port-kombination. 

Funktionen Apptjänst-miljö kan du köra en instans av Azure App Service i ditt VNet. Detta gör att dina appar komma åt resurser i ditt virtuella nätverk utan extra steg. Några av fördelarna med en Apptjänst-miljö är att du kan använda Dv2 baserat anställda med upp till 14 GB RAM-minne. En annan fördel är att du kan skala system för att uppfylla dina behov. Till skillnad från flera innehavare-miljöer där ASP är begränsad till 20 instanser i en ASE kan du skala upp till 100 ASP-instanser. En av de saker som du får med en ASE som du inte med VNet-integrering är att en Apptjänst-miljö kan arbeta med en ExpressRoute-VPN. 

När det inte finns några använder case överlappar varandra, kan ingen av de här funktionerna ersätta något av de andra. Att veta vilken funktion du ska använda är knutna till dina behov. Exempel:

* Om du utvecklar och bara vill köra en plats i Azure och har åtkomst till databasen på arbetsstationen under skrivbordet, är det enklast att använda Hybridanslutningar. 
* Om du är en stor organisation som vill försätta ett stort antal egenskaper för webbtjänst för offentliga moln och hantera dem i ditt eget nätverk och du vill gå med Apptjänst-miljön. 
* Om du har ett antal App Service finns appar och för att komma åt resurser i ditt VNet, sedan VNet-integrering är att. 

Utöver användningsområden, det finns vissa enkelhet relaterade aspekter. Om ditt VNet redan är ansluten till ditt lokala nätverk är med VNet integrering eller en Apptjänst-miljö ett enkelt sätt att använda lokala resurser. Å andra sidan, om ditt VNet inte är anslutet till det lokala nätverket är det mycket mer kostnader för att konfigurera en plats till plats-VPN med ditt VNet jämfört med att installera HCM. 

Utöver de funktionella skillnaderna det också priser skillnader. Funktionen för Apptjänst-miljö är en Premium tjänsterbjudande men erbjuder den mest network configuration möjligheter utöver andra funktionerna. VNet-integrering kan användas med Standard eller Premium ASP och är perfekt för förbrukning av resurser i ditt virtuella nätverk från flera innehavare Apptjänst på ett säkert sätt. Hybridanslutningar beroende för närvarande en BizTalk konto, vilket nivåer som startar ledigt och sedan hämta progressivt dyrare baserat på hur mycket du behöver. När det gäller att arbeta i många nätverk men finns det ingen annan funktion som Hybridanslutningar som gör att du kan komma åt resurser i väl över 100 separata nätverk. 

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
