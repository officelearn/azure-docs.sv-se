---
title: Migrera från Azure Access Control Service | Microsoft Docs
description: Läs mer om alternativ för att flytta appar och tjänster från Azure Access Control Service (ACS).
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2018
ms.author: celested
ms.reviewer: jlu, annaba, hirsin
ms.openlocfilehash: 805270fa4cc051929ecb1362f2d3cd4455a17a60
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423389"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Så här: migrera från Azure Access Control Service

Microsoft Azure Access Control Service (ACS), en tjänst i Azure Active Directory (Azure AD), tas ur bruk 7 November 2018. Program och tjänster som använder åtkomstkontroll måste fullständigt flyttas till en annan autentiseringsmekanism innan dess. Den här artikeln beskriver rekommendationer för befintliga kunder som du planerar att inaktualisera din användning av Access Control. Om du inte använder åtkomstkontroll, behöver du inte vidta några åtgärder.

## <a name="overview"></a>Översikt

Access Control är en molntjänst för autentisering som erbjuder ett enkelt sätt att autentisera och auktorisera användare för åtkomst till dina webbprogram och tjänster. Det gör att många funktioner för autentisering och auktorisering för räknas in från din kod. Åtkomstkontroll används främst av utvecklare och arkitekter från Microsoft .NET-klienter, ASP.NET-webbprogram och webbtjänster för Windows Communication Foundation (WCF).

Användningsområden för åtkomstkontroll kan delas upp i tre huvudkategorier:

- Autentisering till vissa Microsoft-molntjänster, inklusive Azure Service Bus och Dynamics CRM. Klientprogram hämta token från Access Control för att autentisera till dessa tjänster för att utföra olika åtgärder.
- Att lägga till autentisering för webbprogram, både anpassade och förpaketerade (till exempel SharePoint). Med hjälp av Access Control ”passiv” autentisering webbprogram har stöd för inloggning med ett Microsoft-konto (tidigare Live ID) och konton från Google, Facebook, Yahoo, Azure AD, och Active Directory Federation Services (AD FS).
- Skydda anpassade webb-tjänster med token som utfärdas av Access Control. Med hjälp av ”aktiv” autentisering, webbtjänster se till att de tillåter endast åtkomst till kända klienter som har autentiserats med åtkomstkontroll.

Var och en av dessa använda fall och rekommenderade migreringen strategier beskrivs i följande avsnitt.

> [!WARNING]
> I de flesta fall är betydande kodändringar krävs för att migrera befintliga appar och tjänster till nyare teknik. Vi rekommenderar att du omedelbart börja planera och kör din migrering för att undvika eventuella potentiella avbrott eller driftstopp.

Access Control har följande komponenter:

- En säker token-tjänsten (STS) som tar emot begäranden om autentisering och utfärdar säkerhetstoken i utbyte.
- Den klassiska Azure-portalen där du skapar, ta bort, och aktivera och inaktivera Access Control-namnområden.
- En separat Access Control-hanteringsportalen, där du kan anpassa och konfigurera åtkomstkontroll namnområden.
- En management-tjänst som du kan använda för att automatisera funktionerna portalerna.
- En token omvandling innehållsleveransmotor, som du kan använda för att definiera avancerad logik för att ändra formatet för utdata i de token som utfärdar Access Control.

Om du vill använda dessa komponenter måste du skapa ett eller flera Access Control-namnområden. En *namnområde* är en dedikerad instans av åtkomstkontroll som dina program och tjänster som kommunicerar med. Ett namnområde är isolerad från alla andra Access Control-kunder. Andra Access Control-kunder använda sina egna namnrymder. Ett namnområde i Access Control har en dedikerad URL som ser ut så här:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

All kommunikation med STS och hanteringsåtgärder är klar på denna URL. Du kan använda olika sökvägar för olika syften. För att avgöra om dina program eller tjänster använder Access Control, övervaka för all trafik till https://&lt;namnområde&gt;. accesscontrol.windows.net. All trafik till denna URL hanteras av Access Control och måste avbrytas. 

Undantaget till detta är all trafik till `https://accounts.accesscontrol.windows.net`. Trafik till denna URL hanteras redan av en annan tjänst och **är inte** påverkas av Access Control-utfasning. 

Mer information om åtkomstkontroll finns i [Access Control Service 2.0 (arkiverade)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Ta reda på vilka av dina appar kommer att påverkas

Följ stegen i det här avsnittet för att ta reda på vilka av dina appar kommer att påverkas av ACS dras tillbaka.

### <a name="download-and-install-acs-powershell"></a>Ladda ned och installera ACS-PowerShell

1. Gå till PowerShell-galleriet och hämta [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
1. Installera modulen genom att köra

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

1. Hämta en lista över alla möjliga kommandon genom att köra

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Om du behöver hjälp med ett visst kommando kör du:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    där `[Command-Name]` är namnet på ACS-kommandot.

### <a name="list-your-acs-namespaces"></a>Lista över ACS-namnområden

1. Anslut till ACS med hjälp av den **Connect AcsAccount** cmdlet.
  
    Du kan behöva köra `Set-ExecutionPolicy -ExecutionPolicy Bypass` innan du kan köra kommandon och vara administratör för dessa prenumerationer för att köra kommandona.

1. Lista över dina tillgängliga Azure-prenumerationer med hjälp av den **Get-AcsSubscription** cmdlet.
1. Lista din ACS namnområden med hjälp av den **Get-AcsNamespace** cmdlet.

### <a name="check-which-applications-will-be-impacted"></a>Kontrollera vilka program som påverkas

1. Användning av namnområdet från föregående steg och gå till `https://<namespace>.accesscontrol.windows.net`

    Till exempel om en av namnrymder är contoso-test, gå till `https://contoso-test.accesscontrol.windows.net`

1. Under **förtroenderelationer**väljer **förlitande partsprogram** vill se en lista över appar som påverkas av ACS dras tillbaka.
1. Upprepa steg 1 – 2 för alla andra ACS-namespace(s) som du har.

## <a name="retirement-schedule"></a>Tillbakadragning schema

Från och med November 2017 är alla komponenter i Access Control fullständigt stöds och operativa. Den enda begränsningen är att du [kan inte skapa nya Access Control-namnområden via den klassiska Azure-portalen](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Här är schemat för avvecklar Access Control-komponenter:

- **November 2017**: Azure AD-administratören användarupplevelsen i den klassiska Azure-portalen [har dragits tillbaka](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Nu hantering av namnområdet för åtkomstkontroll är tillgängligt på en ny, dedikerad URL: `https://manage.windowsazure.com?restoreClassic=true`. Använd denna URl att visa dina befintliga namnområden, aktivera och inaktivera namnområden och ta bort namnområden, om du vill.
- **Den 2 april 2018**: Azure klassiska portal helt dras tillbaka, vilket innebär att hantering av åtkomstkontroll namnområden är inte längre tillgänglig via alla URL: er. Du kan nu inaktivera eller aktivera, ta bort eller räkna upp dina Access Control-namnområden. Access Control-hanteringsportalen kommer dock att helt funktionella och finns på `https://\<namespace\>.accesscontrol.windows.net`. Alla andra komponenter i Access Control fortsätta att fungera normalt.
- **7 november 2018**: alla åtkomstkontroll komponenter permanent stänga. Detta inkluderar Access Control-hanteringsportalen, management-tjänsten, STS och token omvandling regeln motorn. Nu kan alla förfrågningar som skickas till Access Control (finns på \<namnområde\>. accesscontrol.windows.net) misslyckas. Du bör har migrerat alla befintliga appar och tjänster till andra tekniker bra före denna tidpunkt.

> [!NOTE]
> En princip inaktiverar namnområden som inte har begärt en token för en viss tidsperiod. Från och med tidig September 2018 denna tidsperiod är för närvarande på 14 dagar av inaktivitet, men detta kommer att förkortas till 7 dagars inaktivitet under de kommande veckorna. Om du har Access Control-namnområden som för tillfället är inaktiverade, kan du [ladda ned och installera ACS PowerShell](#download-and-install-acs-powershell) återaktivera namespace(s).

## <a name="migration-strategies"></a>Migreringsstrategier

I följande avsnitt beskrivs på hög nivå rekommendationer för att migrera från åtkomstkontroll till andra Microsoft-tekniker.

### <a name="clients-of-microsoft-cloud-services"></a>Klienter i Microsoft-molntjänster

Varje Microsoft-molntjänst som accepterar token som utfärdas av Access Control nu stöder minst en alternativ form av autentisering. Rätt autentiseringsmekanismen varierar för varje tjänst. Vi rekommenderar att du refererar till den specifika dokumentationen för respektive tjänst officiella vägledning. För att underlätta för finns varje uppsättning dokumentation här:

| Tjänst | Riktlinjer |
| ------- | -------- |
| Azure Service Bus | [Migrera till signaturer för delad åtkomst](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [Migrera till signaturer för delad åtkomst](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Managed Cache | [Migrera till Azure Redis Cache](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Migrera till API: er för Cognitive Services](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Migrera till Logic Apps-funktionen i Azure App Service](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migrera till Azure AD-autentisering](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uppgradera Azure Backup-agenten](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-kunder

SharePoint 2013, 2016 och SharePoint Online-kunder har länge använt ACS för autentisering i molnet, lokalt och hybrid-scenarier. Vissa funktioner i SharePoint och användningsfall kommer att påverkas av ACS dras tillbaka medan inte. I tabellen nedan sammanfattas riktlinjer för några av de mest populära SharePoint funktion som utnyttjar ACS:

| Funktion | Riktlinjer |
| ------- | -------- |
| Autentisera användare från Azure AD | Tidigare Azure AD har stöd inte för SAML 1.1-tokens som krävs för SharePoint för autentisering och ACS användes som en mellanhand som gjort SharePoint-kompatibelt med Azure AD-token formaterar. Nu kan du [ansluta SharePoint direkt till Azure AD med Azure AD-Appgalleriet SharePoint på lokala app](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Appautentisering & server-till-server-autentisering i SharePoint on-premises](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Inte påverkas av ACS tillbakadragande; Inga ändringar som behövs. | 
| [Låg förtroende auktorisering för SharePoint-tillägg (värd-providern och SharePoint som värd)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Inte påverkas av ACS tillbakadragande; Inga ändringar som behövs. |
| [SharePoint cloud hybrid search](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Inte påverkas av ACS tillbakadragande; Inga ändringar som behövs. |

### <a name="web-applications-that-use-passive-authentication"></a>Webbprogram som använder passiv autentisering

För webbprogram som använder åtkomstkontroll för autentisering av användare, innehåller Access Control följande funktioner och egenskaper för web application utvecklare och arkitekter:

- Djupgående integrering med Windows Identity Foundation (WIF).
- Federation med Google, Facebook, Yahoo, Azure Active Directory och AD FS-konton och Microsoft-konton.
- Stöd för följande autentiseringsprotokoll: OAuth 2.0-utkast 13, WS-Trust och Web Services Federation (WS-Federation).
- Stöd för följande token format: JSON Web Token (JWT), SAML 1.1, SAML 2.0 och Simple Web Token (SWT).
- En startsfär affärsanalyser, integrerade i WIF, som tillåter användare att välja den typ av konto som de använder för att logga in. Den här upplevelsen är värd för webbprogrammet och är helt anpassningsbar.
- Token omvandling som tillåter omfattande anpassning av anspråk som tas emot av webbprogrammet från Access Control, inklusive:
    - Passera anspråk från identitetsleverantörer.
    - Lägger till ytterligare anpassade anspråk.
    - Enkel if-then-logik för att utfärda anspråk på vissa villkor.

Det finns tyvärr inte en tjänst som erbjuder alla dessa motsvarande funktioner. Du ska utvärdera vilka funktioner i Access Control du behöver och välj sedan mellan att använda [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) eller en annan molnautentisering tjänsten.

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

En sökväg till Överväg integrerar dina appar och tjänster direkt med Azure AD. Azure AD är molnbaserad identitetsleverantör för Microsoft arbets- eller skolkonton. Azure AD är identitetsleverantören för Office 365, Azure och mycket mer. Det ger liknande federerad autentiseringsfunktioner för åtkomstkontroll, men stöder inte alla funktioner för åtkomstkontroll. 

Det primära exemplet är federation med sociala identitetsleverantörer, till exempel Facebook, Google och Yahoo. Om användarna måste logga in med de här typerna av autentiseringsuppgifter, Azure AD är inte lösningen för dig. 

Azure AD stöder inte heller nödvändigtvis exakt samma autentiseringsprotokoll som Access Control. Till exempel både åtkomstkontroll och Azure AD stöder OAuth, men det finns vissa skillnader mellan varje implementering. Olika implementeringar måste du ändra koden som en del av en migrering.

Dock tillhandahåller Azure AD flera potentiella fördelar för Access Control-kunder. Den internt stöder Microsoft arbets- eller skolkonton som ligger i molnet, och som ofta används av kunder för åtkomstkontroll. 

Också kan vara externa Azure AD-klient till en eller flera instanser av den lokala Active Directory via AD FS. På så sätt kan din app kan autentisera molnbaserade användare och användare som finns lokalt. Det stöder även WS-Federation-protokollet, vilket gör det relativt enkelt att integrera med ett webbprogram med hjälp av WIF.

I följande tabell jämförs de funktioner för åtkomstkontroll som är relevanta för webbprogram med de funktioner som är tillgängliga i Azure AD. 

På en hög nivå *Azure Active Directory är antagligen det bästa valet för migreringen om du låter användare registrera i endast med sina Microsoft arbets- eller skolkonton*.

| Funktion | Stöd för kontroll av åtkomst | Stöd för Azure AD |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Arbets- eller skolkonto för Microsoft | Stöds | Stöds |
| Konton från Windows Server Active Directory och AD FS |-Stöds via federation med en Azure AD-klient <br />-Stöds via direct federation med AD FS | Stöds endast via federation med en Azure AD-klient | 
| Konton från andra företagssystem identity management |-Möjlig via federation med en Azure AD-klient <br />-Stöds via direct federation | Möjligt via federation med en Azure AD-klient |
| Microsoft-konton för personligt bruk | Stöds | Stöds via Azure AD v2.0 OAuth-protokollet, men inte över några andra protokoll | 
| Facebook, Google, Yahoo konton | Stöds | Stöds inte helst |
| **Protokoll och SDK-kompatibilitet** | | |
| WIF | Stöds | Stöds men begränsade instruktioner finns |
| WS-Federation | Stöds | Stöds |
| OAuth 2.0 | Stöd för utkast 13 | Stöd för RFC 6749, i de flesta moderna specifikationen |
| WS-Trust | Stöds | Stöds inte |
| **Token-format** | | |
| JWT | Stöds i Beta | Stöds |
| SAML 1.1 | Stöds | Förhandsversion |
| SAML 2.0 | Stöds | Stöds |
| SWT | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbara startsfär identifiering/konto-välja UI | Nedladdningsbara kod som kan införlivas i appar | Stöds inte |
| Ladda upp anpassade certifikat för tokensignering | Stöds | Stöds |
| Anpassa anspråk i token |-Passera inkommande anspråk från identitetsleverantörer<br />-Hämta åtkomsttoken från identitetsprovider som ett anspråk<br />-Utfärda utgående anspråk baserat på värdena för inkommande anspråk<br />-Utfärda utgående anspråk med fasta värden |-Det går inte att passera anspråk från federerad identitetsleverantörer<br />-Det går inte att hämta åtkomsttoken från identitetsprovider som ett anspråk<br />-Det går inte att utfärda utgående anspråk baserat på värdena för inkommande anspråk<br />-Kan utfärda utgående anspråk med fasta värden<br />-Kan utfärda utgående anspråk baserat på egenskaperna för användare som synkroniseras till Azure AD |
| **Automation** | | |
| Automatisera konfiguration och hantering av uppgifter | Stöds via Access Control Management Service | Stöds via Microsoft Graph och Azure AD Graph API |

Om du väljer att Azure AD är den bästa migreringsvägen för dina program och tjänster, bör du vara medveten om två sätt att integrera din app med Azure AD.

Om du vill använda WS-Federation eller WIF för att integrera med Azure AD, rekommenderar vi följande metoden som beskrivs i [konfigurera federerad enkel inloggning för en icke-galleriprogram](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Artikeln refererar till att konfigurera Azure AD för SAML-baserad enkel inloggning, men fungerar även för att konfigurera WS-Federation. Den här metoden kräver en Azure AD Premium-licens. Den här metoden har två fördelar:

- Du får fullständig flexibilitet för Azure AD-token anpassning. Du kan anpassa de anspråk som utfärdas av Azure AD för att matcha de anspråk som utfärdas av Access Control. Detta inkluderar särskilt användar-ID eller namnidentifierare anspråket. Se till att användar-ID utfärdats av Azure AD matchar de utfärdade av Access Control för att fortsätta att ta emot konsekvent användaridentifierare för dina användare när du har ändrat tekniker.
- Du kan konfigurera ett certifikat för tokensignering som är specifik för ditt program, och med en livslängd som du bestämmer.

> [!NOTE]
> Den här metoden kräver en Azure AD Premium-licens. Om du är en Access Control-kund och du behöver en premiumlicens för att konfigurera enkel inloggning för ett program, kontakta oss. Vi gärna att ge utvecklare licenser som du kan använda.

En annan metod är att följa [detta kodexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), vilket ger något annorlunda instruktioner för att konfigurera WS-Federation. Det här kodexemplet använder inte WIF, men i stället ASP.NET 4.5 OWIN-mellanprogrammet. Men instruktionerna för registrering av appen är giltiga för appar som använder WIF och kräver inte en Azure AD Premium-licens. 

Om du väljer den här metoden måste du förstå [signeringsnyckel i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Den här metoden använder Azure AD globala signeringsnyckel till problemet token. Som standard uppdateras WIF inte automatiskt Signeringsnycklar. När Azure AD roterar dess globala Signeringsnycklar, måste implementeringen WIF förberedas för att godkänna ändringarna. Mer information finns i [viktig information om signeringsnyckel i Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Om du kan integrera med Azure AD via OpenID Connect eller OAuth-protokoll, rekommenderar vi då. Vi har en omfattande dokumentation och vägledning om hur du integrerar Azure AD i ditt webbprogram som är tillgängliga i vår [utvecklarguide för Azure AD](https://aka.ms/aaddev).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrera till Azure Active Directory B2C

Andra migreringssökväg att tänka på är Azure AD B2C. Azure AD B2C är en molntjänst för autentisering som gör att utvecklare kan lägga ut sin autentisering och identitet management logik i en molnbaserad tjänst som Access Control. Det är en betaltjänst (med kostnadsfri och premium-nivåer) som är utformad för konsumentorienterade program som kan ha upp till flera miljoner aktiva användare.

Precis som Access Control är en av de mest attraktiva funktionerna i Azure AD B2C att den stöder många olika typer av konton. Med Azure AD B2C kan du logga in användare med hjälp av sitt Microsoft-konto eller Facebook, Google, LinkedIn, GitHub eller Yahoo konton och mycket mer. Azure AD-B2C stöder också ”lokala konton” eller användarnamn och lösenord som användare skapar specifikt för ditt program. Azure AD B2C tillhandahåller även omfattande skalbarhet som du kan använda för att anpassa dina flöden för inloggning. 

Dock har inte stöd för Azure AD B2C-autentiseringsprotokoll bredd och token formaterar åtkomstkontrollen kunder kan kräva. Du kan också använda Azure AD B2C för att hämta token och fråga för ytterligare information om användaren från identitetsprovidern, Microsoft eller på annat sätt.

I följande tabell jämförs de funktioner för åtkomstkontroll som är relevanta för webbprogram med de som är tillgängliga i Azure AD B2C. På en hög nivå *Azure AD B2C förmodligen är det rätta valet för din migrering om programmet kan riktas mot konsumenter, eller om den har stöd för många olika typer av konton.*

| Funktion | Stöd för kontroll av åtkomst | Support för Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Arbets- eller skolkonto för Microsoft | Stöds | Stöds via anpassade principer  |
| Konton från Windows Server Active Directory och AD FS | Stöds via direct federation med AD FS | Stöds via SAML federation med hjälp av anpassade principer |
| Konton från andra företagssystem identity management | Stöds via direct federation via WS-Federation | Stöds via SAML federation med hjälp av anpassade principer |
| Microsoft-konton för personligt bruk | Stöds | Stöds | 
| Facebook, Google, Yahoo konton | Stöds | Facebook och Google stöds internt, Yahoo stöds via OpenID Connect federation med hjälp av anpassade principer |
| **Protokoll och SDK-kompatibilitet** | | |
| Windows Identity Foundation (WIF) | Stöds | Stöds inte |
| WS-Federation | Stöds | Stöds inte |
| OAuth 2.0 | Stöd för utkast 13 | Stöd för RFC 6749, i de flesta moderna specifikationen |
| WS-Trust | Stöds | Stöds inte |
| **Token-format** | | |
| JWT | Stöds i Beta | Stöds |
| SAML 1.1 | Stöds | Stöds inte |
| SAML 2.0 | Stöds | Stöds inte |
| SWT | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbara startsfär identifiering/konto-välja UI | Nedladdningsbara kod som kan införlivas i appar | Helt anpassningsbara Gränssnittet via anpassade CSS |
| Ladda upp anpassade certifikat för tokensignering | Stöds | Anpassade Signeringsnycklar, inte certifikat stöds via anpassade principer |
| Anpassa anspråk i token |-Passera inkommande anspråk från identitetsleverantörer<br />-Hämta åtkomsttoken från identitetsprovider som ett anspråk<br />-Utfärda utgående anspråk baserat på värdena för inkommande anspråk<br />-Utfärda utgående anspråk med fasta värden |-Kan passera anspråk från identitetsleverantörer; anpassade principer som krävs för några anspråk<br />-Det går inte att hämta åtkomsttoken från identitetsprovider som ett anspråk<br />-Kan utfärda utgående anspråk baserat på värdena för inkommande anspråk via anpassade principer<br />-Kan utfärda utgående anspråk med fasta värden via anpassade principer |
| **Automation** | | |
| Automatisera konfiguration och hantering av uppgifter | Stöds via Access Control Management Service |-Skapa användare som tillåts via Azure AD Graph API<br />-Det går inte att skapa B2C-klienter, program eller principer programmässigt |

Om du väljer att Azure AD B2C är den bästa migreringsvägen för dina program och tjänster, börjar du med följande resurser:

- [Dokumentation om Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Priser för Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till Ping Identity eller Auth0

I vissa fall kan det kan hända att Azure AD och Azure AD B2C inte är tillräckligt för att ersätta Access Control i dina webbprogram utan att göra ändringar i större koden. Några vanliga exempel kan vara:

- Webbprogram som använder WIF eller WS-Federation för inloggning med sociala identitetsleverantörer, till exempel Google eller Facebook.
- Webbprogram som utför direkt federation till en enterprise-identitetsprovider över WS-Federation-protokollet.
- Webbprogram som kräver åtkomst-token som utfärdats av en social identitetsprovider (till exempel Google eller Facebook) som ett anspråk i token som utfärdas av Access Control.
- Webbprogram med komplexa token omvandling regler som Azure AD eller Azure AD B2C inte kan återskapa.
- Webbprogram för flera klienter som använder ACS centralt hantera federation till många olika identitetsleverantörer

I dessa fall kan vilja du bör du migrera ditt webbprogram till en annan molntjänst för autentisering. Vi rekommenderar att utforska följande alternativ. Var och en av följande alternativ erbjuder funktioner som liknar Access Control:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) är en flexibel molnidentitetstjänst som har skapat [övergripande riktlinjer för kunder för åtkomstkontroll](https://auth0.com/acs), och har stöd för nästan alla funktioner som ACS. |
| ![Pinga](./media/active-directory-acs-migration/rsz_ping.png) | [Ping Identity](https://www.pingidentity.com) erbjuder två lösningar som liknar ACS. PingOne är en molnbaserad identitetstjänst som har stöd för många av samma funktioner som ACS och PingFederate är en produkt med liknande lokal identitet som erbjuder mer flexibilitet. Referera till [Ping's ACS tillbakadragande vägledning](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) för mer information om hur du använder dessa produkter. |

Vårt syfte i att arbeta med Ping Identity och Auth0 är att säkerställa att alla Access Control-kunder har en migreringsvägen för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från Access Control.

<!--

## Sharepoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webbtjänster som använder active-autentisering

Access Control erbjuder följande funktioner och egenskaper för webbtjänster som skyddas med token som utfärdas av Access Control:

- Möjligheten att registrera en eller flera *tjänstidentiteter* i Access Control-namnområdet. Tjänstidentiteter kan användas för att begära token.
- Stöd för OAuth OMSLUTA och OAuth 2.0 Draft 13 protokoll för att begära token, med hjälp av följande typer av autentiseringsuppgifter:
    - Enkla lösenord som har skapats för tjänstidentitet
    - En signerad SWT med hjälp av en symmetrisk nyckel eller X509 certifikat
    - En SAML-token som utfärdats av en betrodd identitetsleverantör (vanligtvis en AD FS-instans)
- Stöd för följande token format: JWT, SAML 1.1, SAML 2.0 och SWT.
- Enkel token omvandling regler.

-Tjänstidentiteter i Access Control används vanligtvis för att implementera server-till-server-autentisering. 

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

Vår rekommendation för den här typen av autentiseringsflödet är att migrera till [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD är molnbaserad identitetsleverantör för Microsoft arbets- eller skolkonton. Azure AD är identitetsleverantören för Office 365, Azure och mycket mer. 

Du kan också använda Azure AD för autentisering av server-till-server med hjälp av Azure AD-implementeringen av OAuth-klientautentiseringsuppgifter. I följande tabell jämförs funktionerna i Access Control i server-till-server-autentisering med de som är tillgängliga i Azure AD.

| Funktion | Stöd för kontroll av åtkomst | Stöd för Azure AD |
| ---------- | ----------- | ---------------- |
| Så här registrerar du en webbtjänst | Skapa en förlitande part i Access Control-hanteringsportalen | Skapa en Azure AD-webbapp i Azure portal |
| Så här registrerar du en klient | Skapa en tjänstidentitet i Access Control-hanteringsportalen | Skapa en annan Azure AD-webbappen i Azure portal |
| Protokoll som används |-OAuth OMSLUTNING protokoll<br />-Beviljande av autentiseringsuppgifter för OAuth 2.0 Draft 13-klient | Beviljande av autentiseringsuppgifter för OAuth 2.0-klient |
| Klientautentiseringsmetoder |-Enkla lösenord<br />-Signerade SWT<br />-SAML token från en extern identitetsprovider |-Enkla lösenord<br />-Signerade JWT |
| Token-format |-JWT<br />-SAML 1.1<br />-SAML 2.0<br />-SWT<br /> | Endast JWT |
| Token-transformering |-Lägga till anpassade anspråk<br />-Enkel if-then-anspråksutfärdande logik | Lägg till anpassade anspråk | 
| Automatisera konfiguration och hantering av uppgifter | Stöds via Access Control Management Service | Stöds via Microsoft Graph och Azure AD Graph API |

Information om hur du implementerar server-till-server-scenarier finns i följande resurser:

- Tjänst-till-tjänst-avsnittet i den [utvecklarguide för Azure AD](https://aka.ms/aaddev)
- [Daemon för kodexempel med hjälp av enkla lösenord klientautentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Daemon för kodexempel med hjälp av autentiseringsuppgifter för certifikat-klient](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till Ping Identity eller Auth0

I vissa fall kanske du upptäcker att autentiseringsuppgifter för Azure AD-klienten och OAuth ger implementering inte är tillräckligt för att ersätta Access Control i din arkitektur utan större kodändringar. Några vanliga exempel kan vara:

- Server-till-server-autentisering med hjälp av token format än JWTs.
- Server-till-server-autentisering med hjälp av en inkommande token som tillhandahålls av en extern identitetsleverantör.
- Server-till-server-autentisering med token omvandling regler som Azure AD inte kan återskapa.

I dessa fall kan du migrera ditt webbprogram till en annan molntjänst för autentisering. Vi rekommenderar att utforska följande alternativ. Var och en av följande alternativ erbjuder funktioner som liknar Access Control:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) är en flexibel molnidentitetstjänst som har skapat [övergripande riktlinjer för kunder för åtkomstkontroll](https://auth0.com/acs), och har stöd för nästan alla funktioner som ACS. |
| ![Pinga](./media/active-directory-acs-migration/rsz_ping.png) | [Ping Identity](https://www.pingidentity.com) erbjuder två lösningar som liknar ACS. PingOne är en molnbaserad identitetstjänst som har stöd för många av samma funktioner som ACS och PingFederate är en produkt med liknande lokal identitet som erbjuder mer flexibilitet. Referera till [Ping's ACS tillbakadragande vägledning](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) för mer information om hur du använder dessa produkter. |

Vårt syfte i att arbeta med Ping Identity och Auth0 är att säkerställa att alla Access Control-kunder har en migreringsvägen för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från Access Control.

#### <a name="passthrough-authentication"></a>Direktautentisering

För-autentisering med godtyckliga token omvandling finns det ingen motsvarande Microsoft-teknik till ACS. Om det är vad dina kunder behöver vara Auth0 som ger den närmaste uppskattning-lösningen.

## <a name="questions-concerns-and-feedback"></a>Frågor och funderingar feedback

Vi förstår att många kunder för åtkomstkontroll inte att hitta en säker migreringssökväg när du har läst den här artikeln. Du kanske behöver lite hjälp eller vägledning för att fastställa rätt plan. Om du vill diskutera dina Migreringsscenarier och frågor kan du lämna en kommentar på den här sidan.
