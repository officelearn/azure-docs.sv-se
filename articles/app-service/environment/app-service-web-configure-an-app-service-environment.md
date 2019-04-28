---
title: Så här konfigurerar du en App Service Environment version 1 – Azure
description: Konfiguration, hantering och övervakning av App Service Environment v1
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
ms.custom: seodec18
ms.openlocfilehash: 5c0b4117f6e7b48dce1746ad6eb3dbe29c0d16af
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130627"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurera en App Service Environment version 1

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1.  Det finns en nyare version av App Service Environment som är enklare att använda och körs på kraftfullare infrastruktur. Mer information om den nya versionen början med den [introduktion till App Service Environment](intro.md).
> 

## <a name="overview"></a>Översikt
En Azure App Service-miljö består av flera viktiga komponenter på en hög nivå:

* Värdtjänsten för beräkningsresurser som körs i App Service Environment
* Storage
* En databas
* Ett Classic(V1) eller resurs Manager(V2) Azure virtuellt nätverk (VNet) 
* Ett undernät med App Service Environment som värd-tjänsten som körs i det.

### <a name="compute-resources"></a>Beräkningsresurser
Du använder beräkningsresurserna för fyra resurspooler.  Varje App Service Environment (ASE) har en uppsättning av klientdelar och tre möjliga arbetarpooler. Du behöver inte använda alla tre arbetarpooler--om du vill kan du bara använda en eller två.

Värdar i resurspooler (klientdelar och arbetare) är inte direkt tillgänglig för klienterna. Du kan inte använda Remote Desktop Protocol (RDP) kan ansluta till dem, ändra deras etablering eller fungera som en administratör på dem.

Du kan ange resource pool antalet och storleken. I en ASE har fyra storleksalternativ som är märkta med P1 till P4. Mer information om dessa storlekar och deras priser finns i [prissättning för App Service](https://azure.microsoft.com/pricing/details/app-service/).
Ändra kvantitet eller storlek kallas en skalningsåtgärd.  Endast en skalningsåtgärd kan vara pågår samtidigt.

**Klientdelens ends**: Frontend-datorer är HTTP/HTTPS-slutpunkter för dina appar som hålls kvar i din ASE. Du köra inte arbetsbelastningar i frontend-datorer.

* En ASE som börjar med två P2s som är tillräckligt för arbetsbelastningar för utveckling/testning och på låg nivå produktionsarbetsbelastningar. Vi rekommenderar starkt P3s för dig att tung produktionsarbetsbelastningar.
* För dig som har stor produktionsarbetsbelastningar rekommenderar vi att du har minst fyra P3s att se till att det finns tillräckligt med frontend-datorer som körs när sker schemalagt underhåll. Schemalagt underhållsaktiviteter kommer påverkar en klientdel i taget. Detta minskar den totala tillgängliga frontend kapacitet under underhållsaktiviteter.
* Klientdelar kan ta upp till en timme att etablera. 
* För ytterligare skalning finjusteringar, bör du övervaka CPU-procent, minnesprocent och aktiva begäranden mått för adresspoolen på klientsidan. Om procentandelar CPU eller minne är över 70 procent när du kör P3s, lägger du till flera klientdelar. Om värdet för aktiva begäranden beräknar medelvärdet till 15 000 till 20 000 begäranden per klient, bör du också lägga till flera klientdelar. Övergripande mål är att hålla CPU och minne procenttal nedan 70% och aktiva begäranden som beräknar medelvärdet till under 15 000 begäranden per front avslutas när du kör P3s.  

**Arbetare**: Arbetarna är där apparna faktiskt körs. När du skalar upp din App Service-planer som använder arbetare i arbetarpoolen associerade.

* Du kan inte direkt lägga till arbetare. De kan ta upp till en timme att etablera.
* Skala storleken på en beräkningsresurs för en pool tar < 1 timme per uppdateringsdomän. Det finns 20 uppdateringsdomäner i en ase-miljö. Om du skalat beräkningsstorleken för en arbetarpool med 10 instanser, kan det ta upp till 10 timmar att slutföra.
* Om du ändrar storleken på de beräkningsresurser som används i en arbetspool genereras kallstarter appar som körs i den arbetarpoolen.

Det snabbaste sättet att ändra resursen beräkningsstorleken för en arbetarpool som inte kör några appar är att:

* Skala ned antalet arbetare till 2.  Minsta skala ned storlek i portalen är 2. Det tar några minuter att frigöra dina instanser. 
* Välj ny beräkning storlek och antalet instanser. Här kan tar det upp till 2 timmar att slutföra.

Om dina appar kräver en större beräkningsstorleken för resursen, kan du dra nytta av föregående procedur. I stället för att ändra storleken på arbetarpoolen som är värd för dessa appar kan du fylla i en annan arbetarpool med Worker av önskad storlek och flytta dina appar över till poolen.

* Skapa flera instanser av nödvändiga beräkningsstorleken i en annan arbetarpoolen. Detta kan ta upp till en timme att slutföra.
* Tilldela om din App Service-planer som är värd för appar som behöver en större storlek till den nyligen konfigurerade arbetarpoolen. Det här är en snabb åtgärd som ska vidtas på mindre än en minut att slutföra.  
* Skala ned första arbetarpoolen om du inte behöver dessa instanser som inte används längre. Den här åtgärden tar några minuter att slutföra.

**Automatisk skalning**: En av de verktyg som kan hjälpa dig att hantera din beräkning resursförbrukning är automatisk skalning. Du kan använda automatisk skalning för klientdelen eller arbetarpooler. Du kan göra sådant som ökar dina instanser av valfri pooltyp på morgonen och minska på kvällen. Eller kanske du kan lägga till instanser när antalet arbetare som är tillgängliga i en arbetspool sjunker under ett visst tröskelvärde.

Om du vill ange regler för automatisk skalning runt compute resource pool mått och Tänk på den tid som etablering kräver. Mer information om automatisk skalning App Service-miljöer finns i [så här konfigurerar du automatisk skalning i en App Service Environment][ASEAutoscale].

### <a name="storage"></a>Storage
Varje ASE har konfigurerats med 500 GB lagringsutrymme. Den här utrymme som används i alla appar i ase-miljön. Det här lagringsutrymmet ingår i ASE och för närvarande kan inte växlas om du vill använda ditt lagringsutrymme. Om du gör justeringar i ditt virtuella nätverksroutning eller säkerhet, måste du fortfarande tillåta åtkomst till Azure Storage, dels eller fungerar inte ASE.

### <a name="database"></a>Databas
Databasen innehåller information som definierar miljön, samt information om appar som körs i den. Det här är för en del av lagras i Azure-prenumeration. Det är inte något som du har en direkt möjlighet att ändra. Om du gör justeringar i ditt virtuella nätverksroutning eller säkerhet, måste du fortfarande tillåta åtkomst till SQL Azure-- eller fungerar inte ASE.

### <a name="network"></a>Nätverk
Det virtuella nätverket som används med din ASE kan vara en som du gjorde när du skapade ASE eller en som du har gjort i tid. När du skapar undernätet under ASE-generering, tvingar ASE finnas i samma resursgrupp som det virtuella nätverket. Om du behöver den resursgrupp som används av din ASE ska vara densamma som för ditt VNet, måste du skapa ASE med en resource manager-mall.

Det finns vissa begränsningar i det virtuella nätverket som används för en ase-miljö:

* Det virtuella nätverket måste vara ett regionalt virtuellt nätverk.
* Det måste finnas ett undernät med 8 eller flera adresser där ASE har distribuerats.
* När ett undernät används som värd för en ASE, kan inte ändra adressintervallet för undernätet. Därför rekommenderar vi att undernätet innehåller minst 64-adresser för att hantera eventuell tillväxt i ASE.
* Det kan vara inget annat i undernätet men ASE.

Till skillnad från den värdbaserade tjänsten som innehåller ASE, den [virtuellt nätverk] [ virtualnetwork] och undernätet finns under användarkontroll.  Du kan administrera ditt virtuella nätverk via virtuella nätverk Användargränssnittet eller PowerShell.  En ASE kan distribueras i en klassisk eller Resource Manager virtuellt nätverk.  De portal och API: et kan variera mellan klassiska och Resource Manager-VNets men ASE-upplevelsen är samma.

Det virtuella nätverk som används för att vara värd för en ASE kan använda antingen privata RFC1918 IP-adresser eller använda offentliga IP-adresser.  Om du vill använda ett IP-adressintervall som inte omfattas av RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) måste du skapa ditt VNet och undernät som ska användas av din ASE före ASE-generering.

Eftersom den här funktionen placerar Azure App Service i ditt virtuella nätverk, innebär det att dina appar som finns i din ASE kan nu komma åt resurser som är tillgängliga via ExpressRoute eller plats-till-plats virtuellt privat nätverk (VPN) direkt. De appar som är i din App Service-miljö kräver inte ytterligare funktioner för att komma åt resurser som är tillgängliga för det virtuella nätverket som är värd för din App Service Environment. Det innebär att du inte behöver använda VNET-integrering eller Hybrid Connections för att få till resurser i eller anslutet till det virtuella nätverket. Du kan fortfarande använda båda dessa funktioner även om att komma åt resurser i nätverk som inte är anslutna till det virtuella nätverket.

Du kan till exempel använda VNET-integrering för att integrera med ett virtuellt nätverk som finns i din prenumeration men är inte ansluten till det virtuella nätverket som din ASE finns i. Du kan fortfarande också använda Hybridanslutningar för att komma åt resurser som finns i andra nätverk, precis som vanligt.  

Om du har ditt virtuella nätverk som konfigurerats med en ExpressRoute VPN, bör du vara medveten om några av de behov av routning som har en ase-miljö. Det finns vissa konfigurationer, en användardefinierad väg (UDR) som inte är kompatibla med en ase-miljö. Mer information om hur du kör en ASE i ett virtuellt nätverk med ExpressRoute finns i [som kör en App Service Environment i ett virtuellt nätverk med ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Skydda inkommande trafik
Det finns två huvudsakliga sätt att kontrollera inkommande trafik till din ASE.  Du kan använda Network Security Groups(NSGs) för att styra vilka IP adresser kan komma åt din ASE som beskrivs här [Kontrollera inkommande trafik i en App Service Environment](app-service-app-service-environment-control-inbound-traffic.md) och du kan också konfigurera din ASE med en intern belastningsutjämnare (ILB).  Dessa funktioner kan också användas tillsammans om du vill begränsa åtkomst med NSG: er till din ILB ASE.

När du skapar en ASE, skapas en VIP i ditt virtuella nätverk.  Det finns två VIP, externa och interna.  När du skapar en ASE med extern VIP kommer dina appar i din ASE att komma åt via en internet-dirigerbara IP-adress. När du väljer intern ASE kommer att konfigureras med en ILB och kan inte direkt åtkomlig via internet.  En ILB ASE måste fortfarande extern VIP används endast för att hantera och underhålla åtkomst i Azure.  

Under ILB ASE-generering ange underdomänen som används av ILB ASE och kommer att behöva hantera din egen DNS för underdomänen som du anger.  Eftersom du ange namnet på underdomän behöver du också hantera certifikatet som används för HTTPS-åtkomst.  När ASE har skapats uppmanas du att ange certifikatet.  Mer information om hur du skapar och använder en ILB ASE läsa [med hjälp av en intern belastningsutjämnare med en App Service Environment][ILBASE]. 

## <a name="portal"></a>Portalen
Du kan hantera och övervaka din App Service Environment med hjälp av Användargränssnittet i Azure-portalen. Om du har en ASE är troligt att App Service-symbolen på din sidopanelen. Den här symbolen används för att representera App Service-miljöer i Azure portal:

![App Service Environment symbol][1]

För att öppna Användargränssnittet som innehåller alla App Service-miljöer, du kan använda ikonen eller välj på ikonen (”>” symbol) längst ned på sidopanelen att välja App Service-miljöer. Genom att välja en av ase-miljöer som visas, kan du öppna Användargränssnittet som används för att övervaka och hantera den.

![Användargränssnittet för att övervaka och hantera din App Service Environment][2]

Det här första bladet visar vissa egenskaper för din ASE, tillsammans med ett måttdiagram per resurspool. Vissa av egenskaperna som visas i den **Essentials** block är också hyperlänkar öppnas bladet som är associerade med den. Du kan till exempel välja den **virtuellt nätverk** namn så att du öppnar Användargränssnittet som är associerade med det virtuella nätverket som din ASE körs i. **App Service-planer** och **appar** varje öppna blad som innehåller dessa artiklar som finns i din ASE.  

### <a name="monitoring"></a>Övervakning
Diagrammen kan du se olika prestandamått i varje resurspool. Du kan övervaka den genomsnittliga CPU och minne för adresspoolen på klientsidan. Du kan övervaka det antal som används och det antal som är tillgängliga för worker-pooler.

Flera App Service planer kan göra använda arbetare i en arbetarpoolen. Arbetsbelastningen distribueras inte på samma sätt som med frontend-servrar så processor- och minnesanvändning inte mycket vägen för användbar information. Det är viktigare att spåra hur många arbetare som du har använt och är tillgängliga, särskilt om du hanterar det här systemet så att andra kan använda.  

Du kan också använda alla mått som kan spåras i diagrammen ställa in aviseringar. Ställa in aviseringar här fungerar på samma som någon annanstans i App Service. Du kan ställa in en avisering från antingen den **aviseringar** UI delar eller från att gå i några mått användargränssnitt och välja **Lägg till avisering**.

![Mått UI][3]

Mått som har beskrivit är App Service Environment-mått. Det finns även mätvärden som är tillgängliga på nivån för App Service-plan. Det här är där övervakning processor och minne är en mycket god idé.

I en ASE är alla App Service-planer dedikerade App Service-planer. Det innebär att de enda appar som körs på värdar som allokerats till att App Service-plan är apparna i den här App Service-planen. Om du vill visa information om App Service-planen, ta fram App Service-planen från någon av listor i ASE UI eller från **Bläddra App Service-planer** (som listar alla).   

### <a name="settings"></a>Inställningar
I bladet ASE finns en **inställningar** avsnitt som innehåller flera viktiga funktioner:

**Inställningar för** > **egenskaper**: Den **inställningar** bladet öppnas automatiskt när du skapar din ASE-bladet. Högst upp är **egenskaper**. Det finns ett antal objekt i här som är redundant till det som visas i **Essentials**, men det är mycket användbart är **virtuella IP-adressen**, samt **utgående IP-adresser**.

![Inställningsbladet och egenskaper][4]

**Inställningar för** > **IP-adresser**: När du skapar en IP-Secure Sockets Layer (SSL)-app i din ASE, behöver du en IP SSL-adress. Din ASE behöver för att skaffa en IP SSL-adresser som den äger som kan allokeras. När en ASE har skapats kan den har en IP SSL-adress för detta ändamål, men du kan lägga till fler. Det finns en avgift för ytterligare IP SSL-adresser, som visas i [prissättning för App Service] [ AppServicePricing] (i avsnittet i SSL-anslutningar). Ytterligare priset är IP SSL-priset.

**Inställningar för** > **Klientdelspool** / **Arbetarpooler**: Var och en av dessa pool resursbladen ger dig möjlighet att se information endast om den resurspoolen, förutom att tillhandahålla kontroller för att fullständigt skala den resurspoolen.  

Bladet grundläggande för varje resurspool innehåller ett diagram med mätvärden för den resurspoolen. Precis som med diagram från bladet ASE kan du gå till diagrammet och ställa in aviseringar efter behov. Ställer in en avisering från bladet ASE för en specifik resurspool gör samma sak som att göra det från resurspoolen. Från arbetarpoolen **inställningar** bladet du har åtkomst till alla appar eller App Service-planer som körs i den här arbetarpool.

![Worker-poolinställningar UI][5]

### <a name="portal-scale-capabilities"></a>Portalen skalningsfunktioner
Det finns tre skalningsåtgärder:

* Ändra antalet IP-adresser i ASE som är tillgängliga för IP SSL-användning.
* Ändra storlek på beräkningsresursen som används i en resurspool.
* Ändra antalet beräkningsresurser som används i en resurspool, antingen manuellt eller via automatisk skalning.

Det finns tre sätt att styra hur många servrar som du har i resurspooler i portalen:

* En skalningsåtgärd från ASE huvudbladet högst upp. Du kan göra flera skala konfigurationsändringar till klient- och worker-pooler. Alla tillämpas de som en enda åtgärd.
* En manuell skalningsåtgärd från enskilda resurspoolen **skala** blad som är under **inställningar**.
* Automatisk skalning som du skapar från enskilda resurspoolen **skala** bladet.

Dra skjutreglaget till det antal som du vill och spara om du vill använda skalningsåtgärden på ASE-bladet. Det här Gränssnittet stöder också ändra storlek.  

![Skala UI][6]

Om du vill använda funktionerna för manuell eller automatisk skalning i en specifik resurspool, gå till **inställningar** > **Pool på klientsidan** / **Arbetarpooler** som lämpliga. Öppna sedan den pool som du vill ändra. Gå till **inställningar** > **skala ut** eller **inställningar** > **skala upp**. Den **skala ut** bladet kan du styra instans kvantitet. **Skala upp** gör att du kan styra storleken för resursen.  

![Inställningar för Användargränssnittet][7]

## <a name="fault-tolerance-considerations"></a>Överväganden för feltolerans
Du kan konfigurera en App Service-miljö för att använda upp till 55 totala beräkningsresurser. Av dessa 55 beräkningsresurser användas endast 50 för att värdens arbetsbelastningar. Anledningen är dubbelt. Det finns minst 2 frontend beräkningsresurser.  Som lämnar upp till 53 för worker-poolallokering. För att kunna tillhandahålla feltolerans som du behöver ha en ytterligare beräkningsresurs som har allokerats enligt följande regler:

* Varje arbetarpool måste ha minst 1 ytterligare beräkningsresurs som inte är tillgängligt som ska tilldelas en arbetsbelastning.
* När mängden beräkningsresurser i en arbetspool mer än ett visst värde, måste en annan beräkningsresurs anges för feltolerans. Detta gäller inte i poolen på klientsidan.

Inom en enskild worker-pool är kraven för feltolerans som för en viss värdet för X-resurser som tilldelats en arbetarpool:

* Om X är mellan 2 och 20, är användbara beräkningsresurser som du kan använda för arbetsbelastningar X-1.
* Om X är mellan 21 och 40, är användbara beräkningsresurser som du kan använda för arbetsbelastningar X-2.
* Om X är mellan 41 och 53, är användbara beräkningsresurser som du kan använda för arbetsbelastningar X-3.

Förbrukar har 2 frontservrar och 2 Worker-arbeten.  Med ovanstående stämmer sedan följer här några exempel för att tydliggöra:  

* Om du har 30 arbetare i en enda pool användas 28 av dem för att värdens arbetsbelastningar.
* Om du har 2 anställda i en enda pool användas 1 för att värdens arbetsbelastningar.
* Om du har 20 arbetare i en enda pool användas 19 för att värdens arbetsbelastningar.  
* Om du har 21 arbetare i en enda pool sedan fortfarande bara 19 användas för att värdens arbetsbelastningar.  

Feltolerans aspekt är viktigt, men du behöver ha i åtanke när du skalar ovanför vissa gränser. Om du vill lägga till mer kapacitet från 20 instanser, går du till 22 eller högre eftersom 21 inte lägga till några mer kapacitet. Samma sak gäller gå över 40, där de närmast som lägger till kapacitet är 42.  

## <a name="deleting-an-app-service-environment"></a>Ta bort en App Service-miljö
Om du vill ta bort en App Service Environment kan bara använder den **ta bort** åtgärd överst på bladet App Service Environment. När du gör detta uppmanas du att ange namnet på din App Service Environment att bekräfta att du verkligen vill göra detta. Observera att när du tar bort en App Service Environment kan du ta bort allt innehåll i den också.  

![Ta bort en App Service Environment UI][9]  

## <a name="getting-started"></a>Komma igång
Kom igång med App Service-miljöer, se [så här skapar du en App Service Environment](app-service-web-how-to-create-an-app-service-environment.md).

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
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
