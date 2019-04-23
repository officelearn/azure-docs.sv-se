---
title: Planera en distribution i Azure Active Directory Application Proxy
description: En slutpunkt till slutpunkt-guide för planering av distribution av Application proxy i din organisation
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04-04-2019
ms.author: barbaraselden
ms.reviewer: ''
ms.openlocfilehash: 44393f80ab6ea01f0c2f52cb01dcd6241fab3d2d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000716"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planera en distribution av Azure AD Application Proxy

Azure Active Directory (Azure AD) Application Proxy är en lösning för säker och kostnadseffektiv fjärråtkomst för lokala program. Det ger en omedelbar övergången sökväg för ”Cloud First” organisationer att hantera åtkomst till äldre lokala program som ännu inte kan använda modern protokoll. Ytterligare inledande information finns i [vad är Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) och [hur programmet fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Den här artikeln innehåller de resurser du behöver för att planera, driva och hantera Azure AD-programproxy. 

## <a name="plan-your-implementation"></a>Planera implementeringen

Följande avsnitt innehåller en bred vy för nyckeln planera element som kommer in du för en effektiv distributionsupplevelse. 

### <a name="prerequisites"></a>Nödvändiga komponenter

Du måste uppfylla följande krav innan du påbörjar din implementering. Du kan se mer information om hur du konfigurerar din miljö, inklusive dessa krav i det här [självstudien](application-proxy-add-on-premises-application.md).

* **Kopplingar**: Kopplingar är förenklad agenter som du kan distribuera till:
   * Fysisk maskinvara på plats
   * En virtuell dator i en hypervisor-lösning
   * En virtuell dator i Azure för att aktivera utgående anslutning till Application Proxy-tjänsten.

Se [förstå Azure AD App Proxy kopplingar](application-proxy-connectors.md) för en mer detaljerad översikt.

   * Anslutningen är värd för måste [aktiveras för TLS 1.2](application-proxy-add-on-premises-application.md) innan du installerar anslutningarna.

   * Distribuera om möjligt kopplingar i den [samma nätverk](application-proxy-network-topology.md) och segment som backend-webb-programservrar. Det är bäst att distribuera anslutningstjänsten värdar när du har slutfört en identifiering av program.

* **Inställningar för åtkomst**: Azure AD Application Proxy-anslutningsappar [försöker ansluta till Azure via HTTPS (TCP-Port 443) och HTTP (TCP-Port 80)](application-proxy-add-on-premises-application.md). 

   * Avslutande connector TLS trafik stöds inte och förhindra att kopplingar upprättar en säker kanal med sina respektive Azure App Proxy-slutpunkter.

   * Undvik att alla former av infogade-kontroll på utgående TLS-kommunikationen mellan kopplingar och Azure. Intern granskning mellan en koppling och backend-program kan dock försämra användarupplevelsen och rekommenderas därför inte.

   * Belastningsutjämning av proxyanslutningsprogram själva är inte heller stöds eller nödvändigt.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Att tänka på innan du konfigurerar Azure AD Application Proxy

Följande grundläggande krav måste uppfyllas för att konfigurera och implementera Azure AD-programproxy.

*  **Azure onboarding**: Innan du distribuerar programproxy användaridentiteter synkroniseras från en lokal katalog eller skapat direkt i din Azure AD-klienter. Identitetssynkronisering kan Azure AD att autentisera användare förväg innan bevilja dem åtkomst till App Proxy publicerade program och ha nödvändiga användarinformationen identifierare för enkel inloggning (SSO).

* **Offentliga certifikat**: Om du använder anpassade domännamn, måste du skaffa ett offentligt certifikat som utfärdats av en icke-Microsoft betrodd certifikatutfärdare. Hämta ett certifikat kan ta lite tid beroende på din organisations krav, och vi rekommenderar börjar så snart som möjligt. Standard, har stöd för Azure Application Proxy [jokertecken](application-proxy-wildcard.md), eller SAN-baserade certifikat.

* **Domänkrav**: Kräver att en värd för anslutningen är domänansluten till samma AD-domän som de program som publicerar enkel inloggning till dina publicerade program med hjälp av Kerberos-begränsad delegering (KCD). Detaljerad information om ämnet finns i [KCD för enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md) med programproxy. Connector-tjänsten körs i kontexten för det lokala systemet och ska inte konfigureras för att använda en anpassad identitet.

* **DNS-poster för URL: er**

   * Innan du använder anpassade domäner i Application Proxy måste du skapa en CNAME-post i offentlig DNS så att klienterna att lösa den anpassade definierade externa URL: en till den fördefinierade Application Proxy-adressen. Inte kan skapa en CNAME-post för ett program som använder en anpassad domän förhindrar att fjärranslutna användare ansluta till programmet. Steg som krävs för att lägga till CNAME-poster kan skilja sig från DNS leverantörer, så Läs hur du [hantera DNS-poster och postuppsättningar med hjälp av Azure portal](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * På samma sätt måste connector värdar kunna matcha den interna URL: en för program som publiceras.

* **Administrativa behörigheter och roller**

   * **Installation av** kräver lokal administratörsbehörighet för den Windows-server som installeras på. Det också kräver minst en administratörsroll för programmet att autentisera och registrera connector-instans till Azure AD-klienten. 

   * **Programpublicering och administration** kräver den *programadministratör* rollen. Administratörer kan hantera alla program i katalogen, inklusive registreringar, inställningar för enkel inloggning, användaren och grupptilldelningar och licensiering, Application Proxy-inställningar och godkännande. Det ger inte möjlighet att hantera villkorlig åtkomst. Den *Molnprogramadministratör* rollen har alla funktioner av programadministratör förutom att den inte tillåter hantering av Application Proxy-inställningar.

* **Licensiering**: Programproxyn är tillgänglig via Azure AD Basic-prenumeration. Referera till den [Azure Active Directory-prissättning](https://azure.microsoft.com/pricing/details/active-directory/) för en fullständig lista över licensiering alternativ och funktioner. 

* En höjning av rollen kan komma att behöva hämta programmet administratörsbehörighet via [Privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) (PIM), så kontrollera att ditt konto är berättigade. 

### <a name="application-discovery"></a>Programidentifiering

Kompilera en förteckning över alla program som omfattade som publiceras via programproxyn genom att samla in följande information:

| Informationstyp| Att samla in |
|---|---|
| Typ av tjänst| Exempel: SharePoint, SAP, CRM, anpassat webbprogram, API: et |
| Programplattform | Exempel: Windows IIS, Apache på Linux, Tomcat eller NGINX |
| Domänmedlemskap| Webbserverns fullständigt kvalificerade domännamnet (FQDN) |
| Application plats | Var webbserver eller -grupp finns i din infrastruktur |
| Intern åtkomst | Den URL som används vid åtkomst till programmet internt. <br> Om en grupp är vilken typ av belastningsutjämning används? <br> Om programmet hämtar innehåll från källor än den egna.<br> Avgör om programmet fungerar över WebSockets. |
| Extern åtkomst | Leverantörslösning som programmet redan exponeras för externt. <br> Den URL som du vill använda för extern åtkomst. Om SharePoint, se till att alternativa åtkomstmappningar konfigureras per [den här vägledningen](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Om inte, du måste definiera externa URL: er. |
| Offentliga certifikat | Om du använder en anpassad domän, skaffa ett certifikat med ett motsvarande ämnesnamn. Observera serienummer och plats där det kan finnas tillgängliga om det finns ett certifikat. |
| Autentiseringstyp| Typ av autentisering som stöds av programmets support, till exempel Basic, Windows-integrerad autentisering, formulärbaserad, rubrikbaserad, och anspråk. <br>Om programmet är konfigurerad för att köras under en viss domänkonto, Observera det fullständigt kvalificerade domännamn (FQDN) för kontot.<br> Om SAML-baserad URL: er med identifierare och svar. <br> Om rubrikbaserad, skriver leverantörslösning och särskilda krav för hantering av autentisering. |
| Namn på anslutningen | Det logiska namnet för gruppen med kopplingar som avsedd för att ange överföringskanal och enkel inloggning till backend-programmet. |
| Användare/grupper åtkomst | De användare eller användargrupper som ska ha extern åtkomst till programmet. |
| Ytterligare krav | Observera några ytterligare fjärråtkomst eller säkerhetskrav som ska räknas in i publicera programmet. |

Du kan ladda ned det [programvaruinventering kalkylblad](https://aka.ms/appdiscovery) att inventera dina appar.

### <a name="define-organizational-requirements"></a>Definiera organisatoriska krav

Här följer områden som du bör definiera din organisations behov. Varje område innehåller exempel på krav

 **Åtkomst**

* Domän- och Azure AD-användare kan komma åt publicerade program på ett säkert sätt med sömlös enkel inloggning (SSO) när på alla domänanslutna och Azure AD-anslutna enheter.

* Användare med godkända personliga enheter kan få säker åtkomst till publicerade program förutsatt att de är registrerade i MFA och har registrerat Microsoft Authenticator-appen på sin mobiltelefon som autentiseringsmetod.

**Styrning** 

* Administratörer kan definiera och övervaka livscykeln för användartilldelningar till program som publicerats via programproxy.

**Säkerhet**

* Endast användare tilldelade till program via gruppmedlemskap eller individuellt kan komma åt dessa program.

**Prestanda**

* Det finns inga försämring av prestanda jämfört med åtkomst till program från interna nätverket.

**Användarupplevelse**

* Användarna är medvetna om hur du kommer åt sina program med hjälp av välbekanta företagets URL: er på valfri enhetsplattform.

**Granskning**
* Administratörer kan granska användaraktivitet för åtkomst.


### <a name="best-practices-for-a-pilot"></a>Metodtips för ett pilotprojekt

Bestäm mängden tid och ansträngning som krävs för att fullständigt ett enda program för fjärråtkomst med enkel inloggning (SSO). Du kan göra det genom att köra ett pilotprojekt som tar hänsyn till den inledande identifieringen, publicering och allmänt testningen. Med hjälp av en enkel IIS-baserad webbapp som redan är förkonfigurerade för integrerad Windows autentisering (IWA) skulle att skapa en baslinje, eftersom den här installationen kräver minimal ansträngning har pilot fjärråtkomst och enkel inloggning.

Följande designelement bör öka framgången för implementeringen pilot direkt i en produktionsklient.  

**-Kopplingens**:  

* Kopplingar spela en viktig roll i att tillhandahålla en lokal överföringskanal till dina program. Med hjälp av den **standard** anslutningsgrupp är tillräcklig för inledande pilot testning av publicerade program innan idriftsättning dem till produktion. Har testade program kan sedan flyttas till produktion anslutningsapp-grupper.

**Programhantering**:

* Personalen sannolikt att komma ihåg en extern Webbadress som är vanliga och relevanta. Undvik att publicera ditt program med våra fördefinierade msappproxy.net eller onmicrosoft.com-suffix. I stället ange en bekant översta verifierad domän föregås av ett logiskt värdnamn som *intranät. < customers_domain > .com*.

* Begränsa synligheten för pilot programikonen för en pilotgrupp genom att dölja sitt starta ikonen format Azure MyApps-portalen. När du är klar för produktion kan du begränsa appen att dess respektive målgrupp, i samma Förproduktion klienten eller genom att även publicera programmet i din produktionsklient.

**Enkel inloggning inställningar**: Vissa inställningar för enkel inloggning har specifika beroenden som kan ta tid att ställa in, så Undvik att ändringskontroll fördröjningar genom att säkerställa beroenden behandlas i tid. Detta inkluderar domänen som ansluter till anslutningen värdar för att utföra SSO med hjälp av Kerberos-begränsad delegering (KCD) och att ta hand om andra tidskrävande aktiviteter. Till exempel ställa in en PING Access-instans om behöver huvud-baserad enkel inloggning.

**SSL mellan värd för anslutningen och målprogrammet**: Säkerhet är avgörande, så TLS mellan anslutningsprogram för värden och målet alltid ska användas. Särskilt om webbprogrammet har konfigurerats för formulärbaserad autentisering (FBA) när användaruppgifter skickas sedan effektivt i klartext.

**Implementera stegvis och testa varje steg**. Utför grundläggande funktionell testning när du har publicerat ett program så att alla användare och företag krav uppfylls genom att följa anvisningarna nedan:

1. Testa och validera allmän åtkomst till webbprogrammet med förautentisering inaktiverad.
2. Om detta lyckas aktivera förautentisering och tilldela användare och grupper. Testa och verifiera åtkomst.
3. Sedan lägger du till SSO-metod för ditt program och testa igen för att verifiera åtkomst.
4. Använda villkorlig åtkomst och MFA-principer som krävs. Testa och verifiera åtkomst.

**Felsökningsverktyg**: När du felsöker kan alltid börja genom att verifiera åtkomst till det publicerade programmet i webbläsaren på connector-värden och bekräfta att programmet fungerar som förväntat. Den enklare din konfiguration, desto lättare att fastställa rotorsaken, så fundera över försöker återskapa problem med en minimal konfiguration, till exempel med bara en enda anslutning och ingen SSO. I vissa fall bevisa verktyg, till exempel Telerik Fiddler för felsökning av webbplatser oumbärlig för felsökning av problem med åtkomst eller innehåll i program som nås via en proxyserver. Fiddler kan också fungera som en proxy för att spåra och felsöka trafik för mobila plattformar som iOS och Android och praktiskt taget allt som kan konfigureras för dirigering via en proxyserver. Se den [felsökningsguide](application-proxy-troubleshoot.md) för mer information.

## <a name="implement-your-solution"></a>Implementera din lösning

### <a name="deploy-application-proxy"></a>Distribuera Application Proxy

Stegen för att distribuera Application Proxy beskrivs i det här [självstudie för att lägga till ett lokalt program för fjärråtkomst](application-proxy-add-on-premises-application.md). Om installationen inte lyckas, Välj **felsöka programproxyn** i portalen eller Använd felsökningsguiden[för problem med att installera den Programproxyagenten](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publicera program via programproxy

Publicera program förutsätter att du har uppfyllt alla krav och att du har flera kopplingar visas som registrerad och aktiv på sidan Application Proxy.

Du kan även publicera program med hjälp av [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Nedan visas några metodtips att följa när du publicerar ett program:

* **Använda Anslutningsgrupper**: Tilldela en anslutningsgrupp som har angetts för publicering av varje respektive program.

* **Ange tidsgränsen för Backend-programmet**: Den här inställningen är användbar i scenarier där programmet kan kräva mer än 75 sekunder att bearbeta en transaktion för klienten. Till exempel fungerar när en klient skickar en fråga till ett webbprogram som som klientdel till en databas. Klienten skickar den här frågan till sin serverdatabas server och väntar på svar, men med tiden det tar emot ett svar, klientsidan av konversationen tidsgränsen. Anger timeout-värdet till länge ger 180 sekunder för längre transaktioner har slutförts.

* **Använd lämplig Cookie-typer**

   * **Endast HTTP-Cookie**: Ger ökad säkerhet genom att använda Application Proxy inkludera flaggan HTTPOnly i set-cookie HTTP-svarshuvuden. Den här inställningen hjälper till att minimera hot, till exempel cross site scripting (XSS). Låta det vara inställt på Nej för klienter/användaragenter som kräver åtkomst till sessions-cookie. Till exempel publiceras RDP/MTSC-klient som ansluter till en fjärrskrivbordsgateway via App Proxy.

   * **Säker Cookie**: När en cookie är inställd med attributet säker, omfattar användaragent (klientside-app) endast cookien i HTTP-förfrågningar om begäran skickas över en säker TLS-kanal. På så sätt kan du minska risken för en cookie äventyras i klartext kanaler, så ska aktiveras.

   * **Beständiga Cookie**: Tillåter sessions-cookie Application Proxy att bevara mellan webbläsaren har stängt av återstående giltig tills den upphör att gälla eller tas bort. Används för scenarier där ett omfattande program, till exempel office har åtkomst till ett dokument i en publicerad webbapplikation, utan att användaren meddelas igen för autentisering. Aktivera med försiktighet dock som beständiga cookies slutligen lämna en tjänst på risken för obehörig åtkomst, om inte används tillsammans med andra kompenserande kontroller. Den här inställningen bör endast användas för äldre program som inte kan dela cookies mellan processer. Är det bättre att uppdatera programmet ska hantera delning cookies mellan processer istället för att använda den här inställningen.

* **Översätt URL: er i rubriker**: Du kan aktivera det för scenarier där internt DNS inte kan konfigureras för att matcha företagets offentliga namnområde (även kallade dela DNS). Om inte programmet kräver ursprungliga värdhuvudet i klientbegäran kan du lämna det här värdet inställt på Ja. Alternativt är att kopplingen använder det fullständiga Domännamnet i den interna URL: en för routning av faktiska trafiken och FQDN i den externa URL: en som du värdhuvudet. I de flesta fall detta alternativ ger programmet att fungera som vanligt när nås via fjärranslutning, men användarna förlora fördelarna med att ha en matchande i och utanför URL: en.

* **Översätt URL: er i brödtexten för programmet**: Aktivera brödtext för program länköversättning för en app när du vill att länkar från appen översättas i svar tillbaka till klienten. Om aktiverad kan ger den här funktionen ett bästa försök vid översättning av alla interna länkar som Approxy söker efter i HTML och CSS-svar returneras till klienter. Det är användbart när du publicerar appar som innehåller antingen hårdkodat absoluta eller NetBIOS-kortnamn länkar i innehållet eller appar med innehåll som länkar till andra lokala program.

Aktivera länk translation eller varje program för scenarier där en publicerad app länkar till andra publicerade appar, så att du har kontroll över användarupplevelsen på per app-nivå.

Anta att du har tre program som publicerats via programproxy att alla länkar till varandra: Fördelar, kostnader, och resor samt en fjärde app, Feedback, som inte har publicerats via programproxy.

![Bild 1](media/App-proxy-deployment-plan/link-translation.png)

När du aktiverar länköversättning för appen fördelar omdirigeras länkarna till utgifter och resor till de externa URL: er för dessa appar så att användare med åtkomst till program från utanför företagsnätverket kan komma åt dem. Länkar från utgifter och resor tillbaka till förmånerna fungerar inte eftersom länken translation inte har aktiverats för dessa två appar. Länk till Feedback är inte omdirigeras eftersom det finns inga externa URL: en, så att användare som använder appen fördelar inte kommer kunna komma åt appen feedback från utanför företagsnätverket. Se detaljerad information om [länka översättning och andra alternativ för omdirigering](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Åtkomst till ditt program

Det finns flera alternativ för att hantera åtkomst till App Proxy publicerade resurser, så du väljer den lämpligaste för behoven för angivna scenariot och skalbarhet. Vanliga metoder för omfattar: med den lokala grupper som synkroniseras via Azure AD Connect kan skapa dynamiska grupper i Azure AD baserat på användarattribut, med hjälp av självbetjäningsgrupper som hanteras av en resursägare eller en kombination av alla dessa. Se de länkade resurserna för fördelarna med var och en.

Det mest okomplicerat sättet för att tilldela användare åtkomst till ett program kommer in till den **användare och grupper** alternativ från det vänstra fönstret i ditt publicerade program och direkt tilldela grupper eller enskilda personer.

![Bild 24](media/App-proxy-deployment-plan/add-user.png)

Du kan också tillåta användare att själva åtkomsten till ditt program genom att tilldela en grupp som de inte är för närvarande en medlem och konfigurering av alternativ för självbetjäning.

![Bild 25](media/App-proxy-deployment-plan/allow-access.png)

Om aktiverad kan användare sedan kommer att kunna logga in i MyApps-portalen och begär åtkomst och antingen vara automatisk godkänts och läggs till i den redan tillåtna själva gruppen eller behöver godkännas från en godkänd kontrollant.

Gästanvändare kan också vara [bjuds in att komma åt interna program som publicerats via programproxy via Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

För lokala program som är normalt tillgängliga anonymt, kräver ingen autentisering, kanske du föredrar att inaktivera alternativet finns i programmets **egenskaper**.

![Bild 26](media/App-proxy-deployment-plan/assignment-required.png)


Om du lämnar det här alternativet inställt på Nej kan användarna att få åtkomst till dina lokala program via Azure AD App Proxy utan behörighet, så Använd med försiktighet.

När programmet har publicerats ska den vara åtkomlig genom att skriva dess externa URL: en i en webbläsare eller på ikonen [ https://myapps.microsoft.com ](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Aktivera förautentisering

Kontrollera att ditt program är tillgänglig via programproxyn. 

1. Gå till **Azure Active Directory** > **företagsprogram** > **alla program** och välj den app som du vill hantera.

2. Välj **programproxy**.

3. I den **förautentisering** fältet, Använd den nedrullningsbara listan för att välja **Azure Active Directory**, och välj **spara**.

Azure AD kommer utmaning eftersom du för autentisering med förautentisering aktiverat, och sedan backend-programmet bör också utmana dig om autentisering krävs. Ändrar förautentisering från genomströmning till Azure AD konfigurerar även en extern URL med HTTPS, så att alla program som ursprungligen konfigurerats för HTTP ska nu skyddas med HTTPS.

### <a name="enable-single-sign-on"></a>Aktivera enkel inloggning

Enkel inloggning ger den bästa möjliga användarupplevelsen och säkerheten eftersom användarna behöver bara logga in en gång vid åtkomst till Azure AD. När en användare redan har autentiserat utförs enkel inloggning med programproxy-koppling som autentiserar till dina lokala program, användarens räkning. Backend-applikationer bearbetar inloggningen som om det vore användaren själva. 

Välja den **genomströmning** alternativet låter användare komma åt det publicerade programmet utan att autentisera till Azure AD.

Utför enkel inloggning är endast möjligt om Azure AD kan identifiera den användare som begär åtkomst till en resurs, så att ditt program måste konfigureras för att autentisera förväg användare vid åtkomst för enkel inloggning till funktionen, annars alternativ för enkel inloggning kommer att inaktiveras.

Läs [enkel inloggning till program i Azure AD](what-is-single-sign-on.md) för att välja den lämpligaste SSO-metoden när du konfigurerar dina program.

###  <a name="working-with-other-types-of-applications"></a>Arbeta med andra typer av program

Azure AD Application Proxy kan också stöd för program som har utvecklats för att använda vår Azure AD-Autentiseringsbiblioteket ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) eller Microsoft Authentication Library ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Det stöder ursprungliga klientappar som använder Azure AD som utfärdade token som tas emot i Filhuvudinformationen i klientens begäran att utföra förautentisering åt användarna.

Läs [publicering av interna och mobila klientappar](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) och [anspråksbaserade applikationer](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) vill veta mer om tillgängliga konfigurationer för Application Proxy.

### <a name="use-conditional-access-to-strengthen-security"></a>Använd villkorlig åtkomst för att stärka säkerheten

Programsäkerhet kräver en avancerad uppsättning funktioner för säkerhet som kan skydda mot och svara till komplexa hot både lokalt och i molnet. Angripare får oftast åtkomst till företagets nätverk via svaga, standard eller stulna användarautentiseringsuppgifter.  Microsoft identity-styrd säkerhet minskar användningen av stulna autentiseringsuppgifter genom att hantera och skydda Privilegierade och icke-privilegierade identiteter.

Följande funktioner kan användas för Azure AD Application Proxy:

* Användar- och platsbaserad villkorlig åtkomst: Skydda känsliga data skyddas genom att begränsa användaråtkomst baserat på geografiska plats eller en IP-adress med [platsbaserad villkorlig åtkomstprinciper](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Enhetsbaserad villkorlig åtkomst: Se till att endast registrerade, godkända och kompatibla enheter kan komma åt företagets data med [enhetsbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Programbaserad villkorsstyrd åtkomst: Arbetet har inte att stoppa när en användare som inte är i företagsnätverket. [Säker åtkomst till företagets appar för molnet och lokala](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) och Behåll kontrollen med villkorlig åtkomst.

* Riskbaserad villkorlig åtkomst: Skydda dina data från hackare med en [riskbaserad villkorlig åtkomstprincip](https://www.microsoft.com/cloud-platform/conditional-access) som kan tillämpas på alla appar och alla användare, oavsett om den lokala eller i molnet.

* Panelen för Azure AD-program: Med Application Proxy-tjänsten distribueras och program som publicerats på ett säkert sätt, ger användarna en enkel hubb för att upptäcka och komma åt sina program. Öka produktiviteten med självbetjäning funktioner, till exempel möjligheten att begära åtkomst till nya appar och grupper eller hantera åtkomst till dessa resurser för andra, via den [åtkomstpanelen](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Hantera din implementering

### <a name="required-roles"></a>Nödvändiga roller

Microsoft representanter principen om beviljar den lägsta möjliga behörigheten för att utföra nödvändiga åtgärder med Azure AD. [Granska de olika Azure-roller som är tillgängliga](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) och välja den rätta till behoven för varje person. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen är klar.

| Rollen för företag| Uppgifter| Azure AD-roller |
|---|---|---|
| Help desk-administratör | Vanligtvis begränsad till berättigade slutanvändaren rapporterade problem och utföra begränsade uppgifter, till exempel ändra användarnas lösenord, ogiltigförklara uppdateringstoken och övervaka tjänstens hälsotillstånd. | Supportavdelningsadministratör |
| Identity-administratör| Läs Azure AD-inloggningen rapporter och granskningsloggarna Felsök App Proxy-relaterade problem.| Säkerhetsläsare |
| Programmets ägare| Skapa och hantera alla aspekter av företagsprogram och programregistreringar proxyinställningarna för programmet.| Program-administratör |
| Infrastruktur-administratör | Ägare för förnyelse av certifikat | Program-administratör |

Minimera antalet personer som har åtkomst till säker information eller resurser gör det lättare att minska risken för en skadliga aktörer få obehörig åtkomst eller en auktoriserad användare oavsiktligt påverkar en känsliga resurs. 
 
Användare behöver dock fortfarande att utföra dagliga Privilegierade åtgärder, så att framtvinga just-in-time (JIT) baserade [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) principer för att tillhandahålla på begäran privilegierad åtkomst till Azure-resurser och Azure AD är vår rekommendationer för att effektivt hantera administrativ åtkomst och granskning.

### <a name="reporting-and-monitoring"></a>Rapportering och övervakning

Azure AD kan ge ytterligare insikter om din organisations användaretablering användning och hälsotillstånd via granskningsloggar och rapporter. 

#### <a name="application-audit-logs"></a>Granskningsloggar för program

Dessa loggar redogör för inloggningar till program som har konfigurerats med Application Proxy, samt information om enheten och användaren åtkomst till programmet. De är placerade i Azure-portalen och granska API.

#### <a name="windows-event-logs-and-performance-counters"></a>Windows-händelseloggar och prestandaräknare

Kopplingar har både admin och sessionen loggar. Administratörsloggar innehåller viktiga händelser och deras fel. Sessionsloggar innehåller alla transaktioner och bearbetningsinformation om. Loggar och prestandaräknare finns i Windows-händelseloggar och följ den här [självstudie för att konfigurera händelseloggen datakällor i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Felsökningsguide för och steg

Läs mer om vanliga problem och hur du löser dem med vår guide om hur du [felsökning](application-proxy-troubleshoot.md) felmeddelanden. 

De här artiklarna täcker vanliga scenarier, men du kan också skapa egna felsökningsguider för supportavdelningen. 

* [Problem med att visa appsida](application-proxy-page-appearance-broken-problem.md)
* [För lång programinläsning](application-proxy-page-load-speed-problem.md)
* [Länkar på programsidan fungerar inte](application-proxy-page-links-broken-problem.md)
* [Vilka portar ska jag öppna för min app?](application-proxy-connectivity-ports-how-to.md)
* [Ingen fungerande anslutningsapp i en grupp med anslutningsappar för min app](application-proxy-connectivity-no-working-connector.md)
* [Konfigurera i administratörsportalen](application-proxy-config-how-to.md)
* [Konfigurera enkel inloggning till min app](application-proxy-config-sso-how-to.md)
* [Problem med att skapa en app i administratörsportalen](application-proxy-config-problem.md)
* [Konfigurera begränsad Kerberos-delegering](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurera med PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Det går inte att komma åt den här företagets programfel](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problem med att installera anslutningsappen för programproxyagenten](application-proxy-connector-installation-problem.md)
* [Inloggningsproblem](application-sign-in-problem-on-premises-application-proxy.md)
