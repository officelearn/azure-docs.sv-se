---
title: Konfigurera Azure Multi-Factor Authentication | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar Azure Multi-Factor Authentication-inställningar i Azure-portalen
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 509df5274a5e44342390036511689fa8f8146807
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869809"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurera Azure Multi-Factor Authentication-inställningar

Den här artikeln hjälper dig att hantera Azure Multi-Factor Authentication är igång. Den omfattar olika avsnitt som hjälper dig att få ut mesta möjliga av Azure Multi-Factor Authentication. Alla funktioner är inte tillgängliga i varje [versionen av Azure Multi-Factor Authentication](concept-mfa-whichversion.md#what-features-do-i-need).

| Funktion | Beskrivning | 
|:--- |:--- |
| [Blockera och avblockera användare](#block-and-unblock-users) |Använd funktionen blockera/avblockera användare för att hindra användare från att ta emot begäranden om autentisering. |
| [Bedrägerivarning](#fraud-alert) |Konfigurera bedrägeri postaviseringsfunktionen så att användarna kan rapportera bedrägliga försöker få åtkomst till sina resurser. |
| [Engångsförbikoppling](#one-time-bypass) |Funktionen engångsförbikoppling för att tillåta användare att autentisera en gång av _kringgå_ Multi-Factor Authentication. |
| [Anpassade röstmeddelanden](#custom-voice-messages) |Använd funktionen anpassade röst meddelanden för att använda din egen inspelningar eller helg med Multifaktorautentisering. |
| [Cachelagring](#caching-in-azure-multi-factor-authentication) |Använd funktionen cachelagring för att ange en viss tidsperiod, så att efterföljande autentiseringsförsök lyckas automatiskt. |
| [Tillförlitliga IP-adresser](#trusted-ips) |Administratörer för en hanterad eller federerade klient kan använda funktionen tillförlitliga IP-adresser för att kringgå tvåstegsverifiering för användare som loggar in från intranätet. |
| [Applösenord](#app-passwords) |Använd funktionen app lösenord för att aktivera ett program för att hoppa över Multifaktorautentisering och fortsätta arbeta. |
| [Kom ihåg Multifaktorautentisering för betrodda enheter och webbläsare](#remember-multi-factor-authentication-for-trusted-devices) |Använd denna funktion för att komma ihåg betrodda enheter och webbläsare för ett visst antal dagar efter att en användare har har loggat in med hjälp av Multifaktorautentisering. |
| [Valbar verifieringsmetoderna](#selectable-verification-methods) |Använd denna funktion för att välja listan med autentiseringsmetoder som användarna ska kunna använda. |

## <a name="block-and-unblock-users"></a>Blockera och avblockera användare

Använd den _blockera och avblockera användare_ funktion för att förhindra att användare tar emot begäranden om autentisering. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Användare är blockerade i 90 dagar från den tidpunkt då de blockeras.

### <a name="block-a-user"></a>Blockera en användare

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA-Server** > **blockera/avblockera användare**.
3. Välj **Lägg till** blockerar en användare.
4. Välj den **replikeringsgruppen**. Ange användarnamnet för blockerade användare som **användarnamn<span></span>@domain.com**. Ange en kommentar i den **orsak** fältet.
5. Välj **Lägg till** till slut att användaren blockeras.

### <a name="unblock-a-user"></a>Avblockera en användare

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA-Server** > **blockera/avblockera användare**.
3. Välj **avblockera** i den **åtgärd** kolumnen bredvid användaren att avblockera.
4. Ange en kommentar i den **skäl till avblockering** fältet.
5. Välj **avblockera** till slut att användaren avblockeras.

## <a name="fraud-alert"></a>Bedrägerivarning

Konfigurera den _bedrägerivarning_ funktion så att användarna kan rapportera bedrägliga försöker få åtkomst till sina resurser. Användare kan rapportera bedrägeri försök med hjälp av mobilappen eller via telefonen.

### <a name="turn-on-fraud-alerts"></a>Aktivera bedrägerivarningar

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA-Server** > **bedrägerivarning**.

   ![Aktivera bedrägerivarningar](./media/howto-mfa-mfasettings/fraudalert.png)

3. Ange den **Tillåt användare att skicka bedrägerivarningar** till **på**.
4. Välj **Spara**.

### <a name="configuration-options"></a>Konfigurationsalternativ

- **Blockera användare när bedrägeri rapporteras**: om en användare rapporterar bedrägeri, sitt konto blockeras i 90 dagar eller tills en administratör avblockeras sitt konto. En administratör kan granska inloggningar med hjälp av rapporten inloggning och vidta lämpliga åtgärder för att förhindra framtida bedrägeri. En administratör kan sedan [avblockera](#unblock-a-user) användarens konto.
- **Kod för att rapportera bedrägeri under inledande hälsning**: när användaren tar emot ett telefonsamtal för att utföra tvåstegsverifiering, de normalt trycker **#** bekräfta sina inloggning. Att rapportera bedrägeri användaren anger en kod innan du trycker på **#**. Den här koden är **0** som standard, men du kan anpassa den.

  >[!NOTE]
  >Standard-rösthälsning från Microsoft Instruera användarna att trycka på **0#** att skicka en bedrägeriavisering om. Om du vill använda en kod än **0**, registrera och överför din egen anpassade rösthälsning med lämpliga instruktioner för dina användare.
  >

### <a name="view-fraud-reports"></a>Visa bedrägeri rapporter

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > **inloggningar**. Bedrägeri rapporten är nu en del av standardrapport i Azure AD inloggningar.

## <a name="one-time-bypass"></a>Engångsförbikoppling

Den _engångsförbikoppling_ funktionen kan en användare autentiseras en gång utan att utföra tvåstegsverifiering. Förbikopplingen är tillfällig och upphör att gälla efter ett angivet antal sekunder. Du kan låta en engångsförbikoppling så att användaren kan komma åt en resurs i situationer där mobilappen eller phone inte tar emot ett meddelande eller telefonsamtal.

### <a name="create-a-one-time-bypass"></a>Skapa en engångsförbikoppling

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA-Server** > **engångsförbikoppling**.

   ![Skapa engångsförbikoppling](./media/howto-mfa-mfasettings/onetimebypass.png)

3. Välj **Lägg till**.
4. Om det behövs väljer du replikeringsgruppen för förbikopplingen.
5. Ange ett användarnamn som **användarnamn<span></span>@domain.com**. Ange antalet sekunder som förbikopplingen ska gälla. Ange en orsak till förbikopplingen. 
6. Välj **Lägg till**. Tidsgränsen träder i kraft omedelbart. Användaren måste logga in innan engångsförbikopplingen upphör att gälla. 

### <a name="view-the-one-time-bypass-report"></a>Visa rapporten engångsförbikoppling

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Bläddra till **Active Directory** > **MFA-Server** > **engångsförbikoppling**.

## <a name="custom-voice-messages"></a>Anpassade röstmeddelanden
Du kan använda dina egna inspelningar eller helg för tvåstegsverifiering med den _anpassade röstmeddelanden_ funktion. Dessa meddelanden kan användas i tillägg till eller ersätta Microsoft-registreringar.

Innan du börjar bör du vara medveten om följande begränsningar:

* Filformat som stöds är .wav eller .mp3.
* Maximal filstorlek är 5 MB.
* Autentiseringsmeddelanden måste vara kortare än 20 sekunder. Meddelanden som är längre än 20 sekunder kan orsaka verifieringen misslyckas. Användaren kan inte hantera innan meddelandet är klar och verifieringen timeout.

### <a name="set-up-a-custom-message"></a>Konfigurera ett anpassat meddelande

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA-Server** > **telefonsamtal inställningar**.

   ![Posten anpassade telefon meddelandena](./media/howto-mfa-mfasettings/phonecallsettings.png)

3. Välj **Lägg till hälsning**.
4. Välj vilken typ av hälsning. Välj språk.
5. Välj en .mp3 eller .wav fil att överföra.
6. Välj **Lägg till**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Cachelagring i Azure Multi-Factor Authentication

Du kan ange en tidsperiod för att tillåta autentiseringsförsök när en användare autentiseras med hjälp av den _cachelagring_ funktion. Efterföljande autentiseringsförsök för användare i den angivna lyckas tidsperioden automatiskt. Cachelagring används främst när lokalt system, t.ex VPN, skickar flera verifiering begäranden när den första begäranden pågår fortfarande. Den här funktionen kan efterföljande förfrågningar ska lyckas automatiskt när användaren lyckas första kontroll pågår. 

>[!NOTE]
>Funktionen cachelagring är inte avsedd att användas för inloggningar till Azure Active Directory (AD Azure).

### <a name="set-up-caching"></a>Konfigurera cachelagring 

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA-Server** > **cachelagring regler**.

   ![Konfigurera cachelagring regler](./media/howto-mfa-mfasettings/cachingrules.png)

3. Välj **Lägg till**.
4. Välj den **cachetyp** från den nedrullningsbara listan. Ange det maximala antalet **cachelagra sekunder**. 
5. Om det behövs, Välj typ av autentisering och ange ett program. 
6. Välj **Lägg till**.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Den _tillförlitliga IP-adresser_ funktion i Azure Multi-Factor Authentication används av administratörer för en hanterad eller federerade klient. Funktionen kringgår tvåstegsverifiering för användare som loggar in från intranätet. Funktionen är tillgänglig med den fullständiga versionen av Azure Multi-Factor Authentication och inte den kostnadsfria versionen för administratörer. Mer information om hur du hämtar den fullständiga versionen av Azure Multi-Factor Authentication finns [Azure Multi-Factor Authentication](multi-factor-authentication.md).

Om din organisation distribuerar NPS-tillägget för att ge MFA till lokala program anteckning visas alltid källans IP-adress ska autentiseringen försök flödar genom NPS-servern.

| Typ av Azure AD-klient | Betrodda Funktionsalternativ för IP-adresser |
|:--- |:--- |
| Hanterad |**Specifik IP-adresser**: administratörer ange ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från intranätet.|
| Federerad |**Alla federerade användare**: alla federerade användare som loggar in från inom organisationen kan kringgå tvåstegsverifiering. Användarna kringgå verifiering med hjälp av ett anspråk som utfärdas av Active Directory Federation Services (AD FS).<br/>**Specifik IP-adresser**: administratörer ange ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från intranätet. |

Betrodda IP-adresser kringgå fungerar bara i intranätet. Om du väljer den **alla federerade användare** alternativet och en användare loggar du in från utanför företagets intranät användaren måste autentiseras med hjälp av tvåstegsverifiering. Processen är densamma, även om användaren anger ett AD FS-anspråk. 

**Slutanvändarens upplevelse i corpnet**

När funktionen tillförlitliga IP-adresser är inaktiverad krävs tvåstegsverifiering för webbläsaren flöden. Applösenord krävs för äldre rich-klientprogram. 

När funktionen tillförlitliga IP-adresser aktiveras tvåstegsverifiering är *inte* krävs för webbläsaren flöden. Applösenord är *inte* krävs för äldre rich-klientprogram, förutsatt att användaren inte har skapat ett applösenord. När ett applösenord används fortfarande lösenord krävs. 

**Slutanvändarens upplevelse utanför corpnet**

Oavsett om funktionen tillförlitliga IP-adresser aktiveras krävs tvåstegsverifiering för webbläsaren flöden. Applösenord krävs för äldre rich-klientprogram. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Aktivera namngivna platser med hjälp av villkorlig åtkomst

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **villkorlig åtkomst** > **med namnet platser**.
3. Välj **ny plats**.
4. Ange ett namn för platsen.
5. Välj **Markera som betrodd plats**.
6. Ange IP-adressintervall i CIDR-notation som **192.168.1.1/24**.
7. Välj **Skapa**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Aktivera funktionen tillförlitliga IP-adresser med hjälp av villkorlig åtkomst

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **villkorlig åtkomst** > **med namnet platser**.
3. Välj **konfigurera MFA tillförlitliga IP-adresser**.
4. På den **tjänstinställningar** sidan under **tillförlitliga IP-adresser**, välja mellan följande två alternativ:
   
   * **För förfrågningar från externa användare som kommer från intranätet**: Välj det här alternativet genom att markera kryssrutan. Alla federerade användare logga in från företagsnätverket kringgå tvåstegsverifiering med hjälp av ett anspråk som utfärdas av AD FS. Kontrollera att AD FS har en regel för att lägga till intranätet anspråk till lämpliga trafik. Om regeln inte finns skapar du följande regel i AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **För begäranden från en viss mängd offentliga IP-adresser**: Välj det här alternativet genom att ange IP-adresser i textrutan med hjälp av CIDR-notering.
   
     * Använd notation som för IP-adresser som finns i intervallet xxx.xxx.xxx.1 via xxx.xxx.xxx.254 **xxx.xxx.xxx.0/24**.
     * För en IP-adress, använder du notation som **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgå tvåstegsverifiering.

5. Välj **Spara**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Aktivera funktionen tillförlitliga IP-adresser med hjälp av service-inställningar

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**.
4. Välj under Multi-Factor Authentication **tjänstinställningar**.
5. På den **tjänstinställningar** sidan under **tillförlitliga IP-adresser**, välja mellan följande två alternativ:
   
   * **För förfrågningar från externa användare i intranätet**: Välj det här alternativet genom att markera kryssrutan. Alla federerade användare logga in från företagsnätverket kringgå tvåstegsverifiering med hjälp av ett anspråk som utfärdas av AD FS. Kontrollera att AD FS har en regel för att lägga till intranätet anspråk till lämpliga trafik. Om regeln inte finns skapar du följande regel i AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **För begäranden från en angiven IP-adressundernät**: Välj det här alternativet genom att ange IP-adresser i textrutan med hjälp av CIDR-notering. 
     
     * Använd notation som för IP-adresser som finns i intervallet xxx.xxx.xxx.1 via xxx.xxx.xxx.254 **xxx.xxx.xxx.0/24**.
     * För en IP-adress, använder du notation som **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgå tvåstegsverifiering.

6. Välj **Spara**.

![Aktivera tillförlitliga IP-adresser med tjänstinställningar](./media/howto-mfa-mfasettings/trustedips3.png)

## <a name="app-passwords"></a>Applösenord

Vissa program, t.ex. Office 2010 eller tidigare och Apple Mail stöder inte tvåstegsverifiering. Apparna som inte är konfigurerade för att godkänna en andra verifiering. Om du vill använda dessa program utnyttja den _applösenord_ funktion. Du kan använda ett applösenord i stället för lösenordet traditionella så att en app kan kringgå tvåstegsverifiering och fortsätta arbeta.

Modern autentisering stöds för Microsoft Office 2013-klienter och senare. Office 2013-klienter inklusive Outlook, stöd för modern autentiseringsprotokoll och kan aktiveras för att fungera med tvåstegsverifiering. När klienten har aktiverats måste inte applösenord behövs för klienten.

### <a name="considerations-about-app-passwords"></a>Överväganden om applösenord

När du använder applösenord, Tänk på följande viktiga:

* Applösenord endast anges en gång per program. Användare behöver hålla reda på lösenord eller ange dem varje gång.
* Det faktiska lösenordet genereras automatiskt och tillhandahålls inte av användaren. Automatiskt genererade lösenordet är svårare att gissa och är säkrare.
* Det finns en gräns på 40 lösenord per användare. 
* Program som cachelagrar lösenord och använda dem i lokala scenarier kan starta misslyckas eftersom applösenordet inte är känd utanför arbets-eller skolkonto. Ett exempel på det här scenariot är Exchange e-postmeddelanden som finns lokalt, men den arkiverade e-posten är i molnet. I detta scenario fungerar inte samma lösenord.
* När Multifaktorautentisering har aktiverats på en användares konto, kan applösenord användas med de flesta icke-webbläsarklienter som Outlook och Microsoft Skype för företag. Administrativa åtgärder kan inte utföras med hjälp av applösenord med icke-webbläsarprogram, till exempel Windows PowerShell. Åtgärder kan inte utföras även om användaren har ett administratörskonto. Skapa ett tjänstkonto med ett starkt lösenord för att köra PowerShell-skript och aktivera inte kontot för tvåstegsverifiering.

>[!WARNING]
>Applösenord fungerar inte i hybridmiljöer där klienter kommunicera med både lokalt och molnet slutpunkter för automatisk upptäckt. Domänlösenord krävs för att autentisera lokalt. Applösenord krävs för att autentisera med molnet.
>

### <a name="guidance-for-app-password-names"></a>Riktlinjer för applösenord

Applösenord bör avspegla den enhet där de används. Om du har en bärbar dator som har icke-webbläsarprogram Outlook, Word och Excel kan du skapa ett applösenord med namnet **bärbara** för dessa appar. Skapa en annan applösenord med namnet **Desktop** för de program som körs på din dator. 

>[!NOTE]
>Vi rekommenderar att du skapar ett applösenord per enhet, i stället för ett applösenord per program.

### <a name="federated-or-single-sign-on-app-passwords"></a>Federerade eller enkel inloggning applösenord

Azure AD stöder federation eller enkel inloggning (SSO) med lokala Windows Server Active Directory Domain Services (AD DS). Om din organisation är federerat med Azure AD och du använder Azure Multi-Factor Authentication, Tänk på följande om applösenord.

>[!NOTE]
>Följande punkter gäller endast för federerade (SSO)-kunder.

* Applösenord verifieras av Azure AD, och därför kringgå federation. Federation används aktivt bara när du ställer in applösenord.
* Identitetsprovider (IdP) kontaktas inte för federerade användare (SSO), till skillnad från det passiva flödet. Applösenord lagras i arbets-eller skolkonto. Om en användare lämnar företaget, användarens information flödar till arbets-eller skolkonto med hjälp av **DirSync** i realtid. Inaktivering/borttagning av kontot kan ta upp till tre timmar att synkronisera, vilket kan fördröja inaktivering/borttagning av applösenord i Azure AD.
* Lokala inställningar för åtkomstkontroll hanteras inte av funktionen app lösenord.
* Ingen lokal autentisering loggning/granskning kapaciteten är tillgängliga för användning med funktionen app lösenord.
* Vissa avancerade arkitekturer kräver en kombination av autentiseringsuppgifter för tvåstegsverifiering med klienter. De här autentiseringsuppgifterna kan innehålla en arbetet eller skolan Kontoanvändarnamn och lösenord och applösenord. Kraven för beror på hur autentiseringen utförs. För klienter som autentiseras mot en lokal infrastruktur, en arbetet eller skolan användarnamn och lösenord för en obligatorisk. Ett applösenord krävs för klienter som autentiseras mot Azure AD.

  Anta att du har följande arkitektur:

  * Din lokala Active Directory-instans är federerat med Azure AD.
  * Du använder Exchange online.
  * Du måste använda Skype för företag lokalt.
  * Du använder Azure Multi-Factor Authentication.

  ![Använda applösenord i en federerad organisation](./media/howto-mfa-mfasettings/federated.png)

  I det här scenariot kan du använda följande autentiseringsuppgifter:

  * Använd ditt arbete eller skola Kontoanvändarnamn och lösenord för att logga in till Skype för företag.
  * Använda ett applösenord för att komma åt adressboken från ett Outlook-klienten som ansluter till Exchange online.

### <a name="allow-users-to-create-app-passwords"></a>Tillåt användare att skapa applösenord

Användare kan inte skapa applösenord som standard. Funktionen app lösenord måste aktiveras. Använd följande procedur för att ge användare möjlighet att skapa applösenord måste:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**.
4. Välj under Multi-Factor Authentication **tjänstinställningar**.
5. På den **tjänstinställningar** väljer den **låta användarna skapa applösenord för att logga in på icke-webbläsarprogram** alternativet.

   ![Tillåt användare att skapa applösenord](./media/howto-mfa-mfasettings/trustedips3.png)

### <a name="create-app-passwords"></a>Skapa applösenord

Användare kan skapa applösenord vid första registreringen. Användaren har du möjlighet att skapa applösenord i slutet av registreringsprocessen.

Användare kan också skapa applösenord efter registreringen. Applösenord kan ändras via inställningarna i Azure-portalen eller Office 365-portalen. Mer information och detaljerade anvisningar för dina användare finns [vad är applösenord i Azure Multi-Factor Authentication?](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Kom ihåg Multifaktorautentisering för betrodda enheter
Den _komma ihåg Multifaktorautentisering_ funktionen för enheter och webbläsare som är betrodda av användaren är en kostnadsfri för alla Multi-Factor Authentication-användare. Användarna kan kringgå efterkontroll för ett angivet antal dagar efter att de har har inloggad till en enhet med hjälp av Multifaktorautentisering. Funktionen förbättrar användbarheten genom att minimera antalet gånger som en användare har att utföra tvåstegsverifiering på samma enhet.

>[!IMPORTANT]
>Om ett konto eller en enhet har komprometterats bör kan komma ihåg Multifaktorautentisering för betrodda enheter påverka säkerheten. Om ett företagskonto har komprometterats eller en betrodd enhet tappas bort eller blir stulen, bör du [Återställ Multifaktorautentisering på alla enheter](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>Åtgärden Återställ återkallar betrodda status från alla enheter och användaren krävs för att utföra tvåstegsverifiering igen. Du kan också instruera användarna att återställa Multi-Factor Authentication på sina egna enheter med instruktionerna i [hantera inställningar för tvåstegsverifiering](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Hur funktionen fungerar

Funktionen Kom ihåg Multifaktorautentisering anger en beständig cookie i webbläsaren när en användare väljer den **fråga inte igen efter X dagar** alternativet vid inloggning. Användaren är inte tillfrågas igen för Multifaktorautentisering från samma webbläsaren tills cookien förfaller. Om användaren startar en annan webbläsare på samma enhet eller rensar sina cookies, uppmanas de igen att verifiera. 

Den **fråga inte igen efter X dagar** alternativet inte visas i icke-webbläsarprogram, oavsett om appen har stöd för modern autentisering. Använd de här apparna _uppdateringstoken_ som ger nya åtkomsttoken varje timme. När en uppdateringstoken verifieras kontrollerar Azure AD att senaste tvåstegsverifiering inträffat inom det angivna antalet dagar. 

Funktionen minskar antalet autentiseringar i webbprogram som normalt fråga varje gång. Funktionen ökar antalet autentiseringar för modern autentiseringsklienter som normalt fråga efter 90 dagar.

>[!IMPORTANT]
>Den **komma ihåg Multifaktorautentisering** funktionen är inte kompatibel med den **Håll mig inloggad** funktion i AD FS, när användare utför tvåstegsverifiering för AD FS via Azure Multi-Factor Authentication-servern eller en lösning från tredje part multifaktorautentisering.
>
>Om användarna väljer **Håll mig inloggad** på AD FS och även markera sin enhet som är betrodd för multi-Factor Authentication kan användaren inte automatiskt verifieras efter den **komma ihåg multifaktorautentisering**antal dagar upphör att gälla. Azure AD begär en ny tvåstegsverifiering, men AD FS returnerar en token med den ursprungliga Multifaktorautentisering anspråk och datum, istället för att genomföra tvåstegsverifiering igen. Den här reaktion anger av en kontroll skapas mellan Azure AD och AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Aktivera komma ihåg Multifaktorautentisering

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**.
4. Välj under Multi-Factor Authentication **tjänstinställningar**.
5. På den **tjänstinställningar** sidan under **hantera komma ihåg multifaktorautentisering**, Välj den **Tillåt användare att komma ihåg multifaktorautentisering på enheter de litar på**alternativet.

   ![Kom ihåg Multifaktorautentisering för betrodda enheter](./media/howto-mfa-mfasettings/remember.png)

6. Ange antalet dagar för att tillåta betrodda enheter att kringgå tvåstegsverifiering. Standardvärdet är 14 dagar.
7. Välj **Spara**.

### <a name="mark-a-device-as-trusted"></a>Markera en enhet som betrodd

När du har aktiverat funktionen Kom ihåg Multifaktorautentisering användare kan markera en enhet som betrodda när de loggar in genom att välja **fråga inte igen**.

![Välj ”fråga inte igen” för betrodda enheter](./media/howto-mfa-mfasettings/trusted.png)

## <a name="selectable-verification-methods"></a>Valbar verifieringsmetoderna

Du kan välja verifieringsmetoderna som är tillgängliga för användarna med hjälp av den _valbar verifieringsmetoderna_ funktion. Följande tabell innehåller en kort översikt över metoderna.

När användarna registrerar sina konton för Multifaktorautentisering i Azure, kan de välja sin önskade verifieringsmetod från de alternativ som du har aktiverat. Vägledning för användaren registreringen finns i [Konfigurera mitt konto för tvåstegsverifiering](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md).

| Metod | Beskrivning |
|:--- |:--- |
| Samtal till telefon |Placerar en automatiserad röstsamtal. Användaren svara och trycka på #-tangenten för att autentisera. Telefonnumret har inte synkroniserats till lokala Active Directory. |
| Textmeddelande till telefon |Skickar ett SMS med en Verifieringskod. Användaren uppmanas att ange verifieringskoden i inloggning-gränssnittet. Den här processen kallas enkelriktad SMS. Dubbelriktad SMS innebär att användaren måste text tillbaka en viss kod. Dubbelriktad SMS inaktuella och stöds inte efter 14 November 2018. Användare som har konfigurerats för dubbelriktad SMS växlar automatiskt till _anrop till phone_ verifiering vid den tiden.|
| Meddelande via mobilapp |Skickar ett push-meddelande till din telefon eller en registrerad enhet. Användaren ser meddelandet och väljer **Kontrollera** att slutföra verifieringen. Microsoft Authenticator-appen är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), och [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Verifieringskod från mobilapp |Microsoft Authenticator-appen genererar en ny OATH-Verifieringskod var 30: e sekund. Användaren anger verifieringskoden i gränssnittet för inloggning. Microsoft Authenticator-appen är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), och [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Aktivera och inaktivera verifieringsmetoderna

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**.
4. Välj under Multi-Factor Authentication **tjänstinställningar**.
5. På den **tjänstinställningar** sidan under **verifieringsalternativ**, Markera/avmarkera metoder för att ge till användarna.

   ![Välj verifieringsmetoderna](./media/howto-mfa-mfasettings/authmethods.png)

6. Klicka på **Spara**.
