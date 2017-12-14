---
title: Konfigurera Azure MFA | Microsoft Docs
description: "Detta är Azure Multi-Factor authentication-sidan som beskriver vad du kan göra med MFA.  Detta inkluderar rapporter, bedrägerivarning, engångsförbikoppling, anpassade röstmeddelanden som cachelagring, tillförlitliga IP-adresser och app-lösenord."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 4dc029e01762d9ab6eb1c930f2941d0c1ac8ed7d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurera Azure Multi-Factor Authentication-inställningar

Den här artikeln hjälper dig att hantera Azure Multi-Factor Authentication nu när du är igång.  Den omfattar olika avsnitt som hjälper dig att få ut mesta möjliga av Azure Multi-Factor Authentication.  Alla dessa funktioner är inte tillgängliga i varje [versionen av Azure Multi-Factor Authentication](/multi-factor-authentication-get-started.md#what-features-do-i-need).

| Funktion | Beskrivning | 
|:--- |:--- |
| [Blockera och avblockera användare](#block-and-unblock) |Blockera/avblockera användare kan hindra användare från att ta emot begäranden om autentisering. |
| [Bedrägerivarning](#fraud-alert) |Bedrägerivarning kan konfigureras och ställa in så att användarna kan rapportera bedrägliga försöker få åtkomst till sina resurser. |
| [Engångsförbikoppling](#one-time-bypass) |En engångsförbikoppling kan en användare autentiseras en gång genom att ”kringgå” multifaktorautentisering. |
| [Anpassade röstmeddelanden](#custom-voice-messages) |Anpassade röstmeddelanden kan du använda dina egna inspelningar eller helg med multifaktorautentisering. |
| [Cachelagring](#caching-in-azure-multi-factor-authentication) |Cachelagring kan du ange en viss tid tidsperiod så att efterföljande autentiseringsförsök lyckas automatiskt. |
| [Tillförlitliga IP-adresser](#trusted-ips) |Administratörer av en hanterad eller extern klient kan använda betrodda IP-adresser för att kringgå tvåstegsverifiering för användare som loggar in från företagets lokalt intranät. |
| [Applösenord](#app-passwords) |Ett applösenord kan ett program som inte är MFA-medveten hoppa över multifaktorautentisering och fortsätta arbeta. |
| [Kom ihåg Multi-Factor Authentication för sparade enheter och webbläsare](#remember-multi-factor-authentication-for-devices-that-users-trust) |Kan du komma ihåg enheter för ett visst antal dagar efter att en användare har loggat in med MFA. |
| [Valbar verifieringsmetoderna](#selectable-verification-methods) |Kan du välja de autentiseringsmetoder som är tillgängliga för användare att använda. |

## <a name="block-and-unblock"></a>Blockera och avblockera
Blockera/avblockera användare kan användas för att förhindra att användare tar emot begäranden om autentisering. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Blockerade användare är blockerade för 90 dagar från att de blockeras.

### <a name="block-a-user"></a>Blockera en användare
1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **MFA-Server** > **blockera/avblockera användare**.
3. Klicka på **Lägg till** blockerar en användare.
4. Välj den **replikeringsgruppen**, ange blockerade användarnamnet som  **username@domain.com** , och ange en kommentar i den **orsak** fältet.
5. Klicka på **Lägg till** till slut att användaren blockeras.

### <a name="unblock-a-user"></a>Avblockera en användare
1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **MFA-Server** > **blockera/avblockera användare**.
3. Klicka på **avblockera** i den **åtgärd** kolumnen bredvid den användare du vill avblockera.
4. Ange en kommentar i den **skäl till avblockering** fältet.
5. Klicka på **avblockera** till slut att användaren avblockeras.

## <a name="fraud-alert"></a>Bedrägerivarning
Bedrägerivarning kan konfigureras och ställa in så att användarna kan rapportera bedrägliga försöker få åtkomst till sina resurser.  Användare kan rapportera bedrägeri med mobilappen eller via telefonen.

### <a name="turn-on-fraud-alert"></a>Aktivera bedrägerivarning
1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **MFA-Server** > **bedrägerivarning**.

   ![Bedrägerivarning](./media/multi-factor-authentication-whats-next/fraudalert.png)

3. Aktivera **Tillåt användare att skicka bedrägerivarningar** till **på**.
4. Välj **Spara**.

### <a name="configuration-options"></a>Konfigurationsalternativ

- **Blockera användare när bedrägeri rapporteras** – om en användare rapporter bedrägeri, sitt konto har blockerats i 90 dagar eller tills en administratör avblockeras sitt konto. En administratör kan granska inloggningar med hjälp av rapporten inloggning och vidta lämpliga åtgärder för att förhindra framtida bedrägeri. En administratör kan sedan [avblockera](#unblock-a-user) användarens konto.
- **Kod för att rapportera bedrägeri under inledande hälsning** – när användarna får ett telefonsamtal för att utföra tvåstegsverifiering, de normalt tryck på # för att bekräfta sina inloggning. Om de vill rapportera bedrägeri ange de en kod innan du trycker på #. Den här koden är **0** som standard, men du kan anpassa den.

> [!NOTE]
> Microsofts standard rösthälsning Instruera användarna att trycka på 0# för att skicka en bedrägeriavisering om. Om du vill använda en kod än 0, bör du registrera och överföra din egen anpassade rösthälsning med lämpliga instruktioner.

### <a name="view-fraud-reports"></a>Visa bedrägeri rapporter
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj **Active Directory** till vänster.
3. Välj den katalog som du vill hantera. 
4. Välj **konfigurera**
5. Välj under Multi-Factor Authentication **hantera tjänstinställningar**.
6. Längst ned på sidan för inställningar väljer **gå till portalen**.
7. I Azure Multi-Factor Authentication-hanteringsportalen, under Visa en rapport klickar du på **bedrägeriförsök**.
8. Ange datumintervallet som du vill visa i rapporten. Du kan också ange användarnamn, telefonnummer och användarens status.
9. Klicka på **kör** att få fram en rapport över bedrägerivarningar. Klicka på **exportera till CSV** om du vill exportera rapporten.

## <a name="one-time-bypass"></a>Engångsförbikoppling
En engångsförbikoppling kan en användare autentiseras en gång utan att utföra tvåstegsverifiering. Förbikopplingen är tillfällig och upphör att gälla efter ett angivet antal sekunder. I situationer där mobilappen eller phone inte tar emot ett meddelande eller telefonsamtal, aktiverar du en engångsförbikoppling så att användaren kan komma åt en resurs.

### <a name="create-a-one-time-bypass"></a>Skapa en engångsförbikoppling

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **MFA-Server** > **engångsförbikoppling**.

   ![Engångsförbikoppling](./media/multi-factor-authentication-whats-next/onetimebypass.png)
3. Välj **Lägg till**.
4. Välj replikeringsgruppen för den här kringgå om det behövs.
5. Ange användarnamnet (i form av username@domain.com), antal sekunder som förbikopplingen ska gälla för och orsaken till förbikopplingen. 
6. Välj **Lägg till**. Tidsgränsen träder i kraft omedelbart, så att användaren behöver logga in innan engångsförbikopplingen upphör att gälla. 

### <a name="view-the-one-time-bypass-report"></a>Visa rapporten engångsförbikoppling
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj **Active Directory** till vänster.
3. Välj den katalog som du vill hantera. 
4. Välj **konfigurera**
5. Välj under Multi-Factor Authentication **hantera tjänstinställningar**.
6. Längst ned på sidan för inställningar väljer **gå till portalen**.
7. I Azure Multi-Factor Authentication-hanteringsportalen, under Visa en rapport klickar du på **Engångsförbikoppling**.
8. Ange datumintervallet som du vill visa i rapporten. Du kan också ange användarnamn, telefonnummer och användarens status.
9. Klicka på **kör** att få fram en rapport över förbikopplingar. Klicka på **exportera till CSV** om du vill exportera rapporten.

## <a name="custom-voice-messages"></a>Anpassade röstmeddelanden
Anpassade röstmeddelanden kan du använda dina egna inspelningar eller helg för tvåstegsverifiering. Dessa kan användas som tillägg till eller ersätta Microsoft poster.

Innan du börjar ska du vara medveten om följande begränsningar:

* Filformat som stöds är .wav eller .mp3.
* Maximal filstorlek är 5 MB.
* Autentiseringsmeddelanden måste vara kortare än 20 sekunder. Något längre tid än 20 sekunder kan leda till att verifieringen misslyckas eftersom användaren inte svarar innan meddelandet avslutas orsakar verifieringen timeout.

### <a name="set-up-a-custom-message"></a>Konfigurera ett anpassat meddelande

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **MFA-Server** > **telefonsamtal inställningar**.

   ![Inställningar för telefonsamtal](./media/multi-factor-authentication-whats-next/phonecallsettings.png)

3. Välj **Lägg till hälsning**.
4. Välj vilken typ av hälsning och språket.
5. Välj en .mp3 eller .wav fil att överföra.
6. Välj **Lägg till**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Cachelagring i Azure Multi-Factor Authentication
Cachelagring kan du ange en viss tid tidsperiod så att efterföljande autentiseringsförsök inom denna tidsperiod lyckas automatiskt. Det här används främst när lokalt system som VPN skickar flera verifiering begäranden när den första begäranden pågår fortfarande. Cachelagring kan efterföljande förfrågningar ska lyckas automatiskt när användaren lyckas första kontroll pågår. 

Cachelagring är inte avsedd att användas för inloggningar till Azure AD.

### <a name="set-up-caching"></a>Konfigurera cachelagring 
1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Gå till **Azure Active Directory** > **MFA-Server** > **cachelagring regler**.

   ![Regler för cachelagring](./media/multi-factor-authentication-whats-next/cachingrules.png)

4. Välj **Lägg till**.
5. Välj cachetyp i listrutan och ange antalet cachelagringssekunder som max. 
6. Om det behövs, Välj en autentiseringstyp och ange ett program. 
7. Välj **Lägg till**.


## <a name="trusted-ips"></a>Tillförlitliga IP-adresser
Betrodda IP-adresser är en funktion i Azure MFA som administratörer för en hanterad eller extern klient kan använda för att kringgå tvåstegsverifiering för användare som loggar in från företagets lokalt intranät. Den här funktionen är tillgänglig med den fullständiga versionen av Azure Multi-Factor Authentication, inte den kostnadsfria versionen för administratörer. Mer information om hur du hämtar den fullständiga versionen av Azure Multi-Factor Authentication finns [Azure Multi-Factor Authentication](multi-factor-authentication.md).

| Typ av Azure AD-klient | Alternativen för tillförlitlig IP |
|:--- |:--- |
| Hanterad |<li>Specifik IP-adressintervall – administratörer kan ange ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från företagets intranät.</li> |
| Federerad |<li>Alla externa användare – alla federerade användare som loggar in från inom organisationen kringgå tvåstegsverifiering med hjälp av ett anspråk som utfärdats av AD FS.</li><br><li>Specifik IP-adressintervall – administratörer kan ange ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från företagets intranät. |

För att kringgå fungerar bara från i företagets intranät. Till exempel om du har valt alla federerade användare, och en användare loggar in från utanför företagets intranät, har användaren autentiseras med tvåstegsverifiering även om användaren anger ett AD FS-anspråk. 

**Slutanvändarens upplevelse i corpnet:**

När tillförlitliga IP-adresser är inaktiverat tvåstegsverifiering krävs för webbläsaren flöden och applösenord krävs för äldre klientappar. 

När tillförlitliga IP-adresser aktiveras tvåstegsverifiering är *inte* krävs för webbläsaren flöden och applösenord är *inte* krävs för äldre appar rich-klient, förutsatt att användaren har redan skapat en app lösenordet. När ett applösenord används kan fortfarande det krävs. 

**Slutanvändarens upplevelse utanför corpnet:**

Om betrodda IP-adresser är aktiverad eller inte kan tvåstegsverifiering krävs för webbläsaren flöden och applösenord krävs för äldre klientappar. 

### <a name="enable-named-locations-using-conditional-access"></a>Aktivera sökvägarna med villkorlig åtkomst

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **villkorlig åtkomst** > **med namnet platser**
3. Välj **ny plats**
4. Ange ett namn för platsen
5. Välj **Markera som betrodd plats**
6. Ange IP-adressintervall i CIDR-notation (exempel 192.168.1.1/24)
7. Välj **skapa**

### <a name="enable-trusted-ips-using-conditional-access"></a>Aktivera tillförlitliga IP-adresser med villkorlig åtkomst

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **villkorlig åtkomst** > **med namnet platser**
3. Välj **konfigurera MFA tillförlitliga IP-adresser**
4. På sidan Inställningar under betrodda IP-adresser, har du två alternativ:
   
   * **För förfrågningar från externa användare som kommer från intranätet** – Markera kryssrutan. Alla externa användare som loggar in från företagsnätverket kringgå tvåstegsverifiering med hjälp av ett anspråk som utfärdats av AD FS. Kontrollera att AD FS har en regel för att lägga till intranätet anspråk till lämpliga trafik. Om regeln inte finns, skapa följande regel i AD FS ”: c: [typen ==” http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork ”] = > issue(claim = c)”;

   * **För begäranden från en viss mängd offentliga IP-adresser** – ange IP-adresser i textrutan med CIDR-notering. Till exempel: xxx.xxx.xxx.0/24 för IP-adresser i intervallet xxx.xxx.xxx.1 – xxx.xxx.xxx.254 eller xxx.xxx.xxx.xxx/32 för en enda IP-adress. Du kan ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgå tvåstegsverifiering.
5. Välj **Spara**.

### <a name="enable-trusted-ips-using-service-settings"></a>Aktivera tillförlitliga IP-adresser med hjälp av service-inställningar

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**
3. Välj **Multi-Factor Authentication**
4. Välj under Multi-Factor Authentication **tjänstinställningar**.
5. På sidan Inställningar under betrodda IP-adresser, har du två alternativ:
   
   * **För förfrågningar från externa användare som kommer från intranätet** – Markera kryssrutan. Alla externa användare som loggar in från företagsnätverket kringgå tvåstegsverifiering med hjälp av ett anspråk som utfärdats av AD FS. Kontrollera att AD FS har en regel för att lägga till intranätet anspråk till lämpliga trafik. Om regeln inte finns, skapa följande regel i AD FS ”: c: [typen ==” http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork ”] = > issue(claim = c)”;

   * **För begäranden från en viss mängd offentliga IP-adresser** – ange IP-adresser i textrutan med CIDR-notering. Till exempel: xxx.xxx.xxx.0/24 för IP-adresser i intervallet xxx.xxx.xxx.1 – xxx.xxx.xxx.254 eller xxx.xxx.xxx.xxx/32 för en enda IP-adress. Du kan ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgå tvåstegsverifiering.
6. Välj **Spara**.

![Tillförlitliga IP-adresser](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>Applösenord
Vissa appar, t.ex. Office 2010 eller senare och Apple Mail stöder inte tvåstegsverifiering. De är inte konfigurerad för att godkänna en andra verifiering. Om du vill använda dessa appar behöver du använda ”applösenord” istället för lösenordet traditionella. Applösenordet tillåter program att kringgå tvåstegsverifiering och fortsätta arbeta.

> [!NOTE]
> Modern autentisering för Office 2013-klienter
> 
> Office 2013-klienter (inklusive Outlook) och nyare stöd för modern autentiseringsprotokoll och kan aktiveras för att fungera med tvåstegsverifiering. När du har aktiverat, krävs inte applösenord för dessa klienter.  Mer information finns i [Office 2013 modern autentisering offentlig förhandsgranskning av](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

### <a name="important-things-to-know-about-app-passwords"></a>Viktiga saker du behöver veta om applösenord
Följande är en viktig lista över saker som du bör känna till om applösenord.

* Applösenord behöver bara anges en gång per app. Användare behöver hålla ordning på dem och ange dem varje gång.
* Det faktiska lösenordet genereras automatiskt och tillhandahålls inte av användaren. Detta beror på att det automatiskt genererade lösenordet är svårare för angripare att gissa och är säkrare.
* Det finns en gräns på 40 lösenord per användare. 
* Appar som cachelagrar lösenord och använda det i lokala scenarier kan bli felaktiga eftersom applösenordet inte känns igen utanför organisations-id. Ett exempel är Exchange e-postmeddelanden som är lokalt men den arkiverade e-posten inte finns i molnet. Samma lösenord fungerar inte.
* När multifaktorautentisering har aktiverats på en användares konto kan applösenord användas med de flesta icke-webbläsarklienter som Outlook och Lync. Administrativa åtgärder kan inte utföras med applösenord med icke-webbläsarprogram, till exempel Windows PowerShell, även om användaren har ett administratörskonto.  Skapa ett tjänstkonto med ett starkt lösenord för att köra PowerShell-skript och aktivera inte kontot för tvåstegsverifiering.

> [!WARNING]
> Applösenord fungerar inte i hybridmiljöer där klienter kommunicera med både lokalt och molnet slutpunkter för automatisk upptäckt. Detta beror på att domänlösenord krävs för att autentisera lokalt och applösenord krävs för att autentisera med molnet.

### <a name="naming-guidance-for-app-passwords"></a>Riktlinjer för Applösenord för namngivning
Applösenord bör avspegla den enhet som används. Till exempel om du har en bärbar dator som har icke-webbläsarappar, till exempel Outlook, Word och Excel, skapa ett applösenord med namnet bärbar och använda det applösenordet i dessa program. Skapa sedan en annan applösenord med namnet skrivbordet för samma program på din dator. 

Microsoft rekommenderar att skapa ett applösenord per enhet, inte ett applösenord per program.

### <a name="federated-sso-app-passwords"></a>Applösenord för federerade (SSO)
Azure AD stöder federation (enkel inloggning) med lokala Windows Server Active Directory Domain Services (AD DS). Om din organisation är federerat med Azure AD och du kommer att använda Azure Multi-Factor Authentication, är det viktigt att du med följande information om applösenord. Det här avsnittet gäller bara för federerade (SSO)-kunder.

* Applösenord verifieras av Azure AD och därför kringgå federation. Federation används bara aktivt när du ställer in applösenord.
* För federerade användare för (SSO), är det inte att kontakta identitetsprovider (IdP), till skillnad från det passiva flödet. Lösenorden lagras i organisations-id. Om användaren lämnar företaget, har denna information kan flöda till organisationens id via DirSync i realtid. Inaktivering/borttagning av konto kan ta upp till tre timmar att synkronisera, vilket fördröjer inaktivering/borttagning av Applösenord i Azure AD.
* Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
* Ingen lokal autentisering loggning/granskning kapaciteten är tillgänglig för Applösenord.
* Vissa avancerade arkitektur Designer kan kräva en kombination av organisationens användarnamn och lösenord och lösenord när du använder tvåstegsverifiering med klienter, beroende på var de autentiseras. För klienter som autentiseras mot en lokal infrastruktur, använder du en organisations användarnamn och lösenord. För klienter som autentiseras mot Azure AD, använder du applösenordet.

  Anta att du har en arkitektur som består av följande:

  * Du federerar din lokala Active Directory med Azure AD-instans
  * Du använder Exchange online
  * Du använder Lync som är specifikt lokalt
  * Du använder Azure Multi-Factor Authentication

  ![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

  I så fall måste göra du följande:

  * När logga in på Lync, använda din organisationer användarnamn och lösenord.
  * När du försöker komma åt adressboken via ett Outlook-klienten som ansluter till Exchange online kan du använda ett applösenord.

### <a name="allow-app-password-creation"></a>Tillåt att appen lösenord skapas
Användare kan inte skapa applösenord som standard. Den här funktionen måste vara aktiverat. Använd följande procedur för att ge användare möjlighet att skapa applösenord:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**
3. Välj **Multi-Factor Authentication**
4. Välj under Multi-Factor Authentication **tjänstinställningar**.
6. Välj knappen bredvid **låta användarna skapa applösenord för att logga in på icke-webbläsarappar**.

![Skapa Applösenord](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Skapa applösenord
Användare kan skapa applösenord vid första registreringen. De får ett alternativ i slutet av registreringsprocessen som kan användas för att skapa applösenord.

Användare kan också skapa applösenord efter registreringen genom att ändra inställningarna i Azure-portalen eller Office 365-portalen. Mer information och detaljerade anvisningar för dina användare kan se [vad är applösenord i Azure Multi-Factor Authentication](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Kom ihåg Multifaktorautentisering för enheter som har förtroende för användare
Komma ihåg Multifaktorautentisering för enheter och webbläsare att användare förtroende är en kostnadsfri funktion för alla MFA-användare. Den här inställningen kan du välja att hoppa över Multifaktorautentisering för ett visst antal dagar efter en lyckad logga in med MFA. Detta kan förbättra användbarhet genom att minimera antalet gånger som en användare kan utföra tvåstegsverifiering på samma enhet.

Men om ett konto eller en enhet har komprometterats bör kan komma ihåg MFA för betrodda enheter påverka säkerheten. Om ett företagskonto har komprometterats eller en betrodd enhet tappas bort eller blir stulen, bör du [Återställ Multifaktorautentisering på alla enheter](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Återkallar den här åtgärden betrodda status från alla enheter och användaren krävs för att utföra tvåstegsverifiering igen. Du kan också instruera användarna att återställa MFA på sina egna enheter med instruktionerna i [hantera inställningar för tvåstegsverifiering](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Hur det fungerar

Kom ihåg Multi-Factor Authentication fungerar genom att ange beständiga cookies i webbläsaren när en användare checkar den ”fråga inte igen för **X** dagar” kryssrutan vid inloggning. Användaren ombeds inte för MFA igen från den webbläsaren tills cookien förfaller. Om användaren startar en annan webbläsare på samma enhet eller rensar sina cookies, uppmanas de att verifiera igen. 

Den ”fråga inte igen för **X** dagar” kryssrutan inte visas i icke-webbläsarappar, oavsett om de stöder modern autentisering. De här apparna använda uppdaterings-tokens som ger nya åtkomsttoken varje timme. När en uppdateringstoken är validerade, Azure AD-kontroller att den senaste gången tvåstegsverifiering utfördes var inom det konfigurerade antalet dagar. 

Därför kan komma ihåg Multifaktorautentisering på betrodda enheter minskar antalet autentiseringar i webbprogram (som normalt fråga varje gång) men ökar antalet autentiseringar för modern autentisering klienter (vilket normalt fråga efter 90 dagar).

> [!NOTE]
>Den här funktionen är inte kompatibel med funktionen ”jag vill förbli inloggad” i AD FS när användare utför tvåstegsverifiering för AD FS via Azure MFA-Server eller en MFA-lösning från tredje part. Om användarna Markera ”jag vill förbli inloggad” på AD FS och även markera sin enhet som betrodd för MFA, kan de inte verifiera efter ”Kom ihåg MFA” antalet dagar upphör att gälla. Azure AD begär en ny tvåstegsverifiering, men AD FS returnerar en token med den ursprungliga MFA anspråk och datumet i stället för att utföra tvåstegsverifiering igen. Anger av en kontroll skapas mellan Azure AD och AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Aktivera Kom ihåg multifaktorautentisering
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**
3. Välj **Multi-Factor Authentication**
4. Välj under Multi-Factor Authentication **tjänstinställningar**.
5. På sidan Inställningar under **hantera komma ihåg multifaktorautentisering**, kontrollera den **Tillåt användare att komma ihåg multifaktorautentisering på enheter de litar** rutan.

   ![Kom ihåg enheter](./media/multi-factor-authentication-whats-next/remember.png)

6. Ange antalet dagar som du vill tillåta att kringgå tvåstegsverifiering betrodda enheter. Standardvärdet är 14 dagar.
7. Välj **Spara**.

### <a name="mark-a-device-as-trusted"></a>Markera en enhet som betrodd

När du aktiverar den här funktionen kan användare kan markera en enhet som betrodda när de loggar in genom att kontrollera **fråga inte igen**.

![Fråga inte igen – skärmbild](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Valbar verifieringsmetoderna
Du kan välja vilka verifieringsmetoderna är tillgängliga för användarna. Följande tabell innehåller en kort översikt över varje metod.

När användarna registrerar sina konton för MFA, kan de välja sin önskade verifieringsmetod utanför de alternativ som du har aktiverat. Vägledning för sina registreringen ingår i [Konfigurera mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md)

| Metod | Beskrivning |
|:--- |:--- |
| Samtal till telefon |Placerar en automatiserad röstsamtal. Användaren svara och trycka på #-tangenten för att autentisera. Det här telefonnumret har inte synkroniserats till lokala Active Directory. |
| Textmeddelande till telefon |Skickar ett SMS med verifieringskoden. Användaren uppmanas att ange verifieringskoden i inloggning-gränssnittet. Den här processen kallas enkelriktad SMS. Dubbelriktad SMS innebär att användaren måste text tillbaka en viss kod. Dubbelriktad SMS är inaktuell och kommer inte längre stöds från och med 14 November 2018. Användare som har konfigurerats för dubbelriktad SMS ska stängas automatiskt till ”anrop till phone” autentisering vid den tiden.|
| Meddelande via mobilapp |Skickar ett push-meddelande till din telefon eller en registrerad enhet. Användaren ser meddelandet och väljer **Kontrollera** att slutföra verifieringen. <br>Microsoft Authenticator-appen är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), och [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Verifieringskod från mobilapp |Microsoft Authenticator-appen genererar en ny OATH-Verifieringskod var 30: e sekund. Användaren anger den här koden i gränssnittet för inloggning.<br>Microsoft Authenticator-appen är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), och [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Hur du aktiverar/inaktiverar autentiseringsmetoder
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**
3. Välj **Multi-Factor Authentication**
4. Välj under Multi-Factor Authentication **tjänstinställningar**.
5. På sidan Inställningar under **verifieringsalternativ**, Markera/avmarkera de alternativ som du vill använda.

   ![Alternativ för verifiering](./media/multi-factor-authentication-whats-next/authmethods.png)

6. Klicka på **Spara**.
