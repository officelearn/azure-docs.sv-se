---
title: GEO-distribuerad skala med App Service Environment - Azure
description: Lär dig att vågrätt skala appar med hjälp av geo-replikering med Traffic Manager och App Service-miljöer.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 2a2fafb5da50dbd26786284592cd330df7f5557a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113710"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geodistribuerad skalning med App Service Environment
## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Programscenarier som kräver mycket hög skala kan överskrida resurskapacitet beräknings vara tillgängliga för en distribution av en app.  Rösta program är sportevenemang och TV underhållning händelser alla exempel på scenarier som kräver mycket hög skala. Hög skala kraven kan uppfyllas genom att vågrätt skala ut appar, med flera appdistributioner görs inom en enda region, samt i regioner, för att hantera extrem belastning.

App Service Environment är en perfekt plattform för horisontell skalbarhet.  När en App Service Environment configuration har markerats som har stöd för ett känt begäran-pris, kan utvecklare distribuera ytterligare App Service-miljöer i ”kakmall” sätt att uppnå en önskad högsta belastning kapacitet.

Anta exempelvis att en app som körs på en App Service Environment-konfiguration har testats för att hantera 20K begäranden per sekund (RPS).  Om den önskade högsta belastning kapaciteten är 100K RPS, kan sedan fem (5) App Service-miljöer skapas och konfigureras för att säkerställa att programmet kan hantera den maximala planerade belastningen.

Eftersom kunder normalt inte komma åt appar som använder en anpassad (eller anpassad)-domän, måste utvecklare ett sätt att distribuera appen begäranden på alla instanser för App Service Environment.  Ett bra sätt att åstadkomma detta är att lösa en anpassad domän med hjälp av en [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  Traffic Manager-profilen kan konfigureras för att peka på alla enskilda App Service-miljöer.  Traffic Manager hanterar automatiskt fördela kunder i alla App Service-miljöer baserat på inställningarna i Traffic Manager-profil för belastningsutjämning.  Den här metoden fungerar oavsett om alla App Service-miljöer är distribuerad i en enda Azure-region eller distribueras över hela världen över flera Azure-regioner.

Eftersom kunder åt appar via anpassad domän, är dessutom kunder ovetande om antalet App Service-miljöer som kör en app.  Därmed kan utvecklare snabbt och enkelt lägga till och ta bort, baserat på observerade trafikbelastningen för App Service-miljöer.

Det konceptuella diagrammet nedan visar en app som Vågrätt skalade ut över tre App Service-miljöer inom en enda region.

![Konceptuell arkitektur][ConceptualArchitecture] 

Resten av det här avsnittet beskriver steg som ingår med att konfigurera en distribuerad topologi för exempelappen använder flera App Service-miljöer.

## <a name="planning-the-topology"></a>Planera topologin
Innan du bygga ut en distribuerad app-fotavtryck hjälper det för att ha några delar informationen förbereds i förväg.

* **Anpassad domän för appen:**  Vad är det anpassade domännamnet som kunder använder för att få åtkomst till appen?  Exempelappen det anpassade domännamnet är *www.scalableasedemo.com*
* **Traffic Manager-domän:**  Ett domännamn måste väljas när du skapar en [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  Det här namnet kommer att kombineras med den *trafficmanager.net* suffix för att registrera en post för domänen som hanteras av Traffic Manager.  Valt namnet är för exempelappen, *skalbar ase demo*.  Därför är det fullständiga domännamnet som hanteras av Traffic Manager *skalbar ase demo.trafficmanager.net*.
* **Strategi för att skala app-fotavtryck:**  Program-fotavtryck distribueras över flera App Service-miljöer i en enda region?  Flera regioner?  Ett-blanda med båda metoderna?  Beslutet bör baseras på förväntningar som kundtrafiken kommer kommer från, samt hur väl resten av en app stöd för backend-infrastruktur kan skala.  Till exempel med ett 100% tillståndslösa program, kan en app massivt skalas med hjälp av en kombination av flera App Service-miljöer per Azure-region, multiplicerat med App Service-miljöer som distribueras över flera Azure-regioner.  Kunder med 15 + offentliga Azure-regioner kan välja mellan, kan verkligen skapa ett världsomspännande storskaliga program-fotavtryck.  För exempelapp som används för den här artikeln, har tre App Service-miljöer skapats i en enda Azure-region (södra centrala USA).
* **Namngivningskonventionen för App Service-miljöer:**  Varje App Service-miljö kräver ett unikt namn.  Utöver en eller två App Service-miljöer är det bra att ha en namngivningskonvention för att identifiera varje App Service Environment.  En enkel namngivningskonvention för exempelappen har använts.  Namnen på de tre App Service-miljöer är *fe1ase*, *fe2ase*, och *fe3ase*.
* **Namngivningskonvention för appar:**  Eftersom flera instanser av appen ska distribueras, krävs ett namn för varje instans av den distribuerade appen.  En mindre kända men mindre praktisk funktion i App Service-miljöer är att samma appnamn kan användas i flera App Service-miljöer.  Eftersom varje App Service Environment har ett unikt domänsuffix, kan utvecklare du återanvända exakt samma appnamn i varje miljö.  Utvecklare kan till exempel ha appar som namnges enligt följande: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*osv.  För exempelappen om varje app-instansen har också ett unikt namn.  App-instansnamn som används är *webfrontend1*, *webfrontend2*, och *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Konfigurera Traffic Manager-profilen
När flera instanser av en app distribueras på flera App Service-miljöer, kan instanser för enskilda appar registreras med Traffic Manager.  Exempelappen en Traffic Manager profilen krävs för *skalbar ase demo.trafficmanager.net* som kan dirigera kunderna till någon av följande distribuerade appen instanser:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  En instans av exempelappen som distribuerats på den första App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  En instans av exempelappen distribueras på andra App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  En instans av exempelappen som distribuerats på tredje App Service Environment.

Det enklaste sättet att registrera flera Azure App Service-slutpunkter kan alla som körs i den **samma** Azure-region, är med Powershell [support för Azure Resource Manager Traffic Manager] [ ARMTrafficManager].  

Det första steget är att skapa en Azure Traffic Manager-profil.  Koden nedan visar hur profilen har skapats för exempelappen:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Observera hur *RelativeDnsName* parameter har angetts *skalbar ase demo*.  Detta är hur domännamnet *skalbar ase demo.trafficmanager.net* som associeras med en Traffic Manager-profil.

Den *TrafficRoutingMethod* parametern definierar principen Traffic Manager använder för att bestämma hur du sprida kundbelastning över alla tillgängliga slutpunkter för belastningsutjämning.  I det här exemplet på *viktat* metod har valts.  Detta resulterar i kundernas önskemål som sprids över alla registrerade programmet slutpunkter baserat på den relativa vikten som är associerade med varje slutpunkt. 

Med den profil som skapats läggs varje app-instansen till profilen som en intern Azure-slutpunkt.  Koden nedan hämtar en referens till varje klientdel webbapp och lägger sedan till varje app som en Traffic Manager-slutpunkt med hjälp av den *TargetResourceId* parametern.

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Observera hur det är ett anrop till *Lägg till AzureTrafficManagerEndpointConfig* för varje enskild app-instans.  Den *TargetResourceId* i varje Powershell-kommando refererar till någon av tre distribuerad app-instanserna.  Traffic Manager-profilen kommer sprida belastningen i alla tre slutpunkter som registrerats i profilen.

Alla tre slutpunkter använder samma värde (10) för den *vikt* parametern.  Detta resulterar i Traffic Manager sprida Återkopplingen över alla tre appinstanser jämnt. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Peka appens anpassade domän på Traffic Manager-domän
Det sista steget som behövs är så att den pekar på den anpassade domänen för appen vid Traffic Manager-domän.  Exempelappen det innebär som pekar *www.scalableasedemo.com* på *skalbar ase demo.trafficmanager.net*.  Det här steget måste utföras med domänregistratorn som hanterar den anpassade domänen.  

En CNAME-post registrerar med din registrator domän management tools, måste skapas som pekar till den anpassade domänen i Traffic Manager-domänen.  I bilden nedan visas ett exempel på hur den här CNAME-konfigurationen ser ut:

![CNAME-post för anpassad domän][CNAMEforCustomDomain] 

Även om inte omfattas i det här avsnittet, Kom ihåg att varje enskild app-instansen måste ha den anpassade domänen som registrerats med den också.  Om en begäran som gör det till en appinstans och programmet har inte den anpassade domänen som registrerats med appen, annars misslyckas begäran.  

I det här exemplet är den anpassade domänen *www.scalableasedemo.com*, och varje programinstans den anpassade domänen som är kopplade till den.

![Egen domän][CustomDomain] 

En sammanfattning av att registrera en anpassad domän med Azure App Service-appar, finns i följande artikel på [registrera anpassade domäner][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Testa den distribuerade topologin
Slutresultatet av Traffic Manager och DNS-konfigurationen är att begäranden för *www.scalableasedemo.com* flödar genom följande ordning:

1. En webbläsare eller enhet blir en DNS-sökning *www.scalableasedemo.com*
2. CNAME-posten på domänregistratorn gör att DNS-sökning för att omdirigeras till Azure Traffic Manager.
3. En DNS-sökning görs *skalbar ase demo.trafficmanager.net* mot en Azure Traffic Manager DNS-servrar.
4. Baserat på princip för belastningsutjämning (den *TrafficRoutingMethod* parametern använde tidigare när du skapar Traffic Manager-profil), Traffic Manager kommer väljer du något av de konfigurerade slutpunkterna och returnera det fullständiga Domännamnet för den slutpunkten för den webbläsare eller enhet.
5. Eftersom det fullständiga Domännamnet för slutpunkten är webbadressen till en appinstans som körs på en App Service Environment, ber webbläsare eller enhet en Microsoft Azure DNS-server för att matcha detta FQDN till en IP-adress. 
6. Webbläsare eller enhet skickar HTTP/S-begäran till IP-adressen.  
7. Begäran anländer till någon av app-instanser som körs på en App Service-miljöer.

Konsolen bilden nedan visar en DNS-sökning för exemplet appens anpassade domän har matchar en appinstans som körs på en av de tre exempel App Service-miljöer (i det här fallet andra av de tre App Service-miljöer):

![DNS-sökning][DNSLookup] 

## <a name="additional-links-and-information"></a>Ytterligare länkar och Information
Dokumentation om Powershell [support för Azure Resource Manager Traffic Manager][ARMTrafficManager].  

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
