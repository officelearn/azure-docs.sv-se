---
title: Konfigurera Azure Multi-Factor Authentication
description: Den här artikeln beskrivs hur du konfigurerar Azure Multi-Factor Authentication-inställningar i Azure portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee6c6559b5dd6c6a6e357ff2f258bad79d57205c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192650"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurera Azure Multi-Factor Authentication-inställningar

Den här artikeln hjälper dig att hantera inställningar för multi-Factor Authentication i Azure-portalen. Det tar upp olika ämnen som hjälper dig att få ut det mesta av Azure Multi-Factor Authentication. Inte alla funktioner är tillgängliga i varje [versionen av Azure Multi-Factor Authentication](concept-mfa-whichversion.md#what-features-do-i-need).

Du kan komma åt inställningar som rör Multi-Factor Authentication i Azure Portal genom att bläddra till **Azure Active Directory** > **MFA**.

![Azure portal – Azure AD Multi-Factor Authentication-inställningar](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Inställningar

Några av de här inställningarna gäller för MFA-servern eller den Azure MFA.

| Funktion | Beskrivning |
| ------- | ----------- |
| Kontoutelåsning | Tillfälligt låsa konton i Multi-Factor authentication-tjänsten om det finns för många nekade autentiseringsförsök i följd. Den här funktionen gäller enbart för användare som anger en PIN-kod för att autentisera. (MFA Server) |
| [Blockera/avblockera användare](#block-and-unblock-users) | För att blockera specifika användare på MFA Server (lokalt) från att kunna ta emot Multi-Factor Authentication-begäranden. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Användare att vara blockerad i 90 dagar från den tidpunkt då de är blockerade. |
| [Bedrägerivarning](#fraud-alert) | Konfigurera inställningar som rör användare möjlighet att rapportera bedrägliga begäranden från MFA-servern. |
| Meddelanden | Aktivera meddelanden av händelser från MFA-servern. |
| [OATH-token](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Används i molnbaserade miljöer för Azure MFA för att hantera OATH-token för användare. |
| [Inställningarna för telefonsamtal](#phone-call-settings) | Konfigurera inställningar för telefonsamtal och hälsningar för molnet och lokala miljöer. |
| Leverantörer | Det här alternativet visas alla befintliga autentiseringsproviders att du kanske har associerat med ditt konto. Nya autentiseringsproviders kan inte skapas från och med 1 September 2018 |

## <a name="manage-mfa-server"></a>Hantera MFA Server

Inställningar i det här avsnittet gäller endast för MFA-servern.

| Funktion | Beskrivning |
| ------- | ----------- |
| Serverinställningar | Ladda ned MFA-servern och skapa autentiseringsuppgifter för aktivering för att initiera miljön |
| [Engångsförbikoppling](#one-time-bypass) | Tillåt en användare att autentisera utan att utföra tvåstegsverifiering under en begränsad tid. |
| [Cachelagringsregler](#caching-rules) |  Cachelagring är främst används när den lokala system, t.ex VPN, skickar flera begäranden när den första begäran pågår fortfarande. Den här funktionen kan de efterföljande förfrågningar ska lyckas automatiskt när användaren lyckas första verifiering pågår. |
| Serverstatus | Se status för dina lokala MFA-servrar, inklusive version, status, IP-adress och senaste kommunikation tid och datum. |

## <a name="activity-report"></a>Aktivitetsrapport

Reporting tillgänglig här är specifik för MFA Server (lokalt). Azure MFA (moln) rapporter finns i rapporten inloggningar i Azure AD.

## <a name="block-and-unblock-users"></a>Blockera och avblockera användare

Använd den _blockera och avblockera användare_ funktionen för att förhindra att användare tar emot begäranden om autentisering. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Användare att vara blockerad i 90 dagar från den tidpunkt då de är blockerade. Den här funktionen är specifik för MFA Server (lokalt).

### <a name="block-a-user"></a>Blockera en användare

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** > **blockera/avblockera användare**.
3. Välj **Lägg till** blockerar en användare.
4. Välj den **replikeringsgruppen**. Ange användarnamnet för blockerade användare som **username@domain.com**. Ange en kommentar i den **orsak** fält.
5. Välj **Lägg till** att slutföra att användaren blockeras.

### <a name="unblock-a-user"></a>Avblockera en användare

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** > **blockera/avblockera användare**.
3. Välj **avblockera** i den **åtgärd** kolumnen bredvid användaren att avblockera.
4. Ange en kommentar i den **skäl till avblockeringen** fält.
5. Välj **avblockera** att slutföra att användaren avblockeras.

## <a name="fraud-alert"></a>Bedrägerivarning

Konfigurera den _bedrägerivarning_ funktion så att dina användare kan rapportera bedrägliga försöker komma åt sina resurser. Användare kan rapportera bedrägeri försök med hjälp av mobilappen eller via telefonen. Den här funktionen är specifik för MFA Server (lokalt).

### <a name="turn-on-fraud-alerts"></a>Aktivera bedrägerivarningar

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** > **bedrägerivarning**.
3. Ange den **Tillåt användare att skicka bedrägerivarningar** att ställa in **på**.
4. Välj **Spara**.

### <a name="configuration-options"></a>Konfigurationsalternativ

* **Blockera användare när bedrägeri rapporteras**: Om en användare rapporterar bedrägerier, är deras konto blockerad i 90 dagar eller tills en administratör häver blockeringen för sitt konto. En administratör kan granska inloggningar med hjälp av rapporten inloggning och vidta lämpliga åtgärder för att förhindra framtida bedrägerier. En administratör kan sedan [avblockera](#unblock-a-user) användarens konto.
* **Kod för att rapportera bedrägeri under inledande hälsning**: När användarna får ett telefonsamtal för tvåstegsverifiering, de normalt trycker du på **#** att bekräfta deras inloggning. Att rapportera bedrägeri användaren anger en kod innan du trycker på **#**. Den här koden är **0** som standard, men du kan anpassa den.

   >[!NOTE]
   >Standard röstmeddelanden från Microsoft Instruera användarna att trycka på **0#** att skicka ett bedrägeriförsök. Om du vill använda en kod än **0**, registrera och ladda upp dina egna anpassade röstmeddelanden med rätt anvisningar för dina användare.
   >

### <a name="view-fraud-reports"></a>Visa bedrägeri rapporter

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > **inloggningar**. Bedrägeri rapporten är nu en del av standardrapporterna för Azure AD-inloggningar.

## <a name="phone-call-settings"></a>Inställningarna för telefonsamtal

### <a name="caller-id"></a>Nummerpresentation

**MFA-Uppringarens ID-nummer** -detta är antalet användarna ser på sin telefon. Endast amerikanska tal tillåts.

>[!NOTE]
>När Multi-Factor Authentication-samtal görs via det offentliga telefon-nätverket, skickas ibland de genom en operatör som inte stöder anroparen-ID. Därför garanteras inte Uppringarens ID, även om Multi-Factor Authentication-systemet skickar alltid.

### <a name="custom-voice-messages"></a>Anpassade röstmeddelanden

Du kan använda din egen inspelningar eller hälsningar för tvåstegsverifiering med den _anpassade röstmeddelanden_ funktionen. Dessa meddelanden kan användas i tillägg till eller ersätta Microsoft inspelningar.

Innan du börjar måste du vara medveten om följande begränsningar:

* Filformat som stöds är .wav eller .mp3.
* Gränsen för filstorlek är 5 MB.
* Autentiseringsmeddelanden bör vara kortare än 20 sekunder. Meddelanden som är längre än 20 sekunder kan orsaka verifieringen att misslyckas. Användaren kan inte hantera innan meddelandet är klar och tidsgränsen uppnås för verifiering.

### <a name="custom-message-language-behavior"></a>Anpassat meddelande språk beteende

När ett anpassat meddelande spelas för användaren, beror språket för meddelandet på faktorer:

* Den aktuella användaren språk.
  * Det språket som identifierats av användarens webbläsare.
  * Andra autentiseringsscenarier fungera annorlunda.
* Språket i tillgängliga anpassade meddelanden.
  * Det här språket väljs av administratören, när du lägger till ett anpassat meddelande.

Exempel: om det finns bara ett anpassat meddelande med tyska som systemspråk:

* En användare som autentiserar tyska hör anpassade tyska meddelandet.
* En användare som autentiseras på engelska hör meddelandet engelska.

### <a name="set-up-a-custom-message"></a>Konfigurera ett anpassat meddelande

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
1. Bläddra till **Azure Active Directory** > **MFA** > **telefonsamtalsinställningarna**.
1. Välj **Lägg till hälsning**.
1. Välj typ av hälsning.
1. Välj språket.
1. Välj en .mp3 eller .wav fil att ladda upp.
1. Välj **Lägg till**.

## <a name="one-time-bypass"></a>Engångsförbikoppling

Den _engångsförbikoppling_ funktionen låter en användare autentiseras en gång utan att genomföra någon tvåstegsverifiering. Förbikopplingen är tillfällig och upphör att gälla efter ett angivet antal sekunder. I situationer där i mobilappen eller telefon inte tar emot ett meddelande eller telefonsamtal kan tillåta du en engångsförbikoppling så att användaren kan komma åt en resurs.

### <a name="create-a-one-time-bypass"></a>Skapa en engångsförbikoppling

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** > **engångsförbikoppling**.
3. Välj **Lägg till**.
4. Om det behövs väljer du gruppen för ögonblicksbildreplikering för förbikopplingen.
5. Ange ett användarnamn som **username@domain.com**. Ange hur många sekunder som förbikopplingen ska. Ange en orsak till förbikopplingen.
6. Välj **Lägg till**. Tidsgränsen börjar gälla omedelbart. Användaren måste logga in innan engångsförbikopplingen upphör att gälla.

### <a name="view-the-one-time-bypass-report"></a>Visa rapporten engångsförbikoppling

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Bläddra till **Azure Active Directory** > **MFA** > **engångsförbikoppling**.

## <a name="caching-rules"></a>Cachelagringsregler

Du kan ange en tidsperiod för att tillåta autentiseringsförsök när en användare autentiseras med hjälp av den _cachelagring_ funktionen. Efterföljande autentiseringsförsök för användaren inom den angivna lyckas tidsperioden automatiskt. Cachelagring är främst används när den lokala system, t.ex VPN, skickar flera begäranden när den första begäran pågår fortfarande. Den här funktionen kan de efterföljande förfrågningar ska lyckas automatiskt när användaren lyckas första verifiering pågår.

>[!NOTE]
>Funktionen cachelagring är inte avsedd att användas för inloggningar till Azure Active Directory (AD Azure).

### <a name="set-up-caching"></a>Konfigurera cachelagring

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** > **Cachelagringsregler**.
3. Välj **Lägg till**.
4. Välj den **cachetyp** från den nedrullningsbara listan. Ange det maximala antalet **cachelagra sekunder**.
5. Om det behövs, Välj en autentiseringstyp och ange ett program.
6. Välj **Lägg till**.

## <a name="mfa-service-settings"></a>MFA-tjänstinställningar

Inställningar för lösenord, betrodda IP-adresser, alternativ för verifiering och Kom ihåg Multi-Factor authentication för Azure Multi-Factor Authentication kan hittas i inställningarna för tjänsten. Inställningar för tjänsten kan nås från Azure portal genom att bläddra till **Azure Active Directory** > **MFA** > **komma igång**  >  **Konfigurera** > **ytterligare inställningar för molnbaserad MFA**.

![Inställningar för Azure Multi-Factor Authentication-tjänsten](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>Applösenord

Vissa program, t.ex. Office 2010 eller tidigare och Apple Mail innan iOS 11 stöder inte tvåstegsverifiering. Apparna som inte är konfigurerade för att godkänna en andra verifiering. Om du vill använda dessa program kan du utnyttja den _applösenord_ funktionen. Du kan använda ett applösenord i stället för dina traditionella lösenord för att tillåta en app kan kringgå tvåstegsverifiering och fortsätta arbeta.

Modern autentisering stöds för Microsoft Office 2013-klienter och senare. Office 2013-klienter, däribland Outlook, stöd för modern autentiseringsprotokoll och kan aktiveras för att fungera med tvåstegsverifiering. När klienten har aktiverats kan inte applösenord behövs för klienten.

>[!NOTE]
>Applösenord fungerar inte med principer för villkorlig åtkomst baserat multifaktorautentisering och modern autentisering.

### <a name="considerations-about-app-passwords"></a>Överväganden om applösenord

När du använder lösenord, Tänk på följande viktiga punkter:

* Applösenord endast anges en gång per program. Användare behöver hålla reda på lösenord eller ange dem varje gång.
* Det faktiska lösenordet genereras automatiskt och tillhandahålls inte av användaren. Det automatiskt genererade lösenordet är svårare för angripare att gissa och är säkrare.
* Det finns en gräns på 40 lösenord per användare.
* Program som cachelagrar lösenord och använda dem i lokala scenarier kan börja att misslyckas eftersom applösenordet inte är känd utanför arbets-eller skolkonto. Ett exempel på det här scenariot är Exchange e-postmeddelanden som finns på plats, men den arkiverade e-posten är i molnet. I det här scenariot fungerar inte samma lösenord.
* När Multifaktorautentisering har aktiverats på en användares konto kan applösenord användas med de flesta icke-webbläsarklienter som Outlook och Microsoft Skype för företag. Administrativa åtgärder kan inte utföras med hjälp av applösenord med icke-webbläsarprogram, till exempel Windows PowerShell. Att går inte utföra åtgärderna som även om användaren har ett administrativt konto. Skapa ett tjänstkonto med ett starkt lösenord för att köra PowerShell-skript, och inte aktivera konto för tvåstegsverifiering.

>[!WARNING]
>Applösenord fungerar inte i hybridmiljöer där klienter kommunicera med både lokalt och molnet automatiskt upptäcka slutpunkter. Domänlösenord måste autentiseras lokalt. Applösenord krävs för att autentisera med molnet.

### <a name="guidance-for-app-password-names"></a>Riktlinjer för lösenord appnamn

Appnamn lösenord bör avspegla den enhet där de används. Om du har en bärbar dator med icke-webbläsarbaserade program som Outlook, Word och Excel kan du skapa ett applösenord med namnet **bärbara** för dessa appar. Skapa ett nytt applösenord med namnet **Desktop** för de program som körs på din dator.

>[!NOTE]
>Vi rekommenderar att du skapar ett applösenord per enhet i stället för ett applösenord per program.

### <a name="federated-or-single-sign-on-app-passwords"></a>Federerade eller enkel inloggning för applösenord

Azure AD stöder federation eller enkel inloggning (SSO) med den lokala Windows Server Active Directory Domain Services (AD DS). Om din organisation är federerad med Azure AD och du använder Azure Multi-Factor Authentication, Tänk på följande om applösenord.

>[!NOTE]
>Följande punkter gäller endast för federerad (SSO)-kunder.

* Applösenord verifieras av Azure AD och kringgår därför federation. Federation används aktivt endast när du konfigurerar applösenord.
* Identitetsprovider (IdP) kontaktas inte för federerade användare (SSO), till skillnad från det passiva flödet. Applösenord lagras i arbets-eller skolkonto. Om en användare lämnar företaget, användarens information flödar till arbets-eller skolkonto genom att använda **DirSync** i realtid. Inaktiveringen eller borttagningen av kontot kan ta upp till tre timmar att synkronisera, vilket kan försena inaktiveringen eller borttagningen av applösenordet i Azure AD.
* Lokala inställningar för åtkomstkontroll hanteras inte av funktionen app-lösenord.
* Ingen lokal autentisering loggning/granskningsfunktionen är tillgängliga för användning med funktionen app-lösenord.
* Vissa avancerade arkitekturer kräver en kombination av autentiseringsuppgifter för tvåstegsverifiering med klienter. Dessa autentiseringsuppgifter kan innehålla ett arbets eller skolans Kontoanvändarnamn och lösenord och lösenord. Kraven beror på hur autentiseringen utförs. För klienter som autentiseras mot en lokal infrastruktur, kontot en skola eller arbete användarnamn och lösenord för en nödvändig. Ett applösenord krävs för klienter som autentiseras mot Azure AD.

  Anta exempelvis att du har följande arkitektur:

  * Din lokala instans av Active Directory är federerad med Azure AD.
  * Du använder Exchange online.
  * Du använder Skype för företag lokalt.
  * Du använder Azure Multi-Factor Authentication.

  I det här scenariot använder du följande autentiseringsuppgifter:

  * Använd ditt arbete eller skola Kontoanvändarnamn och lösenord för att logga in till Skype för företag.
  * Använda ett applösenord för att komma åt adressboken från en Outlook-klient som ansluter till Exchange online.

### <a name="allow-users-to-create-app-passwords"></a>Tillåt användare att skapa applösenord

Som standard det går inte att användare skapar applösenord. Funktionen app lösenord måste aktiveras. Använd följande procedur för att ge användarna möjlighet att skapa applösenord:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**.
4. Under Multi-Factor Authentication, väljer **tjänstinställningar**.
5. På den **tjänstinställningar** väljer den **Tillåt användare att skapa applösenord för att logga in på icke-webbläsarbaserade appar** alternativet.

### <a name="create-app-passwords"></a>Skapa applösenord

Användare kan skapa applösenord under sin första registreringen. Användaren har alternativet för att skapa applösenord i slutet av registreringsprocessen.

Användarna kan också skapa applösenord efter registreringen. Applösenord kan ändras via inställningarna i Azure portal eller Office 365-portalen. Mer information och detaljerade anvisningar för dina användare finns i [vad är applösenord i Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Den _tillförlitliga IP-adresser_ funktion i Azure Multi-Factor Authentication används av administratörer för en hanterad eller federerade klient. Funktionen kringgår tvåstegsverifiering för användare som loggar in från intranätet. Funktionen är tillgänglig med den fullständiga versionen av Azure Multi-Factor Authentication och inte den kostnadsfria versionen för administratörer. Mer information om hur du hämtar den fullständiga versionen av Azure Multi-Factor Authentication finns [Azure Multi-Factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> Det betrodda MFA IP-adresser och villkorlig åtkomst – namngivna platser fungerar endast med IPV4-adresser.

Om din organisation distribuerar NPS-tillägget för att ge MFA till lokala program anteckning visas alltid källans IP-adress till att autentiseringen flödar genom att försöka NPS-servern.

| Azure AD tenant-typ | Betrodda IP-adresser Funktionsalternativ |
|:--- |:--- |
| Hanterad |**Specifika IP-adressintervall**: Administratörer ange ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från intranätet.|
| Federerad |**Alla federerade användare**: Alla externa användare som loggar in därifrån inom organisationen kan kringgå tvåstegsverifiering. Användarna kringgå verifiering med hjälp av ett anspråk som utfärdas av Active Directory Federation Services (AD FS).<br/>**Specifika IP-adressintervall**: Administratörer ange ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från intranätet. |

Tillförlitliga IP-adresser kringgå fungerar endast från i intranätet. Om du väljer den **alla federerade användare** alternativet och en användare loggar du in från utanför företagets intranät som användaren måste autentiseras med hjälp av tvåstegsverifiering. Processen är samma även om användaren anger ett AD FS-anspråk. 

### <a name="end-user-experience-inside-of-corpnet"></a>Slutanvändarens upplevelse i corpnet

När funktionen tillförlitliga IP-adresser är inaktiverad, krävs tvåstegsverifiering för webbläsaren flöden. Applösenord krävs för äldre rich-klientprogram.

När funktionen tillförlitliga IP-adresser aktiveras tvåstegsverifiering är *inte* krävs för webbläsaren flöden. Applösenord är *inte* krävs för äldre rich-klientprogram, förutsatt att användaren inte har skapat ett applösenord. När ett applösenord används fortfarande lösenordet krävs. 

### <a name="end-user-experience-outside-corpnet"></a>Slutanvändarens upplevelse utanför corpnet

Oavsett om funktionen tillförlitliga IP-adresser är aktiverat, krävs tvåstegsverifiering för webbläsaren flöden. Applösenord krävs för äldre rich-klientprogram.

### <a name="enable-named-locations-by-using-conditional-access"></a>Aktivera namngivna platser med hjälp av villkorlig åtkomst

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **villkorlig åtkomst** > **namngivna platser**.
3. Välj **ny plats**.
4. Ange ett namn för platsen.
5. Välj **Markera som betrodd plats**.
6. Ange IP-adressintervall i CIDR-notation som **192.168.1.1/24**.
7. Välj **Skapa**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Aktivera funktionen tillförlitliga IP-adresser med hjälp av villkorlig åtkomst

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **villkorlig åtkomst** > **namngivna platser**.
3. Välj **konfigurera betrodda MFA IP-adresser**.
4. På den **tjänstinställningar** sidan under **tillförlitliga IP-adresser**, Välj från någon av följande två alternativ:

   * **För förfrågningar från federerade användare som kommer från mitt intranät**: Markera kryssrutan för att välja det här alternativet. Alla externa användare som loggar in från företagsnätverket kringgå tvåstegsverifiering med hjälp av ett anspråk som utfärdas av AD FS. Kontrollera att AD FS har en regel för att lägga till intranätet anspråk på lämpligt trafik. Om regeln inte finns, kan du skapa följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **För förfrågningar från en viss mängd offentliga IP-adresser**: Välj det här alternativet genom att ange IP-adresser i textrutan med hjälp av CIDR-notation.
      * För IP-adresser som är i intervallet xxx.xxx.xxx.1 via xxx.xxx.xxx.254, Använd notering som **xxx.xxx.xxx.0/24**.
      * För en enskild IP-adress, använder du notation som **xxx.xxx.xxx.xxx/32**.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgå tvåstegsverifiering.

5. Välj **Spara**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Aktivera funktionen tillförlitliga IP-adresser med hjälp av inställningarna för tjänsten

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare**.
3. Välj **Multifaktorautentisering**.
4. Under Multi-Factor Authentication, väljer **tjänstinställningar**.
5. På den **tjänstinställningar** sidan under **tillförlitliga IP-adresser**, väljer du en (eller båda) av följande två alternativ:

   * **För förfrågningar från federerade användare från mitt intranät**: Markera kryssrutan för att välja det här alternativet. Alla externa användare som loggar in från företagsnätverket kringgå tvåstegsverifiering med hjälp av ett anspråk som utfärdas av AD FS. Kontrollera att AD FS har en regel för att lägga till intranätet anspråk på lämpligt trafik. Om regeln inte finns, kan du skapa följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **För förfrågningar från ett angivet intervall med IP-adressundernät**: Välj det här alternativet genom att ange IP-adresser i textrutan med hjälp av CIDR-notation.
      * För IP-adresser som är i intervallet xxx.xxx.xxx.1 via xxx.xxx.xxx.254, Använd notering som **xxx.xxx.xxx.0/24**.
      * För en enskild IP-adress, använder du notation som **xxx.xxx.xxx.xxx/32**.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgå tvåstegsverifiering.

6. Välj **Spara**.

## <a name="verification-methods"></a>Verifieringsmetoder

Du kan välja verifieringsmetoderna som är tillgängliga för användarna. Följande tabell innehåller en kort översikt över metoderna.

När användarna registrerar sina konton för Azure Multi-Factor Authentication kan välja de sitt prioriterad verifieringsmetod från de alternativ som du har aktiverat. Vägledning för användaren registreringen finns i [konfigurerar mitt konto för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metod | Beskrivning |
|:--- |:--- |
| Samtal till telefon |Placerar rings upp automatiskt. Användaren svara och trycka på #-tangenten för att autentisera. Telefonnumret synkroniseras inte till den lokala Active Directory. |
| Textmeddelande till telefon |Skickar ett textmeddelande som innehåller en Verifieringskod. Användaren uppmanas att ange verifieringskoden i inloggningsgränssnittet. Den här processen kallas envägs-SMS. Dubbelriktad SMS innebär att användaren måste text tillbaka en viss kod. Dubbelriktad SMS är föråldrade och stöds inte efter den 14 November 2018. Användare som är konfigurerade för dubbelriktad SMS automatiskt växlas till _samtal till telefon_ verifiering vid den tidpunkten.|
| Meddelande via mobilapp |Skickar ett push-meddelande till din telefon eller en registrerad enhet. Användaren visar meddelandet och väljer **Kontrollera** att slutföra verifieringen. Microsoft Authenticator-appen är tillgänglig för [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072), och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Verifieringskod från mobilapp eller maskinvarutoken |Microsoft Authenticator-appen genererar en ny OATH-Verifieringskod med 30 sekunders mellanrum. Användaren anger verifieringskoden i i inloggningsgränssnittet. Microsoft Authenticator-appen är tillgänglig för [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072), och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Aktivera och inaktivera verifieringsmetoder

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**.
4. Under Multi-Factor Authentication, väljer **tjänstinställningar**.
5. På den **tjänstinställningar** sidan under **verifieringsalternativ**, Markera/avmarkera metoder för att förse dina användare.
6. Klicka på **Spara**.

Mer information om användning av autentiseringsmetoder finns i artikeln [vad är autentiseringsmetoder](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Kom ihåg Multi-Factor Authentication

Den _komma ihåg Multifaktorautentisering_ funktionen för enheter och webbläsare som är betrodda av den här användaren är en kostnadsfri funktion för alla Multi-Factor Authentication-användare. Användarna kan kringgå efterkontroll för ett angivet antal dagar efter att de har har loggat in till en enhet med hjälp av Multi-Factor Authentication. Funktionen förbättrar användbarheten genom att minimera antalet gånger som en användare har att utföra tvåstegsverifiering på samma enhet.

>[!IMPORTANT]
>Om ett konto eller en enhet har komprometterats, kan det påverka säkerhet att komma ihåg Multifaktorautentisering för betrodda enheter. Om ett företagskonto har komprometterats eller en betrodd enhet blir borttappad eller stulen, bör du [Återställ Multifaktorautentisering på alla enheter](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>Återställningsåtgärden återkallar betrodda status från alla enheter och användaren måste utföra tvåstegsverifiering igen. Du kan även Instruera användarna att återställa Multi-Factor Authentication på sina egna enheter med instruktionerna i [hantera dina inställningar för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Hur funktionen fungerar

Kom ihåg Multi-Factor Authentication-funktionen anger en beständig cookie för webbläsaren när en användare väljer den **fråga inte igen efter X dagar** alternativet när du loggar in. Användaren inte ska uppmanas igen Multifaktorautentisering från den samma webbläsaren tills cookien upphör att gälla. Om användaren öppnar en annan webbläsare på samma enhet eller tar bort sina cookies, uppmanas de igen att verifiera.

Den **fråga inte igen efter X dagar** alternativet visas inte på icke-webbläsarbaserade program, oavsett om appen stöder modern autentisering. De här apparna använda _uppdateringstoken_ som ger nya åtkomsttoken varje timme. När en uppdateringstoken har verifierats kan Azure AD som kontrollerar att den senaste tvåstegsverifieringen inträffade inom det angivna antalet dagar.

Funktionen minskar antalet autentiseringar i web apps, som normalt att efterfråga varje gång. Funktionen ökar antalet autentiseringar för modern autentiseringsklienter som normalt frågar efter 90 dagar. Kan också öka antalet autentiseringar i kombination med principer för villkorlig åtkomst.

>[!IMPORTANT]
>Den **komma ihåg Multifaktorautentisering** funktionen är inte kompatibel med den **vill förbli inloggad** funktion i AD FS, när användare utför tvåstegsverifiering för AD FS via Azure Multi-Factor Authentication-servern eller en lösning från tredje part för multifaktorautentisering.
>
>Om användarna väljer **vill förbli inloggad** på AD FS och även markera enheten som betrodda för multi-Factor Authentication kan användaren inte automatiskt verifieras efter den **komma ihåg multifaktorautentisering**antalet dagar upphör att gälla. Azure AD begär en ny tvåstegsverifiering och AD FS returnerar en token med den ursprungliga Multi-Factor Authentication-anspråk och datum i stället utföra tvåstegsverifiering igen. **Den här reaktion anger av en verifiering loop mellan Azure AD och AD FS.**
>

### <a name="enable-remember-multi-factor-authentication"></a>Aktivera komma ihåg Multifaktorautentisering

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster, Välj **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multifaktorautentisering**.
4. Under Multi-Factor Authentication, väljer **tjänstinställningar**.
5. På den **tjänstinställningar** sidan **hantera komma ihåg multifaktorautentisering**väljer den **Tillåt användare att komma ihåg multifaktorautentisering på enheter de litar på**alternativet.
6. Ange antalet dagar för att tillåta betrodda enheter att kringgå tvåstegsverifiering. Standardvärdet är 14 dagar.
7. Välj **Spara**.

### <a name="mark-a-device-as-trusted"></a>Markera en enhet är betrodda

När du har aktiverat funktionen Kom ihåg Multi-Factor Authentication användarna kan markera en enhet som betrodda när de loggar in genom att välja **fråga inte igen**.

## <a name="next-steps"></a>Nästa steg

[Ändra Azure AD-inloggningssida anpassning](../fundamentals/customize-branding.md)
