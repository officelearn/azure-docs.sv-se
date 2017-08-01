---
title: 'Azure AD Connect: Anpassad installation | Microsoft Docs'
description: "Det här dokumentet beskriver de anpassade installationsalternativen för Azure AD Connect. Följ dessa instruktioner om du ska installera Active Directory via Azure AD Connect."
services: active-directory
keywords: "vad är Azure AD Connect, installera Active Directory, komponenter som krävs för Azure AD"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 82e8d7e0ea975f140eaf73a625d181a4ec68eaa7
ms.contentlocale: sv-se
ms.lasthandoff: 07/21/2017

---
# <a name="custom-installation-of-azure-ad-connect"></a>Anpassad installation av Azure AD Connect
Du använder **anpassade inställningar** för Azure AD Connect om du behöver fler installationsalternativ. Du använder dem till exempel om du har flera skogar eller om du vill konfigurera valfria funktioner som inte omfattas av snabbinstallationen. De används i samtliga fall där en [**snabbinstallation**](active-directory-aadconnect-get-started-express.md) inte uppfyller dina distributions- eller topologikrav.

Innan du börjar installera Azure AD Connect [laddar du ned Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) och kontrollerar att du uppfyller kraven i [Azure AD Connect: Maskinvara och krav](active-directory-aadconnect-prerequisites.md). Kontrollera också att du har nödvändiga konton tillgängliga. Mer information finns i [Azure AD Connect: Konton och behörigheter](active-directory-aadconnect-accounts-permissions.md).

Om de anpassade inställningarna inte matchar din topologi, till exempel för att uppgradera DirSync, läser du [relaterad dokumentation](#related-documentation) för andra scenarier.

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Installation av Azure AD Connect med anpassade inställningar
### <a name="express-settings"></a>Standardinställningar
På den här sidan klickar du på **Anpassa** för att starta en installation med anpassade inställningar.

### <a name="install-required-components"></a>Installera nödvändiga komponenter
När du installerar synkroniseringstjänsterna kan du lämna avsnittet för valfri konfiguration avmarkerat så konfigurerar Azure AD Connect allt automatiskt. Verktyget konfigurerar en instans av SQL Server 2012 Express LocalDB, skapar relevanta grupper och tilldelar behörigheter. Om du vill ändra standardinställningarna kan du använda följande tabell som hjälper dig att förstå de valfria konfigurationsalternativen.

![Nödvändiga komponenter](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)

| Valfri konfiguration | Beskrivning |
| --- | --- |
| Använda en befintlig SQL-server |Med det här alternativet kan du ange namnet på SQL-servern och namnet på instansen. Välj det här alternativet om du redan har en databasserver som du vill använda. Ange instansnamnet följt av ett kommatecken och portnummer i **Instansnamn** om bläddring inte är aktiverat för SQL-servern. |
| Använda ett befintligt tjänstkonto |Som standard använder Azure AD Connect ett lokalt tjänstkonto som ska användas av synkroniseringstjänsterna. Om du använder en fjärransluten SQL-server eller om du använder en proxyserver som kräver autentisering så behöver använda ett **hanterat tjänstkonto** eller ett tjänstkonto i domänen och måste även känna till lösenordet. I detta fall anger du det konto som ska användas. Kontrollera att användaren som kör installationen är en SA i SQL så att en inloggning för tjänstkontot kan skapas. Se [Azure AD Connect: Konton och behörigheter](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) |
| Ange anpassade synkroniseringsgrupper |Som standard skapar Azure AD Connect fyra grupper som är lokala på servern när synkroniseringstjänsterna installeras. Dessa grupper är: gruppen Administratörer, gruppen Operatorer, gruppen Bläddra och gruppen Återställning av lösenord. Du kan ange dina egna grupper här. Grupperna måste vara lokala på servern och de kan inte hittas i domänen. |

### <a name="user-sign-in"></a>Användarinloggning
När du har installerat de nödvändiga komponenterna uppmanas du att välja användaruppgifter för enkel inloggning. Följande tabell innehåller en kort beskrivning av de tillgängliga alternativen. En fullständig beskrivning av inloggningsmetoderna finns i [Användarinloggning](active-directory-aadconnect-user-signin.md).

![Användarinloggning](./media/active-directory-aadconnect-get-started-custom/usersignin2.png)

| Alternativ för enkel inloggning | Beskrivning |
| --- | --- |
| Lösenordssynkronisering |Användare kan logga in till Microsoft-molntjänster, till exempel Office 365, med samma lösenord som de använder i deras lokala nätverk. Användarnas lösenord synkroniseras med Azure AD som lösenordshasher och autentiseringen sker i molnet. Mer information finns i [Lösenordssynkronisering](active-directory-aadconnectsync-implement-password-synchronization.md). |
|Direktautentisering (förhandsversion)|Användare kan logga in till Microsoft-molntjänster, till exempel Office 365, med samma lösenord som de använder i deras lokala nätverk.  Användarnas lösenord skickas till den lokala Active Directory-kontrollanten för verifiering.
| Federation med AD FS |Användare kan logga in till Microsoft-molntjänster, till exempel Office 365, med samma lösenord som de använder i deras lokala nätverk.  Användarna dirigeras till deras lokala AD FS-instans för att logga in och autentiseringen sker lokalt. |
| Konfigurera inte |Ingen av funktionerna installeras eller konfigureras. Välj det här alternativet om du redan har en federationsserver från en annan tillverkare eller en annan befintlig lösning på plats. |
|Aktivera enkel inloggning|Det här alternativet är tillgängligt med både lösenordssynkronisering och Direktautentisering och tillhandahåller enkel inloggning för datoranvändare i företagsnätverket.  Mer information finns i avsnittet om [enkel inloggning](active-directory-aadconnect-sso.md). </br>Observera att det här alternativet inte är tillgängligt för AD FS-kunder eftersom AD FS redan erbjuder samma nivå av enkel inloggning.</br>(om PTA inte har släppts samtidigt)
|Inloggningsalternativ|Det här alternativet är tillgängligt för kunder som behöver lösenordssynkronisering och tillhandahåller en miljö med enkel inloggning för datoranvändare i företagsnätverket.  </br>Mer information finns i avsnittet om [enkel inloggning](active-directory-aadconnect-sso.md). </br>Observera att det här alternativet inte är tillgängligt för AD FS-kunder eftersom AD FS redan erbjuder samma nivå av enkel inloggning.


### <a name="connect-to-azure-ad"></a>Anslut till Azure AD
Ange ett globalt administratörskonto och lösenord på skärmen Anslut till Azure AD. Om du valde **Federation med AD FS** på föregående sida ska du inte logga in med ett konto i en domän som du planerar att aktivera för federation. En rekommendation är att använda ett konto i standarddomänen **onmicrosoft.com**, som medföljer din Azure AD-katalog.

Det här kontot används bara för att skapa ett tjänstkonto i Azure AD och används inte när guiden har slutförts.  
![Användarinloggning](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

Om MFA är aktiverat för ditt globala administratörskonto måste du ange lösenordet igen i popup-fönstret för inloggning och slutföra MFA-kontrollen. Kontrollen kan bestå i att uppge en verifieringskod eller att ringa ett telefonsamtal.  
![Användarinloggning i MFA](./media/active-directory-aadconnect-get-started-custom/connectaadmfa.png)

[Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md) kan också vara aktiverat för det globala administratörskontot.

Om du får ett fel och har problem med anslutningen läser du [Felsöka anslutningsproblem](active-directory-aadconnect-troubleshoot-connectivity.md).

## <a name="pages-under-the-section-sync"></a>Sidor under synkroniseringsavsnittet

### <a name="connect-your-directories"></a>Anslut dina kataloger
För att kunna ansluta till Azure Directory-domäntjänsten behöver Azure AD Connect skogsnamnet och autentiseringsuppgifterna för ett konto med tillräcklig behörighet.

![Anslut katalog](./media/active-directory-aadconnect-get-started-custom/connectdir01.png)

När du har angett det första skogsnamnet och klickat på  **Lägg till katalog** visas en popup-dialogruta som uppmanar dig med följande alternativ:

| Alternativ | Beskrivning |
| --- | --- |
| Använda befintligt konto | Välj det här alternativet om du vill tillhandahålla ett befintligt AD DS-konto som ska användas av Azure AD Connect för att ansluta till AD-skogen under katalogsynkronisering. Du kan ange domändelen i NetBios- eller FQDN-format, dvs. FABRIKAM\syncuser eller fabrikam.com\syncuser. Det här kontot kan vara ett vanligt användarkonto eftersom det bara behöver standardläsbehörighet. Beroende på scenario kan du dock behöva fler behörigheter. Mer information finns i [Azure AD Connect: Konton och behörigheter](active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account). |
| Skapa ett nytt konto | Välj det här alternativet om du vill att Azure AD Connect-guiden ska skapa det AD DS-konto som krävs av Azure AD Connect för att ansluta till AD-skogen under katalogsynkronisering. När det är alternativet är valt anger du användarnamnet och lösenordet för ett företagsadministratörskonto. Företagets administratörskonto som angetts används av Azure AD Connect-guiden för att skapa AD DS-kontot som krävs. Du kan ange domändelen i NetBios- eller FQDN-format, d.v.s. FABRIKAM\administrator eller fabrikam.com\administrator. |

![Anslut katalog](./media/active-directory-aadconnect-get-started-custom/connectdir02.png)


### <a name="azure-ad-sign-in-configuration"></a>Inloggningskonfiguration för Azure AD
På den här sidan kan du granska de UPN-domäner som finns i lokala AD DS och som har verifierats i Azure AD. På den här sidan kan du också konfigurera attributet som ska användas för userPrincipalName.

![Overifierade domäner](./media/active-directory-aadconnect-get-started-custom/aadsigninconfig.png)  
Granska varje domän som markerats med **Inte tillagd** och **Inte verifierad**. Kontrollera att de domäner som du använder har verifierats i Azure AD. Klicka på symbolen Uppdatera när du har verifierat dina domäner. Mer information finns i [Lägga till och verifiera domänen](../active-directory-add-domain.md)

**UserPrincipalName** – Attributet userPrincipalName är det attributet som användare använder när de loggar in i Azure AD och Office 365. Domänerna som används, även kallade UPN-suffixet, bör verifieras i Azure AD innan användarna synkroniseras. Microsoft rekommenderar att du behåller standardattributet userPrincipalName. Om det här attributet är icke-dirigerbart och inte kan verifieras går det att välja ett annat attribut. Du kan till exempel välja email som attributet som ska innehålla inloggnings-ID:t. Om du använder ett annat attribut än userPrincipalName kallas det för ett **Alternativt ID**. Attributvärdet för ett alternativt ID måste följa standarden RFC822. Ett alternativt ID kan användas med både lösenordssynkronisering och federation.

>[!NOTE]
> När du aktiverar Direktautentisering måste du ha minst en verifierad domän för att kunna fortsätta med guiden.

> [!WARNING]
> Det går inte att använda ett alternativt ID med alla Office 365-arbetsbelastningar. Mer information finns i [Konfigurera alternativt inloggnings-ID](https://technet.microsoft.com/library/dn659436.aspx).
>
>

### <a name="domain-and-ou-filtering"></a>Domän- och organisationsenhetsfiltrering
Som standard synkroniseras alla domäner och organisationsenheter. Om det finns vissa domäner och organisationsenheter som du inte vill synkronisera till Azure AD kan du avmarkera dessa domäner och organisationsenheter.  
![Filtrering av domän-OU](./media/active-directory-aadconnect-get-started-custom/domainoufiltering.png)  
På den här sidan i guiden konfigureras den domänbaserade och OU-baserade filtreringen. Om du planerar att göra ändringar, se [domänbaserad filtrering](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) och [OU-baserad filtrering](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) innan du gör dessa ändringar. Vissa organisationsenheter är viktiga för funktionen och får inte vara omarkerade.

Om du använder OU-baserad filtrering med en Azure AD Connect-version som är äldre än 1.1.524.0 synkroniseras nya organisationsenheter som läggs till senare som standard. Om du inte vill att nya organisationsenheter ska synkroniseras kan du konfigurera det när guiden har slutförts med [OU-baserad filtrering](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering). För Azure AD Connect version 1.1.524.0 eller senare kan du ange om du vill att nya organisationsenheter ska synkroniseras eller inte.

Om du planerar att använda [gruppbaserad filtrering](#sync-filtering-based-on-groups) ska du kontrollera att organisationsenheten med gruppen ingår och inte filtreras med OU-filtrering. OU-filtrering utvärderas före gruppbaserad filtrering.

Det är också möjligt att vissa domäner inte kan nås på grund av brandväggsbegränsningar. Dessa domäner är omarkerade som standard och visas med en varning.  
![Domäner som inte kan nås](./media/active-directory-aadconnect-get-started-custom/unreachable.png)  
Om du ser den här varningen kontrollerar du att dessa domäner verkligen inte kan nås och att varningen är att förvänta.

### <a name="uniquely-identifying-your-users"></a>Identifiera användarna unikt

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>Välj hur användare ska identifieras i dina lokala kataloger
Med funktionen Matchande mellan skogar kan du definiera hur användare från AD DS-skogar representeras i Azure AD. En användare kan antingen representeras endast en gång i alla skogar eller ha en kombination av aktiverade och inaktiverade konton. Användaren kan också vara representerad som en kontakt i vissa skogar.

![Unik](./media/active-directory-aadconnect-get-started-custom/unique.png)

| Inställning | Beskrivning |
| --- | --- |
| [Dina användare representeras endast en gång över alla skogar](active-directory-aadconnect-topologies.md#multiple-forests-single-azure-ad-tenant) |Alla användare skapas som enskilda objekt i Azure AD. Objekten är inte anslutna i metaversum. |
| [E-postattribut](active-directory-aadconnect-topologies.md#multiple-forests-single-azure-ad-tenant) |Det här alternativet kopplar ihop användare och kontakter om e-postattributet har samma värde i olika skogar. Använd det här alternativet om dina kontakter har skapats med hjälp av GALSync. Om du väljer det här alternativet synkroniseras inte användarobjekt vars e-postattribut inte har fyllts i till Azure AD. |
| [ObjectSID och msExchangeMasterAccountSID/ msRTCSIP-OriginatorSid](active-directory-aadconnect-topologies.md#multiple-forests-single-azure-ad-tenant) |Det här alternativet kopplar ihop en aktiverad användare i en kontoskog med en inaktiverad användare i en resursskog. I Exchange kallas den här konfigurationen för en länkad postlåda. Det här alternativet kan också användas om du bara använder Lync och Exchange inte finns i resursskogen. |
| sAMAccountName och MailNickName |Det här alternativet kopplar ihop attribut om det förväntas att inloggnings-ID:t för användaren kan hittas. |
| Ett specifikt attribut |Med det här alternativet kan du välja ett eget attribut. Om du väljer det här alternativet synkroniseras inte användarobjekt vars (markerade) attributet inte har fyllts i till Azure AD. **Begränsning:** Se till att välja ett attribut som redan finns i metaversum. Om du väljer ett anpassat attribut (inte i metaversum) kan guiden inte slutföras. |

#### <a name="select-how-users-should-be-identified-with-azure-ad---source-anchor"></a>Välj hur användare ska identifieras med Azure AD – källfästpunkt
Attributet sourceAnchor är ett attribut som inte kan ändras under ett användarobjekts livslängd. Det är den primära nyckeln som länkar den lokala användaren med användaren i Azure AD.

| Inställning | Beskrivning |
| --- | --- |
| Låt Azure hantera källfästpunkten | Välj det här alternativet om du vill att Azure AD ska hämta attributet. Om du väljer det här alternativet använder Azure AD Connect attributet sourceAnchor-attributvalslogiken som beskrivs i avsnittet [Azure AD Connect: Design concepts - Using msDS-ConsistencyGuid as sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor) (Azure AD Connect: Designbegrepp - Använda msDS-ConsistencyGuid som sourceAnchor). Guiden informerar dig vilket attribut som har valts som källfästpunktsattribut när den anpassade installationen har slutförts. |
| Ett specifikt attribut | Välj det här alternativet om du vill ange ett befintligt AD-attribut som sourceAnchor-attribut. |

Eftersom attributet inte kan ändras måste du planera för ett lämpligt attribut som ska användas. En bra kandidat är objectGUID. Det här attributet ändras inte, såvida inte användarkontot flyttas mellan skogar/domäner. I en miljö med flera skogar där du flyttar konton mellan skogar måste ett annat attribut användas, till exempel ett attribut med employeeID. Undvik attribut som kan ändras när en person gifter sig eller får nya uppgifter. Du kan inte använda attribut med ett @-sign. Det betyder att du inte kan använda email eller userPrincipalName. Attributet är också skiftlägeskänsligt. Därför är det viktigt att du inte ändrar gemener/versaler om du flyttar ett objekt mellan skogar. Binära attribut är base64-kodade, men andra attributtyper är kvar i kodat tillstånd. I federationsscenarier och i vissa Azure AD-gränssnitt kallas det här attributet även för immutableID. Mer information om källfästpunkten finns i [designbegreppen](active-directory-aadconnect-design-concepts.md#sourceanchor).

### <a name="sync-filtering-based-on-groups"></a>Synkroniseringsfiltrering baserat på grupper
Med funktionen för gruppfiltrering kan du synkronisera en mindre deluppsättning objekt för en pilotdistribution. Om du vill använda den här funktionen skapar du en grupp för detta ändamål i din lokala Active Directory. Lägg sedan till användare och grupper som ska synkroniseras till Azure AD som direkta medlemmar. Senare kan du lägga till och ta bort användare i den här gruppen så att du har en lista med objekt som ska finnas i Azure AD. Alla objekt som du vill synkronisera måste vara direkta medlemmar i gruppen. Användare, grupper, kontakter och datorer/enheter måste vara direkta medlemmar. Kapslade gruppmedlemskap stöds inte. När du lägger till en grupp som en medlem läggs bara själva gruppen till, inte dess medlemmar.

![Synkronisera filtrering](./media/active-directory-aadconnect-get-started-custom/filter2.png)

> [!WARNING]
> Den här funktionen är endast avsedd att användas vid en pilotdistribution. Använd den inte i en fullständig produktionsdistribution.
>
>

I en komplett produktionsdistribution blir det svårt att underhålla en enda grupp med alla objekt som ska synkroniseras. I stället bör du använda någon av metoderna i [Konfigurera filtrering](active-directory-aadconnectsync-configure-filtering.md).

### <a name="optional-features"></a>Valfria funktioner
På den här sidan kan du välja de valfria funktionerna för dina specifika scenarier.

![Valfria funktioner](./media/active-directory-aadconnect-get-started-custom/optional.png)

> [!WARNING]
> Om DirSync eller Azure AD Sync är aktiverat för närvarande ska du inte aktivera någon av tillbakaskrivningsfunktionerna i Azure AD Connect.
>
>

| Valfria funktioner | Beskrivning |
| --- | --- |
| Exchange-hybridinstallation |Funktionen Exchange-hybridinstallation gör att Exchange-postlådor kan samexistera lokalt och i Office 365. Azure AD Connect synkroniserar en specifik uppsättning [attribut](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) från Azure AD tillbaka till din lokala katalog. |
| Gemensamma mappar för Exchange-e-post | Med funktionen Gemensamma mappar för Exchange-e-post kan du synkronisera e-postaktiverade objekt från gemensamma mappar på din lokala Active Directory till Azure AD. |
| Filtrering av Azure AD-appar och -attribut |Genom att aktivera filtrering av Azure AD-appar och -attribut kan du skräddarsy samlingen med synkroniserade attribut. Det här alternativet lägger till ytterligare två konfigurationssidor i guiden. Mer information finns i [Filtrering av Azure AD-appar och -attribut](#azure-ad-app-and-attribute-filtering). |
| Lösenordssynkronisering |Om du valde federation som inloggningslösning kan du aktivera det här alternativet. Lösenordssynkronisering kan sedan användas som ett reservalternativ. Mer information finns i [Lösenordssynkronisering](active-directory-aadconnectsync-implement-password-synchronization.md). </br></br>Om du valde Direktautentisering är det här alternativet aktiverat som standard för att ge stöd för äldre klienter och som ett säkerhetskopieringsalternativ. Mer information finns i [Lösenordssynkronisering](active-directory-aadconnectsync-implement-password-synchronization.md).|
| Tillbakaskrivning av lösenord |Om du aktiverar tillbakaskrivning av lösenord skrivs lösenordsändringar som kommer från Azure AD tillbaka till din lokala katalog. Mer information finns i [Komma igång med lösenordshantering](../active-directory-passwords-getting-started.md). |
| Tillbakaskrivning av grupp |Om du använder funktionen **Office 365-grupper** kan dessa grupper vara representerade i din lokala Active Directory. Det här alternativet är endast tillgänglig om Exchange finns i din lokala Active Directory. Mer information finns i [Tillbakaskrivning av grupp](active-directory-aadconnect-feature-preview.md#group-writeback). |
| Tillbakaskrivning av enheter |Med det här alternativet kan du skriva tillbaka enhetsobjekt i Azure AD till din lokala Active Directory för scenarier med villkorlig åtkomst. Mer information finns i [Aktivera tillbakaskrivning av enheter i Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md). |
| Synkronisering av katalogtilläggsattribut |Om du aktiverar Synkronisering av katalogtilläggsattribut synkroniseras angivna attribut till Azure AD. Mer information finns i [Katalogtillägg](active-directory-aadconnectsync-feature-directory-extensions.md). |

### <a name="azure-ad-app-and-attribute-filtering"></a>Filtrering av Azure AD-appar och -attribut
Om du vill begränsa vilka attribut som ska synkroniseras till Azure AD börjar du med att välja vilka tjänster som du använder. Om du gör konfigurationsändringar på den här sidan måste en ny tjänst väljas uttryckligen genom att du kör installationsguiden igen.

![Valfria funktioner – appar](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

Beroende på vilka tjänster du valde i det föregående steget visas alla attribut som synkroniseras på den här sidan. Den här listan är en kombination av alla typer av objekt som synkroniseras. Om det finns vissa specifika attribut som du inte behöver synkronisera kan du avmarkera dessa attribut.

![Valfria funktioner – attribut](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

> [!WARNING]
> Funktionaliteten kan påverkas om du tar bort attribut. Bästa praxis och rekommendationer finns i [attribut som synkroniseras](active-directory-aadconnectsync-attributes-synchronized.md#attributes-to-synchronize).
>
>

### <a name="directory-extension-attribute-sync"></a>Synkronisering av katalogtilläggsattribut
Du kan utvidga schemat i Azure AD med anpassade attribut som läggs till av din organisation eller andra attribut i Active Directory. Om du vill använda den här funktionen väljer du **Synkronisering av katalogtilläggsattribut** på sidan **Valfria funktioner**. Du kan markera fler attribut som ska synkroniseras på den här sidan.

![Katalogtillägg](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Mer information finns i [Katalogtillägg](active-directory-aadconnectsync-feature-directory-extensions.md).

### <a name="enabling-single-sign-on-sso"></a>Aktivera enkel inloggning (SSO)
Det är enkelt att konfigurera enkel inloggning för användning med lösenordssynkronisering eller Direktautentisering och du behöver bara utföra processen en gång för varje skog som synkroniseras med Azure AD. Konfigurationen omfattar följande två steg:

1.  Skapa nödvändigt datorkonto i lokala Active Directory.
2.  Konfigurera zonen Intranät på klientdatorerna så att den har stöd för enkel inloggning.

#### <a name="create-the-computer-account-in-active-directory"></a>Skapa datorkontot i Active Directory
För varje skog som har lagts till i Azure AD Connect måste du ange inloggningsuppgifterna för domänadministratören så att datorkontot kan skapas i varje skog. Autentiseringsuppgifter används bara för att skapa kontot och varken lagras eller används för andra åtgärder. Lägg bara till autentiseringsuppgifterna på sidan **Aktivera enkel inloggning** i Azure AD Connect-guiden som du ser:

![Aktivera enkel inloggning](./media/active-directory-aadconnect-get-started-custom/enablesso.png)

>[!NOTE]
>Du kan hoppa över en viss skog om du inte vill använda enkel inloggning med den skogen.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>Konfigurera zonen Intranät för klientdatorer
För att se till att klienten loggar in automatiskt i zonen Intranät måste du kontrollera att två URL:er ingår i zonen Intranät. Detta säkerställer att den domänanslutna datorn automatiskt skickar en Kerberos-biljett till Azure AD när den ansluter till företagsnätverket.
På en dator med verktyg för grupprinciphantering.

1.  Öppna verktygen för grupprinciphantering
2.  Redigera grupprincipen som ska tillämpas på alla användare. Till exempel standarddomänprincipen.
3.  Gå till **Användarkonfiguration\Administrativa mallar\Windows-komponenter\Internet Explorer\Internet på Kontrollpanelen\Sidan Säkerhet** och välj **Tilldelning av platser till zoner** enligt bilden nedan.
4.  Aktivera principen och ange följande två poster i dialogrutan.

        Value: `https://autologon.microsoftazuread-sso.com`  
        Data: 1  
        Value: `https://aadg.windows.net.nsatc.net`  
        Data: 1

5.  Det bör se ut ungefär så här:  
![Intranätszoner](./media/active-directory-aadconnect-get-started-custom/sitezone.png)

6.  Klicka på **OK** två gånger.

## <a name="configuring-federation-with-ad-fs"></a>Konfigurera federation med AD FS
Du kan konfigurera AD FS med Azure AD Connect med bara några klickningar. Du behöver följande innan konfigurationen.

* En Windows Server 2012 R2-server för federationsservern med fjärrhantering aktiverat
* En Windows Server 2012 R2-server för webbprogramproxyservern med fjärrhantering aktiverat
* Ett SSL-certifikat för federationstjänstnamnet som du tänker använda (t.ex. sts.contoso.com)

### <a name="ad-fs-configuration-pre-requisites"></a>Förutsättningar för AD FS-konfiguration
Om du vill konfigurera din AD FS-servergrupp med hjälp av Azure AD Connect kontrollerar du att WinRM är aktiverat på fjärrservrarna. Gå också igenom portkraven som anges i [Tabell 3 – Azure AD Connect och federationsservrar/WAP](active-directory-aadconnect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap).

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Skapa en ny AD FS-servergrupp eller använd en befintlig AD FS-servergrupp
Du kan använda en befintlig AD FS-servergrupp eller välja att skapa en ny AD FS-servergrupp. Om du väljer att skapa en ny servergrupp måste du uppge SSL-certifikatet. Om SSL-certifikatet är lösenordsskyddat uppmanas du att ange lösenordet.

![AD FS-servergrupp](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

Om du väljer att använda en befintlig AD FS-servergrupp kommer du direkt till sidan för konfiguration av förtroenderelationen mellan AD FS och AD Azure.

### <a name="specify-the-ad-fs-servers"></a>Ange AD FS-servrarna
Ange de servrar som du vill installera AD FS på. Du kan lägga till en eller flera servrar baserat på dina kapacitetsplaneringsbehov. Anslut alla servrar till Active Directory innan du utför den här konfigurationen. Microsoft rekommenderar att du installerar en enskild AD FS-server för test- och pilotdistributioner. Lägg sedan till och distribuera fler servrar beroende på dina skalningsbehov genom att köra Azure AD Connect igen efter den första konfigurationen.

> [!NOTE]
> Se till att alla servrar är anslutna till en AD-domän innan du gör den här konfigurationen.
>
>

![AD FS-servrar](./media/active-directory-aadconnect-get-started-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Ange webbprogramproxyservrarna
Ange de servrar som du vill använda som dina webbprogramproxyservrar. Webbprogramproxyservern distribueras i DMZ (mot ett extranät) och stöder autentiseringsbegäranden från extranätet. Du kan lägga till en eller flera servrar baserat på dina kapacitetsplaneringsbehov. Microsoft rekommenderar att du installerar en enskild webbprogramproxyserver för test- och pilotdistributioner. Lägg sedan till och distribuera fler servrar beroende på dina skalningsbehov genom att köra Azure AD Connect igen efter den första konfigurationen. Vi rekommenderar att du har motsvarande antal proxyservrar som krävs för att uppfylla autentiseringen från intranätet.

> [!NOTE]
> <li> Om det konto som du använder inte är en lokal administratör på AD FS-servrarna uppmanas du att ange administratörsautentiseringsuppgifter.</li>
> <li> Kontrollera att det finns en HTTP/HTTPS-anslutning mellan Azure AD Connect-servern och webbprogramproxyservern innan du utför det här steget.</li>
> <li> Kontrollera att det finns en HTTP/HTTPS-anslutning mellan webbappservern och AD FS-servern så att autentiseringsförfrågningar kan passera.</li>
>

![Webbapp](./media/active-directory-aadconnect-get-started-custom/adfs3.png)

Du uppmanas att ange autentiseringsuppgifter så att webbappservern kan upprätta en säker anslutning till AD FS-servern. Dessa autentiseringsuppgifter måste vara en lokal administratör på AD FS-servern.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Ange tjänstkontot för AD FS-tjänsten
AD FS-tjänsten kräver ett domäntjänstkonto för att autentisera användare och söka efter användarinformation i Active Directory. Två typer av tjänstkonton stöds:

* **Grupphanterat tjänstkonto** – Introducerades i Active Directory Domain Services med Windows Server 2012. Den här typen av konto tillhandahåller tjänster, till exempel AD FS, för ett konto utan att kontolösenordet behöver uppdateras regelbundet. Använd det här alternativet om du redan har Windows Server 2012-domänkontrollanter i den domän som AD FS-servrarna tillhör.
* **Domänanvändarkonto** – Den här typen av konto kräver att du anger ett lösenord och regelbundet uppdaterar lösenordet när lösenordet ändras eller upphör att gälla. Använd bara det här alternativet om du inte har Windows Server 2012-domänkontrollanter i den domän som AD FS-servrarna tillhör.

Om du har valt Grupphanterat tjänstkonto och funktionen aldrig har använts i Active Directory uppmanas du att ange autentiseringsuppgifter som företagsadministratör. Dessa autentiseringsuppgifter används för att initiera nyckelarkivet och aktivera funktionen i Active Directory.

![AD FS-tjänstkonto](./media/active-directory-aadconnect-get-started-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>Välj den Azure AD-domän som du vill federera
Den här konfigurationen används för att konfigurera federationsrelationen mellan AD FS och Azure AD. Den konfigurerar AD FS för att utfärda säkerhetstoken till Azure AD och konfigurerar Azure AD att lita på token från den här specifika instansen av AD FS. På den här sidan kan du bara konfigurera en enda domän i den inledande installationen. Du kan konfigurera flera domäner senare genom att köra Azure AD Connect igen.

![Azure AD-domän](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Verifiera Azure AD-domänen som valts för federation
När du väljer domänen som ska vara federerad får du nödvändig information av Azure AD Connect för att verifiera en overifierade domän. Information om hur du använder den här informationen finns i [Lägga till och verifiera domänen](../active-directory-add-domain.md).

![Azure AD-domän](./media/active-directory-aadconnect-get-started-custom/verifyfeddomain.png)

> [!NOTE]
> AD Connect försöker verifiera domänen under konfigurationsfasen. Om du fortsätter att konfigurera utan att lägga till de nödvändiga DNS-posterna kan guiden inte slutföra konfigurationen.
>
>

## <a name="configure-and-verify-pages"></a>Konfigurera och verifiera sidor
Konfigurationen sker på den här sidan.

> [!NOTE]
> Innan du fortsätter installationen, och om du har konfigurerat federation, kontrollerar du att du har konfigurerat [namnmatchning för federationsservrar](active-directory-aadconnect-prerequisites.md#name-resolution-for-federation-servers).
>
>

![Klart att konfigurera](./media/active-directory-aadconnect-get-started-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>Mellanlagringsläge
Det går att konfigurera en ny synkroniseringsserver parallellt med mellanlagringsläget. Du kan bara ha en synkroniseringsserver som exporterar till en katalog i molnet. Men om du vill flytta från en annan server, till exempel en som kör DirSync, kan du aktivera Azure AD Connect i mellanlagringsläge. När synkroniseringsmotorn har aktiverats importerar och synkroniserar den data som vanligt, men exporterar inte något till Azure AD eller AD. Funktionerna för lösenordssynkronisering och tillbakaskrivning av lösenord är inaktiverade när mellanlagringsläge är aktiverat.

![Mellanlagringsläge](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)

I mellanlagringsläge kan du göra nödvändiga ändringar i synkroniseringsmotorn och se vad som är på väg att exporteras. När konfigurationen ser bra ut kör du installationsguiden igen och inaktiverar mellanlagringsläge. Nu exporteras data till Azure AD från den här servern. Se till att inaktivera den andra servern på samma gång så att endast en server exporterar aktivt.

Mer information finns i [Mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode).

### <a name="verify-your-federation-configuration"></a>Verifiera federationkonfigurationen
Azure AD Connect verifierar DNS-inställningarna åt dig när du klickar på knappen Kontrollera.

![Slutför](./media/active-directory-aadconnect-get-started-custom/completed.png)

![Verifiera](./media/active-directory-aadconnect-get-started-custom/adfs7.png)

Utför även följande verifieringssteg:

* Kontrollera att du kan logga in från en webbläsare från en domänansluten dator i intranätet: Anslut till https://myapps.microsoft.com och kontrollera inloggningen med ditt inloggade konto. Det inbyggda AD DS-administratörskontot synkroniseras inte och kan inte användas för verifiering.
* Kontrollera att du kan logga in från en enhet från extranätet. Anslut till https://myapps.microsoft.com och ange dina autentiseringsuppgifter på en hemdator eller mobil enhet.
* Verifiera inloggningen på en rich-klient. Anslut till https://testconnectivity.microsoft.com, välj fliken **Office 365** och välj **Test av enkel inloggning i Office 365**.

## <a name="next-steps"></a>Nästa steg
När installationen har slutförts kan du logga ut och logga in igen i Windows innan du använda Synchronization Service Manager eller Synchronization Rule Editor.

Nu när Azure AD Connect är installerat kan du [verifiera installationen och tilldela licenser](active-directory-aadconnect-whats-next.md).

Läs mer om dessa funktioner, som aktiverades med installationen: [Förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) och [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).

Läs mer om [schemaläggaren och hur du utlöser synkronisering](active-directory-aadconnectsync-feature-scheduler.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

