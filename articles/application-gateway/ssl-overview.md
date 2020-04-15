---
title: Aktivera på TLS på Azure Application Gateway
description: Den här artikeln är en översikt över Programmet Gateway till TLS-stöd.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311849"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Översikt över TLS-avslutning och på TLS med Application Gateway

Transport Layer Security (TLS), tidigare känd som Secure Sockets Layer (SSL), är standardsäkerhetstekniken för att upprätta en krypterad länk mellan en webbserver och en webbläsare. Den här länken säkerställer att alla data som skickas mellan webbservern och webbläsare förblir privata och krypterade. Programgateway stöder både TLS-avslutning vid gatewayen samt slutpunkt till TLS-kryptering.

## <a name="tls-termination"></a>TLS-avslutning

Application Gateway stöder TLS-avslutning vid gatewayen, varefter trafiken vanligtvis flödar okrypterad till serverdaservrarna. Det finns ett antal fördelar med att göra TLS-avslutning vid programgatewayen:

- **Förbättrad prestanda** – Den största prestandaträffen när du gör TLS-dekryptering är det första handskakningen. För att förbättra prestanda cachelagrar servern som gör dekryptering TLS-sessions-ID:er och hanterar TLS-sessionsbiljetter. Om detta görs vid programgatewayen kan alla begäranden från samma klient använda de cachelagrade värdena. Om det görs på serverdaservrarna måste klienten autentiseras varje gång klientens begäranden går till en annan server. Användningen av TLS-biljetter kan bidra till att minska problemet, men de stöds inte av alla klienter och kan vara svåra att konfigurera och hantera.
- **Bättre utnyttjande av backend-servrar** - SSL / TLS bearbetning är mycket CPU-intensiv, och blir mer intensiv som viktiga storlekar ökar. Om du tar bort det här arbetet från serveringsservrarna kan de fokusera på vad de är mest effektiva på och leverera innehåll.
- **Intelligent routning** – Genom att dekryptera trafiken har programgatewayen åtkomst till begärandeinnehållet, till exempel rubriker, URI och så vidare, och kan använda dessa data för att dirigera begäranden.
- **Certifikathantering** – Certifikat behöver bara köpas och installeras på programgatewayen och inte alla servergruppsservrar. Detta sparar både tid och pengar.

För att konfigurera TLS-avslutning måste ett TLS/SSL-certifikat läggas till i lyssnaren för att programgatewayen ska kunna härleda en symmetrisk nyckel enligt TLS/SSL-protokollspecifikationen. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. TLS/SSL-certifikatet måste vara i PFX-format (Personal Information Exchange). Med det här filformatet kan du exportera den privata nyckeln som krävs av programgatewayen för att utföra kryptering och dekryptering av trafik.

> [!NOTE] 
>
> Programgateway ger inte någon möjlighet att skapa ett nytt certifikat eller skicka en certifikatbegäran till en certifikatutfärdare.

För att TLS-anslutningen ska fungera måste du se till att TLS/SSL-certifikatet uppfyller följande villkor:

- Att det aktuella datumet och tiden ligger inom datumintervallet "Giltig från" och "Giltig till" på certifikatet.
- Att certifikatets ”vanliga namn” (CN) matchar värdhuvudet i begäran. Exempelvis om klienten gör en begäran till `https://www.contoso.com/`, måste CN vara `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certifikat som stöds för TLS-avslutning

Programgateway stöder följande typer av certifikat:

- CA-certifikat (certifikatutfärdare): Ett CA-certifikat är ett digitalt certifikat som utfärdats av en certifikatutfärdare
- EV-certifikat (Extended Validation) : Ett EV-certifikat är ett certifikat som överensstämmer med branschstandardcertifikatriktlinjer. Detta kommer att vända webbläsaren locator bar grönt och publicera företagets namn också.
- Jokerteckencertifikat: Det här certifikatet stöder valfritt antal underdomäner baserat på *.site.com, där underdomänen skulle ersätta *. Det stöder dock inte site.com, så om användarna har tillgång till din webbplats utan att skriva den ledande "www", kommer jokertecken certifikatet inte täcka det.
- Självsignerade certifikat: Klientwebbläsare litar inte på dessa certifikat och varnar användaren om att den virtuella tjänstens certifikat inte ingår i en förtroendekedja. Självsignerade certifikat är bra för testning eller miljöer där administratörer styr klienterna och på ett säkert sätt kan kringgå webbläsarens säkerhetsaviseringar. Produktionsarbetsbelastningar bör aldrig använda självsignerade certifikat.

Mer information finns i [konfigurera TLS-avslutning med programgateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Certifikatets storlek
Kontrollera avsnittet Begränsningar för [Programgateway](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) för att veta den maximala TLS/SSL-certifikatstorleken som stöds.

## <a name="end-to-end-tls-encryption"></a>Slutpunkt till TLS-kryptering

Vissa kunder kanske inte vill ha okrypterad kommunikation till serveringsservrarna. Det kan bero på säkerhetskrav eller efterföljandekrav eller att programmet bara accepterar säkra anslutningar. För sådana program stöder programgateway slutpunkt till TLS-kryptering.

Med TLS från till kan du på ett säkert sätt överföra känsliga data till den krypterade backend-funktionen samtidigt som du drar nytta av fördelarna med belastningsutjämningsfunktioner för Layer 7 som programgateway tillhandahåller. Vissa av dessa funktioner är cookiebaserad-tillhörighet, URL-baserad routning, stöd för routning baserat på platser eller möjligheten att injicera X-Forwarded-*-huvuden.

När den konfigureras med på TLS-kommunikationsläge avslutar programgatewayen TLS-sessionerna vid gatewayen och dekrypterar användartrafik. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Programgateway initierar sedan en ny TLS-anslutning till serverdservern och krypterar om data med serverdaserverns offentliga nyckelcertifikat innan begäran överförs till serverdan. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren. TLS från till aktiveras genom att ange protokollinställning i [HTTP-inställning för backend](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) på HTTPS, som sedan tillämpas på en backend-pool.

TLS-principen gäller både frontend- och backend-trafik. I klientdelen fungerar Application Gateway som server och tillämpar principen. På backend fungerar Application Gateway som klient och skickar protokoll-/chifferinformationen som invald under TLS-handskakningen.

Programgateway kommunicerar endast med de servernamnsinstanser som antingen har vitlistat sitt certifikat med programgatewayen eller vars certifikat är signerade av välkända certifikatutfärdare där certifikatet CN matchar värdnamnet i HTTP-serverningsinställningarna. Dessa inkluderar betrodda Azure-tjänster som Azure App-tjänst webbappar och Azure API Management.

Om certifikaten för medlemmarna i serverdelspoolen inte är signerade av välkända ca-myndigheter måste varje instans i serverdelspoolen med TLS-slut till konfigureras med ett certifikat för säker kommunikation. Genom att lägga till certifikatet säkerställs att programgatewayen endast kommunicerar med kända backend-instanser. Detta säkerställer ytterligare kommunikation från slutna till.

> [!NOTE] 
>
> Konfiguration av autentiseringscertifikat krävs inte för betrodda Azure-tjänster som Azure App-tjänstwebbappar och Azure API Management.

> [!NOTE] 
>
> Certifikatet som läggs till **i servertill-HTTP-inställningen** för att autentisera serverdservrarna kan vara samma som certifikatet som lagts till **i lyssnaren** för TLS-avslutning vid programgateway eller annorlunda för förbättrad säkerhet.

![på tls-scenario][1]

I det här exemplet dirigeras begäranden som använder TLS1.2 till serverdelsservrar i Pool1 med slut-till-slut TLS.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>på slutet TLS och vitlista av certifikat

Application Gateway kommunicerar bara med kända serverdelsinstanser som har vitlistat sina certifikat med Application Gateway. För att aktivera vitlistning av certifikat så behöver du överföra den offentliga nyckeln för serverdels-serverns certifikat till Application Gateway (inte rotcertifikatet). Endast anslutningar till kända och vitlistade serverdelar tillåts sedan. De återstående serverdelar resulterar i ett gateway-fel. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste vitlistas med programgatewayen enligt beskrivningen i föregående steg innan de kan användas.

> [!NOTE]
> Konfiguration av autentiseringscertifikat krävs inte för betrodda Azure-tjänster som Azure App Service.

## <a name="end-to-end-tls-with-the-v2-sku"></a>på slutet TLS med v2 SKU

Autentiseringscertifikat har inaktuella och ersatts av betrodda rotcertifikat i Application Gateway v2 SKU. De fungerar på samma sätt som autentiseringscertifikat med några få viktiga skillnader:

- Certifikat som signerats av välkända ca-myndigheter vars CN matchar värdnamnet i HTTP-serverdelsinställningarna kräver inget ytterligare steg för att till TLS ska fungera. 

   Om serverdacertifikaten till exempel utfärdas av en välkänd certifikatutfärdare och har ett KN-contoso.com, och serverda http-inställningens värdfält också är inställt på contoso.com, krävs inga ytterligare steg. Du kan ange serverd http-inställningsprotokollet till HTTPS och både hälsoavsökningen och datasökvägen skulle vara TLS-aktiverad. Om du använder Azure App Service eller andra Azure-webbtjänster som din backend är dessa implicit betrodda också och inga ytterligare steg krävs för att avsluta TLS.
   
> [!NOTE] 
>
> För att ett TLS/SSL-certifikat ska vara tillförlitligt måste det certifikatet för serverdservern ha utfärdats av en certifikatutfärdaren som ingår i det betrodda arkivet för Application Gateway.If certifikatet inte utfärdades av en betrodd certifikatutfärdar, Application Gateway kontrollerar sedan om certifikatet för den utfärdande certifikatutfärdaren utfärdades av en betrodd certifikatutfärdare och så vidare tills antingen en betrodd certifikatutfärdare hittas (då en betrodd, säker anslutning upprättas) eller ingen betrodd certifikatutfärdare kan hittas (då application gateway kommer att markera serverda fel). Därför rekommenderas att servercertifikatet för serverservern innehåller både rot- och mellanliggande certifikatutfärdare.

- Om certifikatet är självsignerat eller signerat av okända mellanhänder måste ett betrott rotcertifikat för att kunna avsluta TLS i v2 SKU. Application Gateway kommunicerar endast med serverserverds rotcertifikat som matchar en av listan över betrodda rotcertifikat i serverda http-inställningen som är associerad med poolen.

> [!NOTE] 
>
> Det självsignerade certifikatet måste vara en del av en certifikatkedja. Ett enda självsignerat certifikat utan kedja stöds inte i V2 SKU.

- Förutom rotcertifikatmatchning validerar Application Gateway även om den värdinställning som anges i serverda http-inställningen matchar det vanliga namnet (CN) som visas av serverdserverns TLS/SSL-certifikat. När du försöker upprätta en TLS-anslutning till servergången anger Application Gateway tillägget Servernamnsindikering (SNI) till den värd som anges i inställningen för serveråtkomst http.
- Om **plockvärdnamn från servergruppsadress** väljs i stället för fältet Värd i serverhuvudet http-inställningen, är SNI-huvudet alltid inställt på serverdapoolen FQDN och CN på serverservern TLS/SSL-certifikatet måste matcha dess FQDN. Backend pool medlemmar med IPs stöds inte i det här scenariot.
- Rotcertifikatet är ett base64-kodat rotcertifikat från serverservercertifikaten.

## <a name="next-steps"></a>Nästa steg

När du har lärt dig om på TLS går du till [Konfigurera TLS från till med hjälp av Application Gateway med PowerShell](application-gateway-end-to-end-ssl-powershell.md) för att skapa en programgateway med slut-till-slut-TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
