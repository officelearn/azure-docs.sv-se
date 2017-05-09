---
title: Introduktion till Azure Application Gateway | Microsoft Docs
description: "Den här sidan ger en översikt över Application Gateway-tjänsten layer 7 belastningsutjämning, inklusive gatewaystorlekar, HTTP-belastningsutjämning, cookie-baserad sessionstillhörighet och SSL-avlastning."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: b37a2473-4f0e-496b-95e7-c0594e96f83e
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: e9dfb5a744a7c63ef9805b1341236c4f3c57ec4d
ms.contentlocale: sv-se
ms.lasthandoff: 05/02/2017


---
# <a name="overview-of-application-gateway"></a>Översikt över Application Gateway

Microsoft Azure Application Gateway är en dedikerad virtuell installation som erbjuder ADC (Application Delivery Controller) som en tjänst, vilket ger flera layer 7-belastningsutjämningsfunktioner för ditt program. Detta gör det möjligt för kunder att optimera webbservergruppens produktivitet genom att avlasta CPU-intensiv SSL-avslutning till Application Gateway. Här finns även andra layer 7-routningsfunktioner, till exempel resursallokeringsdistribution av inkommande trafik, cookiebaserad sessionstillhörighet, URL-sökvägsbaserad routning och möjligheten att vara värd för flera webbplatser bakom en enda Application Gateway. En brandvägg för webbaserade program (WAF) ingår också i Application Gateway WAF-SKU:n. Den skyddar webbprogram mot vanliga säkerhetsproblem och hot på Internet. Application Gateway kan konfigureras som internetuppkopplad gateway, endast intern gateway eller en kombination av båda. 

![scenario](./media/application-gateway-introduction/scenario.png)

## <a name="features"></a>Funktioner

Application Gateway stöder för närvarande följande funktioner:


* **[Brandvägg för webbaserade program](application-gateway-webapplicationfirewall-overview.md)** – Brandväggen för webbaserade program (WAF) i Azure Application Gateway skyddar webbprogram från vanliga webbaserade attacker som SQL-inmatning, skriptkörning över flera webbplatser och övertagningar av sessioner.
* **HTTP-belastningsutjämning** –Application Gateway erbjuder belastningsutjämning med resursallokering. Belastningsutjämning görs Layer 7 och används enbart för HTTP(S)-trafik.
* **Cookie-baserad sessionstillhörighet** – Den här funktionen är användbar när du vill behålla en användarsession på samma serverdel. Genom att använda gatewayhanterade cookies, kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Den här funktionen är viktig i de fall där sessionstillstånd har sparats lokalt på serverdels-servern för en användarsession.
* **[Secure Sockets Layer (SSL) avlastning](application-gateway-ssl-arm.md)** – Funktionen sköter den kostsamma uppgiften att dekryptera HTTPS-trafik åt dina webbservrar. Genom att avsluta SSL-anslutningen vid Application Gateway och vidarebefordra begäran till servern okrypterad så behöver inte webbservern sköta dekrypteringen.  Application Gateway återkrypterar svaret innan det skickas tillbaka till klienten. Den här funktionen är användbar i scenarier där serverdelen befinner sig på samma säkrade virtuella nätverk som Application Gateway i Azure.
* **[Slutpunkt-till-slutpunkt SSL](application-gateway-backend-ssl.md)** –Application Gateway stöder kryptering av trafik från slutpunkt-till-slutpunkt. Application Gateway gör det genom att terminera SSL-anslutningen vid application gateway. Gatewayen implementerar därefter routningsreglerna för trafiken, återkrypterar paketet och vidarebefordrar det till den korrekta serverdelen baserat på de routningsregler som definierats. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren.
* **[URL-baserad innehållsroutning](application-gateway-url-route-overview.md)** – Den här funktionen ger möjlighet att använda olika serverdels-servrar för olika trafik. Trafik för en mapp på webbservern eller en CDN kan dirigeras till olika serverdelar, vilket minskar onödig belastning på serverdelarna som inte servar specifikt innehåll.
* **[Routing till flera platser](application-gateway-multi-site-overview.md)** –Application gateway låter dig konsolidera upp till 20 webbplatser på en enda Application Gateway.
* **[Websocket-stöd](application-gateway-websocket.md)** – En annan bra funktion i Application Gateway är inbyggt stöd för Websocket.
* **[Övervakning av hälsotillstånd](application-gateway-probe-overview.md)** –Application gateway övervakar standard hälsotillstånd för serverdelsresurser och anpassade avsökningar för att övervaka mer specifika scenarier.
* **[Avancerad diagnostik](application-gateway-diagnostics.md)** – Application gateway ger fullständig diagnostik och åtkomst till loggar. Brandväggsloggar är tillgängliga för Application Gateway-resurser som har WAF aktiverat.

## <a name="benefits"></a>Fördelar

Application Gateway är användbar för:

* Program som kräver begäranden från samma användare-/klientsession för att nå samma virtuella serverdelsdator. Exempel på sådana applikationer kan vara shoppingvagnsprogram och e-postwebbservrar.
* Tar bort omkostnader för SSL-avslutning för webbservergrupper.
* Program, till exempel ett nätverk för innehållsleverans, som kräver flera HTTP-begäranden för att samma tidskrävande TCP-anslutningen ska routas eller belastningsutjämnad till olika serverdels-servrar.
* Program som stöder websocket-trafik
* Skydda webbprogrammen från vanliga webbaserade attacker som SQL-injection, cross-site skriptattacker och sessionsövertaganden.
* Logisk distribuering av trafik baserat på olika dirigeringsvillkor som url-sökväg eller domänrubriker.

Application Gateway är helt Azure-hanterat, skalbart och med hög tillgänglighet. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner. När du skapar en programgateway, kopplas en slutpunkt (offentlig VIP eller intern ILB IP) och används för inkommande nätverkstrafik. Denna VIP eller ILB IP tillhandahålls av Azure Load Balancer som arbetar på transportnivån (TCP/UDP) och gör att all inkommande nätverkstrafik belastningsutjämnas till Application Gateway-arbetsinstanser. Application Gateway dirigerar sedan HTTP-/HTTPS-trafik baserat på konfigurationen, oavsett om det är en virtuell dator, molntjänst eller en intern eller extern IP-adress.

Application Gateway belastningsutjämning som en Azure-hanterad tjänst tillåter etablering av en belastningsutjämnare för layer 7 bakom Azures belastningsutjämnare som programvara. Traffic Manager kan användas för att färdigställa scenariot som visas i följande bild, där Traffic Manager tillhandahåller omdirigering och åtkomst för trafik till flera Application Gateway-resurser i olika områden, medan Application Gateway ger layer 7 belastningsutjämning i flera regioner. Ett exempel på det här scenariot finns här: [Använda tjänster för belastningsutjämning i Azure-molnet](../traffic-manager/traffic-manager-load-balancing-azure.md)

![scenario för traffic manager och application gateway](./media/application-gateway-introduction/tm-lb-ag-scenario.png)

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Gateway-storlekar och instanser

Application Gateway finns för närvarande i tre storlekar: **liten**, **medel** och **stor**. Smål instansstorlekar är avsedda för utvecklings- och testningsscenarier.

Du kan skapa upp till 50 Application Gateways per prenumeration och varje Application Gateway kan ha upp till 10 instanser styck. Varje Application Gateway kan bestå av 20 http-lyssnare. En fullständig lista över gränserna för programgateways finns i avsnittet om [gränser för Application Gateway-tjänsten](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Följande tabell visar ett genomsnittligt prestanda-dataflöde för varje Application Gateway-instans som har SSL-avlastning aktiverat:

| Sidsvar för serverdel | Liten | Medel | Stor |
| --- | --- | --- | --- |
| 6K |7.5 Mbit/s |13 Mbit/s |50 Mbit/s |
| 100K |35 Mbit/s |100 Mbit/s |200 Mbit/s |

> [!NOTE]
> De här värdena är genomsnittliga värden för ett Application Gateway-dataflöde. Det faktiska dataflödet beror på olika miljöfaktorer som genomsnittlig sidstorlek, plats för serverdelsinstanserna och bearbetningstid för att serva en sida. Du bör köra egna test för exakta prestandavärden. Dessa värden är bara för vägledning vid kapacitetsplanering.

## <a name="health-monitoring"></a>Hälsoövervakning

Azure Application Gateway övervakar automatiskt hälsan för serverdels-instanser via grundläggande eller anpassade hälsotillståndsavsökningar. Med hjälp av hälsotillståndsavsökningar säkerställer det här att enbart felfria värdar svarar på trafik. Mer information finns i [Översikt över Application Gateway hälsoövervakning](application-gateway-probe-overview.md).

## <a name="configuring-and-managing"></a>Konfigurera och hantera

Som slutpunkt kan Application Gateway ha en offentlig eller privat IP-Adress, eller bägge när det är konfigurerat. Application Gateway konfigureras inuti ett virtuellt nätverk i sitt eget undernät. Undernätet som skapas eller används för Application Gateway kan inte innehålla några andra typer av resurser. De enda resurserna som tillåts i undernätet är andra Application Gatewayer. För att skydda dina serverdelsresurser kan serverdels-servern vara innesluten inom ett annat undernät i samma virtuella nätverk som Application Gateway. Det här extra undernätet krävs inte för serverdelsprogrammen så länge Application Gateway kan nå IP-adresssen så kan den tillhandahålla ADC-funktioner för serverdels-servrarna. 

Du kan skapa och hantera en Application Gateway med hjälp av REST API:er, PowerShell-cmdletar, Azure CLI eller [Azure Portal](https://portal.azure.com/). Om du vill veta mer om Application Gateway finns en lista med vanliga frågor och svar i [Vanliga frågor och svar om Application Gateway](application-gateway-faq.md).

## <a name="pricing"></a>Prissättning

Priserna är baserade på avgifter per gatewayinstans per timme och databearbetningsavgifter. Gatewaypriset per timme för WAF SKU skiljer sig från avgifterna för Standard-SKU (se [Priser för Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/)). Databearbetningsavgifterna är desamma.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Vanliga frågor om Application Gateway finns i [Vanliga frågor och svar om Application Gateway](application-gateway-faq.md).

## <a name="next-steps"></a>Nästa steg

När du läst om Application Gateway så kan du [skapa en application gateway](application-gateway-create-gateway-portal.md), eller så kan du [skapa en SSL-avlastning för Application Gateway](application-gateway-ssl-arm.md) för att belastningsutjämna HTTPS-anslutningar.

För att läsa hur man skapar en Application Gateway med hjälp av URL-baserad innehållsroutning, kan du gå till [Skapa en Application Gateway med hjälp av URL-baserad routning](application-gateway-create-url-route-arm-ps.md).

