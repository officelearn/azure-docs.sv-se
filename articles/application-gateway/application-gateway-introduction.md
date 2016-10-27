<properties
   pageTitle="Introduktion till Application Gateway | Microsoft Azure"
   description="Den här sidan ger en översikt över Application Gateway-tjänsten layer 7 belastningsutjämning, inklusive gatewaystorlekar, HTTP-belastningsutjämning, cookie-baserad sessionstillhörighet och SSL-avlastning."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="gwallace"/>


# <a name="application-gateway-overview"></a>Översikt över Application Gateway

## <a name="what-is-application-gateway"></a>Vad är Application Gateway

Microsoft Azure Application Gateway erbjuder ADC (Application Delivery Controller) som en tjänst, vilket ger flera layer 7-belastningsutjämningsfunktioner. Enkelt sett så fungerar det genom att acceptera trafik och dirigera trafiken till rätt serverdelsinstanser baserat på definierade regler.

Belastningsutjämning låter IT-administratörer och utvecklare skapa rouringregler för nätverkstrafik baserat på HTTP-protokollet.  Application Gateway-tjänsten har hög tillgänglighet och är mätbar. För SLA och prissättning, kan du referera till sidorna [SLA](https://azure.microsoft.com/support/legal/sla/) och [prissättning](https://azure.microsoft.com/pricing/details/application-gateway/).

Application Gateway tillämper routingregler till HTTP-trafik, vilket ger layer 7 (HTTP)-belastningsutjämning. När du skapar en Application Gateway, kopplas en slutpunkt (VIP) och används som offentlig IP-adress för inkommande nätverkstrafik. Azure tillhandahåller layer 4 belastningsutjämning via Azure Load Balancer som arbetar på transportnivån (TCP/UDP) och gör att alla inkommande nätverkstrafik belastningsutjämnas till Application Gateway-tjänsten. Application Gateway routar HTTP-trafik baserat på sin konfiguration, oavsett om det är en virtuell dator, molntjänst eller en extern IP-adress.

## <a name="features"></a>Funktioner

Application Gateway stöder för närvarande programleverans med layer 7 med följande funktioner:

- **[Webbprogrambrandvägg (förhandsvisning)](application-gateway-webapplicationfirewall-overview.md)** – webbprogrambrandväggen (WAF) i Azure Application Gateway skyddar webbprogram från vanliga webbaserade attacker som SQL-injection, cross-site skriptattacker och övertagningar av sessioner.
- **HTTP-belastningsutjämning** –Application Gateway erbjuder belastningsutjämning med resursallokering. Belastningsutjämning görs Layer 7 och används enbart för HTTP(S)-trafik.
- **Cookie-baserad sessionstillhörighet** – Den här funktionen är användbar när du vill behålla en användarsession på samma serverdel. Genom att använda gatewayhanterade cookies, kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Den här funktionen är viktig i de fall där sessionstillstånd har sparats lokalt på serverdels-servern för en användarsession.
- **[Secure Sockets Layer (SSL) avlastning](application-gateway-ssl-arm.md)** – Funktionen sköter den kostsamma uppgiften att dekryptera HTTPS-trafik åt dina webbservrar. Genom att terminera SSL-anslutningen vid Application Gateway och vidarebefordra begäran till servern okrypterad så behöver inte webbservern sköta avkrypteringen.  Application Gateway återkrypterar svaret innan det skickas tillbaka till klienten. Den här funktionen är användbar i scenarier där serverdelen befinner sig på samma säkrade virtuella nätverk som Application Gateway i Azure.
- **[Slutpunkt-till-slutpunkt SSL](application-gateway-backend-ssl.md)** –Application Gateway stöder kryptering av trafik från slutpunkt-till-slutpunkt. Application Gateway gör det genom att terminera SSL-anslutningen vid application gateway. Gatewayen implementerar därefter routningsreglerna för trafiken, återkrypterar paketet och vidarebefordrar det till den korrekta serverdelen baserat på de routningsregler som definierats. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren.
- **[URL-baserad innehållsroutning](application-gateway-url-route-overview.md)** – Den här funktionen ger möjlighet att använda olika serverdels-servrar för olika trafik. Trafik för en mapp på webbservern eller en CDN kan dirigeras till olika serverdelar, vilket minskar onödig belastning på serverdelarna som inte servar specifikt innehåll.
- **[Routing till flera platser](application-gateway-multi-site-overview.md)** –Application gateway låter dig konsolidera upp till 20 webbplatser på en enda Application Gateway.
- **[Websocket-stöd](application-gateway-websocket.md)** – En annan bra funktion i Application Gateway är inbyggt stöd för Websocket.
- **[Övervakning av hälsotillstånd](application-gateway-probe-overview.md)** –Application gateway övervakar standard hälsotillstånd för serverdelsresurser och anpassade avsökningar för att övervaka mer specifika scenarier.

## <a name="benefits"></a>Fördelar

Application Gateway är användbar för:

- Program som kräver begäranden från samma användare-/klientsession för att nå samma virtuella serverdelsdator. Exempel på sådana program kan vara appar med shoppingvagnar och e-postwebbservrar.
- Program som vill frigöra server webbgrupper från SSL-anslutningsarbetet.
- Program, till exempel ett nätverk för innehållsleverans, som kräver flera HTTP-begäranden för att samma tidskrävande TCP-anslutningen ska routas eller belastningsutjämnad till olika serverdels-servrar.
- Program som stöder websocket-trafik
- Skydda webbprogrammen från vanliga webbaserade attacker som SQL-injection, cross-site skriptattacker och sessionsövertaganden.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Gateway-storlekar och instanser

Application Gateway finns för närvarande i tre storlekar: liten, medel eller stor. Smål instansstorlekar är avsedda för utvecklings- och testningsscenarier.

Det finns för tillfället två SKU:er för Application Gateway: WAF och Standard.

Du kan skapa upp till 50 Application Gateways per prenumeration och varje Application Gateway kan ha upp till 10 instanser styck. Varje Application Gateway kan bestå av 20 http-lyssnare. Application Gateway belastningsutjämning som en Azure-hanterad tjänst tillåter etablering av en belastningsutjämnare för layer 7 bakom Azures belastningsutjämnare som programvara.

Följande tabell visar ett genomsnittligt prestanda-dataflöde för varje Application Gateway-instans:

| Sidsvar för serverdel | Liten | Medel | Stor|
|---|---|---|---|
| 6K | 7.5 Mbit/s | 13 Mbit/s | 50 Mbit/s |
|100K | 35 Mbit/s | 100 Mbit/s| 200 Mbit/s |

>[AZURE.NOTE] De här värdena är genomsnittliga värden för ett Application Gateway-dataflöde. Det faktiska dataflödet beror på olika miljöfaktorer som genomsnittlig sidstorlek, plats för serverdelsinstanserna och bearbetningstid för att serva en sida. För exakta prestandasiffror bör du köra dina egna tester. De här värdena tillhandahålls bara för vägledning vid kapacitetsplanering.

## <a name="health-monitoring"></a>Hälsoövervakning

Azure Application Gateway övervakar automatiskt hälsan för serverdels-instanser via grundläggande eller anpassade hälsotillståndsavsökningar. Med hjälp av hälsotillståndsavsökningar säkerställer det här att enbart felfria värdar svarar på trafik. Mer information finns i [Översikt över Application Gateway hälsoövervakning](application-gateway-probe-overview.md).

## <a name="configuring-and-managing"></a>Konfigurera och hantera

Som slutpunkt kan Application Gateway ha en offentlig eller privat IP-Adress, eller bägge när det är konfigurerat. Application Gateway konfigureras inuti ett virtuellt nätverk i sitt eget undernät. Undernätet som skapas eller används för Application Gateway kan inte innehålla några andra typer av resurser. De enda resurserna som tillåts i undernätet är andra Application Gatewayer. För att skydda dina serverdelsresurser så kan serverdels-servern vara innesluten inom ett annat undernät i samma virtuella nätverk som Application Gateway. Det här extra undernätet krävs inte för serverdelsprogrammen så länge Application Gateway kan nå IP-adresssen så kan den tillhandahålla ADC-funktioner för serverdels-servrarna.

Du kan skapa och hantera en Application Gateway med hjälp av REST API:er, PowerShell-cmdletar, Azure CLI eller [Azure Portal](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

När du läst om Application Gateway så kan du [skapa en application gateway](application-gateway-create-gateway-portal.md), eller så kan du [skapa en SSL-avlastning för Application Gateway](application-gateway-ssl-arm.md) för att belastningsutjämna HTTPS-anslutningar.

För att läsa hur man skapar en Application Gateway med hjälp av URL-baserad innehållsroutning, kan du gå till [Skapa en Application Gateway med hjälp av URL-baserad routning](application-gateway-create-url-route-arm-ps.md).




<!--HONumber=Oct16_HO3-->


