---
title: Konfigurera Azure Multi Factor-autentisering – Azure Active Directory
description: I den hÃ¤r artikeln beskrivs sÃ¤nder du instÃ¤llning av Azure Multi-Factor Authentication-in settings in the Azure portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 077032e4fe3886d5bf9a678dffdffca1a5802091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263808"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurera inställningar för Azure Multi-Factor Authentication

Den här artikeln hjälper dig att hantera multifaktorautentiseringsinställningar i Azure-portalen. Den innehåller olika avsnitt som hjälper dig att få ut det mesta av Azure Multi-Factor Authentication. Alla funktioner är inte tillgängliga i alla versioner av Azure Multi-Factor Authentication.

Du kan komma åt inställningar som är relaterade till Azure Multi-Factor Authentication från Azure-portalen genom att bläddra till **Azure Active Directory** > **Security** > **MFA**.

![Azure Portal - Azure AD MultiFaktor Autentiseringsinställningar](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Inställningar

Vissa av dessa inställningar gäller för MFA Server, Azure MFA eller båda.

| Funktion | Beskrivning |
| ------- | ----------- |
| Kontoutelåsning | Lås konton i multifaktorautentiseringstjänsten tillfälligt om det finns för många nekade autentiseringsförsök i rad. Den här funktionen gäller endast användare som anger en PIN-kod för att autentisera. (MFA-server) |
| [Blockera/avblockera användare](#block-and-unblock-users) | Används för att blockera specifika användare från att kunna ta emot multifaktorautentiseringsbegäranden. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Användarna är blockerade i 90 dagar från blockeringens starttid. |
| [Bedrägerivarning](#fraud-alert) | Konfigurera inställningar som är relaterade till användares möjlighet att rapportera bedrägliga verifieringsbegäranden |
| [Meddelanden](#notifications) | Aktivera meddelanden om händelser från MFA Server. |
| [OATH-tokens](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Används i molnbaserade Azure MFA-miljöer för att hantera OATH-token för användare. |
| [Inställningar för telefonsamtal](#phone-call-settings) | Konfigurera inställningar som är relaterade till telefonsamtal och hälsningar för molnmiljöer och lokala miljöer. |
| Leverantörer | Detta visar alla befintliga autentiseringsleverantörer som du kan ha kopplat till ditt konto. Nya autentiseringsleverantörer kan inte skapas från och med den 1 september 2018 |

## <a name="manage-mfa-server"></a>Hantera MFA-server

Inställningarna i det här avsnittet gäller endast för MFA-server.

| Funktion | Beskrivning |
| ------- | ----------- |
| Serverinställningar
 | Ladda ner MFA Server och generera aktiveringsautentiseringsuppgifter för att initiera din miljö |
| [En gång bypass](#one-time-bypass) | Tillåt en användare att autentisera utan att utföra tvåstegsverifiering under en begränsad tid. |
| [Cachelagringsregler](#caching-rules) |  Cachelagring används främst när lokala system, till exempel VPN, skickar flera verifieringsbegäranden medan den första begäran fortfarande pågår. Med den här funktionen kan efterföljande begäranden lyckades automatiskt, när användaren har lyckats med den första verifieringen som pågår. |
| Serverstatus | Se status för dina lokala MFA-servrar, inklusive version, status, IP och senaste kommunikationstid och datum. |

## <a name="activity-report"></a>Aktivitetsrapport

Den rapportering som finns här är specifik för MFA Server (lokalt). För Azure MFA -rapporter (moln) finns inloggningsrapporten i Azure AD.

## <a name="block-and-unblock-users"></a>Blockera och avblockera användare

Använd _funktionen blockera och avblockera användare_ för att förhindra att användare tar emot autentiseringsbegäranden. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Användarna är blockerade i 90 dagar från blockeringens starttid.

### <a name="block-a-user"></a>Blockera en användare

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **Security** > **MFA** > **Block/unblock-användare**.
3. Välj **Lägg till** om du vill blockera en användare.
4. Välj **replikeringsgruppen**. Ange användarnamnet för den blockerade användaren som **användarnamn\@domain.com**. Ange en kommentar i fältet **Orsak.**
5. Välj **Lägg till** för att slutföra blockeringen av användaren.

### <a name="unblock-a-user"></a>Avblockera en användare

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **Security** > **MFA** > **Block/unblock-användare**.
3. Välj **Ta bort blockeringen** i kolumnen **Åtgärd** bredvid användaren som ska häva blockeringen.
4. Ange en kommentar i fältet **Orsak till avblockering.**
5. Välj **Ta bort blockeringen** om du vill avsluta blockeringen av användaren.

## <a name="fraud-alert"></a>Bedrägerivarning

Konfigurera _funktionen för bedrägerivarning_ så att användarna kan rapportera bedrägliga försök att komma åt sina resurser. Användare kan rapportera bedrägeriförsök med hjälp av mobilappen eller via sin telefon.

### <a name="turn-on-fraud-alerts"></a>Aktivera bedrägerivarningar

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **Security** > **MFA** > **Fraud alert**.
3. Ange inställningen **Tillåt användare att skicka bedrägerivarningar** till **På**.
4. Välj **Spara**.

### <a name="configuration-options"></a>Konfigurationsalternativ

* **Blockera användare när bedrägeri rapporteras**: Om en användare rapporterar bedrägeri blockeras deras konto i 90 dagar eller tills en administratör avblockerar sitt konto. En administratör kan granska inloggningar med hjälp av inloggningsrapporten och vidta lämpliga åtgärder för att förhindra framtida bedrägerier. En administratör kan sedan [häva blockeringen](#unblock-a-user) av användarens konto.
* **Kod för att rapportera bedrägeri under första hälsning:** När användare får **#** ett telefonsamtal för att utföra tvåstegsverifiering trycker de normalt på för att bekräfta sin inloggning. Om du vill rapportera bedrägeri anger **#** användaren en kod innan du trycker på . Den här koden är **0** som standard, men du kan anpassa den.

   >[!NOTE]
   >Standardrösthälsningarna från Microsoft instruerar användarna att trycka på **0#** för att skicka in en bedrägerivarning. Om du vill använda en annan kod än **0**spelar du in och laddar upp egna anpassade rösthälsningar med lämpliga instruktioner för användarna.
   >

### <a name="view-fraud-reports"></a>Visa bedrägerirapporter

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj Azure Active**Directory-inloggningar** **Azure Active Directory** > . Bedrägerirapporten är nu en del av standardrapporten för Azure AD-inloggningar.

## <a name="notifications"></a>Meddelanden

Konfigurera e-postadresser här för användare som får e-postmeddelanden om bedrägeriavisering.

![E-exempel på e-postprov för meddelandebedrägeri](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Inställningar för telefonsamtal

### <a name="caller-id"></a>Nummerpresentationen

**MFA-nummernummer** - Det här är numret som användarna ser på sin telefon. Endast USA-baserade nummer är tillåtna.

>[!NOTE]
>När multifaktorautentiseringssamtal placeras via det allmänna telefonnätet dirigeras de ibland via en operatör som inte stöder nummerpresentatör. På grund av detta är nummerpresentatör inte garanterat, även om multifaktorautentiseringssystemet alltid skickar det.

I USA, om du inte har konfigurerat MFA-nummerpresentatör, kommer röstsamtal från Microsoft från följande nummer: +1 (866) 539 4191, +1 (855) 330 8653 och +1 (877) 668 6536. Om du använder skräppostfilter måste du utesluta dessa nummer.

### <a name="custom-voice-messages"></a>Anpassade röstmeddelanden

Du kan använda dina egna inspelningar eller hälsningar för tvåstegsverifiering med funktionen _anpassade röstmeddelanden._ Dessa meddelanden kan användas utöver eller ersätta Microsoft-inspelningarna.

Innan du börjar bör du känna till följande begränsningar:

* Filformaten som stöds är WAV och .mp3.
* Filstorleksgränsen är 1 MB.
* Autentiseringsmeddelanden bör vara kortare än 20 sekunder. Meddelanden som är längre än 20 sekunder kan orsaka att verifieringen misslyckas. Användaren kanske inte svarar innan meddelandet är klart och verifieringen är.

### <a name="custom-message-language-behavior"></a>Anpassat meddelandespråkbeteende

När ett anpassat röstmeddelande spelas upp för användaren beror meddelandets språk på följande faktorer:

* Språket för den aktuella användaren.
  * Språket som identifieras av användarens webbläsare.
  * Andra autentiseringsscenarier kan bete sig annorlunda.
* Språket för alla tillgängliga anpassade meddelanden.
  * Det här språket väljs av administratören när ett anpassat meddelande läggs till.

Om det till exempel bara finns ett anpassat meddelande med ett språk i tyska:

* En användare som autentiserar på tyska kommer att höra det anpassade tyska meddelandet.
* En användare som autentiserar på engelska hör det vanliga engelska meddelandet.

### <a name="set-up-a-custom-message"></a>Konfigurera ett anpassat meddelande

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
1. Bläddra till **Azure Active Directory** > **Security** > **MFA-samtalsinställningar** > **Phone call settings**.
1. Välj **Lägg till hälsning**.
1. Välj typ av hälsning.
1. Välj språk.
1. Välj en .mp3- eller .wav-ljudfil som ska laddas upp.
1. Välj **Lägg till**.

### <a name="custom-voice-message-defaults"></a>Standardinställningar för anpassat röstmeddelande

Exempel på skript för att skapa anpassade meddelanden.

| Meddelandenamn | Skript |
| --- | --- |
| Autentiseringen lyckades | Din inloggning har verifierats. Hej då. |
| Fråga om tillägg | Tack för att du använder Microsofts inloggningsverifieringssystem. Tryck på pund för att fortsätta. |
| Bekräftelse av bedrägerier | En bedrägerivarning har lämnats in. Om du vill häva blockeringen av ditt konto kontaktar du företagets IT-support. |
| Bedrägeri hälsning (Standard) | Tack för att du använder Microsofts inloggningsverifieringssystem. Tryck på pundknappen för att slutföra verifieringen. Om du inte initierade den här verifieringen kan någon försöka komma åt ditt konto. Tryck på noll pund för att skicka in en bedrägerivarning. Detta meddelar ditt företags IT-team och blockerar ytterligare verifieringsförsök. |
| Bedrägeri anmält En bedrägerivarning har lämnats in. | Om du vill häva blockeringen av ditt konto kontaktar du företagets IT-support. |
| Aktivering | Tack för att du använder Microsofts inloggningsverifieringssystem. Tryck på pundknappen för att slutföra verifieringen. |
| Autentisering nekad återförsök | Verifiering nekad. |
| Försök igen (standard) | Tack för att du använder Microsofts inloggningsverifieringssystem. Tryck på pundknappen för att slutföra verifieringen. |
| Hälsning (standard) | Tack för att du använder Microsofts inloggningsverifieringssystem. Tryck på pundknappen för att slutföra verifieringen. |
| Hälsning (PIN) | Tack för att du använder Microsofts inloggningsverifieringssystem. Ange din PIN-kod följt av pund-tangenten för att slutföra verifieringen. |
| Hälsning av bedrägerier (PIN- kod) | Tack för att du använder Microsofts inloggningsverifieringssystem.  Ange din PIN-kod följt av pund-tangenten för att slutföra verifieringen. Om du inte initierade den här verifieringen kan någon försöka komma åt ditt konto. Tryck på noll pund för att skicka in en bedrägerivarning. Detta meddelar ditt företags IT-team och blockerar ytterligare verifieringsförsök. |
| Försök igen(PIN)Retry(PIN) | Tack för att du använder Microsofts inloggningsverifieringssystem. Ange din PIN-kod följt av pund-tangenten för att slutföra verifieringen. |
| Tilläggsfråga efter siffror | Om redan vid denna förlängning, tryck på pundknappen för att fortsätta. |
| Autentisering nekad | Jag är ledsen, vi kan inte skriva in dig just nu. Försök igen senare. |
| Aktiveringshälsning (standard) | Tack för att du använder Microsofts inloggningsverifieringssystem. Tryck på pundknappen för att slutföra verifieringen. |
| Återförsök för aktivering (standard) | Tack för att du använder Microsofts inloggningsverifieringssystem. Tryck på pundknappen för att slutföra verifieringen. |
| Aktiveringshälsning (PIN) | Tack för att du använder Microsofts inloggningsverifieringssystem. Ange din PIN-kod följt av pund-tangenten för att slutföra verifieringen. |
| Tilläggsfråga före siffror | Tack för att du använder Microsofts inloggningsverifieringssystem. Vänligen överföra denna uppmaning till förlängning ... |

## <a name="one-time-bypass"></a>En gång bypass

Med _en gång bypass-funktionen_ kan en användare autentisera en enda gång utan att utföra tvåstegsverifiering. Förbikopplingen är tillfällig och upphör att gälla efter ett angivet antal sekunder. I situationer där mobilappen eller telefonen inte tar emot ett meddelande eller telefonsamtal kan du tillåta en engångsförsening så att användaren kan komma åt önskad resurs.

### <a name="create-a-one-time-bypass"></a>Skapa en engångsförsening

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **Security** > **MFA** > **Engångsföregång.**
3. Välj **Lägg till**.
4. Om det behövs väljer du replikeringsgruppen för förbikopplingen.
5. Ange användarnamnet som **användarnamn\@domain.com**. Ange hur många sekunder som förbikopplingen ska pågå. Ange orsaken till förbifartsleden.
6. Välj **Lägg till**. Tidsfristen träder i kraft omedelbart. Användaren måste logga in innan en gång bypass upphör att gälla.

### <a name="view-the-one-time-bypass-report"></a>Visa en gång bypass-rapport

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Bläddra till **Azure Active Directory** > **Security** > **MFA** > **Engångsföregång.**

## <a name="caching-rules"></a>Cachelagringsregler

Du kan ange en tidsperiod för att tillåta autentiseringsförsök när en användare har autentiserats med hjälp av _cachelagringsfunktionen._ Efterföljande autentiseringsförsök för användaren inom den angivna tidsperioden lyckas automatiskt. Cachelagring används främst när lokala system, till exempel VPN, skickar flera verifieringsbegäranden medan den första begäran fortfarande pågår. Med den här funktionen kan efterföljande begäranden lyckades automatiskt, när användaren har lyckats med den första verifieringen som pågår.

>[!NOTE]
>Cachelagringsfunktionen är inte avsedd att användas för inloggningar till Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Konfigurera cachelagring

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **Security** > **MFA** > **Caching regler**.
3. Välj **Lägg till**.
4. Välj **cachetypen** i listrutan. Ange det maximala antalet **cachesekunder**.
5. Om det behövs väljer du en autentiseringstyp och anger ett program.
6. Välj **Lägg till**.

## <a name="mfa-service-settings"></a>MFA-tjänstinställningar

Inställningar för applösenord, betrodda IPs, verifieringsalternativ och kom ihåg multifaktorautentisering för Azure Multi Factor Authentication finns i tjänstinställningarna. Tjänstinställningar kan nås från Azure-portalen genom att bläddra till **Azure Active Directory** > **Security** > **MFA** > **Komma igång** > **Konfigurera** > **ytterligare molnbaserade MFA-inställningar**.

![Tjänstinställningar för Azure Multi Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

De betrodda IP-adressintervallen kan vara privata eller offentliga.

## <a name="app-passwords"></a>Applösenord

Vissa program, till exempel Office 2010 eller tidigare och Apple Mail före iOS 11, stöder inte tvåstegsverifiering. Apparna är inte konfigurerade för att acceptera en andra verifiering. Använd _appens lösenord_ om du vill använda dessa program. Du kan använda ett applösenord i stället för ditt traditionella lösenord för att tillåta en app att kringgå tvåstegsverifiering och fortsätta arbeta.

Modern autentisering stöds för Microsoft Office 2013-klienterna och senare. Office 2013-klienter, inklusive Outlook, stöder moderna autentiseringsprotokoll och kan aktiveras för att fungera med tvåstegsverifiering. När klienten har aktiverats krävs inte applösenord för klienten.

>[!NOTE]
>Applösenord fungerar inte med villkorade åtkomstbaserade principer för multifaktorautentisering och modern autentisering.

### <a name="considerations-about-app-passwords"></a>Överväganden om applösenord

När du använder applösenord bör du tänka på följande viktiga punkter:

* Applösenord anges bara en gång per program. Användarna behöver inte hålla reda på lösenorden eller ange dem varje gång.
* Det faktiska lösenordet genereras automatiskt och tillhandahålls inte av användaren. Det automatiskt genererade lösenordet är svårare för en angripare att gissa och är säkrare.
* Det finns en gräns på 40 lösenord per användare.
* Program som cachelagrar lösenord och använder dem i lokala scenarier kan börja misslyckas eftersom applösenordet inte är känt utanför arbets- eller skolkontot. Ett exempel på det här scenariot är Exchange-e-postmeddelanden som är lokala, men den arkiverade e-postmeddelandet finns i molnet. I det här fallet fungerar inte samma lösenord.
* När Multifaktorautentisering har aktiverats på en användares konto kan applösenord användas med de flesta klienter som inte är webbläsare, till exempel Outlook och Microsoft Skype för företag. Administrativa åtgärder kan inte utföras med hjälp av applösenord via program som inte är webbläsare, till exempel Windows PowerShell. Åtgärderna kan inte utföras även när användaren har ett administrativt konto. Om du vill köra PowerShell-skript skapar du ett tjänstkonto med ett starkt lösenord och aktiverar inte kontot för tvåstegsverifiering.

>[!WARNING]
>Applösenord fungerar inte i hybridmiljöer där klienter kommunicerar med både lokala och moln automatiskt upptäcka slutpunkter. Domänlösenord krävs för att autentisera lokalt. Applösenord krävs för att autentisera med molnet.

### <a name="guidance-for-app-password-names"></a>Vägledning för applösenordsnamn

App lösenordsnamn bör återspegla den enhet där de används. Om du har en bärbar dator som har program som inte är webbläsare som Outlook, Word och Excel skapar du ett **applösenord** med namnet Laptop för dessa appar. Skapa ett annat **applösenord** med namnet Desktop för samma program som körs på din stationära dator.

>[!NOTE]
>Vi rekommenderar att du skapar ett applösenord per enhet i stället för ett applösenord per program.

### <a name="federated-or-single-sign-on-app-passwords"></a>Lösenord för federerade eller enstaka inloggningsappar

Azure AD stöder federation, eller enkel inloggning (SSO), med lokala Windows Server Active Directory Domain Services (AD DS). Om din organisation är federerad med Azure AD och du använder Azure Multi-Factor Authentication bör du tänka på följande punkter om applösenord.

>[!NOTE]
>Följande punkter gäller endast federerade (SSO) kunder.

* Applösenord verifieras av Azure AD och kringgå därför federationen. Federation används aktivt endast när du ställer in applösenord.
* Identitetsprovidern (IdP) kontaktas inte för federerade (SSO) användare, till skillnad från det passiva flödet. Applösenorden lagras i arbets- eller skolkontot. Om en användare lämnar företaget flödar användarens information till arbets- eller skolkontot med hjälp av **DirSync** i realtid. Inaktivera/ta bort kontot kan ta upp till tre timmar att synkronisera, vilket kan fördröja inaktivera/borttagning av applösenordet i Azure AD.
* Lokala inställningar för åtkomstkontroll för klienten respekteras inte av appens lösenordsfunktionen.
* Det finns ingen lokal autentiseringsloggnings-/granskningsfunktion tillgänglig för användning med funktionen för applösenord.
* Vissa avancerade arkitekturer kräver en kombination av autentiseringsuppgifter för tvåstegsverifiering med klienter. Dessa autentiseringsuppgifter kan innehålla ett användarnamn och lösenord för arbets- eller skolkonto och applösenord. Kraven beror på hur autentiseringen utförs. För klienter som autentiserar mot en lokal infrastruktur, ett användarnamn och lösenord för arbets- eller skolkonto som krävs. För klienter som autentiserar mot Azure AD krävs ett applösenord.

  Anta till exempel att du har följande arkitektur:

  * Din lokala instans av Active Directory är federerad med Azure AD.
  * Du använder Exchange online.
  * Du använder Skype för företag lokalt.
  * Du använder Azure Multi-Factor Authentication.

  I det här fallet använder du följande autentiseringsuppgifter:

  * Om du vill logga in på Skype för företag använder du användarnamn och lösenord för ditt arbets- eller skolkonto.
  * Om du vill komma åt adressboken från en Outlook-klient som ansluter till Exchange online använder du ett applösenord.

### <a name="allow-users-to-create-app-passwords"></a>Tillåt användare att skapa applösenord

Som standard kan användarna inte skapa applösenord. Appens lösenordsfunktion måste vara aktiverad. Om du vill ge användarna möjlighet att skapa applösenord använder du följande procedur:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **Users**.
3. Välj **Multifaktorautentisering**.
4. Under Multifaktorautentisering väljer du **tjänstinställningar**.
5. På sidan **Tjänstinställningar** väljer du alternativet **Tillåt användare att skapa applösenord för att logga in på appar som inte är webbläsare.**

### <a name="create-app-passwords"></a>Skapa applösenord

Användare kan skapa applösenord under den första registreringen. Användaren har möjlighet att skapa applösenord i slutet av registreringsprocessen.

Användare kan också skapa applösenord efter registreringen. Mer information och detaljerade steg för användarna finns [i Vad är applösenord i Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Funktionen _Betrodda IPs_ i Azure Multi-Factor Authentication används av administratörer för en hanterad eller federerad klientorganisation. Funktionen kringgår tvåstegsverifiering för användare som loggar in från företagets intranät. Funktionen är tillgänglig med den fullständiga versionen av Azure Multi-Factor Authentication och inte den kostnadsfria versionen för administratörer. Mer information om hur du hämtar den fullständiga versionen av Azure Multi-Factor Authentication finns i [Azure Multi-Factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> MFA-betrodda IPs och villkorlig åtkomst namngivna platser fungerar bara med IPV4-adresser.

Om din organisation distribuerar NPS-tillägget för att tillhandahålla MFA till lokala program observera källan IP-adressen alltid verkar vara NPS-servern autentiseringsförsöket flödar igenom.

| Klienttyp för Azure AD | Alternativ för betrodda IPs-funktionsalternativ |
|:--- |:--- |
| Hanterade |**Specifikt intervall med IP-adresser**: Administratörer anger ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från företagets intranät. Högst 50 betrodda IP-intervall kan konfigureras.|
| Federerade |**Alla federerade användare:** Alla federerade användare som loggar in in från insidan av organisationen kan kringgå tvåstegsverifiering. Användarna kringgår verifieringen med hjälp av ett anspråk som utfärdas av AD FS (Active Directory Federation Services).<br/>**Specifikt intervall med IP-adresser**: Administratörer anger ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från företagets intranät. |

Förbifartsleden betrodda IPs fungerar bara inifrån företagets intranät. Om du väljer alternativet **Alla Federerade användare** och en användare loggar in utanför företagets intranät måste användaren autentisera med hjälp av tvåstegsverifiering. Processen är densamma även om användaren presenterar ett AD FS-anspråk. 

### <a name="end-user-experience-inside-of-corpnet"></a>Slutanvändarupplevelse inom corpnet

När funktionen Betrodda IPs är inaktiverad krävs tvåstegsverifiering för webbläsarflöden. Applösenord krävs för äldre avancerade klientprogram.

När funktionen Betrodda IPs är aktiverad krävs *inte* tvåstegsverifiering för webbläsarflöden. Applösenord krävs *inte* för äldre avancerade klientprogram, förutsatt att användaren inte har skapat ett applösenord. När ett applösenord används krävs lösenordet. 

### <a name="end-user-experience-outside-corpnet"></a>Slutanvändarupplevelse utanför corpnet

Oavsett om funktionen Betrodda IPs är aktiverad krävs tvåstegsverifiering för webbläsarflöden. Applösenord krävs för äldre avancerade klientprogram.

### <a name="enable-named-locations-by-using-conditional-access"></a>Aktivera namngivna platser med villkorsstyrd åtkomst

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du**Namngivna platser**för Azure Active**Directory-säkerhetsvillkorsåtkomst** > **Conditional Access** >  **Azure Active Directory** > .
3. Välj **Ny plats**.
4. Ange ett namn på platsen.
5. Välj **Markera som betrodd plats**.
6. Ange IP-intervallet i CIDR-notation som **192.168.1.1/24**.
7. Välj **Skapa**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Aktivera funktionen Betrodda IPs med villkorsstyrd åtkomst

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du**Namngivna platser**för Azure Active**Directory-säkerhetsvillkorsåtkomst** >  **Conditional Access** >  **Azure Active Directory** > .
3. Välj **Konfigurera MFA-betrodda IPs**.
4. Välj bland något av följande två alternativ under **Betrodda IPs**på sidan **Tjänstinställningar:**

   * **För begäranden från federerade användare som kommer från intranätet**: Markera kryssrutan om du vill välja det här alternativet. Alla federerade användare som loggar in från företagsnätverket kringgår tvåstegsverifiering med hjälp av ett anspråk som utfärdas av AD FS. Kontrollera att AD FS har en regel för att lägga till intranätanspråket i lämplig trafik. Om regeln inte finns skapar du följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **För begäranden från ett visst intervall av offentliga IP-adresser:** Om du vill välja det här alternativet anger du IP-adresserna i textrutan med hjälp av CIDR-notation.
      * För IP-adresser som finns i intervallet xxx.xxx.xxx.1 till xxx.xxx.xxx.254, använd notation som **xxx.xxx.xxx.0/24**.
      * För en enda IP-adress använder du notation som **xxx.xxx.xxx.xxx/32**.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgår tvåstegsverifiering.

5. Välj **Spara**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Aktivera funktionen Betrodda IPs med hjälp av tjänstinställningar

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **Users**.
3. Välj **Multifaktorautentisering**.
4. Under Multifaktorautentisering väljer du **tjänstinställningar**.
5. På sidan **Tjänstinställningar** under **Betrodda IPs**väljer du ett (eller båda) av följande två alternativ:

   * **För begäranden från federerade användare i intranätet**: Markera kryssrutan om du vill välja det här alternativet. Alla federerade användare som loggar in från företagsnätverket kringgår tvåstegsverifiering med hjälp av ett anspråk som utfärdas av AD FS. Kontrollera att AD FS har en regel för att lägga till intranätanspråket i lämplig trafik. Om regeln inte finns skapar du följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **För begäranden från ett angivet intervall med IP-adressundernät:** Om du vill välja det här alternativet anger du IP-adresserna i textrutan med hjälp av CIDR-notation.
      * För IP-adresser som finns i intervallet xxx.xxx.xxx.1 till xxx.xxx.xxx.254, använd notation som **xxx.xxx.xxx.0/24**.
      * För en enda IP-adress använder du notation som **xxx.xxx.xxx.xxx/32**.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgår tvåstegsverifiering.

6. Välj **Spara**.

## <a name="verification-methods"></a>Verifieringsmetoder

Du kan välja vilka verifieringsmetoder som är tillgängliga för användarna. Följande tabell ger en kort översikt över metoderna.

När användarna registrerar sina konton för Azure Multi-Factor Authentication väljer de den önskade verifieringsmetoden bland de alternativ som du har aktiverat. Vägledning för användarregistreringsprocessen finns i [Konfigurera mitt konto för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metod | Beskrivning |
|:--- |:--- |
| Ringa till telefon |Placerar ett automatiserat röstsamtal. Användaren identifierar sig genom att svara och trycka på #-tangenten. Telefonnumret synkroniseras inte med lokal Active Directory. |
| Sms till telefon |Skickar ett textmeddelande som innehåller en verifieringskod. Användaren uppmanas att ange verifieringskoden i inloggningsgränssnittet. Denna process kallas enkelriktad SMS. Tvåvägs SMS innebär att användaren måste skicka tillbaka en viss kod. Tvåvägs-SMS är inaktuellt och stöds inte efter den 14 november 2018. Administratörer bör aktivera en annan metod för användare som tidigare använt tvåvägs-SMS.|
| Avisering via mobilapp |Skickar ett push-meddelande till telefonen eller den registrerade enheten. Användaren visar meddelandet och väljer **Verifiera** för att slutföra verifieringen. Microsoft Authenticator-appen är tillgänglig för [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Verifieringskod från mobilapp eller maskinvarutoken |Microsoft Authenticator-appen genererar en ny OATH-verifieringskod var 30:e sekund. Användaren anger verifieringskoden i inloggningsgränssnittet. Microsoft Authenticator-appen är tillgänglig för [Windows Phone,](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) [Android](https://go.microsoft.com/fwlink/?Linkid=825072)och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Aktivera och inaktivera verifieringsmetoder

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **Users**.
3. Välj **Multifaktorautentisering**.
4. Under Multifaktorautentisering väljer du **tjänstinställningar**.
5. Välj/avmarkera de metoder som ska tillhandahållas användarna under **verifieringsalternativ**på sidan **Tjänstinställningar.**
6. Klicka på **Spara**.

Ytterligare information om användningen av autentiseringsmetoder finns i artikeln [Vilka är autentiseringsmetoder](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Kom ihåg multifaktorautentisering

_Funktionen Kom ihåg multifaktorautentisering_ för enheter och webbläsare som är betrodda av användaren är en kostnadsfri funktion för alla multifaktorautentiseringsanvändare. Användare kan kringgå efterföljande verifieringar under ett visst antal dagar efter att de har loggat in på en enhet med hjälp av Multi-Factor Authentication. Funktionen förbättrar användbarheten genom att minimera antalet gånger en användare har att utföra tvåstegsverifiering på samma enhet.

>[!IMPORTANT]
>Om ett konto eller en enhet äventyras kan det påverka säkerheten om du kommer ihåg multifaktorautentisering för betrodda enheter. Om ett företagskonto äventyras eller en betrodd enhet försvinner eller blir stulen bör du [återkalla MFA-sessioner](howto-mfa-userdevicesettings.md).
>
>Återställningsåtgärden återkallar den betrodda statusen från alla enheter och användaren måste utföra tvåstegsverifiering igen. Du kan också instruera användarna att återställa multifaktorautentisering på sina egna enheter med instruktionerna i [Hantera dina inställningar för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Så här fungerar funktionen

Med funktionen Kom ihåg multifaktorautentisering ställs in en beständig cookie i webbläsaren när en användare väljer alternativet **Fråga inte igen om X-dagar** vid inloggning. Användaren uppmanas inte igen för multifaktorautentisering från samma webbläsare förrän cookien går ut. Om användaren öppnar en annan webbläsare på samma enhet eller rensar sina cookies uppmanas han att verifiera igen.

Alternativet **Fråga inte igen för X-dagar** visas inte i program som inte är webbläsare, oavsett om appen stöder modern autentisering. Dessa appar använder _uppdateringstoken_ som ger nya åtkomsttoken varje timme. När en uppdateringstoken valideras kontrollerar Azure AD att den senaste tvåstegsverifieringen inträffade inom det angivna antalet dagar.

Funktionen minskar antalet autentiseringar på webbappar, som normalt frågar varje gång. Funktionen ökar antalet autentiseringar för moderna autentiseringsklienter som normalt frågar var 90:e dag. Kan också öka antalet autentiseringar i kombination med principer för villkorlig åtkomst.

>[!IMPORTANT]
>**Funktionen kom ihåg multifaktorautentisering** är inte kompatibel med funktionen håll mig inloggad **i** AD FS, när användare utför tvåstegsverifiering för AD FS via Azure Multi-Factor Authentication Server eller en tredjepartslösning för multifaktorautentisering.
>
>Om användarna väljer **att hålla mig inloggad** på AD FS och även markera sin enhet som betrodd för multifaktorautentisering, verifieras inte användaren automatiskt efter att antalet dagar för kom ihåg flera **faktorer** har gått ut. Azure AD begär en ny tvåstegsverifiering, men AD FS returnerar en token med det ursprungliga multifaktorautentiseringsanspråket och datumet, i stället för att utföra tvåstegsverifiering igen. **Den här reaktionen utlöser en verifieringsloop mellan Azure AD och AD FS.**
>
>**Kom ihåg multifaktorautentisering** funktionen är inte kompatibel med B2B-användare och kommer inte att vara synlig för B2B-användare när du loggar in på de inbjudna klienterna.
>

### <a name="enable-remember-multi-factor-authentication"></a>Aktivera kom ihåg multifaktorautentisering

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **Users**.
3. Välj **Multifaktorautentisering**.
4. Under Multifaktorautentisering väljer du **tjänstinställningar**.
5. På sidan **Tjänstinställningar,** **hantera kom ihåg multifaktorautentisering**, välj **alternativet Tillåt användare att komma ihåg multifaktorautentisering på enheter som de litar på.**
6. Ange antalet dagar så att betrodda enheter kan kringgå tvåstegsverifiering. Standardvärdet är 14 dagar.
7. Välj **Spara**.

### <a name="mark-a-device-as-trusted"></a>Markera en enhet som betrodd

När du har aktiverat funktionen Kom ihåg multifaktorautentisering kan användarna markera en enhet som betrodd när de loggar in genom att välja **Fråga inte igen**.

## <a name="next-steps"></a>Nästa steg

[Ändra varumärke för Azure AD-inloggningssidan](../fundamentals/customize-branding.md)
