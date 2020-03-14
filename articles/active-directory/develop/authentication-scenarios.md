---
title: Autentisering i Microsoft Identity Platform | Azure
description: Lär dig grunderna i autentisering i Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 3ec965318da1361454b4a6bb78ed7147562b5fea
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138526"
---
# <a name="authentication-basics"></a>Grundläggande om autentisering

## <a name="what-is-authentication"></a>Vad är autentisering

Den här artikeln beskriver många av de autentiseringsmetoder du behöver känna till för att skapa skyddade webbappar, webb-API: er eller appar som anropar skyddade webb-API: er. Om du ser en term som du inte är bekant med kan du prova vår [ord lista](developer-glossary.md) eller våra [Microsoft Identity Platform-videor](identity-videos.md) som beskriver grundläggande begrepp.

**Autentisering** är en process för att bevisa att du är den som du säger. Autentisering förkortas ibland AuthN.

**Auktorisering** innebär att ge en autentiserad part behörighet att göra något. Den anger vilka data du får åtkomst till och vad du kan göra med dessa data. Auktorisering förkortas ibland AuthZ.

I stället för att skapa appar som var och en upprätthåller sin egen användar namn och lösen ords information, vilket innebär en hög administrativ börda när du behöver lägga till eller ta bort användare över flera appar, kan appar delegera det ansvaret till en centraliserad identitets leverantör.

Azure Active Directory (Azure AD) är en centraliserad identitets leverantör i molnet. Genom att delegera autentisering och auktorisering till det kan du använda scenarier som principer för villkorlig åtkomst som kräver att en användare befinner sig på en speciell plats, användning av Multi-Factor Authentication, samt att göra det möjligt för en användare att logga in en gång och sedan automatiskt loggat in på alla webbappar som delar samma centrala katalog. Den här funktionen kallas enkel inloggning (SSO).

En centraliserad identitets leverantör är ännu viktigare för appar som har användare som finns i hela världen och som inte nödvändigt vis loggar in från företagets nätverk. Azure AD autentiserar användare och ger åtkomst-token. En [åtkomsttoken är en säkerhetstoken som](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) utfärdas av en Authorization Server. Den innehåller information om användaren och appen som token är avsedd för. som kan användas för att få åtkomst till webb-API: er och andra skyddade resurser.

Microsofts identitets plattform fören klar autentiseringen för programutvecklare genom att tillhandahålla identitet som en tjänst, med stöd för bransch standard protokoll som [OAuth 2,0](https://oauth.net/2/) och [OpenID Connect](https://openid.net/connect/), samt bibliotek med öppen källkod för olika plattformar som hjälper dig att snabbt börja koda. Det gör det möjligt för utvecklare att skapa program som loggar in alla Microsoft-identiteter, Hämta token för att anropa [Microsoft Graph](https://developer.microsoft.com/graph/), andra Microsoft API: er eller API: er som utvecklare har skapat. Mer information finns i [utvecklingen av Microsoft Identity Platform](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Klientorganisationer

En moln identitets leverantör hanterar många organisationer. För att användarna ska vara åtskilda från olika organisationer är Azure AD partitionerad i klienter, med en klient organisation per organisation.

Klienter håller reda på användare och deras associerade appar. Microsoft Identity Platform stöder även användare som loggar in med personliga Microsoft-konton.

Azure AD tillhandahåller också Azure Active Directory B2C så att organisationer kan logga in användare, vanligt vis kunder, med sociala identiteter som ett Google-konto. Mer information finns i [Azure Active Directory B2C-dokumentationen](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Säkerhetstoken

Säkerhetstoken innehåller information om användare och appar. Azure AD använder JSON-baserade token (JWTs) som innehåller anspråk.

Ett anspråk ger intyg om en entitet, till exempel ett [klient program](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) eller en [resurs ägare](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner), till en annan entitet, till exempel en [resurs Server](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server).

Anspråk är namn/värde-par som vidarebefordrar fakta om ämnets token. Ett anspråk kan till exempel innehålla fakta om säkerhets objekt som autentiserats av [auktoriseringsservern](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server). De anspråk som förekommer i en specifik token är beroende av många saker, inklusive typen av token, vilken typ av autentiseringsuppgift som används för att autentisera ämnet, program konfigurationen och så vidare.

Program kan använda anspråk för olika uppgifter, till exempel:

* Token verifieras
* Identifierar innehavarens token-innehavare
* Visar användar information
* Avgöra ämnets auktorisering

Ett anspråk består av nyckel/värde-par som innehåller information som:

* Säkerhetstoken som genererade token
* Datum när token genererades
* Ämne (till exempel användare--utom för daemon)
* Mål grupp, som är appen för vilken token genererades
* App (klienten) som bad om token. Om det gäller Web Apps kan detta vara samma som mål gruppen

Mer detaljerad information om anspråk [finns i åtkomsttoken](access-tokens.md) och [ID-token](id-tokens.md).

Det är upp till appen för vilken token har skapats, webbappen som signerade användaren eller webb-API: et som anropades för att validera token. Token signeras av säkerhetstokentjänst (STS) med en privat nyckel. STS publicerar motsvarande offentliga nyckel. För att validera en token verifierar appen signaturen med hjälp av den offentliga STS-nyckeln för att verifiera att signaturen skapades med hjälp av den privata nyckeln.

Tokens är bara giltiga under en begränsad tid. Vanligt vis tillhandahåller STS ett par token: en åtkomsttoken för att få åtkomst till programmet eller den skyddade resursen, och en uppdateringstoken som används för att uppdatera åtkomsttoken när åtkomsttoken ligger nära förfallo datum.

Åtkomsttoken skickas till ett webb-API som Bearer-token i `Authorization`s huvudet. En app kan tillhandahålla en uppdateringstoken till STS, och om användarens åtkomst till appen inte har återkallats kommer den att få tillbaka en ny åtkomsttoken och en ny uppdateringstoken. Detta är hur scenariot för någon som lämnar företaget hanteras. När STS tar emot uppdateringstoken, utfärdar den ingen annan giltig åtkomsttoken om användaren inte längre är auktoriserad.

## <a name="application-model"></a>Programmodell

Program kan logga in användare själva eller delegera inloggning till en identitets leverantör. Se [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md) för att lära dig om inloggnings scenarier som stöds av Azure AD.

För en identitets leverantör att veta att en användare har åtkomst till en viss app måste både användaren och programmet vara registrerat hos identitets leverantören. När du registrerar ditt program med Azure AD ger du en identitets konfiguration för ditt program som gör det möjligt att integrera det med Azure AD. Genom att registrera appen kan du också:

* Anpassa anpassningen av programmet i dialog rutan för inloggning. Detta är viktigt eftersom det är den första upplevelsen som en användare kommer att ha med din app.
* Bestäm om du bara vill låta användarna logga in om de tillhör din organisation. Detta är ett enda klient program. Eller Tillåt användare att logga in med ett arbets-eller skol konto. Detta är ett program med flera innehavare. Du kan också tillåta personliga Microsoft-konton eller ett socialt konto från LinkedIn, Google och så vidare.
* Begär omfångs behörigheter. Du kan till exempel begära kommandot "User. Read", som ger behörighet att läsa profilen för den inloggade användaren.
* Definiera omfattningar som definierar åtkomst till ditt webb-API. När en app vill komma åt ditt API måste du vanligt vis begära behörigheter till de omfattningar som du definierar.
* Dela en hemlighet med Azure AD som visar appens identitet till Azure AD.  Detta är relevant i de fall där appen är ett konfidentiellt klient program. Ett konfidentiellt klient program är ett program som kan lagra autentiseringsuppgifter på ett säkert sätt. De kräver en betrodd backend-server för att lagra autentiseringsuppgifterna.

När programmet har registrerats får du en unik identifierare som appen delar med Azure AD när den begär token. Om appen är ett [konfidentiellt klient program](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application), kommer den också att dela hemligheten eller den offentliga nyckeln * – beroende på om certifikat eller hemligheter användes.

Microsoft Identity Platform representerar program som använder en modell som uppfyller två huvud funktioner:

* Identifiera appen med de autentiseringsprotokoll som stöds
* Ange alla identifierare, URL: er, hemligheter och relaterad information som behövs för att autentisera

Microsoft Identity Platform:

* Innehåller alla data som krävs för att stödja autentisering vid körning
* Innehåller alla data för att bestämma vilka resurser en app kan behöva komma åt och under vilka omständigheter en specifik begäran ska vara uppfylld
* Innehåller en infrastruktur för att implementera app-etablering i appens utvecklares klient organisation och till en annan Azure AD-klient
* Hanterar användarens medgivande under Tokenbegäran och underlättar dynamisk etablering av appar över klient organisationer

Samtycke är en resurs ägare som beviljar auktorisering för ett klient program att komma åt skyddade resurser, under specifika behörigheter för resurs ägarens räkning. Microsoft Identity Platform:

* Möjliggör för användare och administratörer att dynamiskt bevilja eller neka medgivande för appen att få åtkomst till resurser för deras räkning.
* Möjliggör för administratörer att i slutänden bestämma vilka appar som tillåts göra vad och vilka användare som kan använda specifika appar samt hur åtkomsten till katalogresurserna går till.

I Microsoft Identity Platform beskriver ett [program objekt](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) ett program. Vid distributions tillfället använder Microsoft Identity Platform programobjektet som en skiss för att skapa ett [huvud namn för tjänsten](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)som representerar en konkret instans av ett program i en katalog eller klient organisation. Tjänstens huvud namn definierar vad appen faktiskt kan göra i en speciell mål katalog, som kan använda den, vilka resurser den har åtkomst till och så vidare. Microsoft Identity Platform skapar ett huvud namn för tjänsten från ett program objekt via **medgivande**.

Följande diagram visar ett förenklat etablerings flöde för Microsoft Identity Platform som drivs av medgivande. Den visar två klienter: A och B. klient organisation A äger programmet. Klient B instansierar programmet via ett huvud namn för tjänsten.  

![Förenklat etableringsflöde som drivs av medgivande](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

I det här etableringsflödet sker följande:

1. En användare från klient B försöker logga in med appen. slut punkten för auktorisering begär en token för programmet.
1. Autentiseringsuppgifterna för användaren förvärvas och verifieras för autentisering.
1. Användaren uppmanas att ange medgivande för appen för att få åtkomst till klient B.
1. Microsoft Identity Platform använder programobjektet i klient organisationen som en skiss för att skapa ett huvud namn för tjänsten i klient B.
1. Användaren får en begärd token.

Du kan upprepa den här processen för ytterligare klienter. Klient organisation A behåller skissen för appen (program objekt). Användare och administratörer för alla andra innehavare där appen ges tillåtelse att behålla kontrollen över vad programmet tillåts att göra via motsvarande tjänst huvud objekt i varje klient organisation. Mer information finns i [program-och tjänst huvud objekt i Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Inloggnings flöde för webbapp med Azure AD

När en användare navigerar i webbläsaren till en webbapp händer följande:

* Webb programmet avgör om användaren är autentiserad.
* Om användaren inte har autentiserats delegerar webbappen till Azure AD för att logga in användaren. Inloggningen är kompatibel med organisationens princip, vilket kan innebära att användaren anger sina autentiseringsuppgifter, använder Multi-Factor Authentication eller inte använder ett lösen ord alls (till exempel med hjälp av Windows Hello).
* Användaren uppmanas att godkänna åtkomsten som klient appen behöver. Detta är anledningen till att klient program måste registreras med Azure AD, så att Azure AD kan leverera tokens som representerar den åtkomst som användaren har godkänt till.

När användaren har autentiserats:

* Azure AD skickar en token till webbappen.
* En cookie sparas som är kopplad till Azure AD-domänen och som innehåller användarens identitet i webbläsarens jar-jar. Nästa gång en app använder webbläsaren för att navigera till slut punkten för Azure AD-auktorisering, visar webbläsaren cookien så att användaren inte behöver logga in igen. Detta är också det sätt på vilket SSO uppnås. Cookien skapas av Azure AD och kan bara tolkas av Azure AD.
* Webbappen validerar sedan token. Om verifieringen lyckas visas den skyddade sidan i webbappen och en sessions-cookie sparas i webbläsarens Jar-Jar-form. När användaren navigerar till en annan sida vet webbappen att användaren är autentiserad baserat på sessionens cookie.

Följande sekvensdiagram sammanfattar interaktionen:

![autentisering av webbapp](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Hur en webbapp fastställer om användaren är autentiserad

Utvecklare av webbappar kan ange om alla eller endast vissa sidor kräver autentisering. I ASP.NET/ASP.NET Core görs detta till exempel genom att lägga till attributet `[Authorize]` till kontroll enheten. 

Det här attributet gör att ASP.NET söker efter en sessions-cookie som innehåller användarens identitet. Om en cookie inte finns omdirigerar ASP.NET autentiseringen till den angivna identitets leverantören. Om identitets leverantören är Azure AD omdirigerar webbappen autentiseringen till `https://login.microsoftonline.com`, som visar en dialog ruta för inloggning.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Hur en webbapp delegerar inloggning till Azure AD och hämtar en token

Användarautentisering sker via webbläsaren. OpenID-protokollet använder vanliga HTTP-protokoll meddelanden.
* Webbappen skickar en HTTP 302 (Omdirigerad) till webbläsaren för att använda Azure AD.
* När användaren autentiseras skickar Azure AD token till webbappen med hjälp av en omdirigering via webbläsaren.
* Omdirigeringen tillhandahålls av webb programmet i form av en omdirigerings-URI. Den här omdirigerings-URI: n har registrerats med objektet Azure AD-program. Det kan finnas flera omdirigerings-URI: er eftersom programmet kan distribueras på flera URL: er. Webbappen måste också ange den omdirigerings-URi som ska användas.
* Azure AD kontrollerar att den omdirigerings-URI som skickas av webbappen är en av de registrerade omdirigerings-URI: erna för appen.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Skriv bords-och mobilappar med Azure AD

Flödet som beskrivs ovan gäller, med små skillnader, för Station ära och mobila program.

Skriv bords-och mobil program kan använda en inbäddad webb kontroll eller en system webbläsare för autentisering. Följande diagram visar hur en stationär eller mobilapp använder Microsoft Authentication Library (MSAL) för att hämta åtkomsttoken och anropa webb-API: er.

![Desktop-appen hur det verkar vara](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL använder en webbläsare för att hämta tokens. Precis som med Web Apps delegeras autentiseringen till Azure AD.

Eftersom Azure AD sparar samma identitets-cookie i webbläsaren som den gör för webbappar, kommer den interna eller mobilappen att använda system läsaren omedelbart för att få enkel inloggning med motsvarande webbapp.

Som standard använder MSAL system webbläsare. Undantaget är .NET Framework Skriv bords program där en inbäddad kontroll används för att ge en mer integrerad användar upplevelse.

## <a name="next-steps"></a>Nästa steg

* Se [ord listan för Microsoft Identity Platform Developer](developer-glossary.md) för att bekanta dig med vanliga villkor.
* Mer information om andra scenarier för autentisering av användare som stöds av Microsoft Identity Platform finns i [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md) .
* Se [MSAL-bibliotek](msal-overview.md) för att lära dig om de Microsoft-bibliotek som hjälper dig att utveckla program som fungerar med Microsoft-konton, Azure AD-konton och Azure AD B2C användare i en enda, strömlinjeformad programmerings modell.
* Mer information om hur du konfigurerar autentisering för din App Service-app finns i [integrera App Service med Microsoft Identity Platform](/azure/app-service/configure-authentication-provider-aad) .
