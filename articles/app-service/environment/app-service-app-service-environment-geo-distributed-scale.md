---
title: Geodistribuerad skalning med App Service Environment
description: "Lär dig mer om att skala horisontellt appar med geo-distribution med Traffic Manager och App-miljöer."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.openlocfilehash: 21f747239e565aba79a84c8e946a71e306b64968
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geodistribuerad skalning med App Service Environment
## <a name="overview"></a>Översikt
Programscenarier som kräver storskalighet kan överskrida resurskapaciteten för beräkning till en distribution av en app.  Röstning program, är sport händelser och TV underhållning händelser alla exempel på scenarier som kräver mycket hög skala. Hög skalning kraven kan uppfyllas genom att skala ut appar, vågrätt med flera appdistributioner görs inom en enskild region, samt över regioner, för att hantera extrema belastningskraven.

Apptjänstmiljöer finns en perfekt plattform för vågrät skala ut.  När en Apptjänst-miljö konfiguration har valts som kan stödja en känd förfrågningar, utvecklare kan distribuera ytterligare Apptjänstmiljöer ”kakmall” sätt att uppnå en önskad belastning belastningen kapacitet.

Anta till exempel att en app som körs på en Apptjänstmiljö konfiguration har testats för att hantera 20K begäranden per sekund (RPS).  Om den önskade belastning belastning kapaciteten är 100K RPS, kan sedan Apptjänstmiljöer fem (5) skapas och konfigureras för att säkerställa att programmet kan hantera den planerade belastningen.

Eftersom kunder normalt kommer åt appar som använder en anpassad (eller alternativa)-domän, behöver utvecklare ett sätt att distribuera appen begäranden på alla instanser för Apptjänst-miljö.  Ett bra sätt att åstadkomma detta är att matcha den anpassade domänen med hjälp av en [Azure Traffic Manager-profilen][AzureTrafficManagerProfile].  Traffic Manager-profilen kan konfigureras så att den pekar på alla enskilda Apptjänstmiljöer.  Traffic Manager hanterar automatiskt distribuera kunder för alla Apptjänstmiljöer baserat på inställningarna i Traffic Manager-profilen för belastningsutjämning.  Den här metoden fungerar oavsett om alla de Apptjänstmiljöer är distribuerat i en enda Azure region eller distribueras i hela världen över flera Azure-regioner.

Eftersom kunder åtkomst till appar i alternativa domänen, inte dessutom kunder känner av antalet Apptjänstmiljöer som kör en app.  Därför kan utvecklare snabbt och enkelt lägga till och ta bort Apptjänstmiljöer baserat på observerade trafikbelastningen.

Det konceptuella diagrammet nedan visar en app vågrätt skala ut över tre Apptjänstmiljöer inom en enskild region.

![Konceptuell arkitektur][ConceptualArchitecture] 

Resten av det här avsnittet går igenom steg som ingår med att konfigurera en distribuerad topologi för exempelapp med flera Apptjänstmiljöer.

## <a name="planning-the-topology"></a>Planera topologin
Innan du bygga ut en distribuerad app storleken hjälper det för att ha några delar information i förväg.

* **Anpassad domän för appen:** vad är det anpassade domännamnet som kunder använder för att komma åt appen?  För exempelappen det anpassade domännamnet är *www.scalableasedemo.com*
* **Traffic Manager-domän:** ett domännamn måste väljas när du skapar en [Azure Traffic Manager-profilen][AzureTrafficManagerProfile].  Det här namnet kommer att kombineras med de *trafficmanager.net* suffix för att registrera en post för domänen som hanteras av Traffic Manager.  Valt namnet är för sample-appen *skalbara ase demo*.  Därför är det fullständiga domännamnet som hanteras av Traffic Manager *skalbara ase demo.trafficmanager.net*.
* **Strategi för att skala appen storleken:** storleken program distribueras över flera Apptjänstmiljöer i en region?  Flera regioner?  En blanda och matcha-med båda metoderna?  Beslutet ska baseras på förväntningar där kunden trafik kommer kommer, samt hur väl resten av en app stöd för backend-infrastruktur kan skala.  Till exempel med ett 100% tillståndslösa program, kan en app massivt skalas med en kombination av flera Apptjänstmiljöer per Azure-region, multiplicerat med Apptjänstmiljöer distribution över flera Azure-regioner.  Kunder med 15 + offentliga Azure-regioner att välja mellan, kan verkligen bygga ett globalt storskaliga program storleken.  För sample-appen används för den här artikeln, har tre Apptjänstmiljöer skapats i en enda Azure region (södra centrala USA).
* **Namnkonventionen för App Service-miljöer:** varje Apptjänst-miljön kräver ett unikt namn.  Utöver en eller två Apptjänstmiljöer är det bra att ha en namngivningskonvention för att identifiera varje Apptjänst-miljö.  En enkel namngivningskonvention för exempelappen har använts.  Namnen på de tre Apptjänstmiljöer är *fe1ase*, *fe2ase*, och *fe3ase*.
* **Namnkonventionen för apparna:** eftersom flera instanser av appen ska distribueras, krävs ett namn för varje instans av den distribuerade appen.  En mindre kända men är användbart funktion i App Service-miljöer är att samma appnamn kan användas i flera Apptjänstmiljöer.  Eftersom varje Apptjänstmiljö har ett unikt domänsuffix, kan utvecklare du återanvända exakt samma appnamn i varje miljö.  En utvecklare kan till exempel ha appar med namnet på följande sätt: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*osv.  För exempelappen även om varje app-instansen har även ett unikt namn.  App-instansnamn som används är *webfrontend1*, *webfrontend2*, och *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Skapa Traffic Manager-profilen
När flera instanser av en app distribueras på flera Apptjänstmiljöer kan enskilda app-instanser registreras med Traffic Manager.  För exempelapp en Traffic Manager profil krävs för *skalbara ase demo.trafficmanager.net* som kan vidarebefordra kunder till någon av följande distribuerade appen instanser:

* **webfrontend1.fe1ase.p.azurewebsites.NET:** en instans av exempelappen har distribuerats på första Apptjänst-miljön.
* **webfrontend2.fe2ase.p.azurewebsites.NET:** en instans av exempelappen har distribuerats på andra Apptjänst-miljön.
* **webfrontend3.fe3ase.p.azurewebsites.NET:** en instans av exempelappen har distribuerats på tredje Apptjänst-miljön.

Det enklaste sättet att registrera flera Azure App Service-slutpunkter kan alla körs i den **samma** Azure-regionen är med Powershell [stöd för Azure Resource Manager Traffic Manager][ARMTrafficManager].  

Det första steget är att skapa en Azure Traffic Manager-profil.  Koden nedan visar hur profilen har skapats för exempelappen:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Observera hur *RelativeDnsName* parameter angavs för *skalbara ase demo*.  Detta är hur domännamnet *skalbara ase demo.trafficmanager.net* som associeras med en Traffic Manager-profil.

Den *TrafficRoutingMethod* parametern definierar principen Traffic Manager använder för att bestämma hur du sprida kundbelastning över alla tillgängliga slutpunkter för belastningsutjämning.  I det här exemplet i *viktat* har valt metoden.  Detta resulterar i kundernas önskemål är fördelade på alla registrerade programmet slutpunkter baserat på den relativa vikten som är associerade med varje slutpunkt. 

Med den profil som skapas läggs varje app-instansen till profilen som en intern Azure-slutpunkt.  Koden nedan hämtar en referens till varje frontend-webbprogram och lägger sedan till varje app som en Traffic Manager-slutpunkt av den *TargetResourceId* parameter.

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Observera hur det är ett anrop till *Lägg till AzureTrafficManagerEndpointConfig* för varje enskild app-instansen.  Den *TargetResourceId* i varje Powershell-kommando refererar till ett av tre distribuerad app-instanser.  Traffic Manager-profilen kommer sprida belastningen över alla tre slutpunkter som registrerats i profilen.

Alla tre slutpunkter använder samma värde (10) för den *vikt* parameter.  Detta resulterar i Traffic Manager sprids kundönskemål i alla tre app instanser jämnt. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Pekar appens domänen på Traffic Manager-domän
Det sista steget behövs är att peka den anpassade domänen för app på Traffic Manager-domän.  För exempelappen innebär detta att peka *www.scalableasedemo.com* på *skalbara ase demo.trafficmanager.net*.  Det här steget måste utföras med domänregistrator som hanterar anpassade domäner.  

Använda hanteringsverktyg för din registrator domän, en CNAME-post innehåller information måste skapas som anger den anpassade domänen på Traffic Manager-domän.  I bilden nedan visas ett exempel på hur den här CNAME-konfigurationen ser ut:

![CNAME-post för den anpassade domänen][CNAMEforCustomDomain] 

Kom ihåg att varje enskild app-instansen måste ha den anpassade domänen som registrerats med den samt men som inte omfattas i det här avsnittet.  Om programmet inte har den anpassade domänen som registrerats med appen när en begäran gör det till en app-instans och misslyckas annars begäran.  

I det här exemplet den anpassade domänen är *www.scalableasedemo.com*, och varje instans av programmet har den anpassade domänen som är kopplade till den.

![Egen domän][CustomDomain] 

En sammanfattning av registreringen av en anpassad domän med Azure Apptjänst-appar finns i följande artikel på [registrera anpassade domäner][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Testar distribuerad topologi
Slutresultatet av Traffic Manager- och DNS-konfigurationen är som begär för *www.scalableasedemo.com* flödar via följande ordning:

1. En webbläsare eller en enhet blir en DNS-sökning *www.scalableasedemo.com*
2. CNAME-post hos domänregistrator gör att DNS-sökning ska omdirigeras till Azure Traffic Manager.
3. En DNS-sökning görs för *skalbara ase demo.trafficmanager.net* mot en Azure Traffic Manager DNS-servrar.
4. Baserat på den princip för belastningsutjämning (den *TrafficRoutingMethod* parameter används tidigare när du skapar Traffic Manager-profilen), Traffic Manager kommer väljer något av de konfigurerade slutpunkterna och FQDN för den slutpunkten webbläsare och enheter.
5. Eftersom det fullständiga Domännamnet för slutpunkten är webbadressen till en app-instans som körs på en Apptjänst-miljö, webbläsare och enheter kommer att fråga en Microsoft Azure DNS-server för att matcha detta FQDN till en IP-adress. 
6. Webbläsare och enheter kommer skicka HTTP/S-begäran till IP-adressen.  
7. Begäran anländer till en app-instanser som körs på en App Service-miljöer.

I konsolen bilden nedan visas en DNS-sökning för sample-appen anpassade domäner har matcha till en app-instans som körs på en av tre exempel Apptjänstmiljöer (i det här fallet andra av de tre Apptjänstmiljöer):

![DNS-sökning][DNSLookup] 

## <a name="additional-links-and-information"></a>Information och ytterligare länkar
Dokumentation om Powershell [stöd för Azure Resource Manager Traffic Manager][ARMTrafficManager].  

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
