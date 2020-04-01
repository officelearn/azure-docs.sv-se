---
title: Blockera äldre autentisering – Azure Active Directory
description: Lär dig hur du förbättrar din säkerhetsposition genom att blockera äldre autentisering med Azure AD Villkorlig åtkomst.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957aa77e18ea8f910f258d1dc59de0d093b0eab6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476654"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Så här blockerar du äldre autentisering till Azure AD med villkorlig åtkomst   

För att ge användarna enkel åtkomst till dina molnappar stöder Azure Active Directory (Azure AD) ett brett utbud av autentiseringsprotokoll, inklusive äldre autentisering. Äldre protokoll stöder dock inte MFA (Multi factor Authentication). MFA är i många miljöer ett gemensamt krav för att ta itu med identitetsstöld. 

Alex Weinert, director of Identity Security på Microsoft, i sitt blogginlägg den 12 mars 2020 [Nya verktyg för att blockera äldre autentisering i organisationen](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) betonar varför organisationer bör blockera äldre autentisering och vilka ytterligare verktyg Microsoft tillhandahåller för att utföra den här uppgiften:

> För att MFA ska vara effektivt måste du också blockera äldre autentisering. Detta beror på att äldre autentiseringsprotokoll som POP, SMTP, IMAP och MAPI inte kan genomdriva MFA, vilket gör dem till önskade startpunkter för motståndare som attackerar din organisation...
> 
>... Siffrorna för äldre autentisering från en analys av Azure Active Directory-trafik (Azure AD) är skarpa:
> 
> - Mer än 99 procent av lösenordssprayattackerna använder äldre autentiseringsprotokoll
> - Mer än 97 procent av autentiseringsuppgifter fyllningsattacker använder äldre autentisering
> - Azure AD-konton i organisationer som har inaktiverat äldre autentisering upplever 67 procent färre komprometteringar än de där äldre autentisering är aktiverad
>

Om din miljö är redo att blockera äldre autentisering för att förbättra klientens skydd kan du uppnå det här målet med villkorlig åtkomst. I den här artikeln beskrivs hur du kan konfigurera principer för villkorlig åtkomst som blockerar äldre autentisering för din klient.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du är bekant med: 

- De [grundläggande begreppen](overview.md) azure AD villkorlig åtkomst 
- De [bästa metoderna](best-practices.md) för att konfigurera principer för villkorlig åtkomst i Azure-portalen

## <a name="scenario-description"></a>Scenariobeskrivning

Azure AD stöder flera av de mest använda autentiserings- och auktoriseringsprotokollen, inklusive äldre autentisering. Äldre autentisering refererar till protokoll som använder grundläggande autentisering. Vanligtvis kan dessa protokoll inte framtvinga någon typ av andra faktorautentisering. Exempel på appar som baseras på äldre autentisering är:

- Äldre Microsoft Office-appar
- Appar som använder e-postprotokoll som POP, IMAP och SMTP

Autentisering med en faktor (till exempel användarnamn och lösenord) räcker inte i dessa dagar. Lösenord är dåliga eftersom de är lätta att gissa och vi (människor) är dåliga på att välja bra lösenord. Lösenord är också sårbara för en mängd olika attacker som nätfiske och lösenordsspray. En av de enklaste saker du kan göra för att skydda mot lösenordshot är att genomföra MFA. Med MFA, även om en angripare får i besittning av en användares lösenord, är lösenordet inte tillräckligt för att autentisera och komma åt data.

Hur kan du förhindra att appar som använder äldre autentisering kommer åt klientens resurser? Rekommendationen är att bara blockera dem med en princip för villkorlig åtkomst. Om det behövs tillåter du bara vissa användare och specifika nätverksplatser att använda appar som baseras på äldre autentisering.

Principer för villkorlig åtkomst tillämpas efter att den första faktorautentiseringen har slutförts. Villkorlig åtkomst är därför inte avsedd som ett första linjeförsvar för scenarier som DoS-attacker (Denial-of-Service), men kan använda signaler från dessa händelser (till exempel inloggningsrisknivån, platsen för begäran och så vidare) för att fastställa åtkomst.

## <a name="implementation"></a>Implementering

I det här avsnittet beskrivs hur du konfigurerar en princip för villkorlig åtkomst för att blockera äldre autentisering. 

### <a name="legacy-authentication-protocols"></a>Äldre autentiseringsprotokoll

Följande alternativ betraktas som äldre autentiseringsprotokoll

- Autentiserade SMTP - Används av POP och IMAP-klientens för att skicka e-postmeddelanden.
- Automatisk upptäckt - Används av Outlook- och EAS-klienter för att hitta och ansluta till postlådor i Exchange Online.
- Exchange Online PowerShell - Används för att ansluta till Exchange Online med fjärr-PowerShell. Om du blockerar grundläggande autentisering för Exchange Online PowerShell måste du använda Exchange Online PowerShell-modulen för att ansluta. Instruktioner finns i [Anslut till Exchange Online PowerShell med multifaktorautentisering](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Services (EWS) – Ett programmeringsgränssnitt som används av Outlook, Outlook för Mac och appar från tredje part.
- IMAP4 - Används av IMAP-e-postklienter.
- MAPI över HTTP (MAPI/HTTP) - Används av Outlook 2010 och senare.
- OAB (Offline Address Book) – En kopia av adresslistsamlingar som hämtas och används av Outlook.
- Outlook Anywhere (RPC över HTTP) – Används av Outlook 2016 och tidigare.
- Outlook-tjänsten – används av appen E-post och kalender för Windows 10.
- POP3 - Används av POP-e-postklienter.
- Reporting Web Services - Används för att hämta rapportdata i Exchange Online.
- Andra klienter - Andra protokoll som identifierats som använder äldre autentisering.

Mer information om dessa autentiseringsprotokoll och -tjänster finns [i Aktivitetsrapporter för inloggning i Azure Active Directory-portalen](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identifiera äldre autentiseringsanvändning

Innan du kan blockera äldre autentisering i katalogen måste du först förstå om användarna har appar som använder äldre autentisering och hur det påverkar din övergripande katalog. Azure AD-inloggningsloggar kan användas för att förstå om du använder äldre autentisering.

1. Navigera till **Azure Portal** > Azure Active**Directory-inloggningar****Azure Active Directory** > .
1. Lägg till kolumnen Klientapp om den inte visas genom att klicka på **Columns** > **Client App**.
1. **Lägg till filter** > **Klientapp** > välja alla äldre autentiseringsprotokoll och klicka på **Använd**.

Filtrering visar bara inloggningsförsök som har gjorts av äldre autentiseringsprotokoll. Om du klickar på varje enskilt inloggningsförsök visas ytterligare information. Fältet **Klientapp** under fliken **Grundläggande information** anger vilket äldre autentiseringsprotokoll som användes.

Dessa loggar anger vilka användare som fortfarande är beroende av äldre autentisering och vilka program som använder äldre protokoll för att göra autentiseringsbegäranden. För användare som inte visas i dessa loggar och som inte har äldre autentisering implementerar du en princip för villkorlig åtkomst endast för dessa användare.

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering 

I en princip för villkorlig åtkomst kan du ange ett villkor som är kopplat till klientapparna som används för att komma åt dina resurser. Med villkoret för klientappar kan du begränsa omfattningen till appar med äldre autentisering genom att välja **Exchange ActiveSync-klienter** och **Andra klienter** under **Mobilappar och skrivbordsklienter**.

![Övriga klienter](./media/block-legacy-authentication/01.png)

Om du vill blockera åtkomst för dessa appar måste du välja **Blockera åtkomst**.

![Blockera åtkomst](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Välj användare och molnappar

Om du vill blockera äldre autentisering för din organisation tror du förmodligen att du kan åstadkomma detta genom att välja:

- Alla användare
- Alla molnappar
- Blockera åtkomst

![Tilldelningar](./media/block-legacy-authentication/03.png)

Azure har en säkerhetsfunktion som hindrar dig från att skapa en princip som denna eftersom den här konfigurationen bryter mot [de bästa metoderna](best-practices.md) för principer för villkorlig åtkomst.
 
![Principkonfiguration stöds inte](./media/block-legacy-authentication/04.png)

Säkerhetsfunktionen är nödvändig eftersom *blockera alla användare och alla molnappar* har potential att blockera hela organisationen från att logga in på din klientorganisation. Du måste utesluta minst en användare för att uppfylla minimikravet för bästa praxis. Du kan också utesluta en katalogroll.

![Principkonfiguration stöds inte](./media/block-legacy-authentication/05.png)

Du kan uppfylla den här säkerhetsfunktionen genom att utesluta en användare från din policy. Helst bör du definiera några [administrativa konton för nödåtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md) och utesluta dem från din princip.

Om du använder [endast rapportläge](concept-conditional-access-report-only.md) när du aktiverar principen för att blockera äldre autentisering kan din organisation övervaka hur policyn skulle få.

## <a name="policy-deployment"></a>Distribution av princip

Innan du sätter din politik i produktion, ta hand om:
 
- **Tjänstkonton** – Identifiera användarkonton som används som tjänstkonton eller av enheter, till exempel konferensrumstelefoner. Kontrollera att dessa konton har starka lösenord och lägg till dem i en utesluten grupp.
- **Inloggningsrapporter** – Granska inloggningsrapporten och leta efter **annan klienttrafik.** Identifiera toppanvändning och undersöka varför den används. Vanligtvis genereras trafiken av äldre Office-klienter som inte använder modern autentisering eller vissa e-postappar från tredje part. Planera att flytta användningen bort från dessa appar, eller om effekten är låg, meddela användarna att de inte längre kan använda dessa appar.
 
Mer information finns i [Hur ska du distribuera en ny princip?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Det här bör du känna till

Blockera åtkomst med **andra klienter** blockerar också Exchange Online PowerShell och Dynamics 365 med hjälp av grundläggande autentisering.

Konfigurera en princip för **andra klienter** blockerar hela organisationen från vissa klienter som SPConnect. Det här blocket inträffar eftersom äldre klienter autentiserar på oväntade sätt. Problemet gäller inte för större Office-program som de äldre Office-klienterna.

Det kan ta upp till 24 timmar innan policyn träder i kraft.

Du kan välja alla tillgängliga bidragskontroller för villkoret **Övriga klienter.** Dock är slutanvändarens upplevelse alltid densamma - blockerad åtkomst.

Om du blockerar äldre autentisering med villkoret **Övriga klienter** kan du också ange enhetsplattformen och platsvillkoret. Om du till exempel bara vill blockera äldre autentisering för mobila enheter ställer du in **enhetsplattformsvillkoret** genom att välja:

- Android
- iOS
- Windows Phone

![Principkonfiguration stöds inte](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Nästa steg

- [Fastställa påverkan med hjälp av rapportläge med villkorsstyrd åtkomst](howto-conditional-access-report-only.md)
- Om du inte är bekant med att konfigurera principer för villkorlig åtkomst ännu läser du [mfa för specifika appar med Azure Active Directory Villkorlig åtkomst](app-based-mfa.md) till exempel.
- Mer information om modernt autentiseringsstöd finns i [Så här fungerar modern autentisering för Office 2013- och Office 2016-klientappar](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
