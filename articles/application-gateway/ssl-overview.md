---
title: Aktiverar slut punkt till slut punkt SSL på Azure Application Gateway
description: Den här artikeln är en översikt över Application Gateway slut punkt till slut punkt för SSL-stöd.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 64b90afd598b96604fc9c3ddc4bc10586e714363
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279109"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Översikt över SSL-terminering och slut punkt till slut punkt SSL med Application Gateway

Secure Sockets Layer (SSL) är standard säkerhets teknik för att upprätta en krypterad länk mellan en webb server och en webbläsare. Den här länken ser till att alla data som skickas mellan webb servern och webbläsare förblir privata och krypterade. Application Gateway stöder både SSL-avslutning på gatewayen samt för SSL-kryptering från slut punkt till slut punkt.

## <a name="ssl-termination"></a>SSL-avslutning

Application Gateway stöder SSL-terminering vid gatewayen. Därefter flödar trafiken vanligtvis okrypterat fram till serverdelarna. Det finns ett antal fördelar med att göra SSL-avslutning på Application Gateway:

- **Bättre prestanda** – den största prestanda träffen när SSL-dekryptering är den första hand skakningen. För att förbättra prestandan gör servern dekrypteringsnyckeln SSL-sessions-ID: n och hanterar TLS-sessionsbiljetter. Om detta görs på Application Gateway kan alla begär Anden från samma klient använda de cachelagrade värdena. Om den är utförd på backend-servrarna måste klienten autentiseras på nytt varje gång klientens begär Anden skickas till en annan server. Användningen av TLS-biljetter kan hjälpa dig att undvika det här problemet, men det stöds inte av alla klienter och kan vara svårt att konfigurera och hantera.
- **Bättre användning av backend-servrar** – SSL/TLS-bearbetning är mycket processor intensiv och blir mer intensiv eftersom nyckel storlekar ökar. Genom att ta bort det här arbetet från backend-servrarna kan de fokusera på vad de är mest effektiva vid och leverera innehåll.
- **Intelligent routning** – genom att dekryptera trafiken har programgatewayen åtkomst till innehållet i begäran, till exempel SIDHUVUD, URI och så vidare, och kan använda dessa data för att dirigera begär Anden.
- **Certifikat hantering** – certifikat behöver bara köpas och installeras på programgatewayen och inte på alla backend-servrar. Detta sparar både tid och pengar.

Om du vill konfigurera SSL-terminering måste ett SSL-certifikat läggas till i lyssnaren för att Application Gateway ska kunna härleda en symmetrisk nyckel enligt specifikationen för SSL-protokoll. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. SSL-certifikatet måste vara i PFX-format (personal information Exchange). Med det här fil formatet kan du exportera den privata nyckeln som krävs av programgatewayen för att utföra kryptering och dekryptering av trafik.

> [!NOTE] 
>
> Application Gateway tillhandahåller inte någon funktion för att skapa ett nytt certifikat eller skicka en certifikatbegäran till en certifikat utfärdare.

För att SSL-anslutningen ska fungera måste du se till att SSL-certifikatet uppfyller följande villkor:

- Att aktuellt datum och tid är inom datum intervallet "giltigt från" och "giltigt till" i certifikatet.
- Att certifikatets ”vanliga namn” (CN) matchar värdhuvudet i begäran. Exempelvis om klienten gör en begäran till `https://www.contoso.com/`, måste CN vara `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certifikat som stöds för SSL-avslutning

Application Gateway stöder följande typer av certifikat:

- Certifikat UTFÄRDAre (certifikat utfärdare): ett CA-certifikat är ett digitalt certifikat som utfärdats av en certifikat utfärdare (CA)
- EV-certifikat för EV (utökad verifiering): ett EV-certifikat är en rikt linje för bransch standard certifikat. Detta kommer att aktivera webbläsarens lokaliserare grönt och även publicera företags namn.
- Jokertecken: det här certifikatet har stöd för valfritt antal under domäner baserat på *. site.com, där din under domän skulle ersätta *. Den har dock inte stöd för site.com, så om användarna kommer åt din webbplats utan att skriva den inledande "www", kommer jokertecknet för jokertecken inte att gälla.
- Självsignerade certifikat: klient webbläsare kan inte lita på dessa certifikat och varnar användaren om att den virtuella tjänstens certifikat inte ingår i en förtroende kedja. Självsignerade certifikat är bra för testning eller miljöer där administratörer styr klienterna och kan kringgå webbläsarens säkerhets aviseringar på ett säkert sätt. Produktions arbets belastningar bör aldrig använda självsignerade certifikat.

Mer information finns i [Konfigurera SSL-avslutning med Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Certifikatets storlek
Se avsnittet [Application Gateway gränser](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) för att veta hur många SSL-certifikat som stöds.

## <a name="end-to-end-ssl-encryption"></a>Slut punkt till slut punkt SSL-kryptering

Vissa kunder kanske inte vill ha okrypterad kommunikation till backend-servrarna. Det kan bero på säkerhetskrav eller efterföljandekrav eller att programmet bara accepterar säkra anslutningar. För den typen av program, stöder Application Gateway nu slutpunkt-till-slutpunkt SSL-kryptering.

Slutpunkt-till-slutpunkt-SSL låter dig skicka krypterade känsliga data säkert till serverdelen samt dra nytta av de fördelar med Layer 7-belastningsutjämningsfunktioner som Application Gateway tillhandahåller. Vissa av dessa funktioner är cookiebaserad-tillhörighet, URL-baserad routning, stöd för routning baserat på platser eller möjligheten att injicera X-Forwarded-*-huvuden.

När den konfigurerats med slutpunkt-till-slutpunkt SSL-kommunikationsläge, terminerar Application Gateway användarens SSL-sessioner vid gatewayen och avkrypterar användartrafiken. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Application Gateway startar sedan en ny SSL-anslutning till serverdels-servern och återkrypterar data med hjälp av serverdels-serverns offentliga nyckelcertifikat innan begäran skickas till serverdelen. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren. Slutpunkt-till-slutpunkt-SSL aktive ras genom att ställa in protokoll inställningen i [HTTP-inställningen i Server delen](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) till https, som sedan tillämpas på en backend-pool.

SSL-principen gäller för både frontend-och backend-trafik. På klient sidan fungerar Application Gateway som-servern och tillämpar principen. På Server delen fungerar Application Gateway som klienten och skickar protokoll/chiffrering-information som inställningen under SSL-handskakningen.

Application Gateway kommunicerar bara med de Server dels instanser som antingen har vit listas sina certifikat med programgatewayen eller vars certifikat är signerade av välkända CA-myndigheter där certifikatets CN matchar värd namnet i HTTP Server dels inställningar. Dessa inkluderar betrodda Azure-tjänster som Azure App Service Web Apps och Azure API Management.

Om certifikaten för medlemmarna i backend-poolen inte har signerats av välkända CA-utfärdare, måste varje instans i backend-poolen med end to end SSL aktiverat konfigureras med ett certifikat för att tillåta säker kommunikation. Genom att lägga till certifikatet ser du till att programgatewayen bara kommunicerar med kända backend-instanser. Detta skyddar all kommunikation från slut punkt till slut punkt.

> [!NOTE] 
>
> Konfiguration av autentiseringscertifikat krävs inte för betrodda Azure-tjänster som Azure App Service Web Apps och Azure API Management.

> [!NOTE] 
>
> Certifikatet som har lagts till i **Server delens HTTP-inställning** för att autentisera backend-servrarna kan vara samma som det certifikat som lagts till i **LYSSNAren** för SSL-avslutning på Application Gateway eller annat för förbättrad säkerhet.

![slutpunkt till slutpunkt ssl-scenario][1]

I det här exemplet dirigeras begäranden med TLS1.2 till backend-servrarna i Pool1 med hjälp av slutpunkt till slutpunkt-SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Slutpunkt till slutpunkt-SSL och vitlistning av certifikat

Application Gateway kommunicerar bara med kända serverdelsinstanser som har vitlistat sina certifikat med Application Gateway. För att aktivera vitlistning av certifikat så behöver du överföra den offentliga nyckeln för serverdels-serverns certifikat till Application Gateway (inte rotcertifikatet). Endast anslutningar till kända och vitlistade serverdelar tillåts sedan. De återstående serverdelar resulterar i ett gateway-fel. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste vara vit listas med Application Gateway enligt beskrivningen i föregående steg innan de kan användas.

> [!NOTE]
> Konfiguration av autentiseringscertifikat krävs inte för betrodda Azure-tjänster som Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Slutpunkt-till-slutpunkt-SSL med v2-SKU: n

Certifikat för autentisering har ersatts och ersatts av betrodda rot certifikat i Application Gateway v2-SKU: n. De fungerar på samma sätt för att autentisera certifikat med några viktiga skillnader:

- Certifikat signerade av välkända CA-myndigheter vars CN matchar värd namnet i inställningarna för HTTP-backend kräver inte ytterligare steg för slut punkt till slut punkt för SSL för att fungera. 

   Om till exempel Server dels certifikaten utfärdas av en välkänd certifikat utfärdare och har ett CN-contoso.com, och värd fältet för Server delens http-inställning också är inställt på contoso.com, krävs inga ytterligare steg. Du kan ställa in Server delens http-protokollidentifierare på HTTPS och både hälso avsökningen och data Sök vägen är SSL-aktiverad. Om du använder Azure App Service eller andra Azure-webbtjänster som Server del är dessa även implicit betrodda och inga ytterligare åtgärder krävs för slut punkt till slut punkt-SSL.
   
> [!NOTE] 
>
> För att ett SSL-certifikat ska vara betrott måste certifikatet på backend-servern ha utfärdats av en certifikat utfärdare som ingår i det betrodda lagrings platsen för Application Gateway. om certifikatet inte har utfärdats av en betrodd certifikat utfärdare, kommer Application Gateway sedan att kontrol lera för att se om certifikatet för den utfärdande certifikat utfärdaren utfärdades av en betrodd certifikat utfärdare, och så vidare tills antingen en betrodd certifikat utfärdare hittas (där en betrodd, säker anslutning upprättas) eller om ingen betrodd certifikat utfärdare hittas (där Application Gateway kommer att markera Server delen ej felfri). Därför rekommenderas backend-servercertifikatet att innehålla både rot-och Intermidiate-CAs.

- Om certifikatet är självsignerat eller signerat av okända mellanhänder måste du definiera ett betrott rot certifikat för att kunna aktivera slut punkt till slut punkt SSL i v2-SKU: n. Application Gateway kommunicerar bara med Server certifikat vars rot certifikat matchar en av listan över betrodda rot certifikat i Server delens http-inställning som är associerad med poolen.

> [!NOTE] 
>
> Det självsignerade certifikatet måste vara en del av en certifikat kedja. Ett enda självsignerat certifikat utan kedja stöds inte i v2 SKU.

- Förutom matchning av rot certifikat verifierar Application Gateway även om värd inställningen som anges i Server delens http-inställning matchar det egna namnet (CN) som visas av backend-serverns SSL-certifikat. När du försöker upprätta en SSL-anslutning till Server delen ställer Application Gateway in Servernamnindikator (SNI)-tillägget på värden som anges i Server delens http-inställning.
- Om **Välj värd namn från Server dels adress** väljs i stället för värd fältet i http-inställningen för Server delen, anges SNI-huvudet alltid till FQDN för Server delen och CN på backend-SERVERns SSL-certifikat måste matcha sitt fullständiga domän namn. Medlemmar i backend-pooler med IP-adresser stöds inte i det här scenariot.
- Rot certifikatet är ett base64-kodat rot certifikat från backend-serverns certifikat.

## <a name="next-steps"></a>Nästa steg

När du har lärt dig om slutpunkt-till-slutpunkt-SSL går du till [Konfigurera slut punkt till slut punkt-SSL med hjälp av Application Gateway med PowerShell](application-gateway-end-to-end-ssl-powershell.md) för att skapa en Programgateway med slut punkt till slut punkt

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
