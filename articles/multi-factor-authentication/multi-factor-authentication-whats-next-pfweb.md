---
title: Konfigurera Azure MFA | Microsoft Docs
description: "Detta är Azure Multi-Factor authentication-sidan som beskriver vad du kan göra med MFA.  Detta inkluderar rapporter, bedrägerivarning, engångsförbikoppling, anpassade röstmeddelanden som cachelagring, tillförlitliga IP-adresser och app-lösenord."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: a5aa0070f7d64347851a6601c4fc313a31511dd1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Konfigurera Azure Multi-Factor Authentication-inställningar
Den här artikeln hjälper dig att hantera Azure Multi-Factor Authentication nu när du är igång.  Den omfattar olika avsnitt som hjälper dig att få ut mesta möjliga av Azure Multi-Factor Authentication.  Inte alla dessa funktioner är tillgängliga i alla versioner av Azure Multi-Factor Authentication.

>[!NOTE]
>Dessa inställningar är tillgänglig som förhandsversion i Azure-portalen. Dokumentation om hur du hanterar Azure Multi-Factor Authentication-inställningar i preview finns [konfigurerar Azure Multi-Factor Authentication-inställningar – förhandsversion](multi-factor-authentication-whats-next.md).

| Funktion | Beskrivning | 
|:--- |:--- |
| [Bedrägerivarning](#fraud-alert) |Bedrägerivarning kan konfigureras och ställa in så att användarna kan rapportera bedrägliga försöker få åtkomst till sina resurser. |
| [Engångsförbikoppling](#one-time-bypass) |En engångsförbikoppling kan en användare autentiseras en gång genom att ”kringgå” multifaktorautentisering. |
| [Anpassade röstmeddelanden](#custom-voice-messages) |Anpassade röstmeddelanden kan du använda dina egna inspelningar eller helg med multifaktorautentisering. |
| [Cachelagring](#caching-in-azure-multi-factor-authentication) |Cachelagring kan du ange en viss tid tidsperiod så att efterföljande autentiseringsförsök lyckas automatiskt. |
| [Tillförlitliga IP-adresser](#trusted-ips) |Administratörer av en hanterad eller extern klient kan använda betrodda IP-adresser för att kringgå tvåstegsverifiering för användare som loggar in från företagets lokalt intranät. |
| [Applösenord](#app-passwords) |Ett applösenord kan ett program som inte är MFA-medveten hoppa över multifaktorautentisering och fortsätta arbeta. |
| [Kom ihåg Multi-Factor Authentication för sparade enheter och webbläsare](#remember-multi-factor-authentication-for-devices-that-users-trust) |Kan du komma ihåg enheter för ett visst antal dagar efter att en användare har loggat in med MFA. |
| [Valbar verifieringsmetoderna](#selectable-verification-methods) |Kan du välja de autentiseringsmetoder som är tillgängliga för användare att använda. |

## <a name="access-the-azure-mfa-management-portal"></a>Åtkomst till Azure MFA-hanteringsportalen

Funktioner som beskrivs i den här artikeln har konfigurerats i Azure Multi-Factor Authentication-hanteringsportalen. Det finns två sätt att komma åt MFA-hanteringsportalen via den klassiska Azure-portalen. Först är att hantera en leverantör av Multifaktorautent. Andra är via MFA-tjänstinställningar. 

### <a name="use-an-auth-provider"></a>Använd en leverantör av Multifaktorautent

Om du använder en leverantör av Multifaktorautent för förbrukningsbaserad MFA Använd den här metoden för att få åtkomst till hanteringsportalen.

Logga in på den klassiska Azure portalen som administratör för att komma åt via en Multifaktorautentiseringsprovider som Azure MFA-hanteringsportalen och väljer alternativet Active Directory. Klicka på den **Flerfunktionsautentiseringsleverantörer** fliken, Välj din katalog och på den **hantera** längst ned.

### <a name="use-the-mfa-service-settings-page"></a>Sidan Inställningar för MFA 

Om du har en leverantör av Multifaktorautent eller ett Azure MFA, Azure AD Premium eller Enterprise Mobility + Security licens, använder den här metoden för att få åtkomst till inställningssidan för MFA-tjänsten.

Logga in på den klassiska Azure portalen som administratör för att komma åt MFA-hanteringsportalen via inställningssidan för MFA-tjänsten och väljer alternativet Active Directory. Klicka på katalogen och klicka sedan på fliken **Konfigurera**. Välj **Hantera tjänstinställningar** i avsnittet för multifaktorautentisering. Klicka på länken **Gå till portalen** längst ned på sidan för MFA-tjänstinställningar.


## <a name="fraud-alert"></a>Bedrägerivarning
Bedrägerivarning kan konfigureras och ställa in så att användarna kan rapportera bedrägliga försöker få åtkomst till sina resurser.  Användare kan rapportera bedrägeri med mobilappen eller via telefonen.

### <a name="set-up-fraud-alert"></a>Ställ in bedrägerivarning
1. Navigera till MFA-hanteringsportalen per instruktioner överst i den här sidan.
2. Klicka på Azure Multi-Factor Authentication-hanteringsportalen **inställningar** under avsnittet Konfigurera.
3. Under avsnittet bedrägeriförsök för sidan Inställningar för att kontrollera den **Tillåt användare att skicka Bedrägerivarningar** kryssrutan.
4. Välj **spara** tillämpa dina ändringar. 

### <a name="configuration-options"></a>Konfigurationsalternativ

- **Blockera användare när bedrägeri rapporteras** – om en användare rapporter bedrägeri, sitt konto blockeras.
- **Koden till rapporten bedrägeri under inledande hälsning** -användare vanligtvis trycka på # för att bekräfta tvåstegsverifiering. Om de vill rapportera bedrägeri ange de en kod innan du trycker på #. Den här koden är **0** som standard, men du kan anpassa den.

> [!NOTE]
> Microsofts standard rösthälsning Instruera användarna att trycka på 0# för att skicka en bedrägeriavisering om. Om du vill använda en kod än 0, bör du registrera och överföra din egen anpassade rösthälsning med lämpliga instruktioner.

![Bedrägeri aviseringsalternativ – skärmbild](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="how-users-report-fraud"></a>Hur användare rapportera bedrägeri 
Bedrägerivarning rapporteras på två sätt.  Antingen via mobilappen eller via telefon.  

#### <a name="report-fraud-with-the-mobile-app"></a>Rapportera bedrägeri med mobilappen
1. När en kontroll som skickas till din telefon, välja att öppna Microsoft Authenticator-appen.
2. Välj **neka** på meddelandet. 
3. Välj **rapportera bedrägeri**.
4. Stäng appen.

#### <a name="report-fraud-with-a-phone"></a>Rapportera bedrägeri med en telefon
1. När ett verifieringssamtal kommer till din telefon, svara på samtalet.  
2. Rapportera bedrägeri genom att ange bedrägerikoden (standardvärdet är 0) och tecknet #. Du meddelas att en bedrägerivarning har skickats.
3. Avsluta anropet.

### <a name="view-fraud-reports"></a>Visa bedrägeri rapporter
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj Active Directory till vänster.
3. På den översta väljer **Flerfunktionsautentiseringsleverantörer**. Nu visas en lista över dina Flerfunktionsautentiseringsleverantörer.
4. Välj din leverantör av Multifaktorautent och klicka på **hantera** längst ned på sidan. Azure Multi-Factor Authentication-hanteringsportalen öppnas.
5. Klicka på Azure Multi-Factor Authentication-hanteringsportalen, under Visa en rapport **bedrägeriförsök**.
6. Ange datumintervallet som du vill visa i rapporten. Du kan också ange användarnamn, telefonnummer och användarens status.
7. Klicka på **Run** (Kör). Detta öppnar en rapport över bedrägerivarningar. Klicka på **exportera till CSV** om du vill exportera rapporten.

## <a name="one-time-bypass"></a>Engångsförbikoppling
En engångsförbikoppling kan en användare autentiseras en gång utan att utföra tvåstegsverifiering. Förbikopplingen är tillfällig och upphör att gälla efter ett angivet antal sekunder. I situationer där mobilappen eller phone inte tar emot ett meddelande eller telefonsamtal, aktiverar du en engångsförbikoppling så att användaren kan komma åt en resurs.

### <a name="create-a-one-time-bypass"></a>Skapa en engångsförbikoppling
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Navigera till MFA-hanteringsportalen per instruktioner överst i den här sidan.
3. I Azure Multi-Factor Authentication-hanteringsportalen, ser du namnet på din Azure MFA-leverantör eller till vänster med en  **+**  bredvid den, klickar du på den  **+**  finns olika replikeringsgrupper för MFA-Server och Azure-standard-gruppen. Välj lämplig.
4. Under Administration av användaren väljer **Engångsförbikoppling**.
5. På sidan Engångsförbikoppling **nya Engångsförbikoppling**.

  ![Molnet](./media/multi-factor-authentication-whats-next/create1.png)

6. Ange användarnamnet, antalet sekunder som förbikopplingen ska finnas och orsaken till förbikopplingen. Klicka på **kringgå**.
7. Tidsgränsen träder i kraft omedelbart, så att användaren behöver logga in innan engångsförbikopplingen upphör att gälla. 

### <a name="view-the-one-time-bypass-report"></a>Visa rapporten engångsförbikoppling
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj Active Directory till vänster.
3. På den översta väljer **Flerfunktionsautentiseringsleverantörer**. Nu visas en lista över dina Flerfunktionsautentiseringsleverantörer.
4. Välj din leverantör av Multifaktorautent och klicka på **hantera** längst ned på sidan. Azure Multi-Factor Authentication-hanteringsportalen öppnas.
5. Klicka på Azure Multi-Factor Authentication-hanteringsportalen, vänster, under Visa en rapport **Engångsförbikoppling**.
6. Ange datumintervallet som du vill visa i rapporten. Du kan också ange användarnamn, telefonnummer och användarens status.
7. Klicka på **Run** (Kör). Detta öppnar en rapport över förbikopplingar. Klicka på **exportera till CSV** om du vill exportera rapporten.

## <a name="custom-voice-messages"></a>Anpassade röstmeddelanden
Anpassade röstmeddelanden kan du använda dina egna inspelningar eller helg för tvåstegsverifiering. Dessa kan användas som tillägg till eller ersätta Microsoft poster.

Innan du börjar ska du vara medveten om följande:

* Filformat som stöds är .wav eller .mp3.
* Maximal filstorlek är 5 MB.
* Autentiseringsmeddelanden måste vara kortare än 20 sekunder. Något längre tid än det kan leda till verifieringen misslyckas eftersom användaren inte svarar innan meddelandet avslutas orsakar verifieringen timeout.

### <a name="set-up-a-custom-message"></a>Konfigurera ett anpassat meddelande

Det finns två delar att skapa anpassade meddelandet. Du överför meddelandet först och sedan du aktivera den för användarna.

Att överföra din anpassade meddelandet:

1. Skapa ett anpassat meddelande med någon av filformat som stöds.
2. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
3. Navigera till MFA-hanteringsportalen per instruktioner överst i den här sidan.
4. Klicka på Azure Multi-Factor Authentication-hanteringsportalen **röstmeddelanden** under avsnittet Konfigurera.
5. På Konfigurera: röst meddelanden sidan, klickar du på **Nytt röstmeddelande**.
   ![Molnet](./media/multi-factor-authentication-whats-next/custom1.png)
6. På Konfigurera: nya röstmeddelanden sidan, klickar du på **hantera ljudfiler**.
   ![Molnet](./media/multi-factor-authentication-whats-next/custom2.png)
7. På Konfigurera: låter sidan filer genom att klicka på **överför ljudfilen**.
   ![Molnet](./media/multi-factor-authentication-whats-next/custom3.png)
8. På Konfigurera: Överför ljud-fil klickar du på **Bläddra** och navigera till din röstmeddelande klickar du på **öppna**.
9. Lägg till en beskrivning och klicka på **överför**.
10. När det är klart ett meddelande som bekräftar att du har laddats upp filen.

Aktivera meddelandet för dina användare:

1. Klicka på vänster, **röstmeddelanden**.
2. Klicka på under röstmeddelanden **Nytt röstmeddelande**.
3. Välj ett språk språk listrutan.
4. Ange i rutan program om det här meddelandet är för ett visst program.
5. Välj typ av ska ersättas med nya anpassade meddelandet meddelandetypen listrutan.
6. Välj ljudfilen som du överfört ljudfilen listrutan i den första delen.
7. Klicka på **Skapa**. Ett meddelande bekräftar att du har skapat ett röstmeddelande.
    ![Molnet](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Cachelagring i Azure Multi-Factor Authentication
Cachelagring kan du ange en viss tid tidsperiod så att efterföljande autentiseringsförsök inom denna tidsperiod lyckas automatiskt. Det här används främst när lokalt system som VPN skickar flera verifiering begäranden när den första begäranden pågår fortfarande. På så sätt kan efterföljande förfrågningar ska lyckas automatiskt när användaren lyckas första kontroll pågår. 

Cachelagring är inte avsedd att användas för inloggningar till Azure AD.

### <a name="set-up-caching"></a>Konfigurera cachelagring 
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Navigera till MFA-hanteringsportalen per instruktioner överst i den här sidan.
3. Klicka på Azure Multi-Factor Authentication-hanteringsportalen **cachelagring** under avsnittet Konfigurera.
4. Konfigurera cachelagring sidan klickar du på **nytt cacheminne**.
5. Välj typen av cacheminne och cachelagringssekunder. Klicka på **Skapa**.

<center>![Moln](./media/multi-factor-authentication-whats-next/cache.png)</center>

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

### <a name="to-enable-trusted-ips"></a>Så här aktiverar du tillförlitliga IP-adresser
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Gå till sidan Inställningar för MFA per instruktioner i början av den här artikeln.
3. På sidan Inställningar under betrodda IP-adresser, har du två alternativ:
   
   * **För förfrågningar från externa användare som kommer från intranätet** – Markera kryssrutan. Alla externa användare som loggar in från företagsnätverket kringgå tvåstegsverifiering med hjälp av ett anspråk som utfärdats av AD FS.
   * **För begäranden från en viss mängd offentliga IP-adresser** – ange IP-adresser i textrutan med CIDR-notering. Till exempel: xxx.xxx.xxx.0/24 för IP-adresser i intervallet xxx.xxx.xxx.1 – xxx.xxx.xxx.254 eller xxx.xxx.xxx.xxx/32 för en enda IP-adress. Du kan ange upp till 50 IP-adressintervall. Användare som loggar in från dessa IP-adresser kringgå tvåstegsverifiering.
4. Klicka på **Spara**.
5. När uppdateringarna har tillämpats, klickar du på **Stäng**.

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
* När multifaktorautentisering har aktiverats på en användares konto, applösenord kan användas med de flesta icke-webbläsarklienter som Outlook och Lync, men administrativa åtgärder kan utföras med hjälp av applösenord med icke-webbläsarprogram, till exempel Windows PowerShell även om användaren har ett administratörskonto.  Se till att du skapar ett tjänstkonto med ett starkt lösenord för att köra PowerShell-skript och aktivera inte kontot för tvåstegsverifiering.

> [!WARNING]
> Applösenord fungerar inte i hybridmiljöer där klienter kommunicera med både lokalt och molnet slutpunkter för automatisk upptäckt. Detta beror på att domänlösenord krävs för att autentisera lokalt och applösenord krävs för att autentisera med molnet.

### <a name="naming-guidance-for-app-passwords"></a>Riktlinjer för Applösenord för namngivning
Applösenord bör avspegla den enhet som används. Till exempel om du har en bärbar dator som har icke-webbläsarappar, till exempel Outlook, Word och Excel, skapa ett applösenord med namnet bärbar och använda det applösenordet i dessa program. Skapa sedan en annan applösenord med namnet skrivbordet för samma program på din dator. 

Microsoft rekommenderar att skapa ett applösenord per enhet, inte ett applösenord per program.

### <a name="federated-sso-app-passwords"></a>Applösenord för federerade (SSO)
Azure AD stöder federation (enkel inloggning) med lokala Windows Server Active Directory Domain Services (AD DS). Om din organisation är federerat med Azure AD och du kommer att använda Azure Multi-Factor Authentication, är det viktigt att du med följande information om applösenord. Det här avsnittet gäller bara för federerade (SSO)-kunder.

* Applösenord verifieras av Azure AD och därför kringgå federation. Federation används bara aktivt när du ställer in applösenord.
* För federerade (SSO) användare går vi aldrig till identitetsleverantören (IdP) till skillnad från det passiva flödet. Lösenorden lagras i organisations-id. Om användaren lämnar företaget, har denna information kan flöda till organisationens id via DirSync i realtid. Inaktivering/borttagning av konto kan ta upp till tre timmar att synkronisera, vilket fördröjer inaktivering/borttagning av Applösenord i Azure AD.
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

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Gå till sidan Inställningar för MFA per instruktioner i början av den här artikeln.
3. Välj knappen bredvid **låta användarna skapa applösenord för att logga in på icke-webbläsarappar**.

![Skapa Applösenord](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>Skapa applösenord
Användare kan skapa applösenord vid första registreringen. De får ett alternativ i slutet av registreringsprocessen som kan användas för att skapa applösenord.

Användare kan också skapa applösenord efter registreringen genom att ändra inställningarna i Azure-portalen eller Office 365-portalen. Mer information och detaljerade anvisningar för dina användare kan se [vad är applösenord i Azure Multi-Factor Authentication](./end-user/multi-factor-authentication-end-user-app-passwords.md).

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>Kom ihåg Multifaktorautentisering för enheter som har förtroende för användare
Komma ihåg Multifaktorautentisering för enheter och webbläsare att användare förtroende är en kostnadsfri funktion för alla MFA-användare. Du kan ge användarna möjlighet att hoppa över Multifaktorautentisering för ett visst antal dagar efter en lyckad logga in med MFA. Detta kan förbättra användbarhet genom att minimera antalet gånger som en användare kan utföra tvåstegsverifiering på samma enhet.

Men om ett konto eller en enhet har komprometterats bör kan komma ihåg MFA för betrodda enheter påverka säkerheten. Om ett företagskonto har komprometterats eller en betrodd enhet tappas bort eller blir stulen, bör du [Återställ Multifaktorautentisering på alla enheter](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user). Återkallar den här åtgärden betrodda status från alla enheter och användaren krävs för att utföra tvåstegsverifiering igen. Du kan också instruera användarna att återställa MFA på sina egna enheter med instruktionerna i [hantera inställningar för tvåstegsverifiering](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)

### <a name="how-it-works"></a>Hur det fungerar

Kom ihåg Multi-Factor Authentication fungerar genom att ange beständiga cookies i webbläsaren när en användare checkar den ”fråga inte igen för **X** dagar” kryssrutan vid inloggning. Användaren ombeds inte för MFA igen från den Browser tills cookien förfaller. Om användaren startar en annan webbläsare på samma enhet eller rensar sina cookies, uppmanas de att verifiera igen. 

Den ”fråga inte igen för **X** dagar” kryssrutan inte visas i icke-webbläsarappar, oavsett om de stöder modern autentisering. De här apparna använda uppdaterings-tokens som ger nya åtkomsttoken varje timme. När en uppdateringstoken är validerade, Azure AD-kontroller att den senaste gången tvåstegsverifiering utfördes var inom det konfigurerade antalet dagar. 

Därför kan komma ihåg Multifaktorautentisering på betrodda enheter minskar antalet autentiseringar i webbprogram (som normalt fråga varje gång) men ökar antalet autentiseringar för modern autentisering klienter (vilket normalt fråga efter 90 dagar).

> [!NOTE]
>Den här funktionen är inte kompatibel med funktionen ”jag vill förbli inloggad” i AD FS när användare utför tvåstegsverifiering för AD FS via Azure MFA-Server eller en MFA-lösning från tredje part. Om användarna Markera ”jag vill förbli inloggad” på AD FS och även markera sin enhet som betrodd för MFA, kan de inte verifiera efter ”Kom ihåg MFA” antalet dagar upphör att gälla. Azure AD begär en ny tvåstegsverifiering, men AD FS returnerar en token med den ursprungliga MFA anspråk och datumet i stället för att utföra tvåstegsverifiering igen. Anger av en kontroll skapas mellan Azure AD och AD FS. 

### <a name="enable-remember-multi-factor-authentication"></a>Aktivera Kom ihåg multifaktorautentisering
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Gå till sidan Inställningar för MFA per instruktioner i början av den här artikeln.
3. På sidan Inställningar under hantera användarinställningar för enheten, kontrollera den **Tillåt användare att komma ihåg multifaktorautentisering på enheter de litar** rutan.
   ![Kom ihåg enheter](./media/multi-factor-authentication-whats-next/remember.png)
4. Ange antalet dagar som du vill tillåta att kringgå tvåstegsverifiering betrodda enheter. Standardvärdet är 14 dagar.
5. Klicka på **Spara**.
6. Klicka på **Stäng**.

### <a name="mark-a-device-as-trusted"></a>Markera en enhet som betrodd

När du aktiverar den här funktionen kan användare kan markera en enhet som betrodda när de loggar in genom att kontrollera **fråga inte igen**.

![Fråga inte igen – skärmbild](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>Valbar verifieringsmetoderna
Du kan välja vilka verifieringsmetoderna är tillgängliga för användarna. Tabellen nedan innehåller en kort översikt över varje metod.

När användarna registrerar sina konton för MFA, kan de välja sin önskade verifieringsmetod utanför de alternativ som du har aktiverat. Vägledning för sina registreringen ingår i [Konfigurera mitt konto för tvåstegsverifiering](multi-factor-authentication-end-user-first-time.md)

| Metod | Beskrivning |
|:--- |:--- |
| Samtal till telefon |Placerar en automatiserad röstsamtal. Användaren svara och trycka på #-tangenten för att autentisera. Det här telefonnumret har inte synkroniserats till lokala Active Directory. |
| Textmeddelande till telefon |Skickar ett SMS med verifieringskoden. Användaren uppmanas att antingen svara på textmeddelandet med verifieringskoden eller ange verifieringskoden i inloggning-gränssnittet. |
| Meddelande via mobilapp |Skickar ett push-meddelande till din telefon eller en registrerad enhet. Användaren ser meddelandet och väljer **Kontrollera** att slutföra verifieringen. <br>Microsoft Authenticator-appen är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), och [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Verifieringskod från mobilapp |Microsoft Authenticator-appen genererar en ny OATH-Verifieringskod var 30: e sekund. Användaren anger den här koden i gränssnittet för inloggning.<br>Microsoft Authenticator-appen är tillgänglig för [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), och [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="how-to-enabledisable-authentication-methods"></a>Hur du aktiverar/inaktiverar autentiseringsmetoder
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Gå till sidan Inställningar för MFA per instruktioner i början av den här artikeln.
3. På sidan Inställningar för tjänsten, under Alternativ för verifiering, Markera/avmarkera de alternativ som du vill använda.
   ![Alternativ för verifiering](./media/multi-factor-authentication-whats-next/authmethods.png)
4. Klicka på **Spara**.
5. Klicka på **Stäng**.

