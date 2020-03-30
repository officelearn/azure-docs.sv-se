---
title: Migrera från Azure Access Control Service | Microsoft-dokument
description: Lär dig mer om alternativen för att flytta appar och tjänster från Azure Access Control Service (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: 3168d36bf4c2d3c696173725f669b12dc168dcc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155009"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Så här migrerar du från Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS), en tjänst i Azure Active Directory (Azure AD), dras tillbaka den 7 november 2018. Program och tjänster som för närvarande använder Åtkomstkontroll måste migreras helt till en annan autentiseringsmekanism då. I den här artikeln beskrivs rekommendationer för aktuella kunder när du planerar att inaktivera din användning av Åtkomstkontroll. Om du för närvarande inte använder Åtkomstkontroll behöver du inte vidta några åtgärder.

## <a name="overview"></a>Översikt

Åtkomstkontroll är en molnautentiseringstjänst som erbjuder ett enkelt sätt att autentisera och auktorisera användare för åtkomst till dina webbprogram och tjänster. Det gör att många funktioner för autentisering och auktorisering som ska vägas ut ur din kod. Åtkomstkontroll används främst av utvecklare och arkitekter av Microsoft .NET-klienter, ASP.NET webbprogram och WCF-webbtjänster (Windows Communication Foundation).

Användningsfall för åtkomstkontroll kan delas in i tre huvudkategorier:

- Autentisera till vissa Microsoft-molntjänster, inklusive Azure Service Bus och Dynamics CRM. Klientprogram hämtar token från Åtkomstkontroll för att autentisera till dessa tjänster för att utföra olika åtgärder.
- Lägga till autentisering i webbprogram, både anpassade och färdigförpackade (som SharePoint). Genom att använda "passiv" autentisering i Åtkomstkontroll kan webbprogram stödja inloggning med ett Microsoft-konto (tidigare Live ID) och med konton från Google, Facebook, Yahoo, Azure AD och Active Directory Federation Services (AD FS).
- Skydda anpassade webbtjänster med token som utfärdats av Åtkomstkontroll. Genom att använda "aktiv" autentisering kan webbtjänster se till att de endast tillåter åtkomst till kända klienter som har autentiserats med Åtkomstkontroll.

Vart och ett av dessa användningsfall och deras rekommenderade migreringsstrategier diskuteras i följande avsnitt.

> [!WARNING]
> I de flesta fall krävs betydande kodändringar för att migrera befintliga appar och tjänster till nyare teknik. Vi rekommenderar att du omedelbart börjar planera och köra migreringen för att undvika eventuella avbrott eller driftstopp.

Åtkomstkontrollen har följande komponenter:

- En säker tokentjänst (STS), som tar emot autentiseringsbegäranden och utfärdar säkerhetstoken i gengäld.
- Den klassiska Azure-portalen, där du skapar, tar bort och aktiverar och inaktiverar namnområden för Åtkomstkontroll.
- En separat Access Control-hanteringsportal där du anpassar och konfigurerar namnområden för Åtkomstkontroll.
- En hanteringstjänst som du kan använda för att automatisera portalernas funktioner.
- En tokenomvandlingsregelmotor som du kan använda för att definiera komplex logik för att manipulera utdataformatet för token som åtkomstkontrollproblem.

Om du vill använda dessa komponenter måste du skapa ett eller flera namnområden för åtkomstkontroll. Ett *namnområde* är en dedikerad instans av Åtkomstkontroll som dina program och tjänster kommunicerar med. Ett namnområde är isolerat från alla andra Access Control-kunder. Andra Access Control-kunder använder sina egna namnområden. Ett namnområde i Åtkomstkontroll har en dedikerad URL som ser ut så här:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

All kommunikation med STS och hantering sker på den här webbadressen. Du använder olika sökvägar för olika ändamål. Ta reda på om dina program eller tjänster använder Åtkomstkontroll&lt;övervakar&gt;du om all trafik https:// namnområde .accesscontrol.windows.net. All trafik till den här url:en hanteras av Åtkomstkontroll och måste avbrytas. 

Undantaget är all trafik `https://accounts.accesscontrol.windows.net`till . Trafik till den här URL:en hanteras redan av en annan tjänst och påverkas **inte** av avfasningen av åtkomstkontroll. 

Mer information om Åtkomstkontroll finns i [Åtkomstkontrolltjänst 2.0 (arkiverad)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Ta reda på vilka av dina appar som kommer att påverkas

Följ stegen i det här avsnittet för att ta reda på vilka av dina appar som påverkas av ACS-pensionering.

### <a name="download-and-install-acs-powershell"></a>Ladda ned och installera ACS PowerShell

1. Gå till PowerShell-galleriet och hämta [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Installera modulen genom att köra

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Få en lista över alla möjliga kommandon genom att köra

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Om du vill ha hjälp med ett visst kommando kör du:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    där `[Command-Name]` är namnet på ACS-kommandot.

### <a name="list-your-acs-namespaces"></a>Lista dina ACS-namnområden

1. Anslut till ACS med **cmdlet connect-acsaccount.**
  
    Du kan behöva `Set-ExecutionPolicy -ExecutionPolicy Bypass` köra innan du kan köra kommandon och vara administratör för dessa prenumerationer för att kunna köra kommandona.

2. Lista dina tillgängliga Azure-prenumerationer med hjälp av **cmdleten Get-AcsSubscription.**
3. Lista dina ACS-namnområden med cmdleten **Get-AcsNamespace.**

### <a name="check-which-applications-will-be-impacted"></a>Kontrollera vilka program som kommer att påverkas

1. Använd namnområdet från föregående steg och gå till`https://<namespace>.accesscontrol.windows.net`

    Om ett av namnområdena till exempel är contoso-test går du till`https://contoso-test.accesscontrol.windows.net`

2. Under **Förtroenderelationer**väljer du **Förlitande part-program** för att se listan över appar som påverkas av ACS-pensionering.
3. Upprepa steg 1-2 för alla andra ACS-namnområden som du har.

## <a name="retirement-schedule"></a>Pensionsschema

Från och med november 2017 stöds och fungerar alla Access Control-komponenter fullt ut. Den enda begränsningen är att du [inte kan skapa nya namnområden för Åtkomstkontroll via den klassiska Azure-portalen](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Här är schemat för inaktuella Access Control-komponenter:

- **November 2017**: Azure AD-administratörsupplevelsen i Den klassiska Azure-portalen [har dragits tillbaka](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Nu finns namnområdeshantering för Åtkomstkontroll tillgänglig på en `https://manage.windowsazure.com?restoreClassic=true`ny, dedikerad URL: . Använd den här URl för att visa dina befintliga namnområden, aktivera och inaktivera namnområden och ta bort namnområden, om du väljer det.
- **April 2, 2018**: Den klassiska Azure-portalen är helt pensionerad, vilket innebär att åtkomstkontrollnamnområdeshantering inte längre är tillgänglig via någon URL. Nu kan du inte inaktivera eller aktivera, ta bort eller räkna upp namnområdena i Access Control. Access Control-hanteringsportalen kommer dock att `https://\<namespace\>.accesscontrol.windows.net`vara fullt fungerande och placerad på . Alla andra komponenter i Åtkomstkontroll fortsätter att fungera normalt.
- **7 november 2018**: Alla åtkomstkontrollkomponenter stängs av permanent. Detta inkluderar accesskontrollhanteringsportalen, hanteringstjänsten, STS och tokenomvandlingsregelmotorn. Nu misslyckas alla begäranden som skickas \<till\>Åtkomstkontroll (finns vid namnområdet .accesscontrol.windows.net). Du borde ha migrerat alla befintliga appar och tjänster till andra tekniker i god tid innan den här tiden.

> [!NOTE]
> En princip inaktiverar namnområden som inte har begärt en token under en viss tid. Från och med början av september 2018 är denna tidsperiod för närvarande på 14 dagars inaktivitet, men detta kommer att förkortas till 7 dagars inaktivitet under de kommande veckorna. Om du har accesskontrollnamnområden som för närvarande är inaktiverade kan du [hämta och installera ACS PowerShell](#download-and-install-acs-powershell) för att återaktivera namnområdena.

## <a name="migration-strategies"></a>Migreringsstrategier

I följande avsnitt beskrivs rekommendationer på hög nivå för att migrera från Åtkomstkontroll till andra Microsoft-tekniker.

### <a name="clients-of-microsoft-cloud-services"></a>Klienter till Microsofts molntjänster

Varje Microsoft-molntjänst som accepterar token som utfärdas av Åtkomstkontroll stöder nu minst en alternativ form av autentisering. Rätt autentiseringsmekanism varierar för varje tjänst. Vi rekommenderar att du hänvisar till den specifika dokumentationen för varje tjänst för officiell vägledning. För enkelhetens skull finns varje uppsättning dokumentation här:

| Tjänst | Riktlinjer |
| ------- | -------- |
| Azure Service Bus | [Migrera till signaturer för delad åtkomst](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [Migrera till signaturer för delad åtkomst](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure-hanterad cache | [Migrera till Azure Cache för Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Migrera till API:erna för Kognitiva tjänster](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk-tjänster | [Migrera till logic apps-funktionen i Azure App Service](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migrera till Azure AD-autentisering](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uppgradera Azure Backup-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-kunder

SharePoint 2013-, 2016- och SharePoint Online-kunder har länge använt ACS för autentisering i moln-, lokal- och hybridscenarier. Vissa SharePoint-funktioner och användningsfall påverkas av ACS-pensionering, medan andra inte kommer att göra det. I tabellen nedan sammanfattas migreringsvägledningen för några av de mest populära SharePoint-funktionen som utnyttjar ACS:

| Funktion | Riktlinjer |
| ------- | -------- |
| Autentisera användare från Azure AD | Tidigare stödde Inte Azure AD SAML 1.1-token som krävs av SharePoint för autentisering, och ACS användes som en mellanhand som gjorde SharePoint kompatibelt med Azure AD-tokenformat. Nu kan du [ansluta SharePoint direkt till Azure AD med Hjälp av Azure AD App Gallery SharePoint på lokal app](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Appautentisering & autentisering från server till server i lokal SharePoint](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Påverkas inte av ACS pensionering; inga ändringar behövs. | 
| [Låg förtroendeauktorisering för SharePoint-tillägg (värdleverantör och SharePoint-värd)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Påverkas inte av ACS pensionering; inga ändringar behövs. |
| [Hybridsökning i SharePoint-molnet](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Påverkas inte av ACS pensionering; inga ändringar behövs. |

### <a name="web-applications-that-use-passive-authentication"></a>Webbprogram som använder passiv autentisering

För webbprogram som använder Åtkomstkontroll för användarautentisering innehåller Access Control följande funktioner för utvecklare och arkitekter av webbprogram:

- Djup integrering med Windows Identity Foundation (WIF).
- Federation med Konton på Google, Facebook, Yahoo, Azure Active Directory och AD FS och Microsoft-konton.
- Stöd för följande autentiseringsprotokoll: OAuth 2.0 Draft 13, WS-Trust och Web Services Federation (WS-Federation).
- Stöd för följande tokenformat: JSON Web Token (JWT), SAML 1.1, SAML 2.0 och Simple Web Token (SWT).
- En home realm discovery-upplevelse, integrerad i WIF, som gör det möjligt för användare att välja vilken typ av konto de använder för att logga in. Den här upplevelsen är värd för webbprogrammet och är helt anpassningsbar.
- Tokenomvandling som möjliggör omfattande anpassning av de anspråk som tas emot av webbprogrammet från Åtkomstkontroll, inklusive:
    - Skicka igenom anspråk från identitetsleverantörer.
    - Lägga till ytterligare anpassade anspråk.
    - Enkel om-då logik att utfärda anspråk under vissa villkor.

Tyvärr finns det inte en tjänst som erbjuder alla dessa likvärdiga funktioner. Du bör utvärdera vilka funktioner i Åtkomstkontroll du behöver och sedan välja mellan att använda [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), Azure Active Directory [B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) eller en annan molnautentiseringstjänst.

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

En väg att tänka på är att integrera dina appar och tjänster direkt med Azure AD. Azure AD är molnbaserad identitetsleverantör för Microsofts arbets- eller skolkonton. Azure AD är identitetsprovidern för Office 365, Azure och mycket mer. Det ger liknande federerade autentiseringsfunktioner till Åtkomstkontroll, men stöder inte alla åtkomstkontrollfunktioner. 

Det primära exemplet är federation med sociala identitetsleverantörer, till exempel Facebook, Google och Yahoo. Om användarna loggar in med dessa typer av autentiseringsuppgifter är Azure AD inte lösningen för dig. 

Azure AD stöder inte nödvändigtvis exakt samma autentiseringsprotokoll som Åtkomstkontroll. Till exempel, även om både Åtkomstkontroll och Azure AD stöder OAuth, finns det subtila skillnader mellan varje implementering. Olika implementeringar kräver att du ändrar koden som en del av en migrering.

Azure AD ger dock flera potentiella fördelar för Access Control-kunder. Det stöder inbyggt Microsofts arbets- eller skolkonton som finns i molnet och som ofta används av Access Control-kunder. 

En Azure AD-klient kan också federeras med en eller flera instanser av lokala Active Directory via AD FS. På så sätt kan din app autentisera molnbaserade användare och användare som finns lokalt. Det stöder också WS-Federation-protokollet, vilket gör det relativt enkelt att integrera med ett webbprogram med hjälp av WIF.

I följande tabell jämförs funktionerna i Åtkomstkontroll som är relevanta för webbprogram med de funktioner som är tillgängliga i Azure AD. 

På en hög nivå *är Azure Active Directory förmodligen det bästa valet för din migrering om du låter användare logga in endast med sina Microsoft-arbets- eller skolkonton*.

| Funktion | Stöd för åtkomstkontroll | Azure AD-stöd |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsofts arbets- eller skolkonton | Stöds | Stöds |
| Konton från Active Directory och AD FS i Windows Server |- Stöds via federation med en Azure AD-klient <br />- Stöds via direkt federation med AD FS | Stöds endast via federation med en Azure AD-klient | 
| Konton från andra system för hantering av företagsidentiteter |- Möjligt via federation med en Azure AD-klient <br />- Stöds via direkt federation | Möjligt via federation med en Azure AD-klient |
| Microsoft-konton för personligt bruk | Stöds | Stöds via OAuth-protokollet för Azure AD v2.0, men inte över några andra protokoll | 
| Facebook, Google, Yahoo-konton | Stöds | Stöds inte alls |
| **Protokoll och SDK-kompatibilitet** | | |
| WIF | Stöds | Stöds, men begränsade instruktioner finns tillgängliga |
| WS-Federation | Stöds | Stöds |
| OAuth 2.0 | Stöd för utkast 13 | Stöd för RFC 6749, den modernaste specifikationen |
| WS-förtroende | Stöds | Stöds inte |
| **Tokenformat** | | |
| Jwt | Stöds i Beta | Stöds |
| SAML 1,1 | Stöds | Förhandsversion |
| SAML 2.0 | Stöds | Stöds |
| Swt | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbart användargränssnitt för identifiering/kontoplockning för hemsfär | Nedladdningsbar kod som kan införlivas i appar | Stöds inte |
| Ladda upp anpassade tokensigneringscertifikat | Stöds | Stöds |
| Anpassa anspråk i token |- Passera genom inmatningsanspråk från identitetsleverantörer<br />- Få åtkomsttoken från identitetsprovidern som ett anspråk<br />- Utfärda utdataanspråk baserat på värden för ingående fordringar<br />- Utfärda utdataanspråk med konstanta värden |- Kan inte passera anspråk från federerade identitetsleverantörer<br />- Det går inte att hämta åtkomsttoken från identitetsprovidern som ett anspråk<br />- Det går inte att utfärda utdataanspråk baserat på värden för indataanspråk<br />- Kan utfärda utdataanspråk med konstanta värden<br />- Kan utfärda utdataanspråk baserat på egenskaper för användare som synkroniserats med Azure AD |
| **Automation** | | |
| Automatisera konfigurations- och hanteringsuppgifter | Stöds via åtkomstkontrollhanteringstjänsten | Stöds med Hjälp av Microsoft Graph API |

Om du bestämmer dig för att Azure AD är den bästa migreringsvägen för dina program och tjänster bör du vara medveten om två sätt att integrera din app med Azure AD.

Om du vill använda WS-Federation eller WIF för att integrera med Azure AD rekommenderar vi att du följer metoden som beskrivs i [Konfigurera federerad enkel inloggning för ett program som inte är galleri](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Artikeln refererar till konfigurera Azure AD för SAML-baserade enkel inloggning, men fungerar också för att konfigurera WS-Federation. Enligt den här metoden krävs en Azure AD Premium-licens. Detta tillvägagångssätt har två fördelar:

- Du får full flexibilitet i Azure AD-token anpassning. Du kan anpassa anspråk som utfärdas av Azure AD för att matcha de anspråk som utfärdas av Åtkomstkontroll. Detta inkluderar särskilt anspråk på användar-ID eller namnidentifierare. Om du vill fortsätta att ta emot konsekvent användar-IDentifiers för användarna när du har ändrat teknik kontrollerar du att användar-ID:n som utfärdats av Azure AD matchar de som utfärdats av Åtkomstkontroll.
- Du kan konfigurera ett tokensigneringscertifikat som är specifikt för ditt program och med en livstid som du styr.

> [!NOTE]
> Den här metoden kräver en Azure AD Premium-licens. Om du är en Access Control-kund och behöver en premiumlicens för att konfigurera enkel inloggning för en applikation kontaktar du oss. Vi tillhandahåller gärna utvecklarlicenser som du kan använda.

En alternativ metod är att följa [detta kodexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), som ger lite olika instruktioner för att ställa in WS-Federation. Det här kodexemplet använder inte WIF, utan snarare ASP.NET 4,5 OWIN-mellanprogram. Instruktionerna för appregistrering är dock giltiga för appar som använder WIF och kräver inte en Azure AD Premium-licens. 

Om du väljer den här metoden måste du förstå [signering av nyckelöverrullning i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Den här metoden använder azure AD-globala signeringsnyckel för att utfärda token. Som standard uppdaterar WIF inte signeringsnycklar automatiskt. När Azure AD roterar sina globala signeringsnycklar måste WIF-implementeringen vara beredd att acceptera ändringarna. Mer information finns i [Viktig information om signering av nyckelöverrullning i Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Om du kan integrera med Azure AD via OpenID Connect- eller OAuth-protokollen rekommenderar vi att du gör det. Vi har omfattande dokumentation och vägledning om hur du integrerar Azure AD i ditt webbprogram som finns i vår [Azure AD-utvecklarguide](https://aka.ms/aaddev).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrera till Azure Active Directory B2C

Den andra migreringsvägen att tänka på är Azure AD B2C. Azure AD B2C är en molnautentiseringstjänst som, liksom Åtkomstkontroll, gör det möjligt för utvecklare att lägga ut sin autentiserings- och identitetshanteringslogik till en molntjänst. Det är en betald tjänst (med kostnadsfria nivåer och premiumnivåer) som är utformad för konsumentinriktade program som kan ha upp till miljontals aktiva användare.

Precis som Åtkomstkontroll är en av de mest attraktiva funktionerna i Azure AD B2C att den stöder många olika typer av konton. Med Azure AD B2C kan du logga in användare med hjälp av deras Microsoft-konto, eller Facebook-, Google-, LinkedIn-, GitHub- eller Yahoo-konton med mera. Azure AD B2C stöder också "lokala konton" eller användarnamn och lösenord som användare skapar specifikt för ditt program. Azure AD B2C ger också omfattande utökningsbarhet som du kan använda för att anpassa dina inloggningsflöden. 

Azure AD B2C stöder dock inte bredden av autentiseringsprotokoll och tokenformat som Access Control-kunder kan behöva. Du kan inte heller använda Azure AD B2C för att hämta token och fråga efter ytterligare information om användaren från identitetsprovidern, Microsoft eller på annat sätt.

I följande tabell jämförs funktionerna i Åtkomstkontroll som är relevanta för webbprogram med de som är tillgängliga i Azure AD B2C. På en hög nivå *är Azure AD B2C förmodligen det rätta valet för din migrering om ditt program är konsumentvänt, eller om det stöder många olika typer av konton.*

| Funktion | Stöd för åtkomstkontroll | Support för Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsofts arbets- eller skolkonton | Stöds | Stöds via anpassade principer  |
| Konton från Active Directory och AD FS i Windows Server | Stöds via direkt federation med AD FS | Stöds via SAML federation med hjälp av anpassade principer |
| Konton från andra system för hantering av företagsidentiteter | Stöds via direkt federation genom WS-Federation | Stöds via SAML federation med hjälp av anpassade principer |
| Microsoft-konton för personligt bruk | Stöds | Stöds | 
| Facebook, Google, Yahoo-konton | Stöds | Facebook och Google stöds internt, Yahoo stöds via OpenID Connect federation med hjälp av anpassade policyer |
| **Protokoll och SDK-kompatibilitet** | | |
| Windows Identity Foundation (WIF) | Stöds | Stöds inte |
| WS-Federation | Stöds | Stöds inte |
| OAuth 2.0 | Stöd för utkast 13 | Stöd för RFC 6749, den modernaste specifikationen |
| WS-förtroende | Stöds | Stöds inte |
| **Tokenformat** | | |
| Jwt | Stöds i Beta | Stöds |
| SAML 1,1 | Stöds | Stöds inte |
| SAML 2.0 | Stöds | Stöds inte |
| Swt | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbart användargränssnitt för identifiering/kontoplockning för hemsfär | Nedladdningsbar kod som kan införlivas i appar | Helt anpassningsbart användargränssnitt via anpassad CSS |
| Ladda upp anpassade tokensigneringscertifikat | Stöds | Anpassade signeringsnycklar, inte certifikat, som stöds via anpassade principer |
| Anpassa anspråk i token |- Passera genom inmatningsanspråk från identitetsleverantörer<br />- Få åtkomsttoken från identitetsprovidern som ett anspråk<br />- Utfärda utdataanspråk baserat på värden för ingående fordringar<br />- Utfärda utdataanspråk med konstanta värden |- Kan passera genom anspråk från identitetsleverantörer; anpassade principer som krävs för vissa anspråk<br />- Det går inte att hämta åtkomsttoken från identitetsprovidern som ett anspråk<br />- Kan utfärda utdataanspråk baserat på värden för indataanspråk via anpassade principer<br />- Kan utfärda utdataanspråk med konstanta värden via anpassade principer |
| **Automation** | | |
| Automatisera konfigurations- och hanteringsuppgifter | Stöds via åtkomstkontrollhanteringstjänsten |- Skapande av användare som tillåts med hjälp av Microsoft Graph API<br />- Det går inte att skapa B2C-klienter, program eller principer programmässigt |

Om du bestämmer dig för att Azure AD B2C är den bästa migreringsvägen för dina program och tjänster börjar du med följande resurser:

- [Azure AD B2C-dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Anpassade principer för Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C-priser](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till Ping-identitet eller Auth0

I vissa fall kan det hända att Azure AD och Azure AD B2C inte är tillräckliga för att ersätta Åtkomstkontroll i dina webbprogram utan att göra större kodändringar. Några vanliga exempel kan vara:

- Webbprogram som använder WIF eller WS-Federation för inloggning med leverantörer av sociala identiteter som Google eller Facebook.
- Webbprogram som utför direkt federation till en företagsidentitetsprovider över WS-Federation-protokollet.
- Webbprogram som kräver åtkomsttoken som utfärdats av en leverantör av social identitet (till exempel Google eller Facebook) som ett anspråk i de token som utfärdats av Åtkomstkontroll.
- Webbprogram med komplexa tokenomvandlingsregler som Azure AD eller Azure AD B2C inte kan återskapa.
- Webbprogram för flera innehavare som använder ACS för att centralt hantera federation till många olika identitetsleverantörer

I dessa fall kanske du vill överväga att migrera webbprogrammet till en annan molnautentiseringstjänst. Vi rekommenderar att du utforskar följande alternativ. Vart och ett av följande alternativ erbjuder funktioner som liknar Åtkomstkontroll:

|     |     |
| --- | --- |
| ![Den här bilden visar Auth0-logotypen](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs) är en flexibel molnidentitetstjänst som har skapat [migreringsvägledning på hög nivå för kunder i Åtkomstkontroll](https://auth0.com/acs)och stöder nästan alla funktioner som ACS har. |
| ![Den här bilden visar Ping Identity-logotypen](./media/active-directory-acs-migration/rsz-ping.png) | [Ping Identity](https://www.pingidentity.com) erbjuder två lösningar som liknar ACS. PingOne är en molnidentitetstjänst som stöder många av samma funktioner som ACS, och PingFederate är en liknande lokal identitetsprodukt som erbjuder mer flexibilitet. Se [Pings ACS pensionsvägledning](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) för mer information om hur du använder dessa produkter. |

Vårt mål med att arbeta med Ping Identity och Auth0 är att se till att alla Access Control-kunder har en migreringsväg för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från Åtkomstkontroll.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webbtjänster som använder aktiv autentisering

För webbtjänster som är skyddade med token som utfärdats av Åtkomstkontroll erbjuder Åtkomstkontroll följande funktioner:

- Möjlighet att registrera en eller flera *tjänstidentiteter* i ditt accesskontrollnamnområde. Tjänstidentiteter kan användas för att begära token.
- Stöd för OAuth WRAP- och OAuth 2.0 Draft 13-protokoll för begäran av token, med hjälp av följande typer av autentiseringsuppgifter:
    - Ett enkelt lösenord som har skapats för tjänstidentiteten
    - En signerad SWT med hjälp av en symmetrisk nyckel eller X509-certifikat
    - En SAML-token utfärdad av en betrodd identitetsprovider (vanligtvis en AD FS-instans)
- Stöd för följande tokenformat: JWT, SAML 1.1, SAML 2.0 och SWT.
- Enkla tokenomvandlingsregler.

Tjänstidentiteter i Åtkomstkontroll används vanligtvis för att implementera autentisering mellan server och server. 

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

Vår rekommendation för den här typen av autentiseringsflöde är att migrera till [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD är molnbaserad identitetsleverantör för Microsofts arbets- eller skolkonton. Azure AD är identitetsprovidern för Office 365, Azure och mycket mer. 

Du kan också använda Azure AD för server-till-server-autentisering med hjälp av Azure AD-implementeringen av OAuth-klientautentiseringsuppgifterna. I följande tabell jämförs funktionerna i Åtkomstkontroll vid server-till-server-autentisering med de som är tillgängliga i Azure AD.

| Funktion | Stöd för åtkomstkontroll | Azure AD-stöd |
| ---------- | ----------- | ---------------- |
| Så här registrerar du en webbtjänst | Skapa en förlitande part i hanteringsportalen för åtkomstkontroll | Skapa ett Azure AD-webbprogram i Azure-portalen |
| Så här registrerar du en kund | Skapa en tjänstidentitet i hanteringsportalen för åtkomstkontroll | Skapa ett annat Azure AD-webbprogram i Azure-portalen |
| Protokoll som används |- OAuth WRAP-protokollet<br />- OAuth 2.0 Utkast 13 klientuppgifter bevilja | Beviljande av autentiseringsuppgifter för OAuth 2.0-klient |
| Klientautentiseringsmetoder |- Enkelt lösenord<br />- Signerad SWT<br />- SAML-token från en federerad identitetsprovider |- Enkelt lösenord<br />- Signerad JWT |
| Tokenformat |- JWT<br />- SAML 1,1<br />- SAML 2,0<br />- SWT<br /> | Endast JVT |
| Token omvandling |- Lägg till anpassade anspråk<br />- Enkel om-då fordran utfärdande logik | Lägga till anpassade anspråk | 
| Automatisera konfigurations- och hanteringsuppgifter | Stöds via åtkomstkontrollhanteringstjänsten | Stöds med Hjälp av Microsoft Graph API |

Mer information om hur du implementerar server-till-server-scenarier finns i följande resurser:

- Service-to-Service-sektionen i [Utvecklarhandboken](https://aka.ms/aaddev) för Azure AD
- [Daemon-kodexempel med hjälp av enkla klientreferenser för lösenord](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Exempel på demonkod med hjälp av certifikatklientuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till Ping-identitet eller Auth0

I vissa fall kan du upptäcka att Azure AD-klientautentiseringsuppgifterna och OAuth-bevilja implementeringen inte är tillräckliga för att ersätta åtkomstkontroll i din arkitektur utan större kodändringar. Några vanliga exempel kan vara:

- Server-till-server-autentisering med andra tokenformat än JV: er.
- Autentisering från server till server med hjälp av en indatatoken som tillhandahålls av en extern identitetsprovider.
- Server-till-server-autentisering med tokenomvandlingsregler som Azure AD inte kan återskapa.

I dessa fall kan du överväga att migrera webbprogrammet till en annan molnautentiseringstjänst. Vi rekommenderar att du utforskar följande alternativ. Vart och ett av följande alternativ erbjuder funktioner som liknar Åtkomstkontroll:

|     |     |
| --- | --- |
| ![Den här bilden visar Auth0-logotypen](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs) är en flexibel molnidentitetstjänst som har skapat [migreringsvägledning på hög nivå för kunder i Åtkomstkontroll](https://auth0.com/acs)och stöder nästan alla funktioner som ACS har. |
| ![Den här bilden visar Ping Identity-logotypen](./media/active-directory-acs-migration/rsz-ping.png) | [Ping Identity](https://www.pingidentity.com) erbjuder två lösningar som liknar ACS. PingOne är en molnidentitetstjänst som stöder många av samma funktioner som ACS, och PingFederate är en liknande lokal identitetsprodukt som erbjuder mer flexibilitet. Se [Pings ACS pensionsvägledning](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) för mer information om hur du använder dessa produkter. |

Vårt mål med att arbeta med Ping Identity och Auth0 är att se till att alla Access Control-kunder har en migreringsväg för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från Åtkomstkontroll.

#### <a name="passthrough-authentication"></a>Genomgångsautentisering

För vidareströmsautentisering med godtycklig tokenomvandling finns det ingen motsvarande Microsoft-teknik som ACS. Om det är vad dina kunder behöver kan Auth0 vara den som tillhandahåller den närmaste approximationslösningen.

## <a name="questions-concerns-and-feedback"></a>Frågor, funderingar och feedback

Vi förstår att många Access Control-kunder inte hittar en tydlig migreringsväg efter att ha läst den här artikeln. Du kan behöva lite hjälp eller vägledning för att bestämma rätt plan. Om du vill diskutera dina migreringsscenarier och frågor kan du lämna en kommentar på den här sidan.
