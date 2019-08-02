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
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6121ca6c1636c8839110712310a1b94fe7fada49
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619245"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen

Rapporterings arkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – information om användningen av hanterade program och användar inloggnings aktiviteter.
    -  - Gransknings loggar[gransknings loggar](concept-audit-logs.md) innehåller information om system aktivitet för användare och grupp hantering, hanterade program och katalog aktiviteter.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfylld inloggning](concept-risky-sign-ins.md) är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är en legitim ägare till ett användar konto.
    - **Användare som har flaggats för risk** – en [riskfylld användare](concept-user-at-risk.md) är en indikator för ett användar konto som kan ha komprometterats.

I det här avsnittet får du en översikt över inloggnings rapporten.

## <a name="prerequisites"></a>Förutsättningar

### <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
* Användare i rollerna säkerhets administratör, säkerhets läsare och rapport läsare
* Globala administratörer
* Dessutom kan alla användare (icke-administratörer) komma åt sina egna inloggningar 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?
* Din klient organisation måste ha en Azure AD Premium licens kopplad till sig för att kunna se rapporten all inloggnings aktivitet. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Observera att om du inte har några aktivitetsdata före uppgraderingen tar det ett par dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens.

## <a name="sign-ins-report"></a>Rapport över inloggningar

Rapporten användar inloggning innehåller svar på följande frågor:

* Vilket inloggningsmönster har en användare?
* Hur många användare har loggat in under en vecka?
* Vad är status för dessa inloggningar?

Du kan komma åt inloggnings rapporten genom att välja **inloggningar** i avsnittet **aktivitet** på bladet **Azure Active Directory** i [Azure Portal](https://portal.azure.com). Observera att det kan ta upp till två timmar innan några inloggnings poster visas i portalen.

![Inloggningsaktivitet](./media/concept-sign-ins/61.png "inloggningsaktivitet")

> [!IMPORTANT]
> Inloggnings rapporten visar bara de **interaktiva** inloggningarna, det vill säga inloggningar där användaren manuellt loggar in med sitt användar namn och lösen ord. Icke-interaktiva inloggnings program, till exempel tjänst-till-tjänst-autentisering, visas inte i inloggnings rapporten. 

En inloggningslogg har en standardlistvy som visar:

- inloggningsdatum
- den aktuella användaren
- Programmet som användaren har loggat in på
- inloggningsstatus
- status för riskidentifieringen
- status för MFA-kravet (multifaktorautentisering).

![Inloggningsaktivitet](./media/concept-sign-ins/01.png "inloggningsaktivitet")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Inloggningsaktivitet](./media/concept-sign-ins/19.png "inloggningsaktivitet")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Inloggningsaktivitet](./media/concept-sign-ins/02.png "inloggningsaktivitet")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Inloggningsaktivitet](./media/concept-sign-ins/03.png "inloggningsaktivitet")

> [!NOTE]
> Kunder kan nu felsöka principer för villkorlig åtkomst via alla inloggnings rapporter. Genom att klicka på fliken **villkorlig åtkomst** för en inloggnings post kan kunder granska status för villkorlig åtkomst och se information om de principer som tillämpas på inloggningen och resultatet för varje princip.
> Mer information finns i [vanliga frågor och svar om ca-information i alla inloggningar](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrera inloggningsaktivitet

Om du vill begränsa de rapporterade data till en nivå som passar dig kan du filtrera inloggnings data med hjälp av datum fältet som standard filter. Dessutom ger Azure AD dig ett brett utbud av ytterligare filter som du kan ange.

![Inloggningsaktivitet](./media/concept-sign-ins/04.png "inloggningsaktivitet")

Med filtret **Användare** kan du ange namn eller användarhuvudnamn (UPN) för den användare som intresserar dig.

Med filtret **Program** kan du ange namnet på programmet som intresserar dig.

Med filtret **Inloggningsstatus** kan du välja något av följande filter:

- Alla
- Lyckades
- Fel

Med filtret för **villkorlig åtkomst** kan du välja princip status för certifikat utfärdare för inloggningen:

- Alla
- Används inte
- Klart
- Fel

Med filtret **Datum** kan du definiera en tidsram för de data som returneras.  
Möjliga värden är:

- 1 månad
- 7 dagar
- 24 timmar
- Anpassat tidsintervall

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Om du lägger till ytterligare fält i inloggningsvyn läggs de automatiskt till i listan med filter. Om du till exempel lägger till fältet **Klientapp** i listan får du även ett annat filteralternativ som gör att du kan ange följande filter:  
![Inloggningsaktivitet](./media/concept-sign-ins/12.png "inloggningsaktivitet")

- **Webbläsare**  
    Det här filtret visar alla händelser där inloggnings försök utfördes med hjälp av webb läsar flöden.
- **Exchange ActiveSync (stöds)**  
    Det här filtret visar alla inloggnings försök där protokollet Exchange ActiveSync (EAS) har gjorts från plattformar som stöds, t. ex. iOS, Android och Windows Phone.
- **Exchange ActiveSync (stöds inte)**  
    Det här filtret visar alla inloggnings försök där EAS-protokollet har gjorts från plattformar som inte stöds, t. ex. Linux-distributioner.
- **Mobile Apps-och skriv bords klienter** Det här filtret visar alla inloggnings försök som inte använder webb läsar flöden. Detta kan vara mobilappar från vilken plattform som helst med hjälp av valfritt protokoll eller från Skriv bords klient program som Office på Windows eller MacOS.
  
- **Andra klienter**
    - **IMAP**  
        En äldre e-postklient som använder IMAP för att hämta e-post.
    - **MAPI**  
        Office 2013, där ADAL är aktiverat och använder MAPI.
    - **Äldre Office-klienter**  
        Office 2013 i standard konfigurationen där ADAL inte är aktiverat och använder MAPI, eller Office 2016 där ADAL har inaktiverats.
    - **POP**  
        En äldre e-postklient som använder POP3 för att hämta e-post.
    - **SMTP**  
        En äldre e-postklient som använder SMTP för att skicka e-post.

## <a name="download-sign-in-activities"></a>Ladda ned inloggningsaktivitet

Du kan [Hämta inloggnings data](quickstart-download-sign-in-report.md) om du vill arbeta med dem utanför Azure Portal. Om du klickar på **Hämta** får du möjlighet att skapa en CSV-eller JSON-fil med de senaste 250 000 posterna.  

![Ladda ned](./media/concept-sign-ins/71.png "Ladda ned")

> [!IMPORTANT]
> Antalet poster som du kan hämta begränsas av [Azure Active Directory bevarande principer för rapporter](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Genvägar till inloggnings data

Förutom Azure AD tillhandahåller Azure Portal ytterligare start punkter för inloggnings data:

- Översikt över identitetens säkerhets skydd
- Användare
- Grupper
- Företagsprogram

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Användarens inloggnings data i säkerhets skydd för identitet

I den användar inloggnings graf som visas i översikts sidan för **identitetens säkerhets skydd** visas vecko sammanställningar av inloggningar för alla användare under en viss tids period. Standardvärdet för tidsperioden är 30 dagar.

![Inloggningsaktivitet](./media/concept-sign-ins/06.png "inloggningsaktivitet")

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
- Klient
- Location
- IP-adress
- Date
- MFA krävs
- Inloggningsstatus

> [!NOTE]
> IP-adresser utfärdas på ett sådant sätt att det inte finns någon slutgiltig anslutning mellan en IP-adress och där datorn med den adressen är fysiskt placerad. Mappning av IP-adresser är krångligt i det faktum att mobila leverantörer och VPN-nätverk utfärdar IP-adresser från centrala pooler som ofta är mycket från var klient enheten faktiskt används. För närvarande i Azure AD-rapporter är det bästa sättet att konvertera IP-adress till en fysisk plats baserat på spår, register data, omvänd sökning och annan information.

På sidan **Användare** visas en fullständig översikt över alla användarinloggningar om du klickar på **Inloggningar** i avsnittet **Aktivitet**.

![Inloggningsaktivitet](./media/concept-sign-ins/08.png "inloggningsaktivitet")

## <a name="usage-of-managed-applications"></a>Användning av hanterade program

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre främsta programmen i organisationen?
* Jag har nyligen distribuerat ett program. Hur går det för det?

Din startpunkt för denna data är rapporten över de tre främsta programmen i organisationen under de senaste 30 dagarna avsnittet **Översikt**, under **Företagsprogram**.

![Inloggningsaktivitet](./media/concept-sign-ins/10.png "inloggningsaktivitet")

Diagrammets vecko användnings vecko sammanställningar av inloggningar för de tre främsta programmen under en viss tids period. Standardvärdet för tidsperioden är 30 dagar.

![Inloggningsaktivitet](./media/concept-sign-ins/47.png "inloggningsaktivitet")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/concept-sign-ins/single_spp_usage_graph.png "Rapportering")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

![Inloggningsaktivitet](./media/concept-sign-ins/11.png "inloggningsaktivitet")

## <a name="office-365-activity-logs"></a>Office 365-aktivitets loggar

Du kan visa Office 365-aktivitets loggar från [Microsoft 365 administrations Center](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Även om Office 365-aktivitet och Azure AD-aktivitets loggar delar mycket av katalog resurserna, ger endast Microsoft 365 administrations Center en fullständig översikt över aktivitets loggarna för Office 365. 

Du kan också få åtkomst till Office 365-aktivitets loggarna via programmering med hjälp av [office 365 Management-API: er](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

* [Felkoder för inloggnings aktivitet](reference-sign-ins-error-codes.md)
* [Principer för data lagring i Azure AD](reference-reports-data-retention.md)
* [Azure AD-rapportens fördröjning](reference-reports-latencies.md)

