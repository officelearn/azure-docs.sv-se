---
title: Så här konfigurerar du en Apptjänst miljö v1
description: Konfiguration, hantering och övervakning av Apptjänstmiljö v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 34fb3f15c03a3d3ef5f0a27081539bf0a6d19c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23837220"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurera en App Service miljö v1

> [!NOTE]
> Den här artikeln handlar om Apptjänstmiljö v1.  Det finns en nyare version av Apptjänst-miljön som är enklare att använda och körs på mer kraftfulla infrastruktur. Mer information om den nya versionen start med den [introduktion till Apptjänst-miljön](intro.md).
> 

## <a name="overview"></a>Översikt
På en hög nivå, en Azure Apptjänst-miljö som består av flera huvudkomponenter:

* Värdtjänsten beräkningsresurser som körs i Apptjänst-miljön
* Lagring
* En databas
* En Classic(V1) eller resursen Manager(V2) Azure Virtual Network (VNet) 
* Ett undernät med tjänsten Apptjänst-miljö finns i den

### <a name="compute-resources"></a>Beräkna resurser
Du kan använda beräkningsresurserna för dina fyra resurspooler.  Varje App Service miljö (ASE) har en uppsättning frontwebbservrarna och tre möjliga arbetarpooler. Du behöver inte använda alla tre arbetarpooler--om du vill kan du bara använda en eller två.

Värdar i resurspooler (frontwebbservrarna och personer) är inte tillgänglig direkt till innehavare. Du kan inte använda Remote Desktop Protocol (RDP) kan ansluta till dem, ändra deras etablering eller fungera som en administratör på dem.

Du kan ange resource pool antal och storlek. I en ASE har fyra storleksalternativ som är märkta P1 via P4. Mer information om dessa storlekar och deras prisnivå finns [priser för Apptjänst](https://azure.microsoft.com/pricing/details/app-service/).
Ändra antalet eller storlek kallas en skalningsåtgärd.  Endast en skalningsåtgärden kan vara pågår i taget.

**Främre ends**: frontwebbservrarna är HTTP/HTTPS-slutpunkter för dina appar som lagras i din ASE. Du kör inte arbetsbelastningar i främre parterna.

* En ASE som börjar med två P2s som är tillräcklig för arbetsbelastningar för utveckling och testning och låg nivå produktionsarbetsbelastningar. Vi rekommenderar starkt P3s för måttlig till tunga produktionsarbetsbelastningar.
* För dig som tunga produktionsarbetsbelastningar rekommenderar vi att du har minst fyra P3s så det finns tillräcklig frontwebbservrarna körs när schemalagt underhåll sker. Schemalagt underhållsaktiviteter kommer att få ned en klientdelen i taget. Detta minskar den totala tillgängliga frontend kapaciteten under underhållsaktiviteter.
* Frontwebbservrarna kan ta upp till en timme att etablera. 
* För ytterligare skala finjusteringar bör du övervaka prosessorprocent, minnesprocent och aktiva begäranden mått för frontend-pool. Om procenttal CPU eller minne över 70 procent när du kör P3s, lägga till fler frontwebbservrarna. Om värdet för aktiva begäranden som beräknar medelvärdet till 15 000 till 20 000 förfrågningar per klient, bör du också lägga till flera frontwebbservrarna. Aktiva begäranden som beräknar medelvärdet till under 15 000 förfrågningar per framför avslutas när du kör P3s övergripande mål är att hålla CPU och minne procenttal nedan 70%.  

**Anställda**: arbetare är där dina appar verkligen körs. När du skalar upp din App Service-planer som använder arbetare i den associerade arbetspool.

* Du kan inte direkt lägga till anställda. De kan ta upp till en timme att etablera.
* Skalning storleken på en beräkningsresurser för alla poolen tar < 1 timme per uppdateringsdomän. Det finns 20 update domäner i en ASE. Det kan ta upp till 10 timmar om du skala beräknings-storleken på en arbetspool med 10 instanser.
* Om du ändrar storleken på de beräkningsresurser som används i en arbetspool kommer kallstart appar som körs i den poolen, worker.

Det snabbaste sättet att ändra storlek på resursen beräkning av en arbetspool som inte kör några appar är att:

* Skala ned antalet anställda till 2.  Lägsta skala ned storlek i portalen är 2. Det tar några minuter att frigöra dina instanser. 
* Välj ny beräkning storlek och antalet instanser. Härifrån kan tar det upp till två timmar att slutföra.

Om dina appar kräver en större storlek för beräknings-resurs, kan du dra nytta av riktlinjer som tidigare. Du kan fylla i en annan worker-pool med anställda av önskad storlek och flytta dina appar till poolen i stället för att ändra storlek på worker-poolen som är värd för dessa appar.

* Skapa ytterligare instanser av nödvändiga beräknings-storlek i en annan worker-pool. Detta kan ta upp till en timme att slutföra.
* Tilldela om din App Service-planer som är värd för appar som behöver en större storlek till nyligen konfigurerade worker-poolen. Det här är en snabb åtgärd som ska ta mindre än en minut att slutföra.  
* Skala ned den första worker-poolen om du inte behöver de instanserna som inte används längre. Den här åtgärden tar några minuter att slutföra.

**Autoskalning**: ett av de verktyg som hjälper dig att hantera din beräknings-resursanvändningen är autoskalning. Du kan använda autoskalning för frontend eller arbetarpooler. Du kan göra sådant som ökar din instanser av valfri pooltyp på morgonen och minska i kvällen. Eller kanske du kan lägga till instanser när antalet arbetare som är tillgängliga i en arbetspool sjunker under ett visst tröskelvärde.

Om du vill ange Autoskala regler runt beräkning resource pool mått och Kom ihåg den tidpunkt då etablering av kräver. Mer information om autoskalning Apptjänstmiljöer finns [konfigurera autoskalning i en Apptjänst-miljö][ASEAutoscale].

### <a name="storage"></a>Lagring
Varje ASE har konfigurerats med 500 GB lagringsutrymme. Detta utrymme används över alla program i ASE. Detta utrymme är en del av ASE och för närvarande kan inte växlas om du vill använda ditt lagringsutrymme. Om du gör ändringar till din virtuella nätverksroutning eller säkerhet, måste du fortfarande tillåta åtkomst till Azure Storage - eller ASE fungerar inte.

### <a name="database"></a>Databas
Databasen innehåller information som definierar miljö, samt information om de appar som körs på den. Detta är en del av lagras i Azure-prenumeration för. Det är inte något som du har en direkt möjlighet att ändra. Om du gör ändringar till din virtuella nätverksroutning eller säkerhet, måste du fortfarande tillåta åtkomst till SQL Azure-- eller ASE fungerar inte.

### <a name="network"></a>Nätverk
Det VNet som används med din ASE kan vara en som du gjorde när du skapade ASE eller en som du har gjort i förväg. När du skapar undernätet under skapande av ASE tvingar ASE ska vara i samma resursgrupp som det virtuella nätverket. Om du behöver resursgruppens namn används av din ASE vara annorlunda än den som ditt VNet, måste du skapa en ASE med hjälp av en resource manager-mall.

Det finns vissa begränsningar för det virtuella nätverket som används för en ASE:

* Det virtuella nätverket måste vara ett regionalt virtuellt nätverk.
* Det måste finnas ett undernät med 8 eller flera adresser där ASE distribueras.
* När ett undernät används som värd för en ASE kan inte adressintervallet i undernätet ändras. Därför rekommenderar vi att undernätet innehåller minst 64-adresser för att hantera eventuell ASE tillväxt.
* Det kan finnas ingenting i undernätet, men ASE.

Till skillnad från den värdbaserade tjänsten som innehåller ASE, den [virtuellt nätverk] [ virtualnetwork] och undernät som finns under användarkontroll.  Du kan administrera ditt virtuella nätverk via Användargränssnittet för virtuella nätverk eller PowerShell.  En ASE kan distribueras i ett klassiskt eller Resource Manager VNet.  Portal och API-upplevelser är något annorlunda mellan klassisk och Resource Manager VNets ASE upplevelse är densamma.

Det VNet som används som värd för en ASE kan använda antingen privata RFC1918 IP-adresser eller använda offentliga IP-adresser.  Om du vill använda ett IP-adressintervall som inte omfattas av RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) måste du skapa virtuella nätverk och undernät som ska användas av ditt ASE i ASE skapas.

Eftersom den här funktionen placerar Azure App Service i ditt virtuella nätverk, innebär det att dina appar som finns i din ASE kan nu komma åt resurser som är tillgängliga via ExpressRoute eller plats-till-plats virtuella privata nätverk (VPN) direkt. Appar som är inom din Apptjänst-miljön kräver inte ytterligare funktioner för att komma åt resurser som är tillgängliga för det virtuella nätverket som är värd för din Apptjänst-miljö. Det innebär att du inte behöver använda VNET integrering eller Hybridanslutningar för att komma åt resurser i eller ansluten till det virtuella nätverket. Du kan fortfarande använda båda dessa funktioner även om att komma åt resurser i nätverk som inte är anslutna till det virtuella nätverket.

Du kan till exempel använda VNET-integrering för att integrera med ett virtuellt nätverk som har i din prenumeration, men är inte ansluten till det virtuella nätverket som din ASE. Du kan också fortfarande använda Hybridanslutningar att komma åt resurser som finns i andra nätverk, precis som du normalt kan.  

Om du har ditt virtuella nätverk som konfigurerats med en ExpressRoute-VPN, bör du vara medveten om några av Routning behov med en ASE. Det finns vissa konfigurationer, användardefinierad väg (UDR) som inte är kompatibla med en ASE. Mer information om hur du kör en ASE i ett virtuellt nätverk med ExpressRoute finns [körs en Apptjänst-miljö i ett virtuellt nätverk med ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Att säkra inkommande trafik
Det finns två huvudsakliga sätt att kontrollera inkommande trafik till din ASE.  Du kan använda Network Security Groups(NSGs) för att styra vilka IP adresser kan komma åt din ASE som beskrivs här [hur du styr inkommande trafik i en Apptjänst-miljö](app-service-app-service-environment-control-inbound-traffic.md) och du kan också konfigurera din ASE med en intern belastningsutjämnare (ILB).  De här funktionerna kan också användas tillsammans om du vill begränsa åtkomst med hjälp av NSG: er ILB-ASE.

När du skapar en ASE skapas en VIP på ditt VNet.  Det finns två VIP typer externa och interna.  När du skapar en ASE med en extern VIP kommer dina appar i din ASE vara tillgänglig via en internet-dirigerbara IP-adress. När du väljer internt din ASE konfigureras med en ILB och kommer inte att direkt internet som är tillgänglig.  En ILB ASE kräver fortfarande en extern VIP men används bara för Azure åtkomst för hantering och underhåll.  

Under skapande av ILB ASE ange underdomänen används av ILB ASE och kommer att behöva hantera egna DNS för underdomänen som du anger.  Eftersom du ställer in underdomännamnet behöver du även hantera certifikatet som används för HTTPS-åtkomst.  När den har skapats för ASE uppmanas att ange certifikatet.  Mer information om hur du skapar och använder en ILB ASE läsa [med en Apptjänst-miljö med en intern belastningsutjämnare][ILBASE]. 

## <a name="portal"></a>Portalen
Du kan hantera och övervaka din Apptjänst-miljö med hjälp av Användargränssnittet i Azure-portalen. Om du har en ASE, är du troligt att Apptjänst symbolen på din sidopanelen. Den här symbolen används för att representera Apptjänstmiljöer i Azure-portalen:

![Symbolen för App-miljö][1]

Om du vill öppna Gränssnittet som listar alla dina Apptjänstmiljöer som du kan använda ikonen eller välj på ikonen (”>” symbol) längst ned i sidopanelen att välja Apptjänstmiljöer. Genom att välja något av ASEs visas kan öppna du användargränssnitt som används för att övervaka och hantera den.

![Gränssnittet för att övervaka och hantera din Apptjänst-miljö][2]

Det här första bladet visar några egenskaper för din ASE tillsammans med ett mått diagram per resurspoolen. Vissa av egenskaperna som visas i den **Essentials** block är också hyperlänkar öppnas bladet som är kopplade till den. Du kan till exempel välja den **virtuellt nätverk** namn för att öppna Användargränssnittet som är associerade med det virtuella nätverket som din ASE körs i. **Apptjänstplaner** och **appar** öppna varje bladen som innehåller dessa artiklar som finns i din ASE.  

### <a name="monitoring"></a>Övervakning
Diagram kan du se olika prestandamått i varje resurspool. Du kan övervaka Genomsnittlig CPU och minne för frontend-pool. Du kan övervaka det antal som används och det antal som är tillgängliga för worker pooler.

Flera Apptjänst planer kan göra användning av arbetare i en arbetspool. Arbetsbelastningen distribueras inte på samma sätt som med frontend-servrar så processor- och minnesanvändning inte ger mycket form användbar information. Det är viktigare att spåra hur många personer som du har använt och är tillgängliga, särskilt om du hanterar systemet för andra.  

Du kan också använda alla mått som kan spåras i diagrammen ställa in aviseringar. Ställa in aviseringar här fungerar på samma som någon annanstans i App Service. Du kan ange en avisering från antingen den **aviseringar** UI del eller från vidaresökning i några mått UI och välja **Lägg till avisering**.

![Mått UI][3]

Mått som beskrivs bara är Apptjänstmiljö mått. Det finns också mått som är tillgängliga på nivån för App Service-plan. Detta är där övervakning CPU och minne blir mycket bra.

I en ASE är alla App Service-planer dedikerade App Service-planer. Det innebär att bara appar som körs på värdar som allokerats till App Service-plan som appar i den här programtjänstplanen. Om du vill visa information om App Service-plan, ta fram din programtjänstplan från någon av listorna i Användargränssnittet för ASE eller från **Bläddra apptjänstplaner** (som listar alla).   

### <a name="settings"></a>Inställningar
I bladet ASE finns en **inställningar** avsnitt som innehåller flera viktiga funktioner:

**Inställningar för** > **egenskaper**: den **inställningar** blad öppnas automatiskt när du skapar ASE-bladet. Överst finns **egenskaper**. Det finns ett antal objekt i här som är redundant vad som visas i **Essentials**, men det är mycket användbar är **virtuella IP-adressen**, samt **utgående IP-adresser**.

![Inställningsbladet och egenskaper][4]

**Inställningar för** > **IP-adresser**: när du skapar en IP-Secure Sockets Layer (SSL)-app i din ASE, måste en SSL IP-adress. För att få en, måste din ASE SSL IP-adresser som den äger som kan allokeras. När en ASE skapas, den har en SSL IP-adress för detta ändamål, men du kan lägga till fler. Det finns en kostnad för ytterligare IP SSL-adresser, som visas i [priser för Apptjänst] [ AppServicePricing] (i avsnittet i SSL-anslutningar). Ytterligare priset är IP SSL-pris.

**Inställningar för** > **Front End poolen** / **Arbetarpooler**: var och en av dessa resource pool blad ger dig möjlighet att se information endast på den resurspoolen i Förutom att tillhandahålla kontroller för att fullständigt skala den resurspoolen.  

Bladet grundläggande för varje resurspool innehåller ett diagram med mått för den resurspoolen. Precis som med diagram i bladet ASE kan du gå till diagrammet och ställa in aviseringar efter behov. Ställer in en avisering från bladet ASE för en specifik resurspool gör samma sak som att göra det från resurspoolen. Från poolen worker **inställningar** bladet har du tillgång till alla appar eller apptjänstplaner som körs i den här arbetspool.

![Inställningar för Worker UI][5]

### <a name="portal-scale-capabilities"></a>Portalen skala funktioner
Det finns tre skalningsåtgärder:

* Ändra antalet IP-adresser i ASE som är tillgängliga för IP SSL-användning.
* Ändra storlek på beräkningsresurser som används i en resurspool.
* Ändra antalet beräkningsresurser som används i en resurspool, antingen manuellt eller via autoskalning.

Det finns tre sätt att styra hur många servrar som du har i din resurspooler i portalen:

* Skalningsåtgärden från huvudbladet ASE längst upp. Du kan göra flera skala konfigurationsändringar till pooler frontend- och arbetsroller. De tillämpas alla som en enda åtgärd.
* En manuell skalningsåtgärden från enskilda resurspoolen **skala** bladet är under **inställningar**.
* Autoskalning som du skapar från enskilda resurspoolen **skala** bladet.

Om du vill använda åtgärden i bladet ASE skjutreglaget kvantitet du vill använda och spara. Det här Gränssnittet stöder också ändra storlek.  

![Skala UI][6]

Om du vill använda manuell eller automatiska funktioner i en specifik resurspool, gå till **inställningar** > **Front End poolen** / **Arbetarpooler** som det behövs. Öppna sedan upp den pool som du vill ändra. Gå till **inställningar** > **skala ut** eller **inställningar** > **skala upp**. Den **skala ut** bladet kan du styra instansen kvantitet. **Skala upp** gör att du kan styra resursstorleken.  

![Inställningar för Användargränssnittet][7]

## <a name="fault-tolerance-considerations"></a>Överväganden för feltolerans
Du kan konfigurera en Apptjänst-miljö för att använda upp till 55 totala beräkningsresurser. Av dessa 55 beräkningsresurser kan endast 50 användas för arbetsbelastning på värden. Anledningen är dubbelt. Det finns minst 2 frontend beräkningsresurser.  Som lämnar upp till 53 att stödja worker-poolallokering. För att ge feltolerans som du behöver ha en ytterligare beräkningsresurser som allokeras enligt följande regler:

* Varje arbetspool måste minst 1 ytterligare beräkningsresurser som inte är tillgänglig kan tilldelas en arbetsbelastning.
* Om antalet beräkningsresurser i en arbetspool går över ett visst värde, måste en annan beräkningsresurser anges för feltolerans. Detta gäller inte i poolen frontend.

Inom en enskild worker-poolen är kraven för feltolerans som för ett angivet värde för X-resurser som tilldelats en arbetspool:

* Om X är mellan 2 och 20, är mycket användbar beräkningsresurser som du kan använda för arbetsbelastningar X-1.
* Om X är mellan 21 och 40, är mycket användbar beräkningsresurser som du kan använda för arbetsbelastningar X-2.
* Om X är mellan 41 och 53, är mycket användbar beräkningsresurser som du kan använda för arbetsbelastningar X-3.

Den minsta storleken har 2 frontservrar och 2 anställda.  Med ovanstående stämmer sedan är här några exempel för att tydliggöra:  

* Om du har 30 arbetare i en enda pool användas 28 av dem för att värden arbetsbelastningar.
* Om du har 2 anställda i en enda pool användas 1 för att värden arbetsbelastningar.
* Om du har 20 arbetare i en enda pool användas 19 för att värden arbetsbelastningar.  
* Om du har 21 arbetare i en enda pool sedan fortfarande bara 19 kan användas för arbetsbelastning på värden.  

Feltolerans aspekt är viktigt, men du behöver ha i åtanke när du skalar ovan vissa tröskelvärden. Om du vill lägga till mer kapacitet från 20 instanser går du till 22 eller högre eftersom 21 inte lägga till några mer kapacitet. Detsamma gäller kommer ovan 40, där nästa nummer som lägger till kapacitet är 42.  

## <a name="deleting-an-app-service-environment"></a>Ta bort Apptjänst-miljö
Om du vill ta bort en Apptjänst-miljö får bara använda den **ta bort** åtgärd längst upp på bladet Apptjänst-miljö. När du gör det uppmanas du att ange namnet på din Apptjänst-miljö för att bekräfta att du verkligen vill göra detta. Observera att när du tar bort en Apptjänst-miljö kan du ta bort alla även på innehållet i den.  

![Ta bort Apptjänst-miljö UI][9]  

## <a name="getting-started"></a>Komma igång
Kom igång med Apptjänstmiljöer finns [så här skapar du en Apptjänst-miljö](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
