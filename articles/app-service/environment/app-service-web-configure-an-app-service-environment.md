---
title: Konfigurera ASE v1
description: Konfiguration, hantering och övervakning av App Service Environment v1. Det här dokumentet tillhandahålls endast för kunder som använder den äldre v1 ASE.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: b37708e27887b20604a1fe921f14e51387793737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687263"
---
# <a name="configuring-an-app-service-environment-v1"></a>Konfigurera en apptjänstmiljö v1

> [!NOTE]
> Den här artikeln handlar om App Service Environment v1.  Det finns en nyare version av App Service Environment som är enklare att använda och körs på mer kraftfull infrastruktur. Om du vill veta mer om den nya versionen börjar med [introduktionen till App Service Environment](intro.md).
> 

## <a name="overview"></a>Översikt
På en hög nivå består en Azure App Service-miljö av flera huvudkomponenter:

* Beräkna resurser som körs i tjänsten Värd för App Service-miljö
* Lagring
* En databas
* Ett klassiskt(V1) eller Resurshanteraren(V2) Virtuellt nätverk (VNet) 
* Ett undernät med apptjänstmiljön som finns värd för den som körs i det

### <a name="compute-resources"></a>Beräkna resurser
Du använder beräkningsresurserna för dina fyra resurspooler.  Varje App Service Environment (ASE) har en uppsättning främre ändar och tre möjliga arbetspooler. Du behöver inte använda alla tre arbetspooler - om du vill kan du bara använda en eller två.

Värdarna i resurspoolerna (klientdelar och arbetare) är inte direkt tillgängliga för klienter. Du kan inte använda RDP (Remote Desktop Protocol) för att ansluta till dem, ändra deras etablering eller fungera som administratör för dem.

Du kan ange resurspoolens kvantitet och storlek. I en ASE har du fyra storleksalternativ, som är märkta P1 till P4. Mer information om dessa storlekar och priser finns i [Priser för App Service](https://azure.microsoft.com/pricing/details/app-service/).
Att ändra kvantitet eller storlek kallas en skalningsåtgärd.  Endast en skalningsåtgärd kan vara pågående åt gången.

**Klientdelar:** Frontändarna är HTTP/HTTPS-slutpunkterna för dina appar som finns i DIN ASE. Du kör inte arbetsbelastningar i frontändarna.

* En ASE börjar med två P2:er, vilket är tillräckligt för utvecklings-/testarbetsbelastningar och produktionsarbetsbelastningar på låg nivå. Vi rekommenderar starkt P3s för måttliga till tunga produktionsarbetsbelastningar.
* För måttliga till tunga produktionsarbetsbelastningar rekommenderar vi att du har minst fyra P3:er för att säkerställa att det finns tillräckligt med frontändar som körs när schemalagt underhåll inträffar. Schemalagda underhållsaktiviteter kommer att få ner en front i taget. Detta minskar den totala tillgängliga frontend-kapaciteten under underhållsaktiviteter.
* Frontändarna kan ta upp till en timme att etablera. 
* För ytterligare skalnings finjustering bör du övervaka måtten CPU-procent, Minnesprocent och Aktiva begäranden för frontend-poolen. Om processor- eller minnesprocenten är över 70 procent när P3-enheter körs lägger du till fler klientdelar. Om värdet för aktiva begäranden i genomsnitt ut till 15 000 till 20 000 begäranden per klientdel bör du också lägga till fler klientdelar. Det övergripande målet är att hålla CPU och minne procentsatser under 70%, och aktiva begäranden genomsnitt ut till under 15.000 förfrågningar per front end när du kör P3s.  

**Arbetare**: Arbetarna är där dina appar faktiskt körs. När du skalar upp apptjänstplanerna använder du upp arbetare i den associerade arbetarpoolen.

* Du kan inte omedelbart lägga till arbetare. Det kan ta upp till en timme att etablera sig.
* Skala storleken på en beräkningsresurs för en pool tar < 1 timme per uppdateringsdomän. Det finns 20 uppdateringsdomäner i en ASE. Om du skalade beräkningsstorleken för en arbetarpool med 10 instanser kan det ta upp till 10 timmar att slutföra.
* Om du ändrar storleken på de beräkningsresurser som används i en arbetspool kommer du att orsaka kalla starter av de appar som körs i den arbetarpoolen.

Det snabbaste sättet att ändra beräkningsresursstorleken för en arbetspool som inte kör några appar är att:

* Skala ner antalet arbetare till 2.  Den minsta nedskalningsstorleken i portalen är 2. Det tar några minuter att dela upp dina instanser. 
* Välj den nya beräkningsstorleken och antalet instanser. Härifrån tar det upp till 2 timmar att slutföra.

Om dina appar kräver en större beräkningsresursstorlek kan du inte dra nytta av den tidigare vägledningen. I stället för att ändra storleken på arbetspoolen som är värd för dessa appar kan du fylla en annan arbetspool med arbetare av önskad storlek och flytta dina appar över till poolen.

* Skapa ytterligare instanser av den beräkningsstorlek som behövs i en annan arbetspool. Detta tar upp till en timme att slutföra.
* Tilldela om apptjänstplanerna som är värdar för de appar som behöver en större storlek till den nyligen konfigurerade arbetspoolen. Detta är en snabb operation som bör ta mindre än en minut att slutföra.  
* Skala ned den första arbetarpoolen om du inte längre behöver de oanvända instanserna. Den här åtgärden tar några minuter att slutföra.

**Automatisk skalning:** Ett av de verktyg som kan hjälpa dig att hantera förbrukningen av beräkningsresurser är automatisk skalning. Du kan använda automatisk skalning för frontend- eller arbetspooler. Du kan göra saker som att öka dina instanser av någon pooltyp på morgonen och minska den på kvällen. Eller kanske du kan lägga till instanser när antalet arbetare som är tillgängliga i en arbetspool sjunker under ett visst tröskelvärde.

Om du vill ange regler för automatisk skalning runt beräkningsresurspoolmått bör du tänka på den tid som etableringen kräver. Mer information om automatisk skalning av apptjänstmiljöer finns i [Konfigurera automatisk skalning i en apptjänstmiljö][ASEAutoscale].

### <a name="storage"></a>Lagring
Varje ASE är konfigurerad med 500 GB lagringsutrymme. Det här utrymmet används i alla appar i ASE. Det här lagringsutrymmet är en del av ASE och kan för närvarande inte växlas för att använda lagringsutrymmet. Om du gör justeringar i din virtuella nätverksroutning eller säkerhet måste du fortfarande tillåta åtkomst till Azure Storage – eller så kan inte ASE fungera.

### <a name="database"></a>Databas
Databasen innehåller den information som definierar miljön, samt information om de appar som körs i den. Även detta är en del av den Azure-ägda prenumerationen. Det är inte något som du har en direkt förmåga att manipulera. Om du gör justeringar i din virtuella nätverksroutning eller säkerhet måste du fortfarande tillåta åtkomst till SQL Azure – eller så kan inte ASE fungera.

### <a name="network"></a>Nätverk
Det virtuella nätverket som används med din ASE kan vara ett som du gjorde när du skapade ASE eller ett som du gjorde i förväg. När du skapar undernätet när ASE skapas, tvingar det ASE att vara i samma resursgrupp som det virtuella nätverket. Om du behöver resursgruppen som används av ASE för att vara annorlunda än ditt virtuella nätverk måste du skapa din ASE med hjälp av en resurshanterares mall.

Det finns vissa begränsningar för det virtuella nätverket som används för en ASE:

* Det virtuella nätverket måste vara ett regionalt virtuellt nätverk.
* Det måste finnas ett undernät med 8 eller fler adresser där ASE distribueras.
* När ett undernät har använts för att vara värd för ett ASE kan inte undernätets adressintervall ändras. Därför rekommenderar vi att undernätet innehåller minst 64 adresser för att rymma eventuell framtida ASE-tillväxt.
* Det kan inte finnas något annat i undernätet än ASE.

Till skillnad från den värdbaserade tjänsten som innehåller ASE är det [virtuella nätverket][virtualnetwork] och undernätet under användarkontroll.  Du kan administrera ditt virtuella nätverk via användargränssnittet för virtuellt nätverk eller PowerShell.  En ASE kan distribueras i ett virtuella nätverk för klass- eller resurshanteraren.  Portal- och API-upplevelserna skiljer sig något mellan virtuella nätverk för Klassiska och Resource Manager, men ASE-upplevelsen är densamma.

Det virtuella nätverk som används för att vara värd för ett ASE kan använda antingen privata RFC1918 IP-adresser eller använda offentliga IP-adresser.  Om du vill använda ett IP-intervall som inte omfattas av RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) måste du skapa ditt virtuella nätverk och undernät som ska användas av din ASE innan ASE skapas.

Eftersom den här funktionen placerar Azure App Service i ditt virtuella nätverk, betyder det att dina appar som finns i din ASE nu kan komma åt resurser som görs tillgängliga via ExpressRoute eller plats-till-plats virtuella privata nätverk (VPN) direkt. Apparna som finns i apptjänstmiljön kräver inte ytterligare nätverksfunktioner för att komma åt resurser som är tillgängliga för det virtuella nätverket som är värd för din App Service-miljö. Det innebär att du inte behöver använda VNET-integrering eller hybridanslutningar för att komma åt resurser i eller ansluten till det virtuella nätverket. Du kan fortfarande använda båda dessa funktioner men för att komma åt resurser i nätverk som inte är anslutna till ditt virtuella nätverk.

Du kan till exempel använda VNET-integrering för att integrera med ett virtuellt nätverk som finns i din prenumeration men som inte är anslutet till det virtuella nätverk som DIN ASE finns i. Du kan fortfarande också använda hybridanslutningar för att komma åt resurser som finns i andra nätverk, precis som vanligt.  

Om du har konfigurerat ditt virtuella nätverk med en ExpressRoute VPN bör du vara medveten om några av de routningsbehov som en ASE har. Det finns några användardefinierade vägkonfigurationer (UDR) som är inkompatibla med en ASE. Mer information om hur du kör en ASE i ett virtuellt nätverk med ExpressRoute finns [i Köra en apptjänstmiljö i ett virtuellt nätverk med ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Säkra inkommande trafik
Det finns två primära metoder för att styra inkommande trafik till din ASE.  Du kan använda Nätverkssäkerhetsgrupper (NSG) för att styra vilka IP-adresser som kan komma åt din ASE enligt beskrivningen här Så här styr du [inkommande trafik i en App Service-miljö](app-service-app-service-environment-control-inbound-traffic.md) och du kan också konfigurera din ASE med en intern belastningsutjämnare (ILB).  Dessa funktioner kan också användas tillsammans om du vill begränsa åtkomsten med NSG till din ILB ASE.

När du skapar en ASE skapas en VIP i ditt virtuella nätverk.  Det finns två VIP-typer, externa och interna.  När du skapar en ASE med en extern VIP kommer dina appar i din ASE att vara tillgängliga via en internetdigerbar IP-adress. När du väljer Intern kommer din ASE att konfigureras med en ILB och kommer inte att vara direkt tillgänglig för internet.  En ILB ASE kräver fortfarande en extern VIP men den används bara för Azure-hantering och underhållsåtkomst.  

Under ILB ASE skapas du ange underdomän som används av ILB ASE och måste hantera din egen DNS för underdomänen du anger.  Eftersom du anger underdomännamnet måste du också hantera certifikatet som används för HTTPS-åtkomst.  När ASE har skapats uppmanas du att ange certifikatet.  Mer information om hur du skapar och använder en ILB ASE [läser använda en intern belastningsutjämnare med en App Service Environment][ILBASE]. 

## <a name="portal"></a>Portalen
Du kan hantera och övervaka din App Service-miljö med hjälp av användargränssnittet i Azure-portalen. Om du har en ASE kommer det troligen att visas symbolen för App Service i sidofältet. Den här symbolen används för att representera App Service-miljöer i Azure-portalen:

![Symbol för App Service-miljö][1]

Om du vill öppna användargränssnittet som visar alla apptjänstmiljöer kan du använda ikonen eller välja symbolen Chevron (">") längst ned i sidofältet för att välja App servicemiljöer. Genom att välja ett av de ASE-listade öppnar du användargränssnittet som används för att övervaka och hantera det.

![Användargränssnitt för övervakning och hantering av din App Service-miljö][2]

Det här första bladet visar vissa egenskaper för DIN ASE, tillsammans med ett måttdiagram per resurspool. Några av de egenskaper som visas i **Essentials-blocket** är också hyperlänkar som öppnar bladet som är associerat med det. Du kan till exempel välja namnet på **det virtuella nätverket** för att öppna användargränssnittet som är associerat med det virtuella nätverk som ASE körs i. **App Service planer** och **apps** varje öppna blad som listar dessa objekt som finns i din ASE.  

### <a name="monitoring"></a>Övervakning
Diagrammen gör att du kan se en mängd olika prestandamått i varje resurspool. För frontend-poolen kan du övervaka den genomsnittliga processorn och minnet. För arbetspooler kan du övervaka den kvantitet som används och den kvantitet som är tillgänglig.

Flera App Service-planer kan använda arbetarna i en arbetspool. Arbetsbelastningen distribueras inte på samma sätt som med frontend-servrarna, så cpu- och minnesanvändningen ger inte mycket i vägen för användbar information. Det är viktigare att spåra hur många arbetare som du har använt och är tillgängliga - särskilt om du hanterar det här systemet för andra att använda.  

Du kan också använda alla mått som kan spåras i diagrammen för att ställa in aviseringar. Att ställa in aviseringar här fungerar på samma sätt som på andra ställen i App Service. Du kan ange en avisering från antingen **gränssnittsdelen Aviseringar** eller från att borra i ett användargränssnitt för mått och välja **Lägg till avisering**.

![Användargränssnitt för mått][3]

De mått som just har diskuterats är måtten för App Service Environment. Det finns också mått som är tillgängliga på apptjänstplansnivå. Det är där övervakning CPU och minne gör mycket vettigt.

I en ASE är alla App Service-planer dedikerade App Service-planer. Det innebär att de enda appar som körs på värdarna som tilldelats apptjänstplanen är apparna i apptjänstplanen. Om du vill se information om appserviceplanen tar du upp appserviceplanen från någon av listorna i ASE-användargränssnittet eller från **Appens tjänstplaner** (som listar dem alla).   

### <a name="settings"></a>Inställningar
I ASE-bladet finns ett **inställningsavsnitt** som innehåller flera viktiga funktioner:

**Egenskaper** > **för**inställningar : Bladet **Inställningar** öppnas automatiskt när du tar upp ASE-bladet. Högst upp finns **Egenskaper**. Det finns ett antal objekt här som är överflödiga för vad du ser i **Essentials**, men vad som är mycket användbart är **virtuell IP-adress**, samt **utgående IP-adresser**.

![Inställningar blad och egenskaper][4]

**Inställningar** > **IP-adresser:** När du skapar en SSL-app (IP Secure Sockets Layer) i DIN ASE behöver du en IP SSL-adress. För att få en behöver din ASE IP SSL-adresser som den äger som kan allokeras. När en ASE skapas har den en IP SSL-adress för detta ändamål, men du kan lägga till fler. Det finns en avgift för ytterligare IP SSL-adresser, som visas i [App Service-priser][AppServicePricing] (i avsnittet om SSL-anslutningar). Det extra priset är IP SSL-priset.

**Inställningar** > **Front End Pool** / **Worker Pools:** Var och en av dessa resurspoolblad ger möjlighet att bara se information om resurspoolen, förutom att tillhandahålla kontroller för att fullt ut skala den resurspoolen.  

Basbladet för varje resurspool ger ett diagram med mått för resurspoolen. Precis som med diagrammen från ASE-bladet kan du gå in i diagrammet och ställa in varningar som önskat. Ställa in en avisering från ASE-bladet för en viss resurspool gör samma sak som att göra det från resurspoolen. Från **bladet Inställningar för arbetspoolen** har du tillgång till alla appar eller apptjänstplaner som körs i den här arbetarpoolen.

![Användargränssnitt för arbetspoolinställningar][5]

### <a name="portal-scale-capabilities"></a>Funktioner för portalskala
Det finns tre skaloperationer:

* Ändra antalet IP-adresser i ASE som är tillgängliga för IP SSL-användning.
* Ändra storleken på beräkningsresursen som används i en resurspool.
* Ändra antalet beräkningsresurser som används i en resurspool, antingen manuellt eller genom automatisk skalning.

I portalen finns det tre sätt att styra hur många servrar du har i resurspoolerna:

* En skalning från huvudsasssbladet upptill. Du kan göra konfigurationsändringar i flera skalningskonfigurationer i frontend- och arbetspoolerna. De tillämpas alla som en enda operation.
* En manuell skalningsåtgärd från det enskilda **resurspoolen Skala** blad, som är under **Inställningar**.
* Automatisk skalning, som du ställer in från det enskilda **resurspoolen Skala** bladet.

Om du vill använda skalningsåtgärden på ASE-bladet drar du skjutreglaget till den kvantitet du vill ha och sparar. Det här användargränssnittet stöder också att ändra storleken.  

![Skala användargränssnitt][6]

Om du vill använda funktionerna för manuell skalning eller automatisk skalning i en viss resurspool går du till **Inställningar** > **Front End Pool** / **Worker Pools** efter behov. Öppna sedan poolen som du vill ändra. Gå till **Inställningar** > **Skala ut** eller **inställningar** > skala**upp**. Med bladet **Scale Out** kan du styra instanskvantiteten. **Skala upp** kan du styra resursstorleken.  

![Användargränssnitt för skalningsinställningar][7]

## <a name="fault-tolerance-considerations"></a>Feltoleransöverväganden
Du kan konfigurera en apptjänstmiljö så att den använder upp till 55 totala beräkningsresurser. Av dessa 55 beräkningsresurser kan endast 50 användas för att vara värd för arbetsbelastningar. Anledningen till detta är dubbelt. Det finns minst 2 frontend-beräkningsresurser.  Det lämnar upp till 53 för att stödja fördelningen av arbetarpooler. För att kunna ge feltolerans måste du ha ytterligare en beräkningsresurs som allokeras enligt följande regler:

* Varje arbetspool behöver minst 1 ytterligare beräkningsresurs som inte är tillgänglig för att tilldelas en arbetsbelastning.
* När antalet beräkningsresurser i en arbetarpool går över ett visst värde krävs en annan beräkningsresurs för feltolerans. Detta är inte fallet i front-end poolen.

Inom en enskild arbetspool är kraven för feltolerans som gäller för ett givet värde på X-resurser som tilldelats en arbetspool:

* Om X är mellan 2 och 20 är mängden användbara beräkningsresurser som du kan använda för arbetsbelastningar X-1.
* Om X är mellan 21 och 40 är mängden användbara beräkningsresurser som du kan använda för arbetsbelastningar X-2.
* Om X är mellan 41 och 53 är mängden användbara beräkningsresurser som du kan använda för arbetsbelastningar X-3.

Minsta fotavtryck har 2 front-end servrar och 2 arbetare.  Med ovanstående uttalanden då, här är några exempel för att klargöra:  

* Om du har 30 arbetare i en enda pool kan 28 av dem användas som värd för arbetsbelastningar.
* Om du har 2 arbetare i en enda pool kan 1 användas för att vara värd för arbetsbelastningar.
* Om du har 20 arbetare i en enda pool kan 19 användas för att vara värd för arbetsbelastningar.  
* Om du har 21 arbetare i en enda pool kan fortfarande bara 19 användas för att vara värd för arbetsbelastningar.  

Feltolerans aspekten är viktigt, men du måste hålla det i åtanke när du skala över vissa tröskelvärden. Om du vill lägga till mer kapacitet som går från 20 instanser går du sedan till 22 eller högre eftersom 21 inte lägger till någon mer kapacitet. Detsamma gäller att gå över 40, där nästa nummer som lägger till kapacitet är 42.  

## <a name="deleting-an-app-service-environment"></a>Ta bort en apptjänstmiljö
Om du vill ta bort en App Service-miljö använder du helt enkelt åtgärden **Ta bort** högst upp i bladet App Service Environment. När du gör detta uppmanas du att ange namnet på apptjänstmiljön för att bekräfta att du verkligen vill göra detta. Observera att när du tar bort en App Service-miljö tar du bort allt innehåll även i den.  

![Ta bort ett användargränssnitt för apptjänstmiljö][9]  

## <a name="getting-started"></a>Komma igång
Lär dig hur du skapar [en apptjänstmiljö](app-service-web-how-to-create-an-app-service-environment.md).

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
