---
title: Aktivera slutpunkt till SSL på Azure Application Gateway
description: Den här artikeln är en översikt över application gateway end to end SSL-stöd.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 9c4e6124acdbb35233f8e829f43d2665fd4a5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284814"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Översikt över SSL-avslutning och slutpunkt till SSL med Application Gateway

SSL (Secure Sockets Layer) är standardsäkerhetstekniken för att upprätta en krypterad länk mellan en webbserver och en webbläsare. Den här länken säkerställer att alla data som skickas mellan webbservern och webbläsare förblir privata och krypterade. Programgateway stöder både SSL-avslutning vid gatewayen samt på SSL-kryptering.

## <a name="ssl-termination"></a>SSL-avslutning

Application Gateway stöder SSL-terminering vid gatewayen. Därefter flödar trafiken vanligtvis okrypterat fram till serverdelarna. Det finns ett antal fördelar med att göra SSL-avslutning vid programgatewayen:

- **Förbättrad prestanda** – Den största prestandaträffen när du gör SSL-dekryptering är det första handskakningen. För att förbättra prestanda cachelagrar servern SSL-sessions-ID:t och hanterar TLS-sessionsbiljetter. Om detta görs vid programgatewayen kan alla begäranden från samma klient använda de cachelagrade värdena. Om det görs på serverdaservrarna måste klienten autentiseras varje gång klientens begäranden går till en annan server. Användningen av TLS-biljetter kan bidra till att minska problemet, men de stöds inte av alla klienter och kan vara svåra att konfigurera och hantera.
- **Bättre utnyttjande av backend-servrar** - SSL / TLS bearbetning är mycket CPU-intensiv, och blir mer intensiv som viktiga storlekar ökar. Om du tar bort det här arbetet från serveringsservrarna kan de fokusera på vad de är mest effektiva på och leverera innehåll.
- **Intelligent routning** – Genom att dekryptera trafiken har programgatewayen åtkomst till begärandeinnehållet, till exempel rubriker, URI och så vidare, och kan använda dessa data för att dirigera begäranden.
- **Certifikathantering** – Certifikat behöver bara köpas och installeras på programgatewayen och inte alla servergruppsservrar. Detta sparar både tid och pengar.

För att konfigurera SSL-avslutning måste ett SSL-certifikat läggas till i lyssnaren för att programgatewayen ska kunna härleda en symmetrisk nyckel enligt SSL-protokollspecifikationen. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. SSL-certifikatet måste vara i PFX-format (Personal Information Exchange). Med det här filformatet kan du exportera den privata nyckeln som krävs av programgatewayen för att utföra kryptering och dekryptering av trafik.

> [!NOTE] 
>
> Programgateway ger inte någon möjlighet att skapa ett nytt certifikat eller skicka en certifikatbegäran till en certifikatutfärdare.

För att SSL-anslutningen ska fungera måste du se till att SSL-certifikatet uppfyller följande villkor:

- Att det aktuella datumet och tiden ligger inom datumintervallet "Giltig från" och "Giltig till" på certifikatet.
- Att certifikatets ”vanliga namn” (CN) matchar värdhuvudet i begäran. Exempelvis om klienten gör en begäran till `https://www.contoso.com/`, måste CN vara `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certifikat som stöds för SSL-avslutning

Programgateway stöder följande typer av certifikat:

- CA-certifikat (certifikatutfärdare): Ett CA-certifikat är ett digitalt certifikat som utfärdats av en certifikatutfärdare
- EV-certifikat (Extended Validation) : Ett EV-certifikat är ett certifikat som överensstämmer med branschstandardcertifikatriktlinjer. Detta kommer att vända webbläsaren locator bar grönt och publicera företagets namn också.
- Jokerteckencertifikat: Det här certifikatet stöder valfritt antal underdomäner baserat på *.site.com, där underdomänen skulle ersätta *. Det stöder dock inte site.com, så om användarna har tillgång till din webbplats utan att skriva den ledande "www", kommer jokertecken certifikatet inte täcka det.
- Självsignerade certifikat: Klientwebbläsare litar inte på dessa certifikat och varnar användaren om att den virtuella tjänstens certifikat inte ingår i en förtroendekedja. Självsignerade certifikat är bra för testning eller miljöer där administratörer styr klienterna och på ett säkert sätt kan kringgå webbläsarens säkerhetsaviseringar. Produktionsarbetsbelastningar bör aldrig använda självsignerade certifikat.

Mer information finns i [konfigurera SSL-avslutning med programgateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Certifikatets storlek
Kontrollera avsnittet Begränsningar för [Programgateway](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) för att veta den maximala SSL-certifikatstorleken som stöds.

## <a name="end-to-end-ssl-encryption"></a>Slutpunkt till SSL-kryptering

Vissa kunder kanske inte vill ha okrypterad kommunikation till serveringsservrarna. Det kan bero på säkerhetskrav eller efterföljandekrav eller att programmet bara accepterar säkra anslutningar. För den typen av program, stöder Application Gateway nu slutpunkt-till-slutpunkt SSL-kryptering.

Slutpunkt-till-slutpunkt-SSL låter dig skicka krypterade känsliga data säkert till serverdelen samt dra nytta av de fördelar med Layer 7-belastningsutjämningsfunktioner som Application Gateway tillhandahåller. Vissa av dessa funktioner är cookiebaserad-tillhörighet, URL-baserad routning, stöd för routning baserat på platser eller möjligheten att injicera X-Forwarded-*-huvuden.

När den konfigurerats med slutpunkt-till-slutpunkt SSL-kommunikationsläge, terminerar Application Gateway användarens SSL-sessioner vid gatewayen och avkrypterar användartrafiken. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Application Gateway startar sedan en ny SSL-anslutning till serverdels-servern och återkrypterar data med hjälp av serverdels-serverns offentliga nyckelcertifikat innan begäran skickas till serverdelen. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren. Ssl från till aktiveras genom att ange protokollinställning i [HTTP-inställning för bakåtsträvande](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) till HTTPS, som sedan tillämpas på en backend-pool.

SSL-principen gäller både frontend- och backend-trafik. I klientdelen fungerar Application Gateway som server och tillämpar principen. På backend fungerar Application Gateway som klient och skickar protokoll-/chifferinformationen som invald under SSL-handskakningen.

Programgateway kommunicerar endast med de servernamnsinstanser som antingen har vitlistat sitt certifikat med programgatewayen eller vars certifikat är signerade av välkända ca-myndigheter där certifikatet CN matchar värdnamnet i HTTP backend-inställningar. Dessa inkluderar betrodda Azure-tjänster som Azure App-tjänst webbappar och Azure API Management.

Om certifikaten för medlemmarna i serverdelspoolen inte är signerade av välkända ca-myndigheter måste varje instans i serverdelspoolen med ssl-slut konfigureras med ett certifikat för säker kommunikation. Genom att lägga till certifikatet säkerställs att programgatewayen endast kommunicerar med kända backend-instanser. Detta säkerställer ytterligare kommunikation från slutna till.

> [!NOTE] 
>
> Konfiguration av autentiseringscertifikat krävs inte för betrodda Azure-tjänster som Azure App-tjänstwebbappar och Azure API Management.

> [!NOTE] 
>
> Certifikatet som läggs till **i SERVERD HTTP-inställningen** för att autentisera serverdservrarna kan vara samma som certifikatet som lagts till **lyssnaren** för SSL-avslutning vid programgateway eller annorlunda för förbättrad säkerhet.

![slutpunkt till slutpunkt ssl-scenario][1]

I det här exemplet dirigeras begäranden med TLS1.2 till backend-servrarna i Pool1 med hjälp av slutpunkt till slutpunkt-SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Slutpunkt till slutpunkt-SSL och vitlistning av certifikat

Application Gateway kommunicerar bara med kända serverdelsinstanser som har vitlistat sina certifikat med Application Gateway. För att aktivera vitlistning av certifikat så behöver du överföra den offentliga nyckeln för serverdels-serverns certifikat till Application Gateway (inte rotcertifikatet). Endast anslutningar till kända och vitlistade serverdelar tillåts sedan. De återstående serverdelar resulterar i ett gateway-fel. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste vitlistas med programgatewayen enligt beskrivningen i föregående steg innan de kan användas.

> [!NOTE]
> Konfiguration av autentiseringscertifikat krävs inte för betrodda Azure-tjänster som Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Slutpunkt till SSL med v2 SKU

Autentiseringscertifikat har inaktuella och ersatts av betrodda rotcertifikat i Application Gateway v2 SKU. De fungerar på samma sätt som autentiseringscertifikat med några få viktiga skillnader:

- Certifikat som signerats av välkända ca-myndigheter vars CN matchar värdnamnet i HTTP-serverdelsinställningarna kräver inget ytterligare steg för att ssl-slutspelet ska fungera. 

   Om serverdacertifikaten till exempel utfärdas av en välkänd certifikatutfärdare och har ett KN-contoso.com, och serverda http-inställningens värdfält också är inställt på contoso.com, krävs inga ytterligare steg. Du kan ange serverd http-inställningsprotokollet till HTTPS och både hälsoavsökningen och datasökvägen skulle vara SSL-aktiverad. Om du använder Azure App Service eller andra Azure-webbtjänster som din backend är dessa implicit betrodda också och inga ytterligare steg krävs för att avsluta SSL.
   
> [!NOTE] 
>
> För att ett SSL-certifikat ska vara tillförlitligt måste det certifikatet för serverdservern ha utfärdats av en certifikatutfärdar som ingår i det betrodda arkivet för Application Gateway.If certifikatet inte utfärdades av en betrodd certifikatutfärdaren, kontrollerar programgatewayen sedan av en certifikatutfärdar för att se om certifikatet för den utfärdande certifikatutfärdaren har utfärdats av en betrodd certifikatutfärdare, och så vidare tills antingen en betrodd certifikatutfärdare hittas (vid vilken tidpunkt en betrodd, säker anslutning upprättas) eller ingen betrodd certifikatutfärdare kan hittas (då application gateway kommer att markera serverda ohälsosamma). Därför rekommenderas att servercertifikatet för serverservern innehåller både rot- och mellanliggande certifikatutfärdare.

- Om certifikatet är självsignerat eller signerat av okända mellanhänder måste ett betrott rotcertifikatdefinieras för att kunna avsluta SSL i v2 SKU. Application Gateway kommunicerar endast med serverserverds rotcertifikat som matchar en av listan över betrodda rotcertifikat i serverda http-inställningen som är associerad med poolen.

> [!NOTE] 
>
> Det självsignerade certifikatet måste vara en del av en certifikatkedja. Ett enda självsignerat certifikat utan kedja stöds inte i V2 SKU.

- Förutom rotcertifikatmatchning validerar Application Gateway även om den värdinställning som anges i serverda http-inställningen matchar det vanliga namnet (CN) som visas av serverdaserverns SSL-certifikat. När du försöker upprätta en SSL-anslutning till servergången anger Application Gateway tillägget Servernamnsindikering (SNI) till den värd som anges i inställningen för serveråtkomst http.
- Om **plockvärdnamn från servergruppsadress** väljs i stället för fältet Värd i serverhuvudet http-inställningen, är SNI-huvudet alltid inställt på serverdapoolen FQDN och CN på serverhuvudservern SSL-certifikatet måste matcha dess FQDN. Backend pool medlemmar med IPs stöds inte i det här scenariot.
- Rotcertifikatet är ett base64-kodat rotcertifikat från serverservercertifikaten.

## <a name="next-steps"></a>Nästa steg

När du har lärt dig om SSL från slutpunkt till går du till [Konfigurera SSL från slutpunkt till med application gateway med PowerShell](application-gateway-end-to-end-ssl-powershell.md) för att skapa en programgateway med slutpunkt till SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
