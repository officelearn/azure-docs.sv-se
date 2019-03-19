---
title: Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen | Microsoft Docs
description: Introduktion till rapporter om inloggningsaktiviteter i Azure Active Directory-portalen
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b958a8d7aa8566c62d5fdaf6b5b51064e4e5123
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183755"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen

Rapporteringsarkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – Information om användningen av hanterade program och logga in användaraktiviteter.
    - **Granskningsloggar** - [granskningsloggar](concept-audit-logs.md) ger information om användare och grupphantering, hanterade program och katalogaktiviteter systemaktivitet.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – en [riskfyllda inloggningen](concept-risky-sign-ins.md) indikerar en inloggningsförsök som kan ha utförts av någon som inte är tillförlitligt ägare för ett användarkonto.
    - **Användare som har flaggats för risk** – en [riskfylld användare](concept-user-at-risk.md) är en indikator för ett användarkonto som kan ha komprometterats.

Det här avsnittet ger en översikt över rapporten inloggningar.

## <a name="prerequisites"></a>Förutsättningar

### <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
* Användare i rollerna säkerhetsadministratör, Säkerhetsläsare och rapportläsare
* Globala administratörer
* Dessutom kan alla användare (icke-administratörer) kan komma åt sina egna inloggningar 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?
* Din klient måste ha en Azure AD Premium-licens som är associerade med den för att visa inloggningar helt upp i rapporten. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Observera att om du inte har några aktivitetsdata före uppgraderingen tar det ett par dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens.

## <a name="sign-ins-report"></a>Rapport över inloggningar

Användaren loggar in rapporten innehåller svar på följande frågor:

* Vilket inloggningsmönster har en användare?
* Hur många användare har loggat in under en vecka?
* Vad är status för dessa inloggningar?

Du kan komma åt rapporten inloggningar genom att välja **inloggningar** i den **aktivitet** delen av den **Azure Active Directory** -bladet i den [Azure-portalen](https://portal.azure.com). Observera att det kan ta upp till två timmar för vissa poster visas i portalen.

![Inloggningsaktivitet](./media/concept-sign-ins/61.png "inloggningsaktivitet")

> [!IMPORTANT]
> Inloggningar rapporterar endast visar de **interaktiva** inloggningar, som är, inloggningar, där en användare manuellt loggar in med sitt användarnamn och lösenord. Icke-interaktiva inloggningar, till exempel tjänst-till-tjänst-autentisering, visas inte i rapporten inloggningar. 

En inloggningslogg har en standardlistvy som visar:

- inloggningsdatum
- den aktuella användaren
- programmet som användaren har loggat in i
- inloggningsstatus
- status för riskidentifieringen
- status för MFA-kravet (multifaktorautentisering).

![Inloggningsaktivitet](./media/concept-sign-ins/01.png "inloggningsaktivitet")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Inloggningsaktivitet](./media/concept-sign-ins/19.png "inloggningsaktivitet")

På så sätt kan du visa ytterligare fält eller ta bort fält som redan visas.

![Inloggningsaktivitet](./media/concept-sign-ins/02.png "inloggningsaktivitet")

Markera ett objekt i listvyn för att få mer detaljerad information.

![Inloggningsaktivitet](./media/concept-sign-ins/03.png "inloggningsaktivitet")

> [!NOTE]
> Kunder kan nu felsöka principer för villkorlig åtkomst via alla inloggning rapporter. Genom att klicka på den **villkorlig åtkomst** fliken för en inloggning post kan kunder läsa statusen för villkorlig åtkomst och genomgång av information om de principer som tillämpas på inloggningen och resultatet för varje princip.
> Mer information finns i den [vanliga frågor och svar om CA-information i alla inloggningar](reports-faq.md#conditional-access).

![Inloggningsaktivitet](./media/concept-sign-ins/ConditionalAccess.png "inloggningsaktivitet")


## <a name="filter-sign-in-activities"></a>Filtrera inloggningsaktivitet

Om du vill begränsa vilka data som rapporteras till en nivå som passar dig kan du filtrera inloggningsdata med hjälp av följande standardfält:

- Användare
- Program
- Inloggningsstatus
- Villkorlig åtkomst
- Date

![Inloggningsaktivitet](./media/concept-sign-ins/04.png "inloggningsaktivitet")

Med filtret **Användare** kan du ange namn eller användarhuvudnamn (UPN) för den användare som intresserar dig.

Med filtret **Program** kan du ange namnet på programmet som intresserar dig.

Med filtret **Inloggningsstatus** kan du välja något av följande filter:

- Alla
- Lyckades
- Fel

Den **villkorlig åtkomst** filter kan du välja CA Principstatus för att logga in:

- Alla
- Används inte
- Lyckades
- Fel

Med filtret **Datum** kan du definiera en tidsram för de data som returneras.  
Möjliga värden:

- 1 månad
- 7 dagar
- 24 timmar
- Anpassat tidsintervall

När du väljer en anpassad tidsram kan du konfigurera en starttid och en sluttid.

Om du lägger till ytterligare fält i inloggningsvyn läggs de automatiskt till i listan med filter. Om du till exempel lägger till fältet **Klientapp** i listan får du även ett annat filteralternativ som gör att du kan ange följande filter:

- Webbläsare      
- Exchange ActiveSync (stöds)               
- Exchange ActiveSync (stöds inte)
- Övriga klienter               
    - IMAP
    - MAPI
    - Äldre Office-klienter
    - POP
    - SMTP


![Inloggningsaktivitet](./media/concept-sign-ins/12.png "inloggningsaktivitet")


## <a name="download-sign-in-activities"></a>Ladda ned inloggningsaktivitet

Du kan [ladda ned inloggningsdata](quickstart-download-sign-in-report.md) om du vill arbeta med dem utanför Azure-portalen. Klicka på **hämta** ger dig möjlighet att skapa en CSV- eller JSCON fil för de senaste 250 000 posterna.  

![Ladda ned](./media/concept-sign-ins/71.png "Ladda ned")

> [!IMPORTANT]
> Antalet poster som du kan ladda ned är begränsad av den [rapportkvarhållningsregler i Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Inloggningar data genvägar

Förutom att Azure AD ger Azure-portalen dig ytterligare startpunkter för inloggningsdata:

- Identity protection Säkerhetsöversikt
- Användare
- Grupper
- Företagsprogram

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Inloggningsdata för användare i Identitetssäkerhet och skydd

Användaren loggar in diagrammet i det **Identitetssäkerhet och skydd** översiktssida visar veckovisa sammanställningar av inloggningar för alla användare i en viss tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

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
- Client
- Plats
- IP-adress
- Date
- MFA krävs
- Inloggningsstatus

> [!NOTE]
> IP-adresser utfärdas så att det finns ingen slutgiltiga koppling mellan en IP-adress och var datorn med den här adressen är fysiskt. Mappning av IP-adresser är komplicerat av leverantörer av mobil- och VPN-anslutningar att utfärda IP-adresser från central pooler som är ofta mycket är långt från där klientenheten faktiskt används. För närvarande i Azure AD-rapporter är konverterar IP-adress till en fysisk plats en bästa prestanda baserat på spårningar, registerdata, omvänd sökningar och annan information.

På sidan **Användare** visas en fullständig översikt över alla användarinloggningar om du klickar på **Inloggningar** i avsnittet **Aktivitet**.

![Inloggningsaktivitet](./media/concept-sign-ins/08.png "inloggningsaktivitet")

## <a name="usage-of-managed-applications"></a>Användning av hanterade program

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre främsta programmen i organisationen?
* Jag har nyligen distribuerat ett program. Hur går det för det?

Din startpunkt för denna data är rapporten över de tre främsta programmen i organisationen under de senaste 30 dagarna avsnittet **Översikt**, under **Företagsprogram**.

![Inloggningsaktivitet](./media/concept-sign-ins/10.png "inloggningsaktivitet")

Diagram över programanvändning visar veckovisa sammanställning av inloggningar för dina tre främsta program under en given tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Inloggningsaktivitet](./media/concept-sign-ins/47.png "inloggningsaktivitet")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/concept-sign-ins/single_spp_usage_graph.png "Rapportering")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

![Inloggningsaktivitet](./media/concept-sign-ins/11.png "inloggningsaktivitet")

## <a name="office-365-activity-logs"></a>Office 365-aktivitetsloggar

Du kan visa Office 365-aktivitetsloggar från den [Microsoft 365 Administrationscenter](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Även om Office 365-aktivitet och Azure AD delar en massa directory-resurser, endast Microsoft 365 Administrationscenter ger en fullständig överblick över aktivitetsloggarna i Office 365. 

Du kan också komma åt Office 365-aktivitetsloggar programmässigt med hjälp av den [API: er för Office 365-Management](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

* [Felkoder för inloggningsaktivitet rapport](reference-sign-ins-error-codes.md)
* [Azure AD-datalagringsprinciper](reference-reports-data-retention.md)
* [Rapportsvarstider i Azure AD](reference-reports-latencies.md)

