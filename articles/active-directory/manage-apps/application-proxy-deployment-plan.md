---
title: Planera en Azure Active Directory-programproxydistribution
description: En heltäckande guide för planering av distributionen av programproxy inom organisationen
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330909"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Planera en Azure AD-programproxydistribution

Azure Active Directory (Azure AD) Application Proxy är en säker och kostnadseffektiv fjärråtkomstlösning för lokala program. Det ger en omedelbar övergångsväg för "Cloud First"-organisationer för att hantera åtkomst till äldre lokala program som ännu inte kan använda moderna protokoll. Mer inledande information finns i [Vad är Programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Programproxy rekommenderas för att ge fjärranvändare åtkomst till interna resurser. Application Proxy ersätter behovet av en VPN eller omvänd proxy för dessa fjärråtkomst användningsfall. Den är inte avsedd för användare som finns i företagets nätverk. Dessa användare som använder Application Proxy för intranätåtkomst kan uppleva oönskade prestandaproblem.

Den här artikeln innehåller de resurser du behöver för att planera, driva och hantera Azure AD Application Proxy. 

## <a name="plan-your-implementation"></a>Planera implementeringen

I följande avsnitt visas en bred översikt över de viktigaste planeringselementen som kommer att konfigurera dig för en effektiv distributionsupplevelse. 

### <a name="prerequisites"></a>Krav

Du måste uppfylla följande förutsättningar innan du påbörjar implementeringen. Du kan se mer information om hur du konfigurerar din miljö, inklusive dessa förutsättningar, i den här [självstudien](application-proxy-add-on-premises-application.md).

* **Kopplingar:** Kontakter är lätta agenter som du kan distribuera till:
   * Fysisk maskinvara lokalt
   * En virtuell dator som finns i en hypervisorlösning
   * En virtuell dator som finns i Azure för att aktivera utgående anslutning till application proxy-tjänsten.

* Se [Förstå Azure AD App Proxy Connectors](application-proxy-connectors.md) för en mer detaljerad översikt.

     * [Anslutningsmaskiner måste vara aktiverade för TLS 1.2](application-proxy-add-on-premises-application.md) innan kontakterna installeras.

     * Distribuera om möjligt anslutningsappar i [samma nätverk](application-proxy-network-topology.md) och segment som backend-webbprogramservrarna. Det är bäst att distribuera kopplingar när du har slutfört en identifiering av program.
     * Vi rekommenderar att varje kopplingsgrupp har minst två kopplingar för att ge hög tillgänglighet och skala. Att ha tre kontakter är optimalt om du kan behöva serva en maskin när som helst. Granska [anslutningskapacitetstabellen](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) för att hjälpa till med att bestämma vilken typ av dator som ska installeras på. Ju större maskinen desto mer buffert och performant anslutningen kommer att vara.

* **Inställningar för nätverksåtkomst:** Azure AD Application Proxy-kopplingar [ansluter till Azure via HTTPS (TCP Port 443) och HTTP (TCP Port 80)](application-proxy-add-on-premises-application.md). 

   * Att avsluta anslutnings-TLS-trafik stöds inte och förhindrar att kopplingar upprättar en säker kanal med sina respektive Azure App Proxy-slutpunkter.

   * Undvik alla former av infogad inspektion på utgående TLS-kommunikation mellan anslutningar och Azure. Intern inspektion mellan en anslutnings- och backend-program är möjlig, men kan försämra användarupplevelsen och som sådan rekommenderas inte.

   * Belastningsutjämning av själva kopplingarna stöds inte heller, eller ens nödvändigt.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Viktiga överväganden innan du konfigurerar Azure AD Application Proxy

Följande grundläggande krav måste uppfyllas för att konfigurera och implementera Azure AD Application Proxy.

*  **Azure-introduktion:** Innan du distribuerar programproxy måste användaridentiteter synkroniseras från en lokal katalog eller skapas direkt i dina Azure AD-klienter. Identitetssynkronisering gör att Azure AD kan förberätta användare innan de får åtkomst till appproxy-publicerade program och har nödvändig användaridentifierare för att utföra enkel inloggning (SSO).

* **Villkorsstyrd åtkomstkrav:** Vi rekommenderar inte att du använder Programproxy för intranätåtkomst eftersom detta lägger till svarstid som påverkar användarna. Vi rekommenderar att du använder principer för programproxy med förautentisering och villkorlig åtkomst för fjärråtkomst från Internet.  En metod för att ge villkorlig åtkomst för intranätanvändning är att modernisera program så att de direkt kan autentisera med AAD. Mer information [finns i Resurser för att migrera program till AAD.](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) 

* **Servicegränser**: För att skydda mot överkonsumtion av resurser av enskilda klienter finns begränsningsgränser som anges per program och klient. Om du vill se dessa begränsningar finns [azure AD-tjänstbegränsningar och begränsningar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Dessa begränsningsgränser baseras på ett riktmärke långt över den typiska användningsvolymen och ger gott om buffert för en majoritet av distributionerna.

* **Offentligt certifikat**: Om du använder anpassade domännamn måste du skaffa ett TLS/SSL-certifikat. Beroende på dina organisationskrav kan det ta lite tid att få ett certifikat och vi rekommenderar att du påbörjar processen så tidigt som möjligt. Azure Application Proxy stöder standard-, [jokertecken-](application-proxy-wildcard.md)eller SAN-baserade certifikat. Mer information finns i [Konfigurera anpassade domäner med Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

* **Domänkrav**: Enkel inloggning till publicerade program med Kerberos Constrained Delegation (KCD) kräver att servern som kör Anslutningsappen och servern som kör appen är domänansluten och en del av samma domän eller betrodda domäner.
Detaljerad information om ämnet finns i [KCD för enkel inloggning med Programproxy.](application-proxy-configure-single-sign-on-with-kcd.md) Anslutningstjänsten körs i det lokala systemet och bör inte konfigureras för att använda en anpassad identitet.

* **DNS-poster för webbadresser**

   * Innan du använder anpassade domäner i Programproxy måste du skapa en CNAME-post i offentlig DNS, så att klienter kan matcha den anpassade definierade externa URL:en till den fördefinierade Application Proxy-adressen. Om du inte kan skapa en CNAME-post för ett program som använder en anpassad domän hindras fjärranvändare från att ansluta till programmet. Steg som krävs för att lägga till CNAME-poster kan variera från DNS-provider till provider, så lär dig hur du [hanterar DNS-poster och postuppsättningar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * På samma sätt måste anslutningsvärdar kunna lösa den interna URL:en för program som publiceras.

* **Administrativa rättigheter och roller**

   * **Anslutningsinstallation** kräver lokala administratörsrättigheter till Windows-servern som den installeras på. Det kräver också ett minimum av en *programadministratörsroll* för att autentisera och registrera anslutningsinstansen till din Azure AD-klientorganisation. 

   * **Programpublicering och administration** kräver rollen *Programadministratör.* Programadministratörer kan hantera alla program i katalogen, inklusive registreringar, SSO-inställningar, användar- och grupptilldelningar och licensiering, inställningar för programproxy och medgivande. Det ger inte möjlighet att hantera villkorlig åtkomst. Rollen *Cloud Application Administrator* har alla funktioner för programadministratören, förutom att den inte tillåter hantering av programproxyinställningar.

* **Licensiering:** Programproxy är tillgängligt via en Azure AD Premium-prenumeration. Se [sidan Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/) för en fullständig lista över licensieringsalternativ och funktioner.  

### <a name="application-discovery"></a>Identifiering av program

Kompilera en inventering av alla program i omfattning som publiceras via Application Proxy genom att samla in följande information:

| Informationstyp| Information att samla in |
|---|---|
| Typ av tjänst| Till exempel: SharePoint, SAP, CRM, Anpassat webbprogram, API |
| Applikationsplattform | Till exempel: Windows IIS, Apache på Linux, Tomcat, NGINX |
| Domänmedlemskap| Webbserverns fullständigt kvalificerade domännamn (FQDN) |
| Programplats | Var webbservern eller servergruppen finns i infrastrukturen |
| Intern åtkomst | Den exakta URL som används vid åtkomst till programmet internt. <br> Om en servergrupp, vilken typ av belastningsutjämning används? <br> Om programmet hämtar innehåll från andra källor än sig själv.<br> Ta reda på om programmet fungerar via WebSockets. |
| Extern åtkomst | Leverantörslösningen som programmet redan är exponerad för externt. <br> Url:en som du vill använda för extern åtkomst. Om SharePoint kontrollerar du att alternativa åtkomstmappningar är konfigurerade enligt [den här vägledningen](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Om inte, måste du definiera externa webbadresser. |
| Offentligt certifikat | Om du använder en anpassad domän skaffar du ett certifikat med motsvarande ämnesnamn. Om det finns ett certifikat, notera serienumret och platsen där det kan erhållas. |
| Autentiseringstyp| Den typ av autentisering som stöds av programmet stöder till exempel Basic, Windows Integration Authentication, formulärbaserad, rubrikbaserad och anspråk. <br>Om programmet är konfigurerat för att köras under ett visst domänkonto, notera det fullständigt kvalificerade domännamnet (FQDN) för tjänstkontot.<br> Om SAML-baserade, identifieraren och svara webbadresser. <br> Om huvudbaserad, leverantörslösningen och specifika krav för hantering av autentiseringstyp. |
| Namn på kopplingsgrupp | Det logiska namnet för den grupp kopplingar som ska anges för att tillhandahålla kanalen och SSO till det här backend-programmet. |
| Tillgång till användare/grupper | De användare eller användargrupper som ska beviljas extern åtkomst till programmet. |
| Ytterligare krav | Observera eventuella ytterligare fjärråtkomst- eller säkerhetskrav som bör vägas in i publiceringen av programmet. |

Du kan hämta det här [kalkylbladet för programinventering](https://aka.ms/appdiscovery) för att inventera dina appar.

### <a name="define-organizational-requirements"></a>Definiera organisationskrav

Följande är områden där du bör definiera organisationens affärsbehov. Varje område innehåller exempel på krav

 **Åtkomst**

* Fjärranvändare med domänansluten eller Azure AD-anslutna enheter som användare kan komma åt publicerade program på ett säkert sätt med sömlös enkel inloggning (SSO).

* Fjärranvändare med godkända personliga enheter kan komma åt publicerade program på ett säkert sätt förutsatt att de är registrerade i MFA och har registrerat Microsoft Authenticator-appen på sin mobiltelefon som en autentiseringsmetod.

**Styrelseformer** 

* Administratörer kan definiera och övervaka livscykeln för användartilldelningar till program som publiceras via Programproxy.

**Säkerhet**

* Endast användare som tilldelats program via gruppmedlemskap eller individuellt kan komma åt dessa program.

**Prestanda**

* Det finns ingen försämring av programprestanda jämfört med åtkomst till program från det interna nätverket.

**Användarupplevelse**

* Användarna är medvetna om hur de kommer åt sina program med hjälp av välbekanta företagsadresser på alla enhetsplattformar.

**Granskning**
* Administratörer kan granska användaråtkomstaktivitet.


### <a name="best-practices-for-a-pilot"></a>Bästa praxis för en pilot

Bestäm hur mycket tid och ansträngning som krävs för att helt beställa ett enda program för fjärråtkomst med Enkel inloggning (SSO). Gör det genom att köra en pilot som tar hänsyn till den första identifieringen, publiceringen och den allmänna testningen. Om du använder ett enkelt IIS-baserat webbprogram som redan är förkonfigurerat för integrerad Windows-autentisering (IWA) skulle det bidra till att upprätta en baslinje, eftersom den här inställningen kräver minimal ansträngning för att framgångsrikt kunna styra fjärråtkomst och SSO.

Följande designelement bör öka framgången för pilotimplementeringen direkt i en produktionsklient.  

**Hantering av anslutningsdon:**  

* Kopplingar spelar en viktig roll för att tillhandahålla den lokala kanalen till dina program. Att **Default** använda standardkopplingsgruppen är tillräckligt för inledande pilottestning av publicerade program innan de tas i drift i produktion. Testade program kan sedan flyttas till produktionskopplingsgrupper.

**Hantering av ansökningar:**

* Det är mest troligt att din personal kommer ihåg att en extern webbadress är välbekant och relevant. Undvik att publicera ditt program med hjälp av våra fördefinierade msappproxy.net eller onmicrosoft.com suffix. Ange i stället en välbekant verifierad domän på den högsta nivån, som föregås av ett logiskt värdnamn, till exempel *intranät.<customers_domain>.com*.

* Begränsa synligheten för pilotprogrammets ikon till en pilotgrupp genom att dölja dess startikon från Azure MyApps-portalen. När du är klar för produktion kan du begränsa appen till dess respektive målgrupp, antingen i samma förproduktionsklient eller genom att också publicera programmet i din produktionsklient.

**Enkel inloggningsinställningar:** Vissa SSO-inställningar har specifika beroenden som kan ta tid att ställa in, så undvik att ändra kontrollfördröjningar genom att se till att beroenden åtgärdas i förväg. Detta inkluderar domänkopplingsanslutningsappvärdar för att utföra SSO med Kerberos Constrained Delegation (KCD) och ta hand om andra tidskrävande aktiviteter. Till exempel ställa in en PING Access-instans, om det behövs rubrikbaserad SSO.

**TLS Mellan anslutningsvärd och målprogram:** Säkerhet är av största vikt, så TLS mellan anslutningsvärden och målprogrammen bör alltid användas. Särskilt om webbprogrammet är konfigurerat för formulärbaserad autentisering (FBA), som användarautentiseringsuppgifter sedan effektivt överförs i klartext.

**Implementera stegvis och testa varje steg**. Utför grundläggande funktionstester efter att ha publicerat ett program för att säkerställa att alla användar- och affärskrav uppfylls genom att följa anvisningarna nedan:

1. Testa och verifiera allmän åtkomst till webbprogrammet med förautentisering inaktiverad.
2. Om det lyckas aktivera förautentisering och tilldela användare och grupper. Testa och validera åtkomst.
3. Lägg sedan till SSO-metoden för ditt program och testa igen för att validera åtkomsten.
4. Använd principer för villkorlig åtkomst och MFA efter behov. Testa och validera åtkomst.

**Felsökningsverktyg:** Vid felsökning, börja alltid med att validera åtkomsten till det publicerade programmet från webbläsaren på anslutningsvärden och bekräfta att programmet fungerar som förväntat. Ju enklare installation, desto lättare att fastställa orsaken, så överväg att försöka återskapa problem med en minimal konfiguration som att bara använda en enda koppling och ingen SSO. I vissa fall kan webbfelsökningsverktyg som Telerik's Fiddler visa sig vara oumbärliga för att felsöka åtkomst- eller innehållsproblem i program som nås via en proxy. Fiddler kan också fungera som en proxy för att spåra och felsöka trafik för mobila plattformar som iOS och Android, och praktiskt taget allt som kan konfigureras för att dirigera via en proxy. Mer information finns i [felsökningsguiden.](application-proxy-troubleshoot.md)

## <a name="implement-your-solution"></a>Implementera din lösning

### <a name="deploy-application-proxy"></a>Distribuera programproxy

Stegen för att distribuera programproxyn beskrivs i den här [självstudien för att lägga till ett lokalt program för fjärråtkomst](application-proxy-add-on-premises-application.md). Om installationen inte lyckas väljer du **Felsöka programproxy** i portalen eller använder felsökningsguiden [för Problem med att installera Application Proxy Agent Connector](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publicera program via Application Proxy

Publiceringsprogram förutsätter att du har uppfyllt alla förutsättningar och att du har flera kopplingar som visas som registrerade och aktiva på sidan Programproxy.

Du kan också publicera program med [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Nedan följer några metodtips att följa när du publicerar ett program:

* **Använd kopplingsgrupper:** Tilldela en kopplingsgrupp som har utsetts för publicering av respektive program. Vi rekommenderar att varje kopplingsgrupp har minst två kopplingar för att ge hög tillgänglighet och skala. Att ha tre kontakter är optimalt om du kan behöva serva en maskin när som helst. Dessutom läser du [Publicera program på separata nätverk och platser med hjälp av anslutningsgrupper](application-proxy-connector-groups.md) för att se hur du också kan använda anslutningsgrupper för att segmentera dina kontakter efter nätverk eller plats.

* **Ange timeout för bakåttillgångsprogram:** Den här inställningen är användbar i scenarier där programmet kan kräva mer än 75 sekunder för att bearbeta en klienttransaktion. Till exempel när en klient skickar en fråga till ett webbprogram som fungerar som en klientslutsida till en databas. Klientsidan av konversationen time out skickas den här frågan till dess server för server för server för server för server för server för server för server för server för serverdelen och väntar på ett svar, men när den tar emot ett svar tar du ut klientsidan av konversationen. Om du ställer in tidsgränsen till Long får du 180 sekunder för längre transaktioner att slutföras.

* **Använd lämpliga cookietyper**

   * **HTTP-Only Cookie**: Ger ytterligare säkerhet genom att ha Application Proxy inkludera HTTPOnly-flaggan i HTTP-svarshuvuden för set-cookie. Den här inställningen hjälper till att minska kryphål som XSS (Cross-Site Scripting). Lämna den här uppsättningen till Nej för klienter/användaragenter som kräver åtkomst till sessionscookien. Rdp/MTSC-klient som ansluter till en fjärrskrivbordsgateway som publiceras via App Proxy.

   * **Säker cookie:** När en cookie ställs in med attributet Secure kommer användaragenten (klientappen) endast att inkludera cookien i HTTP-begäranden om begäran överförs via en TLS-säker kanal. Detta bidrar till att minska risken för att en cookie äventyras via klartextkanaler, så bör aktiveras.

   * **Beständig cookie:** Tillåter att application proxy-sessionscookien kvarstår mellan webbläsarstängningar genom att vara giltig tills den antingen upphör att gälla eller tas bort. Används för scenarier där ett omfattande program som office kommer åt ett dokument i ett publicerat webbprogram, utan att användaren uppmanas att göra det igen för autentisering. Aktivera dock med försiktighet, eftersom beständiga cookies i slutändan kan lämna en tjänst med risk för obehörig åtkomst, om den inte används tillsammans med andra kompenserande kontroller. Den här inställningen bör endast användas för äldre program som inte kan dela cookies mellan processer. Det är bättre att uppdatera ditt program för att hantera delningscookies mellan processer istället för att använda den här inställningen.

* **Översätt URL:er i rubriker:** Du aktiverar detta för scenarier där intern DNS inte kan konfigureras för att matcha organisationens offentliga namnområde(även om du delar DNS). Om inte programmet kräver det ursprungliga värdhuvudet i klientbegäran lämnar du det här värdet inställt på Ja. Alternativet är att låta kopplingen använda FQDN i den interna URL:en för routning av den faktiska trafiken och FQDN i den externa URL:en, som värdhuvud. I de flesta fall bör det här alternativet göra det möjligt för programmet att fungera som vanligt, när det används på distans, men användarna förlorar fördelarna med att ha en matchning inuti & extern URL.

* **Översätt webbadresser i Application Body:** Aktivera översättning av Application Body-länk för en app när du vill att länkarna från den appen ska översättas i svar tillbaka till klienten. Om den här funktionen är aktiverad kan du göra ett försök att översätta alla interna länkar som App Proxy hittar i HTML- och CSS-svar som returneras till klienter. Det är användbart när du publicerar appar som innehåller antingen hårdkodade absoluta eller NetBIOS-kortnamnslänkar i innehållet, eller appar med innehåll som länkar till andra lokala program.

Scenarier där en publicerad app länkar till andra publicerade appar aktiverar du länköversättning för varje program så att du har kontroll över användarupplevelsen på appnivå.

Anta till exempel att du har tre program publicerade via Programproxy som alla länkar till varandra: Förmåner, utgifter och Resor, plus en fjärde app, Feedback, som inte publiceras via Application Proxy.

![Bild 1](media/App-proxy-deployment-plan/link-translation.png)

När du aktiverar länköversättning för appen Förmåner omdirigeras länkarna till utgifter och resor till externa webbadresser för dessa appar, så att användare som kommer åt programmen utanför företagsnätverket kan komma åt dem. Länkar från utgifter och resor tillbaka till förmåner fungerar inte eftersom länköversättning inte har aktiverats för dessa två appar. Länken till feedback omdirigeras inte eftersom det inte finns någon extern WEBBADRESS, så användare som använder appen Förmåner kan inte komma åt feedbackappen utanför företagsnätverket. Se detaljerad information om [länköversättning och andra omdirigeringsalternativ](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Få tillgång till ditt program

Det finns flera alternativ för att hantera åtkomst till publicerade appproxy-resurser, så välj den lämpligaste för ditt givna scenario och skalbarhetsbehov. Vanliga metoder är: använda lokala grupper som synkroniseras via Azure AD Connect, skapa dynamiska grupper i Azure AD baserat på användarattribut, med hjälp av självbetjäningsgrupper som hanteras av en resursägare eller en kombination av alla dessa. Se de länkade resurserna för fördelarna med var och en.

Det mest raka sättet att tilldela användare åtkomst till ett program är att gå in i alternativen **Användare och grupper** från den vänstra rutan i det publicerade programmet och direkt tilldela grupper eller individer.

![Bild 24](media/App-proxy-deployment-plan/add-user.png)

Du kan också tillåta användare att självbetjäningsåtkomst till ditt program genom att tilldela en grupp som de för närvarande inte är medlem i och konfigurera självbetjäningsalternativen.

![Bild 25](media/App-proxy-deployment-plan/allow-access.png)

Om det är aktiverat kan användarna logga in på MyApps-portalen och begära åtkomst, och antingen automatiskt godkännas och läggas till i den redan tillåtna självbetjäningsgruppen eller behöver godkännande från en utsedd godkännare.

Gästanvändare kan också [uppmanas att komma åt interna program som publiceras via Application Proxy via Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

För lokala program som normalt är tillgängliga anonymt och som inte kräver någon autentisering kanske du föredrar att inaktivera alternativet som finns i programmets **egenskaper**.

![Bild 26](media/App-proxy-deployment-plan/assignment-required.png)


Om du lämnar det här alternativet inställt på Nej kan användare komma åt det lokala programmet via Azure AD App Proxy utan behörigheter, så var försiktig.

När ditt program har publicerats bör den vara tillgänglig genom att skriva [https://myapps.microsoft.com](https://myapps.microsoft.com/)den externa webbadressen i en webbläsare eller genom dess ikon på .

### <a name="enable-pre-authentication"></a>Aktivera förautentisering

Kontrollera att ditt program är tillgängligt via Application Proxy som använder det via den externa url:en. 

1. Navigera till **Azure Active Directory** > **Enterprise-program** > **Alla program** och välj den app du vill hantera.

2. Välj **Programproxy**.

3. I fältet **Före autentisering** använder du listrutan för att välja **Azure Active Directory**och väljer **Spara**.

Med pre-autentisering aktiverat, Azure AD kommer att utmana användarna först för autentisering och om enkel inloggning är konfigurerad då backend-programmet kommer också att verifiera användaren innan åtkomst till programmet beviljas. Om du ändrar förautentiseringsläget från Passthrough till Azure AD konfigureras också den externa URL:en med HTTPS, så alla program som ursprungligen konfigurerats för HTTP kommer nu att skyddas med HTTPS.

### <a name="enable-single-sign-on"></a>Aktivera enkel inloggning

SSO ger bästa möjliga användarupplevelse och säkerhet eftersom användarna bara behöver logga in en gång när de öppnar Azure AD. När en användare har pre-autentiserats utförs SSO av Application Proxy-anslutningen som autentiserar till det lokala programmet för användarens räkning. Backend-programmet bearbetar inloggningen som om det vore användaren själva. 

Om du väljer alternativet **Passthrough** kan användare komma åt det publicerade programmet utan att någonsin behöva autentisera till Azure AD.

Att utföra SSO är endast möjligt om Azure AD kan identifiera användaren som begär åtkomst till en resurs, så ditt program måste konfigureras för att förbekalka användare med Azure AD vid åtkomst för SSO för att fungera, annars inaktiveras SSO-alternativen.

Läs Enkel inloggning till program i Azure AD för att hjälpa dig att välja den lämpligaste [SSO-metoden](what-is-single-sign-on.md) när du konfigurerar dina program.

###  <a name="working-with-other-types-of-applications"></a>Arbeta med andra typer av applikationer

Azure AD Application Proxy kan också stödja program som har utvecklats för att använda vårt Azure AD-autentiseringsbibliotek[(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)eller Microsoft Authentication Library[(MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Den stöder inbyggda klientappar genom att använda Azure AD-utfärdade token som tas emot i huvudinformationen för klientbegäran för att utföra förautentisering för användarnas räkning.

Läs [publicera inbyggda och mobila klientappar](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) och [anspråksbaserade program](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) om du vill veta mer om tillgängliga konfigurationer av Programproxy.

### <a name="use-conditional-access-to-strengthen-security"></a>Använd villkorlig åtkomst för att stärka säkerheten

Programsäkerhet kräver en avancerad uppsättning säkerhetsfunktioner som kan skydda mot och svara på komplexa hot lokalt och i molnet. Angripare får oftast åtkomst till företagsnätverk genom svaga, standard- eller stulna användaruppgifter.  Microsofts identitetsdrivna säkerhet minskar användningen av stulna autentiseringsuppgifter genom att hantera och skydda både privilegierade och icke-privilegierade identiteter.

Följande funktioner kan användas för att stödja Azure AD Application Proxy:

* Användar- och platsbaserad villkorlig åtkomst: Skydda känsliga data genom att begränsa användaråtkomsten baserat på geografisk plats eller en IP-adress med [platsbaserade principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Enhetsbaserad villkorlig åtkomst: Se till att endast registrerade, godkända och kompatibla enheter kan komma åt företagsdata med [enhetsbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Programbaserad villkorlig åtkomst: Arbetet behöver inte sluta när en användare inte finns i företagsnätverket. [Säker åtkomst till företagsmoln och lokala appar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) och behålla kontrollen med villkorlig åtkomst.

* Riskbaserad villkorlig åtkomst: Skydda dina data från illvilliga hackare med en [riskbaserad princip för villkorlig åtkomst](https://www.microsoft.com/cloud-platform/conditional-access) som kan tillämpas på alla appar och alla användare, oavsett om de är lokalt eller i molnet.

* Azure AD Access Panel: Med din application proxy-tjänst distribuerad och program som publiceras på ett säkert sätt, erbjuder användarna ett enkelt nav för att identifiera och komma åt alla sina program. Öka produktiviteten med självbetjäningsfunktioner, till exempel möjligheten att begära åtkomst till nya appar och grupper eller hantera åtkomst till dessa resurser för andras räkning, via [åtkomstpanelen](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Hantera implementeringen

### <a name="required-roles"></a>Obligatoriska roller

Microsoft förespråkar principen att ge minsta möjliga behörighet att utföra nödvändiga uppgifter med Azure AD. [Granska de olika Azure-rollerna som är tillgängliga](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) och välj den rätta för att tillgodose behoven hos varje persona. Vissa roller kan behöva tillämpas tillfälligt och tas bort när distributionen har slutförts.

| Affärsroll| Affärsuppgifter| Azure AD-roller |
|---|---|---|
| Helpdesk admin | Vanligtvis begränsad till kvalificerade slutanvändar rapporterade problem och utföra begränsade uppgifter som att ändra användarnas lösenord, ogiltigförklara uppdateringstoken och övervaka tjänstens hälsa. | Administratör för helpdesk |
| Identitetsadministratör| Läs Azure AD-inloggningsrapporter och granskningsloggar för att felsöka App Proxy-relaterade problem.| Säkerhetsläsare |
| Programägare| Skapa och hantera alla aspekter av företagsprogram, programregistreringar och programproxyinställningar.| Programadministratör |
| Infrastrukturadministratör | Ägare av överrullning av certifikat | Programadministratör |

Minimera antalet personer som har tillgång till säker information eller resurser kommer att bidra till att minska risken för en skadlig aktör att få obehörig åtkomst, eller en behörig användare oavsiktligt påverkar en känslig resurs. 
 
Användare måste dock fortfarande utföra dagliga privilegierade åtgärder, så att tillämpa JIT-principer [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (just-in-time) för att ge privilegierad åtkomst till Azure-resurser på begäran och Azure AD är vår rekommenderade metod för att effektivt hantera administrativ åtkomst och granskning.

### <a name="reporting-and-monitoring"></a>Rapportering och övervakning

Azure AD ger ytterligare insikter om organisationens programanvändning och driftshälsa via [granskningsloggar och rapporter](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Programproxy gör det också mycket enkelt att övervaka anslutningsappar från Azure AD-portalen och Windows-händelseloggarna.

#### <a name="application-audit-logs"></a>Granskningsloggar för program

Dessa loggar ger detaljerad information om inloggningar till program som konfigurerats med Application Proxy och enheten och användaren som använder programmet. [Granskningsloggar](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) finns i Azure-portalen och i [Gransknings-API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) för export. Dessutom är [användnings- och insiktersrapporter](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) också tillgängliga för ditt program.

#### <a name="application-proxy-connector-monitoring"></a>Övervakning av programproxyanslutning

Kontakterna och tjänsten tar hand om alla uppgifter med hög tillgänglighet. Du kan övervaka status för dina anslutningsappar från sidan Programproxy i Azure AD-portalen. Mer information om anslutningsunderhåll finns i [Förstå Azure AD Application Proxy Connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance).

![Exempel: Azure AD Application Proxy-kopplingar](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows-händelseloggar och prestandaräknare

Kopplingar har både administratörs- och sessionsloggar. Administratörsloggarna innehåller viktiga händelser och deras fel. Sessionsloggarna innehåller alla transaktioner och deras bearbetningsinformation. Loggar och räknare finns i Windows-händelseloggar för mer information se [Förstå Azure AD Application Proxy Connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). Följ den här [självstudien för att konfigurera händelseloggdatakällor i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Felsökningsguide och steg

Läs mer om vanliga problem och hur du löser dem med vår guide för felsökning [av](application-proxy-troubleshoot.md) felmeddelanden. 

Följande artiklar innehåller vanliga scenarier som också kan användas för att skapa felsökningsguider för supportorganisationen. 

* [Problem med att visa appsida](application-proxy-page-appearance-broken-problem.md)
* [För lång programinläsning](application-proxy-page-load-speed-problem.md)
* [Länkar på programsidan fungerar inte](application-proxy-page-links-broken-problem.md)
* [Vilka portar ska jag öppna för min app?](application-proxy-connectivity-ports-how-to.md)
* [Ingen fungerande anslutning i en grupp med anslutningar för min app](application-proxy-connectivity-no-working-connector.md)
* [Konfigurera i administratörsportalen](application-proxy-config-how-to.md)
* [Konfigurera enkel inloggning till min app](application-proxy-config-sso-how-to.md)
* [Problem med att skapa en app i administratörsportalen](application-proxy-config-problem.md)
* [Konfigurera begränsad Kerberos-delegering](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurera med PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Fel: Det går inte att få åtkomst till det här företagsprogrammet](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Problem med att installera anslutningsappen för programproxyagenten](application-proxy-connector-installation-problem.md)
* [Inloggningsproblem](application-sign-in-problem-on-premises-application-proxy.md)
