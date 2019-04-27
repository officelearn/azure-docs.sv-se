---
title: Om du aktiverar SSL från slutpunkt till slutpunkt på Azure Application Gateway
description: Den här artikeln är en översikt över Application Gateway från slutpunkt till slutpunkt SSL-stöd.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 92799019d13de71d911767d8e400598513587667
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715235"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Översikt över SSL-avslutning och slutpunkt till slutpunkt-SSL med Programgateway

Secure Sockets Layer (SSL) är den Standardsäkerhet för tekniken för att upprätta en krypterad anslutning mellan en server och en webbläsare. Den här länken säkerställer att alla data som skickas mellan webbservern och webbläsare förblir privata och krypterade. Application gateway stöder både SSL-terminering vid gatewayen samt från slutpunkt till slutpunkt SSL-kryptering.

## <a name="ssl-termination"></a>SSL-avslutning

Application Gateway stöder SSL-avslutning på gatewayen, när flödar trafiken vanligtvis okrypterat fram till backend-servrarna. Det finns flera fördelar med att göra SSL-avslutning på application gateway:

- **Förbättrad prestanda** – största prestanda når när du gör SSL dekryptering är den första handskakningen. För att förbättra prestanda, servern gör dekrypteringen cachelagrar sessionscachen och hanterar TLS sessionsbiljetter. Om detta görs på application gateway, kan alla förfrågningar från samma klient använda cachelagrade värdena. Om det är klart på backend-servrar har varje gång den klientbegäranden går du till en annan server klienten till re‑authenticate. Användningen av TLS-biljetter kan minimera det här problemet, men de stöds inte av alla klienter och kan vara svårt att konfigurera och hantera.
- **Bättre användning av backend-servrarna** – SSL/TLS-bearbetning är mycket Processorintensiva och blir mer intensiva nyckelstorlekar öka. Ta bort detta arbete från backend-servrarna låter dem fokusera på vad de är mest effektiva vid leverans av innehåll.
- **Intelligent routning** – genom att dekryptera trafiken application gateway har åtkomst till innehållet i begäran, till exempel rubriker, URI och så vidare, och kan använda dessa data för begäranden.
- **Certifikathantering** – certifikat behöver bara köpas och installerade på application gateway och inte alla backend-servrarna. Detta sparar både tid och pengar.

Om du vill konfigurera SSL-avslutning, krävs ett SSL-certifikat som ska läggas till lyssnaren att aktivera application gateway att härleda en symmetrisk nyckel enligt specifikationen för SSL-protokollet. Den symmetriska nyckeln används sedan för att kryptera och dekryptera trafiken som skickas till gatewayen. SSL-certifikatet måste ha formatet Personal Information Exchange (PFX). Det här filformatet kan du exportera den privata nyckeln som krävs av application gateway för att utföra kryptering och dekryptering av trafik.

> [!NOTE] 
>
> Application gateway ger inte någon möjlighet att skapa ett nytt certifikat eller skicka en certifikatbegäran till en certifikatutfärdare.

För SSL-anslutningen ska fungera måste du kontrollera att SSL-certifikatet uppfyller följande villkor:

- Det aktuella datum och tid är i ”giltig från” och ”giltig till” datumintervall på certifikatet.
- Den den ”namn” (CN) matchar värdhuvudet i begäran. Exempel: om klienten gör en begäran om att `https://www.contoso.com/`, CN måste vara `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certifikat som stöds för SSL-avslutning

Application gateway stöder följande typer av certifikat:

- Certifikatutfärdare (certifikatutfärdare): Ett CA-certifikat är ett digitalt certifikat som utfärdats av en certifikatutfärdare (CA)
- EV (utökad validering)-certifikat: Ett EV-certifikat är en bransch standardcertifikat riktlinjer. Detta aktiverar positionerare öga grön och publicera företagets namn samt.
- Jokerteckencertifikat: Det här certifikatet har stöd för valfritt antal underdomäner baserat på *. site.com, där din underdomän då ersätts den *. Det stöder inte, men site.com, så om användarna kommer åt din webbplats utan att ange inledande ”www”, jokerteckencertifikat inte tar upp detta.
- Självsignerade certifikat: Klientwebbläsare lita inte på dessa certifikat och kommer att varna användaren att virtual service-certifikat inte är en del av en certifikatkedjan. Självsignerade certifikat är bra för testning eller miljöer där administratörer styra klienterna och kan på ett säkert sätt att kringgå webbläsarens säkerhetsaviseringar. Produktionsarbetsbelastningar använda aldrig självsignerade certifikat.

Mer information finns i [konfigurerar SSL-avslutning med application gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

## <a name="end-to-end-ssl-encryption"></a>Slutpunkt till slutpunkt SSL-kryptering

Vissa kunder kan inte behöver ha en okrypterad kommunikation till backend-servrarna. Det kan bero på säkerhetskrav eller efterföljandekrav eller att programmet bara accepterar säkra anslutningar. För den typen av program, stöder Application Gateway nu slutpunkt-till-slutpunkt SSL-kryptering.

Slutpunkt-till-slutpunkt-SSL låter dig skicka känsliga data säkert till serverdelen i krypterad samt dra nytta av fördelarna med Layer 7-belastningsutjämning funktioner som application gateway tillhandahåller. Vissa av dessa funktioner är cookiebaserad-tillhörighet, URL-baserad routning, stöd för routning baserat på platser eller möjligheten att injicera X-Forwarded-*-huvuden.

När den konfigurerats med slutpunkt-till-slutpunkt SSL-kommunikationsläge, terminerar Application Gateway användarens SSL-sessioner vid gatewayen och avkrypterar användartrafiken. Därefter appliceras de konfigurerade reglerna för att välja en lämplig serverdels-serverpoolinstans att dirigera trafiken till. Application Gateway startar sedan en ny SSL-anslutning till serverdels-servern och återkrypterar data med hjälp av serverdels-serverns offentliga nyckelcertifikat innan begäran skickas till serverdelen. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren. Slutpunkt-till-slutpunkt SSL aktiveras genom att ställa in protokollinställningen i [serverdelens HTTP-inställnings](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) till HTTPS, som sedan appliceras till en serverdelspool.

SSL-principen gäller för både frontend och backend-trafik. På klientdelen Application Gateway fungerar som servern och tillämpar principen. På serversidan Application Gateway fungerar som klienten och skickar informationen protocol/chiffer som inställningen under SSL-handskakningen.

Application gateway kommunicerar bara med dessa serverdelsinstanser som har antingen vitlistat sina certifikat med application gateway eller vars certifikat är signerade av den välkända Certifikatutfärdare myndigheter där Certifikatets CN-namn matchar värdnamnet i HTTP backend-inställningar. Dessa inkluderar de betrodda Azure-tjänsterna, till exempel Azure App service web apps och Azure API Management.

Om certifikat för medlemmarna i serverdelspoolen inte är signerade myndigheters välkänd Certifikatutfärdare, måste varje instans i serverdelspoolen med slutpunkt till slutpunkt SSL aktiverat konfigureras med ett certifikat för att tillåta säker kommunikation. Lägger till certifikatet säkerställer att den application gatewayen kommunicerar bara med kända serverdelsinstanser. Detta skyddar ytterligare slutpunkt till slutpunkt-kommunikation.

> [!NOTE] 
>
> Ställa in autentisering servercertifikat krävs inte för betrodda Azure-tjänster, till exempel Azure App service web apps och Azure API Management.

> [!NOTE] 
>
> Det certifikat som lagts till i **serverdelens HTTP-inställnings** för att autentisera serverdelen kan vara samma som det certifikat som lagts till i **lyssnare** för SSL-avslutning på application gateway eller en annan för Förbättrad säkerhet.

![slutpunkt till slutpunkt ssl-scenario][1]

I det här exemplet dirigeras begäranden med TLS1.2 till backend-servrarna i Pool1 med hjälp av slutpunkt till slutpunkt-SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Slutpunkt till slutpunkt-SSL och vitlistning av certifikat

Application Gateway kommunicerar bara med kända serverdelsinstanser som har vitlistat sina certifikat med Application Gateway. För att aktivera vitlistning av certifikat så behöver du överföra den offentliga nyckeln för serverdels-serverns certifikat till Application Gateway (inte rotcertifikatet). Endast anslutningar till kända och vitlistade serverdelar tillåts sedan. De återstående serverdelar resulterar i ett gateway-fel. Självsignerade certifikat är enbart för testningsändamål och rekommenderas inte för produktions-arbetsbelastningar. Sådana certifikat måste också vitlistas med application gateway såsom beskrivs i föregående steg, innan de kan användas.

> [!NOTE]
> Ställa in autentisering servercertifikat krävs inte för betrodda Azure-tjänster, till exempel Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Slutpunkt till slutpunkt SSL med v2-SKU

Autentiseringscertifikat har inaktuell och ersatts med betrodda rotcertifikat i Application Gateway v2-SKU. De fungerar på samma sätt till certifikat för autentisering med några viktiga skillnader:

- Certifikat som signerats av välkända Certifikatutfärdare myndigheter vars CN matchar värdnamnet i serverdelens HTTP-inställningarna kräver inte några ytterligare steg för slutpunkt till slutpunkt SSL ska fungera. 

   Till exempel om backend-certifikat har utfärdats av en välkänd Certifikatutfärdare och har en CN contoso.com och serverdelens http-inställning värden också anges till contoso.com, krävs sedan inga ytterligare åtgärder. Du kan ange i serverdelens http ställa in protokollet HTTPS både hälsotillstånd avsökningen och sökvägen skulle bli SSL aktiverat. Om du använder Azure App Service eller andra Azure webbtjänster serversidan, det här är implicit betrodda samt och inga ytterligare steg krävs för slutpunkt till slutpunkt SSL.
- Om certifikatet är självsignerat eller signerats av okänd mellanhänder, måste sedan för att aktivera SSL för slutpunkt till slutpunkt i v2-SKU ett betrott rotcertifikat definieras. Application Gateway kommunicerar bara med serverdelar vars servercertifikat rotcertifikat matchar ett av listan över betrodda rotcertifikat i serverdelens http-inställnings som hör till poolen.
- Förutom root certifikat matchar validerar också Application Gateway om värden som anges i serverdelens http-inställnings överensstämmer med det egna namnet (CN) som presenteras av serverdels-serverns SSL-certifikat. När du försöker upprätta en SSL-anslutning till serverdelen anger Application Gateway Server Name Indication (SNI)-tillägget till värden som anges i serverdelens http-inställning.
- Om **Välj värdnamnet från serverdelsadressen** väljs i stället för fältet värd i serverdelens http-inställnings SNI-huvudet är alltid inställd till serverdelspoolen FQDN- och CN på backend-servern SSL certifikatet måste matcha dess FQDN. Medlemmar i Serverdelspool med IP-adresser stöds inte i det här scenariot.
- Rotcertifikatet är en base64-kodad rotcertifikat från serverdels-servercertifikat.

## <a name="next-steps"></a>Nästa steg

När du läst om slutpunkt till slutpunkt SSL, gå till [konfigurera slutpunkt till slutpunkt SSL med Programgateway med PowerShell](application-gateway-end-to-end-ssl-powershell.md) att skapa en Programgateway med hjälp av SSL för slutpunkt till slutpunkt.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
