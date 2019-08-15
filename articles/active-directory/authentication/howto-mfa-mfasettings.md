---
title: Konfigurera Azure Multi-Factor Authentication-Azure Active Directory
description: Den här artikeln beskriver hur du konfigurerar inställningar för Azure Multi-Factor Authentication i Azure Portal
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12fd08fa301cb22857470e3c81d7f4f7c1f04de9
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68948880"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurera inställningar för Azure Multi-Factor Authentication

Den här artikeln hjälper dig att hantera Multi-Factor Authentication-inställningar i Azure Portal. Den täcker olika ämnen som hjälper dig att få ut mesta möjliga av Azure Multi-Factor Authentication. Alla funktioner är inte tillgängliga i alla versioner av Azure Multi-Factor Authentication.

Du kan komma åt inställningar som rör Azure Multi-Factor Authentication från Azure Portal genom att bläddra till **Azure Active Directory** > **MFA**.

![Azure Portal-inställningar för Azure AD Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Inställningar

Några av de här inställningarna gäller för MFA Server, Azure MFA eller både och.

| Funktion | Beskrivning |
| ------- | ----------- |
| Kontoutelåsning | Lås tillfälligt konton i Multi-Factor Authentication-tjänsten om det finns alltför många nekade autentiseringsförsök i en rad. Den här funktionen gäller endast för användare som anger en PIN-kod för autentisering. (MFA-Server) |
| [Blockera/avblockera användare](#block-and-unblock-users) | Används för att blockera vissa användare från att kunna ta emot Multi-Factor Authentication-begäranden. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Användarna är fortfarande blockerade i 90 dagar från den tid som de är blockerade. |
| [Bedrägeri avisering](#fraud-alert) | Konfigurera inställningar för användarnas möjlighet att rapportera falska verifierings begär Anden |
| [Meddelanden](#notifications) | Aktivera meddelanden om händelser från MFA Server. |
| [OATH-token](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Används i Cloud-baserade Azure MFA-miljöer för att hantera OATH-token för användare. |
| [Telefonsamtals inställningar](#phone-call-settings) | Konfigurera inställningar för telefonsamtal och hälsningar för moln miljöer och lokala miljöer. |
| Providers | Då visas befintliga autentiseringsproviders som du kan ha associerat med ditt konto. Nya autentiseringsproviders får inte skapas från den 1 september 2018 |

## <a name="manage-mfa-server"></a>Hantera MFA Server

Inställningarna i det här avsnittet gäller endast MFA Server.

| Funktion | Beskrivning |
| ------- | ----------- |
| Serverinställningar | Hämta MFA Server och generera autentiseringsuppgifter för aktivering för att initiera din miljö |
| [Kringgå vid ett tillfälle](#one-time-bypass) | Tillåt en användare att autentisera utan att utföra tvåstegsverifiering under en begränsad tid. |
| [Regler för cachelagring](#caching-rules) |  Cachelagring används främst när lokala system, till exempel VPN, skickar flera verifierings begär Anden medan den första begäran fortfarande pågår. Den här funktionen gör att efterföljande begär Anden kan lyckas automatiskt när användaren har slutfört den första verifieringen. |
| Serverstatus | Se status för dina lokala MFA-servrar, inklusive version, status, IP och senaste kommunikations tid och datum. |

## <a name="activity-report"></a>Aktivitetsrapport

Den rapportering som är tillgänglig här är unik för MFA Server (lokal). För Azure MFA (Cloud)-rapporter se inloggnings rapporten i Azure AD.

## <a name="block-and-unblock-users"></a>Blockera och avblockera användare

Använd funktionen _blockera och avblockera användare_ om du vill förhindra att användare tar emot autentiseringsbegäranden. Alla autentiseringsförsök för blockerade användare nekas automatiskt. Användarna är fortfarande blockerade i 90 dagar från den tid som de är blockerade.

### <a name="block-a-user"></a>Blockera en användare

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** >  **-blockera/avblockera användare**.
3. Välj **Lägg till** för att blockera en användare.
4. Välj **replikeringsgrupp**. Ange användar namnet för den blockerade användaren **som\@användar namn domain.com**. Ange en kommentar i fältet **orsak** .
5. Välj **Lägg till** för att slutföra blockeringen av användaren.

### <a name="unblock-a-user"></a>Avblockera en användare

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** >  **-blockera/avblockera användare**.
3. Välj **Häv blockering** i kolumnen **åtgärd** bredvid användaren att avblockera.
4. Ange en kommentar i fältet **orsak till att** fältet avblockeras.
5. Klicka på **Tillåt** för att avbryta blockeringen av användaren.

## <a name="fraud-alert"></a>Bedrägerivarning

Konfigurera _bedrägeri aviserings_ funktionen så att användarna kan rapportera falska försök att komma åt sina resurser. Användare kan rapportera bedrägerier-försök med hjälp av mobilappen eller via telefon.

### <a name="turn-on-fraud-alerts"></a>Aktivera bedrägerier-aviseringar

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** > **bedrägeri avisering**.
3. Ange inställningen **Tillåt användare att skicka bedrägeri varningar** till **på**.
4. Välj **Spara**.

### <a name="configuration-options"></a>Konfigurationsalternativ

* **Blockera användare när bedrägeri rapporteras**: Om en användare rapporterar bedrägerier blockeras kontot i 90 dagar eller tills en administratör avblockerar sitt konto. En administratör kan granska inloggningar med hjälp av inloggnings rapporten och vidta lämpliga åtgärder för att förhindra framtida bedrägerier. En administratör kan sedan [avblockera](#unblock-a-user) användarens konto.
* **Kod för att rapportera bedrägerier under inledande hälsning**: När användarna får ett telefonsamtal för att utföra tvåstegsverifiering, trycks **#** de normalt för att bekräfta inloggningen. Användaren kan rapportera bedrägerier genom att ange en kod innan du trycker **#** på. Den här koden är **0** som standard, men du kan anpassa den.

   >[!NOTE]
   >Vanliga röst hälsningar från Microsoft instruerar användarna att trycka på **0 #** för att skicka en bedrägeri avisering. Om du vill använda en annan kod än **0**, spelar du in och laddar upp egna röst hälsningar med lämpliga instruktioner för dina användare.
   >

### <a name="view-fraud-reports"></a>Visa bedrägerier-rapporter

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > **inloggningar**. Bedrägeri rapporten är nu en del av standard rapporten för Azure AD-inloggningar.

## <a name="notifications"></a>Aviseringar

Konfigurera e-postadresser här för användare som ska få meddelanden om bedrägerier.

![Avisering om meddelande bedrägerier e-exempel](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Inställningarna för telefonsamtal

### <a name="caller-id"></a>Uppringarens ID

**MFA-uppringarens ID-nummer** – det här är det tal som användarna ser på sin telefon. Endast amerikanska-baserade tal är tillåtna.

>[!NOTE]
>När Multi-Factor Authentication-samtal placeras via det offentliga telefonnätet dirigeras de ibland via en operatör som inte har stöd för uppringarens ID. Därför garanteras inte anropar-ID, trots att Multi-Factor Authentication-systemet alltid skickar det.

### <a name="custom-voice-messages"></a>Anpassade röst meddelanden

Du kan använda dina egna inspelningar eller hälsningar för tvåstegsverifiering med funktionen för _anpassade röst meddelanden_ . Dessa meddelanden kan användas utöver eller för att ersätta Microsoft-inspelningar.

Innan du börjar bör du vara medveten om följande begränsningar:

* De fil format som stöds är. wav och. mp3.
* Gränsen för fil storlek är 5 MB.
* Autentiseringsfel bör vara kortare än 20 sekunder. Meddelanden som är längre än 20 sekunder kan göra att verifieringen inte fungerar. Användaren kanske inte svarar innan meddelandet har slutförts och verifierings tiden är slut.

### <a name="custom-message-language-behavior"></a>Språk beteende för anpassade meddelanden

När ett anpassat röst meddelande spelas upp till användaren, beror språket i meddelandet på följande faktorer:

* Den aktuella användarens språk.
  * Språket som identifieras av användarens webbläsare.
  * Andra verifierings scenarier kan bete sig annorlunda.
* Språket för alla tillgängliga anpassade meddelanden.
  * Det här språket väljs av administratören när ett anpassat meddelande läggs till.

Om det till exempel bara finns ett anpassat meddelande, med språket tyska:

* En användare som autentiserar på tyska språk kommer att höra det anpassade tyska meddelandet.
* En användare som autentiserar på engelska kommer att höra det engelska standard meddelandet.

### <a name="set-up-a-custom-message"></a>Konfigurera ett anpassat meddelande

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
1. Bläddra till **Azure Active Directory** > **MFA** > -**Telefonsamtals inställningar**.
1. Välj **Lägg till hälsning**.
1. Välj typ av hälsning.
1. Välj språk.
1. Välj en. mp3-eller. WAV-ljudfil som ska överföras.
1. Välj **Lägg till**.

### <a name="custom-voice-message-defaults"></a>Standardinställningar för anpassade röst meddelanden

Exempel skript för att skapa anpassade meddelanden.

| Meddelande namn | Skript |
| --- | --- |
| Autentiseringen lyckades | Inloggningen har verifierats. Hej då. |
| Ange anknytning | Tack för att du använder Microsofts inloggnings verifierings system. Fortsätt genom att trycka på fyrkant. |
| Bekräfta bedrägeri | En bedrägeri avisering har skickats. Om du vill häva blockeringen av ditt konto kontaktar du ditt företags IT-supportavdelning. |
| Bedrägerimeddelande (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Tryck på fyrkant om du vill avsluta verifieringen. Om du inte har initierat den här verifieringen kan någon försöka komma åt ditt konto. Skicka en bedrägeri avisering genom att trycka på noll. Detta kommer att meddela ditt företags IT-team och blockera ytterligare verifierings försök. |
| Bedrägerier rapporterade att en bedrägeri avisering har skickats. | Om du vill häva blockeringen av ditt konto kontaktar du ditt företags IT-supportavdelning. |
| Aktivering | Tack för att du använder Microsofts inloggnings verifierings system. Tryck på fyrkant om du vill avsluta verifieringen. |
| Autentiseringen nekade återförsök | Det gick inte att slutföra verifieringen. |
| Försök igen (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Tryck på fyrkant om du vill avsluta verifieringen. |
| Hälsning (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Tryck på fyrkant om du vill avsluta verifieringen. |
| Hälsning (PIN-kod) | Tack för att du använder Microsofts inloggnings verifierings system. Ange din PIN-kod följt av fyrkant-tangenten för att slutföra verifieringen. |
| Bedrägerimeddelande (PIN-kod) | Tack för att du använder Microsofts inloggnings verifierings system.  Ange din PIN-kod följt av fyrkant-tangenten för att slutföra verifieringen. Om du inte har initierat den här verifieringen kan någon försöka komma åt ditt konto. Skicka en bedrägeri avisering genom att trycka på noll. Detta kommer att meddela ditt företags IT-team och blockera ytterligare verifierings försök. |
| Försök igen (PIN-kod) | Tack för att du använder Microsofts inloggnings verifierings system. Ange din PIN-kod följt av fyrkant-tangenten för att slutföra verifieringen. |
| Ange anknytning efter siffror | Om du redan befinner dig på denna anknytning trycker du på fyrkant om du vill fortsätta. |
| Autentiseringen nekades | Jag kan tyvärr inte logga in dig just nu. Försök igen senare. |
| Aktiveringshälsning (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Tryck på fyrkant om du vill avsluta verifieringen. |
| Nytt aktiveringsförsök (standard) | Tack för att du använder Microsofts inloggnings verifierings system. Tryck på fyrkant om du vill avsluta verifieringen. |
| Aktiveringshälsning (PIN-kod) | Tack för att du använder Microsofts inloggnings verifierings system. Ange din PIN-kod följt av fyrkant-tangenten för att slutföra verifieringen. |
| Ange anknytning före siffror | Tack för att du använder Microsofts inloggnings verifierings system. Överför det här anropet till Extension... |

## <a name="one-time-bypass"></a>Engångsförbikoppling

Med funktionen för att _kringgå_ en funktion kan en användare autentisera en enstaka gång utan att utföra tvåstegsverifiering. Bypass är tillfälligt och upphör att gälla efter ett angivet antal sekunder. I situationer där mobilappen eller telefon inte tar emot ett meddelande eller telefonsamtal kan du tillåta en eng ång slö fördröjning så att användaren kan komma åt den önskade resursen.

### <a name="create-a-one-time-bypass"></a>Skapa en eng ång slö fördröjning

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** >  **-en-tid**.
3. Välj **Lägg till**.
4. Om det behövs väljer du replikeringsgruppen för bypass.
5. Ange användar namnet **\@Domain.com**. Ange antalet sekunder som förbikopplingen ska vara sist. Ange anledningen till kringgåendet.
6. Välj **Lägg till**. Tids gränsen börjar gälla omedelbart. Användaren måste logga in innan eng ång slö tiden upphör att gälla.

### <a name="view-the-one-time-bypass-report"></a>Visa en rapport för att kringgå en stund

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Bläddra till **Azure Active Directory** > **MFA** >  **-en-tid**.

## <a name="caching-rules"></a>Cachelagringsregler

Du kan ange en tids period för att tillåta autentiseringsförsök när en användare autentiseras med hjälp av funktionen för cachelagring. Efterföljande autentiseringsförsök för användaren inom den angivna tids perioden slutförs automatiskt. Cachelagring används främst när lokala system, till exempel VPN, skickar flera verifierings begär Anden medan den första begäran fortfarande pågår. Den här funktionen gör att efterföljande begär Anden kan lyckas automatiskt när användaren har slutfört den första verifieringen.

>[!NOTE]
>Cachelagring-funktionen är inte avsedd att användas för inloggningar till Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Konfigurera cachelagring

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Bläddra till **Azure Active Directory** > **MFA** > **caching-regler**.
3. Välj **Lägg till**.
4. Välj **cache-typ** i list rutan. Ange maximalt antal cache- **sekunder**.
5. Om det behövs väljer du en autentiseringstyp och anger ett program.
6. Välj **Lägg till**.

## <a name="mfa-service-settings"></a>Inställningar för MFA-tjänsten

Inställningar för applösenord, tillförlitliga IP-adresser, verifierings alternativ och kom ihåg Multi-Factor Authentication för Azure Multi-Factor Authentication finns i tjänst inställningar. Du kan komma åt tjänst inställningar från Azure Portal genom att bläddra till **Azure Active Directory** > **MFA** > **komma igång** > **Konfigurera** > **ytterligare molnbaserad MFA-inställningar**.

![Azure Multi-Factor Authentication Service-inställningar](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

## <a name="app-passwords"></a>Applösenord

Vissa program, t. ex. Office 2010 eller tidigare och Apple Mail före iOS 11, stöder inte tvåstegsverifiering. Apparna är inte konfigurerade för att godkänna en andra verifiering. Om du vill använda dessa program kan du dra nytta av funktionen _applösenord_ . Du kan använda ett applösenord i stället för ditt traditionella lösen ord för att tillåta att en app kringgår tvåstegsverifiering och fortsätter att fungera.

Modern autentisering stöds för Microsoft Office 2013-klienter och senare. Office 2013-klienter, inklusive Outlook, stöder moderna autentiseringsprotokoll och kan aktive ras för att fungera med tvåstegsverifiering. När klienten har Aktiver ATS krävs inte applösenord för klienten.

>[!NOTE]
>Applösenord fungerar inte med villkorlig åtkomst baserade Multi-Factor Authentication-principer och modern autentisering.

### <a name="considerations-about-app-passwords"></a>Överväganden om applösenord

När du använder applösenord, bör du tänka på följande viktiga punkter:

* Applösenord anges bara en gång per program. Användarna behöver inte hålla reda på lösen orden eller ange dem varje gång.
* Det faktiska lösen ordet genereras automatiskt och anges inte av användaren. Det automatiskt genererade lösen ordet är svårare för en angripare att gissa sig till och är säkrare.
* Det finns en gräns på 40 lösen ord per användare.
* Program som cachelagrar lösen ord och använder dem i lokala scenarier kan börja fungera eftersom appens lösen ord inte är känt utanför arbets-eller skol kontot. Ett exempel på det här scenariot är Exchange-e-post som är lokalt, men det arkiverade e-postmeddelandet finns i molnet. I det här scenariot fungerar inte samma lösen ord.
* När Multi-Factor Authentication har Aktiver ATS på ett användar konto kan applösenord användas med de flesta klienter som inte är webbläsare, till exempel Outlook och Microsoft Skype för företag. Administrativa åtgärder kan inte utföras med hjälp av applösenord via appar som inte är webb läsar program, till exempel Windows PowerShell. Det går inte att utföra åtgärderna även om användaren har ett administratörs konto. Om du vill köra PowerShell-skript skapar du ett tjänst konto med ett starkt lösen ord och aktiverar inte kontot för tvåstegsverifiering.

>[!WARNING]
>Applösenord fungerar inte i hybrid miljöer där klienter kommunicerar med både lokala och molnbaserade slut punkter för automatisk identifiering. Domän lösen ord krävs för autentisering lokalt. Applösenord krävs för att autentisera med molnet.

### <a name="guidance-for-app-password-names"></a>Vägledning för lösen ords namn för appen

Appens lösen ords namn ska motsvara den enhet där de används. Om du har en bärbar dator som har icke-webbläsarbaserade program som Outlook, Word och Excel, skapar du ett applösenord med namnet **bärbar dator** för dessa appar. Skapa ett annat applösenord med namnet **Desktop** för samma program som körs på din station ära dator.

>[!NOTE]
>Vi rekommenderar att du skapar ett applösenord per enhet, i stället för ett applösenord per program.

### <a name="federated-or-single-sign-on-app-passwords"></a>Applösenord för federerad eller enkel inloggning

Azure AD stöder Federation eller enkel inloggning (SSO) med lokala Windows Server-Active Directory Domain Services (AD DS). Om din organisation är federerad med Azure AD och du använder Azure Multi-Factor Authentication bör du överväga följande punkter om applösenord.

>[!NOTE]
>Följande punkter gäller endast för federerade (SSO)-kunder.

* Applösenord verifieras av Azure AD och kan därför kringgå Federation. Federation används aktivt bara när du konfigurerar applösenord.
* Identitets leverantören (IdP) kontaktas inte för federerade (SSO)-användare, till skillnad från det passiva flödet. Applösenord lagras på arbets-eller skol kontot. Om en användare lämnar företaget kan användarens information flöda till arbets-eller skol kontot med hjälp av **DirSync** i real tid. Det kan ta upp till tre timmar innan kontot kan synkroniseras, vilket kan försena inaktive ring/borttagning av applösenord i Azure AD.
* Lokala klient Access Controls inställningar hanteras inte av appens lösen ord.
* Ingen lokal autentiserings-och gransknings funktion är tillgänglig för användning med funktionen applösenord.
* Vissa avancerade arkitekturer kräver en kombination av autentiseringsuppgifter för tvåstegsverifiering med-klienter. Dessa autentiseringsuppgifter kan omfatta ett arbets-eller skol konto användar namn och lösen ord samt applösenord. Kraven beror på hur autentiseringen utförs. För klienter som autentiseras mot en lokal infrastruktur krävs ett användar namn och lösen ord för arbets-eller skol kontot. För klienter som autentiseras mot Azure AD krävs ett applösenord.

  Anta till exempel att du har följande arkitektur:

  * Din lokala instans av Active Directory är federerad med Azure AD.
  * Du använder Exchange Online.
  * Du använder Skype för företag lokalt.
  * Du använder Azure Multi-Factor Authentication.

  I det här scenariot använder du följande autentiseringsuppgifter:

  * Om du vill logga in på Skype för företag använder du användar namnet och lösen ordet för ditt arbets-eller skol konto.
  * Använd ett applösenord för att få åtkomst till adress boken från en Outlook-klient som ansluter till Exchange Online.

### <a name="allow-users-to-create-app-passwords"></a>Tillåt användare att skapa applösenord

Som standard kan användarna inte skapa applösenord. Funktionen applösenord måste vara aktive rad. Använd följande procedur för att ge användarna möjlighet att skapa applösenord:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multi-Factor Authentication**.
4. Under Multi-Factor Authentication väljer du **tjänst inställningar**.
5. På sidan **tjänst inställningar** väljer du alternativet **Tillåt användare att skapa applösenord för att logga in på icke-webbläsarbaserade appar** .

### <a name="create-app-passwords"></a>Skapa applösenord

Användare kan skapa applösenord under den ursprungliga registreringen. Användaren kan välja att skapa applösenord i slutet av registrerings processen.

Användare kan också skapa applösenord efter registreringen. Mer information och detaljerade anvisningar för dina användare finns i [Vad är app-lösenord i Azure Multi-Factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Funktionen _betrodda IP-adresser_ i Azure Multi-Factor Authentication används av administratörer för en hanterad eller federerad klient. Funktionen hoppar över tvåstegsverifiering för användare som loggar in från företagets intranät. Funktionen är tillgänglig med den fullständiga versionen av Azure Multi-Factor Authentication och inte den kostnads fria versionen för administratörer. Mer information om hur du hämtar den fullständiga versionen av Azure Multi-Factor Authentication finns i [Azure Multi-Factor Authentication](multi-factor-authentication.md).

> [!NOTE]
> MFA-betrodda IP-adresser och namngivna platser för villkorlig åtkomst fungerar bara med IPV4-adresser.

Om din organisation distribuerar NPS-tillägget för att tillhandahålla MFA till lokala program, ser käll-IP-adressen alltid vara den NPS-server som autentiseringen försöker flöda genom.

| Typ av Azure AD-klient | Funktions alternativ för betrodda IP-adresser |
|:--- |:--- |
| Hanterad |**Angivet IP-adressintervall**: Administratörer anger ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från företagets intranät.|
| Federerad |**Alla federerade användare**: Alla federerade användare som loggar in från i organisationen kan kringgå tvåstegsverifiering. Användarna kringgår verifieringen genom att använda ett anspråk som utfärdats av Active Directory Federation Services (AD FS) (AD FS).<br/>**Angivet IP-adressintervall**: Administratörer anger ett intervall med IP-adresser som kan kringgå tvåstegsverifiering för användare som loggar in från företagets intranät. |

De betrodda IP-adresserna kringgår bara Works inifrån företagets intranät. Om du väljer alternativet **alla federerade användare** och en användare loggar in utanför företagets intranät, måste användaren autentisera med hjälp av tvåstegsverifiering. Processen är densamma även om användaren presenterar ett AD FS-anspråk. 

### <a name="end-user-experience-inside-of-corpnet"></a>Slut användar upplevelse i Corpnet

När funktionen betrodda IP-adresser är inaktive rad krävs tvåstegsverifiering för webb läsar flöden. Applösenord krävs för äldre avancerade klient program.

Tvåstegsverifiering krävs *inte* för webb läsar flöden när funktionen betrodda IP-adresser är aktive rad. Applösenord krävs *inte* för äldre avancerade klient program, förutsatt att användaren inte har skapat ett applösenord. När ett applösenord används är lösen ordet obligatoriskt. 

### <a name="end-user-experience-outside-corpnet"></a>Slut användar upplevelse utanför Corpnet

Oberoende av om funktionen för betrodda IP-adresser är aktive rad krävs tvåstegsverifiering för webb läsar flöden. Applösenord krävs för äldre avancerade klient program.

### <a name="enable-named-locations-by-using-conditional-access"></a>Aktivera namngivna platser med hjälp av villkorlig åtkomst

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **villkorlig åtkomst till** > **namngivna platser**.
3. Välj **ny plats**.
4. Ange ett namn på platsen.
5. Välj **Markera som betrodd plats**.
6. Ange IP-intervallet i CIDR-notation, t. ex. **192.168.1.1/24**.
7. Välj **Skapa**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Aktivera funktionen för betrodda IP-adresser med hjälp av villkorlig åtkomst

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **villkorlig åtkomst till** > **namngivna platser**.
3. Välj **Konfigurera MFA-betrodda IP-adresser**.
4. På sidan **tjänst inställningar** under **betrodda IP-adresser**väljer du något av följande två alternativ:

   * **För förfrågningar från federerade användare som kommer från mitt intranät**: Markera kryss rutan om du vill välja det här alternativet. Alla federerade användare som loggar in från företags nätverket kringgår tvåstegsverifiering genom att använda ett anspråk som utfärdas av AD FS. Se till att AD FS har en regel för att lägga till intranät anspråk till lämplig trafik. Om regeln inte finns skapar du följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **För förfrågningar från en bestämd uppsättning offentliga IP-adresser**: Om du vill välja det här alternativet anger du IP-adresserna i text rutan med CIDR-notering.
      * För IP-adresser som ligger inom intervallet xxx. xxx. xxx. 1 till xxx. xxx. xxx. 254 använder du notation som **xxx. xxx. xxx. 0/24**.
      * Använd notation som **xxx.xxx.xxx.xxx/32**för en enskild IP-adress.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser hoppar över tvåstegsverifiering.

5. Välj **Spara**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Aktivera funktionen för betrodda IP-adresser med hjälp av tjänst inställningar

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Azure Active Directory** > **användare**till vänster.
3. Välj **Multi-Factor Authentication**.
4. Under Multi-Factor Authentication väljer du **tjänst inställningar**.
5. På sidan **tjänst inställningar** under **betrodda IP-adresser**väljer du ett (eller båda) av följande två alternativ:

   * **För förfrågningar från federerade användare i mitt intranät**: Markera kryss rutan om du vill välja det här alternativet. Alla federerade användare som loggar in från företags nätverket kringgår tvåstegsverifiering genom att använda ett anspråk som utfärdas av AD FS. Se till att AD FS har en regel för att lägga till intranät anspråk till lämplig trafik. Om regeln inte finns skapar du följande regel i AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **För begär Anden från ett angivet intervall IP-adressundernät**: Om du vill välja det här alternativet anger du IP-adresserna i text rutan med CIDR-notering.
      * För IP-adresser som ligger inom intervallet xxx. xxx. xxx. 1 till xxx. xxx. xxx. 254 använder du notation som **xxx. xxx. xxx. 0/24**.
      * Använd notation som **xxx.xxx.xxx.xxx/32**för en enskild IP-adress.
      * Ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser hoppar över tvåstegsverifiering.

6. Välj **Spara**.

## <a name="verification-methods"></a>Verifierings metoder

Du kan välja de verifierings metoder som är tillgängliga för dina användare. Följande tabell innehåller en kort översikt över metoderna.

När dina användare registrerar sina konton för Azure Multi-Factor Authentication väljer de den önskade verifierings metoden från de alternativ som du har aktiverat. Vägledning för användar registrerings processen finns i [Konfigurera mitt konto för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-first-time.md).

| Metod | Beskrivning |
|:--- |:--- |
| Samtal till telefon |Placerar ett automatiskt röst samtal. Användaren svarar på anropet och trycker på # i telefon tangent bordet för att autentisera. Telefonnumret är inte synkroniserat med lokala Active Directory. |
| Textmeddelande till telefon |Skickar ett textmeddelande som innehåller en verifierings kod. Användaren uppmanas att ange verifierings koden i inloggnings gränssnittet. Den här processen kallas ett enkelriktat SMS. Dubbelriktat SMS innebär att användaren måste använda text för att återställa en viss kod. Dubbelriktat SMS är föråldrat och stöds inte efter 14 november 2018. Användare som har kon figurer ATS för dubbelriktat SMS växlas automatiskt till att _ringa till telefon_ verifieringen vid den tidpunkten.|
| Meddelande via mobilapp |Skickar ett push-meddelande till din telefon eller registrerade enhet. Användaren visar meddelandet och väljer **Verifiera** för att slutföra verifieringen. Microsoft Authenticator-appen är tillgänglig för [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Verifieringskod från mobilapp eller maskinvarutoken |Microsoft Authenticator-appen genererar en ny OATH-verifierings kod var 30: e sekund. Användaren anger verifierings koden i inloggnings gränssnittet. Microsoft Authenticator-appen är tillgänglig för [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)och [iOS](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Aktivera och inaktivera verifierings metoder

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multi-Factor Authentication**.
4. Under Multi-Factor Authentication väljer du **tjänst inställningar**.
5. På sidan **tjänst inställningar** under verifierings **alternativ**väljer/avmarkerar du de metoder som du vill använda för dina användare.
6. Klicka på **Spara**.

Ytterligare information om användningen av autentiseringsmetoder finns i artikeln [Vad är autentiseringsmetoder](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Kom ihåg Multi-Factor Authentication

Funktionen _kom ihåg Multi-Factor Authentication_ för enheter och webbläsare som är betrodda av användaren är en kostnads fri funktion för alla Multi-Factor Authentication-användare. Användare kan kringgå efterföljande verifieringar under ett visst antal dagar efter att de har loggat in på en enhet med hjälp av Multi-Factor Authentication. Funktionen förbättrar användbarhet genom att minimera antalet gånger som en användare måste utföra tvåstegsverifiering på samma enhet.

>[!IMPORTANT]
>Om ett konto eller en enhet komprometteras, kan det påverka säkerheten genom att bli medlem i Multi-Factor Authentication för betrodda enheter. Om ett företags konto blir komprometterat eller om en betrodd enhet tappas bort eller blir stulen, bör du [återställa Multi-Factor Authentication på alla enheter](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>Återställnings åtgärden återkallar betrodd status från alla enheter och användaren måste utföra tvåstegsverifiering igen. Du kan också instruera användarna att återställa Multi-Factor Authentication på sina egna enheter med hjälp av anvisningarna i [Hantera dina inställningar för](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)tvåstegsverifiering.

### <a name="how-the-feature-works"></a>Så här fungerar funktionen

Funktionen kom ihåg Multi-Factor Authentication anger en permanent cookie i webbläsaren när en användare väljer alternativet **fråga inte igen om X dagar** vid inloggning. Användaren ombeds inte igen för Multi-Factor Authentication från samma webbläsare tills cookien upphör att gälla. Om användaren öppnar en annan webbläsare på samma enhet eller rensar sina cookies uppmanas de att verifiera.

Alternativet **fråga inte igen om X dagar** visas inte i program som inte är webbläsare, oavsett om appen stöder modern autentisering eller inte. De här apparna använder _uppdateringstoken_ som ger nya åtkomsttoken varje timma. När en uppdateringstoken verifieras kontrollerar Azure AD att den senaste tvåstegsverifiering har inträffat inom det angivna antalet dagar.

Funktionen minskar antalet autentiseringar i Web Apps, som normalt frågas varje gång. Funktionen ökar antalet autentiseringar för moderna autentiserings klienter som normalt uppmanas var 90: e dag. Kan också öka antalet autentiseringar när de kombineras med villkorliga åtkomst principer.

>[!IMPORTANT]
>Funktionen **kom ihåg Multi-Factor Authentication** är inte kompatibel med funktionen **Håll mig inloggad i** AD FS, när användare utför tvåstegsverifiering för AD FS via Azure Multi-Factor Authentication Server eller en Multi-Factor-faktor från tredje part autentiserings lösning.
>
>Om användarna väljer att **låta mig vara** inloggad på AD FS och även markera enheten som betrodd för Multi-Factor Authentication, kontrol leras inte användaren automatiskt när det **sparade Multi-Factor Authentication** -antalet dagar upphör att gälla. Azure AD begär en ny tvåstegsverifiering, men AD FS returnerar en token med det ursprungliga Multi-Factor Authentication-kravet och-datumet, i stället för att utföra tvåstegsverifiering igen. **Den här reaktionen anger en verifierings slinga mellan Azure AD och AD FS.**
>

### <a name="enable-remember-multi-factor-authentication"></a>Aktivera kom ihåg Multi-Factor Authentication

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Till vänster väljer du **Azure Active Directory** > **användare och grupper** > **alla användare**.
3. Välj **Multi-Factor Authentication**.
4. Under Multi-Factor Authentication väljer du **tjänst inställningar**.
5. På sidan **tjänst inställningar** hanterar du **kom ihåg Multi-Factor Authentication**och väljer alternativet **Tillåt användare att komma ihåg Multi-Factor Authentication på enheter som de litar på** .
6. Ange antalet dagar att tillåta att betrodda enheter kringgår tvåstegsverifiering. Standardvärdet är 14 dagar.
7. Välj **Spara**.

### <a name="mark-a-device-as-trusted"></a>Markera en enhet som betrodd

När du har aktiverat funktionen kom ihåg Multi-Factor Authentication kan användarna Markera en enhet som betrodd när de loggar in genom att välja **fråga inte igen**.

## <a name="next-steps"></a>Nästa steg

[Ändra anpassning av inloggnings sidan för Azure AD](../fundamentals/customize-branding.md)
