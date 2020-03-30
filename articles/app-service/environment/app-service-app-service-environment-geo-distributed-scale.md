---
title: Geo distribuerad skala
description: Lär dig hur du skalar appar horisontellt med hjälp av geodistribution med Traffic Manager- och App Service-miljöer.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 7ab04e23b838f2dfd39b73476db7492947d62e6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688804"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geodistribuerad skalning med App Service Environment
## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Programscenarier som kräver mycket hög skala kan överskrida beräkningsresurskapaciteten som är tillgänglig för en enda distribution av en app.  Röstningsprogram, sportevenemang och tv-sända underhållningsevenemang är alla exempel på scenarier som kräver extremt hög skala. Höga krav kan uppfyllas genom att vågrätt skala ut appar, med flera appdistributioner som görs inom en enda region, samt mellan regioner, för att hantera extrema belastningskrav.

App Service-miljöer är en idealisk plattform för horisontell utskalning.  När en apptjänstmiljökonfiguration har valts som kan stödja en känd begärandehastighet kan utvecklare distribuera ytterligare apptjänstmiljöer på "cookie cutter"-sätt för att uppnå en önskad toppbelastningskapacitet.

Anta till exempel att en app som körs på en App Service Environment-konfiguration har testats för att hantera 20 000 begäranden per sekund (RPS).  Om den önskade toppbelastningskapaciteten är 100 000 RPS kan fem (5) apptjänstmiljöer skapas och konfigureras för att säkerställa att programmet kan hantera den maximala beräknade belastningen.

Eftersom kunder vanligtvis kommer åt appar med en anpassad (eller fåfänga) domän behöver utvecklare ett sätt att distribuera appbegäranden i alla App Service Environment-instanser.  Ett bra sätt att åstadkomma detta är att lösa den anpassade domänen med hjälp av en [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  Traffic Manager-profilen kan konfigureras så att den pekar på alla enskilda App Service-miljöer.  Traffic Manager hanterar automatiskt distribution av kunder i alla App Service-miljöer baserat på belastningsutjämningsinställningarna i Traffic Manager-profilen.  Den här metoden fungerar oavsett om alla App Service-miljöer distribueras i en enda Azure-region eller distribueras över hela världen i flera Azure-regioner.

Eftersom kunder får åtkomst till appar via fåfänga-domänen känner kunderna dessutom inte till antalet App Service-miljöer som kör en app.  Som ett resultat utvecklare kan snabbt och enkelt lägga till, och ta bort, App Service Miljöer baserat på observerade trafikbelastning.

Det konceptuella diagrammet nedan visar en app vågrätt skalas ut över tre App Service-miljöer inom en enda region.

![Konceptuell arkitektur][ConceptualArchitecture] 

Resten av det här avsnittet går igenom stegen som ingår i att konfigurera en distribuerad topologi för exempelappen med hjälp av flera App Service-miljöer.

## <a name="planning-the-topology"></a>Planera topologin
Innan du bygger ut en distribuerad app fotavtryck, hjälper det att ha några bitar information i förväg.

* **Anpassad domän för appen:**  Vilket är det anpassade domännamn som kunderna använder för att komma åt appen?  För exempelappen är det anpassade domännamnet`www.scalableasedemo.com`
* **Traffic Manager-domän:**  Ett domännamn måste väljas när du skapar en [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  Det här namnet kombineras med suffixet *trafficmanager.net* för att registrera en domänpost som hanteras av Traffic Manager.  För exempelappen är namnet som väljs *skalbar-ase-demo*.  Därför är det fullständiga domännamnet som hanteras av Traffic Manager *scalable-ase-demo.trafficmanager.net*.
* **Strategi för skalning av appens fotavtryck:**  Kommer programmets fotavtryck att distribueras över flera App Service-miljöer i en enda region?  Flera regioner?  En mix-and-match av båda metoderna?  Beslutet bör baseras på förväntningar om var kundtrafiken kommer att komma, samt hur väl resten av en apps stödjande backend-infrastruktur kan skalas.  Med ett 100 % tillståndslöst program kan en app skalas kraftigt med hjälp av en kombination av flera App Service-miljöer per Azure-region, multiplicerat med App Service-miljöer som distribueras över flera Azure-regioner.  Med 15+ offentliga Azure-regioner tillgängliga att välja mellan kan kunderna verkligen skapa ett världsomspännande hyperskala programfotavtryck.  För exempelappen som används för den här artikeln skapades tre App Service-miljöer i en enda Azure-region (South Central US).
* **Namngivningskonvention för apptjänstmiljöerna:**  Varje App Service Environment kräver ett unikt namn.  Utöver en eller två App Service-miljöer är det bra att ha en namngivningskonvention för att identifiera varje App Service-miljö.  För exempelappen användes en enkel namngivningskonvention.  Namnen på de tre App Service-miljöerna är *fe1ase*, *fe2ase*och *fe3ase*.
* **Namngivningskonvention för apparna:**  Eftersom flera instanser av appen kommer att distribueras behövs ett namn för varje instans av den distribuerade appen.  En föga känd men mycket bekväm funktion i App Service-miljöer är att samma appnamn kan användas i flera App Service-miljöer.  Eftersom varje App Service Environment har ett unikt domänsuffix kan utvecklare välja att återanvända exakt samma appnamn i varje miljö.  En utvecklare kan till exempel ha appar som heter följande: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, etc.  För exempelappen även om varje appinstans också har ett unikt namn.  Appinstansnamnen som används är *webfrontend1*, *webfrontend2*och *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Ställa in Traffic Manager-profilen
När flera instanser av en app har distribuerats i flera App Service-miljöer kan de enskilda appinstanserna registreras hos Traffic Manager.  För exempelappen behövs en Traffic Manager-profil för *scalable-ase-demo.trafficmanager.net* som kan dirigera kunder till någon av följande distribuerade appinstanser:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  En instans av exempelappen som distribueras i den första App Service-miljön.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  En instans av exempelappen som distribueras i den andra App Service-miljön.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  En instans av exempelappen som distribueras i den tredje App Service-miljön.

Det enklaste sättet att registrera flera Slutpunkter för Azure App Service, som alla körs i **samma** Azure-region, är med [Powershell Azure Resource Manager Traffic Manager-stöd][ARMTrafficManager].  

Det första steget är att skapa en Azure Traffic Manager-profil.  Koden nedan visar hur profilen skapades för exempelappen:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Lägg märke till hur parametern *RelativeDnsName* har angetts till *skalbar-ase-demo*.  Så här skapas *domännamnet scalable-ase-demo.trafficmanager.net* och associeras med en Traffic Manager-profil.

Parametern *TrafficRoutingMethod* definierar den belastningsutjämningsprincip traffic manager ska använda för att avgöra hur kundbelastning ska spridas över alla tillgängliga slutpunkter.  I det här exemplet valdes den *viktade* metoden.  Detta resulterar i att kundbegäranden sprids över alla registrerade programslutpunkter baserat på de relativa vikter som är associerade med varje slutpunkt. 

När profilen har skapats läggs varje appinstans till i profilen som en inbyggd Azure-slutpunkt.  Koden nedan hämtar en referens till varje klientapp och lägger sedan till varje app som en Traffic Manager-slutpunkt via parametern *TargetResourceId.*

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Lägg märke till hur det finns ett anrop till *Add-AzureTrafficManagerEndpointConfig* för varje enskild appinstans.  *Parametern TargetResourceId* i varje Powershell-kommando refererar till en av de tre distribuerade appinstanserna.  Traffic Manager-profilen sprider belastningen över alla tre slutpunkter som registrerats i profilen.

Alla de tre slutpunkterna använder samma värde (10) för parametern *Vikt.*  Detta resulterar i att Traffic Manager sprider kundförfrågningar över alla tre appinstanserna relativt jämnt. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Peka appens anpassade domän på Traffic Manager-domänen
Det sista steget som krävs är att peka den anpassade domänen för appen på Traffic Manager-domänen.  För exempelappen innebär `www.scalableasedemo.com` det `scalable-ase-demo.trafficmanager.net`att peka på .  Det här steget måste slutföras med domänregistraren som hanterar den anpassade domänen.  

Med hjälp av din registrators domänhanteringsverktyg måste en CNAME-post skapas som pekar den anpassade domänen på Traffic Manager-domänen.  Bilden nedan visar ett exempel på hur den här CNAME-konfigurationen ser ut:

![CNAME för anpassad domän][CNAMEforCustomDomain] 

Även om det inte tas upp i det här avsnittet, kom ihåg att varje enskild appinstans måste ha den anpassade domänen registrerad med den också.  Annars om en begäran gör det till en appinstans och programmet inte har den anpassade domänen registrerad med appen, misslyckas begäran.  

I det här exemplet `www.scalableasedemo.com`är den anpassade domänen och varje programinstans har den anpassade domänen associerad med sig.

![Anpassad domän][CustomDomain] 

En sammanfattning av hur du registrerar en anpassad domän med Azure App Service-appar finns i följande artikel om [hur du registrerar anpassade domäner][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Prova distribuerad topologi
Slutresultatet av Traffic Manager och DNS-konfigurationen är att begäranden för `www.scalableasedemo.com` flödar genom följande sekvens:

1. En webbläsare eller enhet gör en DNS-sökning för`www.scalableasedemo.com`
2. CNAME-posten på domänregistraren gör att DNS-sökningen omdirigeras till Azure Traffic Manager.
3. En DNS-sökning görs för *scalable-ase-demo.trafficmanager.net* mot en av DNS-servrarna i Azure Traffic Manager.
4. Baserat på lastutjämningsprincipen (parametern *TrafficRoutingMethod* som användes tidigare när Traffic Manager-profilen skapas) väljer Traffic Manager en av de konfigurerade slutpunkterna och returnerar FQDN för den slutpunkten till webbläsaren eller enheten.
5. Eftersom FQDN för slutpunkten är url:en till en appinstans som körs på en App Service-miljö, kommer webbläsaren eller enheten att be en Microsoft Azure DNS-server att matcha FQDN till en IP-adress. 
6. Webbläsaren eller enheten skickar HTTP/S-begäran till IP-adressen.  
7. Begäran kommer fram till en av appinstanserna som körs på en av apptjänstmiljöerna.

Konsolen bilden nedan visar en DNS-sökning för exempelappens anpassade domän framgångsrikt lösa till en app instans som körs på en av de tre exempel App Service Miljöer (i detta fall den andra av de tre App Service-miljöer):

![DNS-uppslag][DNSLookup] 

## <a name="additional-links-and-information"></a>Ytterligare länkar och information
Dokumentation om [Powershell Azure Resource Manager Traffic Manager-stöd][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
