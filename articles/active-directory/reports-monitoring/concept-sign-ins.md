---
title: Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till rapporter om inloggningsaktiviteter i Azure Active Directory-portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/28/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 778353621491f912d3237900785e6dee17bf975e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014492"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen

Rapporterings arkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – information om användningen av hanterade program och användar inloggnings aktiviteter.
    - **Gransknings loggar** - [gransknings loggar](concept-audit-logs.md) innehåller information om system aktivitet för användare och grupp hantering, hanterade program och katalog aktiviteter.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfylld inloggning](concept-risky-sign-ins.md) är en indikator för ett inloggnings försök av någon som inte är legitim ägare till ett användar konto.
    - **Användare som har flaggats för risk** – en [riskfylld användare](concept-user-at-risk.md) är en indikator för ett användar konto som kan ha komprometterats.

Den här artikeln ger en översikt över inloggnings rapporten.

## <a name="prerequisites"></a>Krav

### <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?

* Användare i rollerna säkerhets administratör, säkerhets läsare och rapport läsare
* Globala administratörer
* Alla användare (icke-administratörer) kan komma åt sina egna inloggningar 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?

* Din klient organisation måste ha en Azure AD Premium licens kopplad till sig för att kunna se rapporten all inloggnings aktivitet. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Det tar några dagar för data att visas i rapporterna efter att du har uppgraderat till en Premium-licens utan data aktiviteter före uppgraderingen.

## <a name="sign-ins-report"></a>Rapport över inloggningar

Rapporten användar inloggning innehåller svar på följande frågor:

* Vilket inloggningsmönster har en användare?
* Hur många användare har loggat in under en vecka?
* Vad är status för dessa inloggningar?

Börja med [Azure Portal](https://portal.azure.com). Du kommer åt inloggnings rapporten genom att välja **inloggningar**, fortsätta till **övervakningen.** Det kan ta upp till två timmar innan några inloggnings poster visas i portalen.

![Inloggnings aktivitet](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Inloggningsaktivitet")

> [!IMPORTANT]
> Inloggnings rapporten visar bara de **interaktiva** inloggningarna, det vill säga inloggningar där användaren manuellt loggar in med sitt användar namn och lösen ord. Icke-interaktiva inloggnings program, till exempel tjänst-till-tjänst-autentisering, visas inte i inloggnings rapporten. 

En inloggningslogg har en standardlistvy som visar:

- inloggningsdatum
- den aktuella användaren
- Programmet som användaren har loggat in på
- inloggningsstatus
- status för riskidentifieringen
- status för MFA-kravet (multifaktorautentisering).

![Inloggnings aktivitet](./media/concept-sign-ins/sign-in-activity.png "Inloggningsaktivitet")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Inloggnings aktivitet](./media/concept-sign-ins/19.png "Inloggningsaktivitet")

Visar ytterligare fält eller tar bort fält som redan visas.

![Inloggnings aktivitet](./media/concept-sign-ins/02.png "Inloggningsaktivitet")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Inloggnings aktivitet](./media/concept-sign-ins/basic-sign-in.png "Inloggningsaktivitet")

> [!NOTE]
> Kunder kan nu felsöka principer för villkorlig åtkomst via alla inloggnings rapporter. Genom att klicka på fliken **villkorlig åtkomst** för en inloggnings post kan kunder granska status för villkorlig åtkomst och se information om de principer som tillämpas på inloggningen och resultatet för varje princip.
> Mer information finns i [vanliga frågor och svar om ca-information i alla inloggningar](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrera inloggningsaktivitet

Först begränsar du de rapporterade data till en nivå som passar dig. Andra, filtrera inloggnings data med hjälp av datum fältet som standard filter. Azure AD ger dig ett brett utbud av ytterligare filter som du kan ange.

![Inloggnings aktivitet](./media/concept-sign-ins/04.png "Inloggningsaktivitet")

Med filtret **Användare** kan du ange namn eller användarhuvudnamn (UPN) för den användare som intresserar dig.

Med filtret **Program** kan du ange namnet på programmet som intresserar dig.

Med filtret **Inloggningsstatus** kan du välja något av följande filter:

- Alla
- Lyckades
- Fel

Med filtret för **villkorlig åtkomst** kan du välja princip status för certifikat utfärdare för inloggningen:

- Alla
- Används inte
- Lyckades
- Fel

Med filtret **Datum** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- En månad
- 7 dagar
- 24 timmar
- Anpassat tidsintervall

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Om du lägger till ytterligare fält i inloggningsvyn läggs de automatiskt till i listan med filter. Om du till exempel lägger till fältet **Klientapp** i listan får du även ett annat filteralternativ som gör att du kan ange följande filter:  
![Inloggnings aktivitet](./media/concept-sign-ins/12.png "Inloggningsaktivitet")

- **Webbläsare**  
    Det här filtret visar alla händelser där inloggnings försök har gjorts med hjälp av webb läsar flöden.
- **Exchange ActiveSync (stöds)**  
    Det här filtret visar alla inloggnings försök där protokollet Exchange ActiveSync (EAS) har gjorts från plattformar som stöds, till exempel iOS, Android och Windows Phone.
- **Exchange ActiveSync (stöds inte)**  
    Det här filtret visar alla inloggnings försök där EAS-protokollet har gjorts från plattformar som inte stöds, t. ex. Linux-distributioner.
- **Mobile Apps-och skriv bords klienter** Filtret visar alla inloggnings försök som inte använder webb läsar flöden. Till exempel mobila appar från vilken plattform som helst med protokoll eller från Skriv bords klient program som Office på Windows eller MacOS.
  
- **Andra klienter**
    - **IMAP**  
        En äldre e-postklient som använder IMAP för att hämta e-post.
    - **MAPI**  
        Office 2013, där ADAL är aktiverat och använder MAPI.
    - **Gamla Office-klienter**  
        Office 2013 i standard konfigurationen där ADAL inte är aktiverat och använder MAPI, eller Office 2016 där ADAL har inaktiverats.
    - **POP**  
        En äldre e-postklient som använder POP3 för att hämta e-post.
    - **SMTP**  
        En äldre e-postklient som använder SMTP för att skicka e-post.

## <a name="download-sign-in-activities"></a>Ladda ned inloggningsaktivitet

Klicka på **hämtnings** alternativet för att skapa en CSV-eller JSON-fil med de senaste 250 000 posterna. Börja med [att hämta inloggnings data](quickstart-download-sign-in-report.md) om du vill arbeta med dem utanför Azure Portal.  

![Ladda ner](./media/concept-sign-ins/71.png "Ladda ned")

> [!IMPORTANT]
> Antalet poster som du kan hämta begränsas av [Azure Active Directory bevarande principer för rapporter](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Genvägar till inloggnings data

Azure AD och Azure Portal ger båda ytterligare start punkter för inloggnings data:

- Översikt över identitetens säkerhets skydd
- Användare
- Grupper
- Företagsprogram

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Användarens inloggnings data i säkerhets skydd för identitet

Användar inloggnings diagrammet på översikts sidan för **identitetens säkerhets skydd** visar vecko agg regeringar för inloggningar. Standardvärdet för tids perioden är 30 dagar.

![Inloggnings aktivitet](./media/concept-sign-ins/06.png "Inloggningsaktivitet")

När du klickar på en dag i inloggningsdiagrammet visas en översikt över inloggningsaktiviteterna för den dagen.

Det här visas på varje rad i listan med inloggningsaktiviteter:

* Vem har loggat in?
* Vilket program var målet för inloggningen?
* Vilken status har inloggningen?
* Vilken MFA-status har inloggningen?

När du klickar på ett objekt visas mer information om inloggningen:

- Användar-ID
- Användare
- Användarnamn
- Program-ID:t
- Program
- Client
- Plats
- IP-adress
- Date
- MFA krävs
- Inloggningsstatus

> [!NOTE]
> IP-adresser utfärdas på ett sådant sätt att det inte finns någon slutgiltig anslutning mellan en IP-adress och där datorn med den adressen är fysiskt placerad. Mappning av IP-adresser är krångligt i det faktum att mobila leverantörer och VPN-nätverk utfärdar IP-adresser från centrala pooler som ofta är mycket från var klient enheten faktiskt används. För närvarande i Azure AD-rapporter är det bästa sättet att konvertera IP-adress till en fysisk plats baserat på spår, register data, omvänd sökning och annan information.

På sidan **Användare** visas en fullständig översikt över alla användarinloggningar om du klickar på **Inloggningar** i avsnittet **Aktivitet**.

![Inloggnings aktivitet](./media/concept-sign-ins/08.png "Inloggningsaktivitet")

## <a name="usage-of-managed-applications"></a>Användning av hanterade program

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre främsta programmen i organisationen?
* Hur fungerar mitt nyaste program?

Start punkten för dessa data är de tre främsta programmen i din organisation. Informationen finns i rapporten de senaste 30 dagarna i **översikts** avsnittet under **företags program**.

![Inloggnings aktivitet](./media/concept-sign-ins/10.png "Inloggningsaktivitet")

Grafen för app-Usage vecko Visa insamlingar av inloggningar för de tre främsta programmen under en viss tids period. Standardvärdet för tidsperioden är 30 dagar.

![Inloggnings aktivitet](./media/concept-sign-ins/graph-chart.png "Inloggningsaktivitet")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/concept-sign-ins/single-app-usage-graph.png "Rapportering")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

## <a name="office-365-activity-logs"></a>Office 365-aktivitets loggar

Du kan visa Office 365-aktivitets loggar från [Microsoft 365 administrations Center](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Betrakta den punkt som, Office 365-aktivitet och Azure AD-aktivitets loggar som delar ett stort antal katalog resurser. Endast Microsoft 365 administrations Center innehåller en fullständig vy över aktivitets loggarna för Office 365. 

Du kan också få åtkomst till Office 365-aktivitets loggarna via programmering med hjälp av [office 365 Management-API: er](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

* [Felkoder för inloggnings aktivitet](reference-sign-ins-error-codes.md)
* [Principer för data lagring i Azure AD](reference-reports-data-retention.md)
* [Azure AD-rapportens fördröjning](reference-reports-latencies.md)

