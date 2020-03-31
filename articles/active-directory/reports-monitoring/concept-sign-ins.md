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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2910933e2c57a8bc80a220726462b02915c4a8eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246525"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen

Rapporteringsarkitekturen i Azure Active Directory (Azure AD) består av följande komponenter:

- **Aktivitet** 
    - **Inloggningar** – Information om användningen av hanterade program och användarloggningsaktiviteter.
    - **Granskningsloggar** - [Granskningsloggar](concept-audit-logs.md) ger information om systemaktivitet om användare och grupphantering, hanterade program och katalogaktiviteter.
- **Säkerhet** 
    - **Riskfyllda inloggningar** – En [riskfylld inloggning](concept-risky-sign-ins.md) är en indikator för ett inloggningsförsök av någon som inte är den legitima ägaren av ett användarkonto.
    - **Användare som flaggats för risk** – En [riskfylld användare](concept-user-at-risk.md) är en indikator för ett användarkonto som kan ha komprometterats.

I den här artikeln får du en översikt över inloggningsrapporten.

## <a name="prerequisites"></a>Krav

### <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?

* Användare i rollerna Säkerhetsadministratör, Säkerhetsläsare, Global Reader och Rapportläsare
* Globala administratörer
* Alla användare (icke-administratörer) kan komma åt sina egna inloggningar 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?

- Aktivitetsrapporten för inloggning är tillgänglig i [alla versioner av Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data).

- Om du vill komma åt inloggningsdata med ett API måste din klient ha en [Azure Active Directory Premium-licens](../fundamentals/active-directory-get-started-premium.md) associerad med sig.



## <a name="sign-ins-report"></a>Rapport över inloggningar

Rapporten för användarloggningar ger svar på följande frågor:

* Vilket inloggningsmönster har en användare?
* Hur många användare har loggat in under en vecka?
* Vad är status för dessa inloggningar?

På [Azure-portalmenyn](https://portal.azure.com) väljer du **Azure Active Directory**eller söker efter och väljer Azure Active **Directory** på valfri sida.

![Välj Azure Active Directory](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

Under **Övervakning**väljer du **Inloggningar** för att öppna [rapporten Inloggningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns).

![Inloggningsaktivitet](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "Inloggningsaktivitet")

Det kan ta upp till två timmar innan vissa inloggningsposter visas i portalen.

> [!IMPORTANT]
> Inloggningsrapporten visar bara interaktiva **interactive** inloggningar, det vill veta logga in där en användare manuellt loggar in med sitt användarnamn och lösenord. Icke-interaktiva inloggningar, till exempel autentisering mellan tjänst och tjänst, visas inte i inloggningsrapporten. 

En inloggningslogg har en standardlistvy som visar:

- inloggningsdatum
- den aktuella användaren
- Programmet som användaren har loggat in på
- inloggningsstatus
- status för riskidentifieringen
- status för MFA-kravet (multifaktorautentisering).

![Inloggningsaktivitet](./media/concept-sign-ins/sign-in-activity.png "Inloggningsaktivitet")

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Inloggningsaktivitet](./media/concept-sign-ins/19.png "Inloggningsaktivitet")

I dialogrutan **Kolumner** får du tillgång till de valbara attributen. I en inloggningsrapport kan du inte ha fält som har mer än ett värde för en viss inloggningsbegäran som kolumn. Detta gäller till exempel för autentiseringsinformation, villkorade åtkomstdata och nätverksplats.   

![Inloggningsaktivitet](./media/concept-sign-ins/columns.png "Inloggningsaktivitet")

Välj ett objekt i listvyn om du vill ha mer detaljerad information.

![Inloggningsaktivitet](./media/concept-sign-ins/basic-sign-in.png "Inloggningsaktivitet")

> [!NOTE]
> Kunder kan nu felsöka principer för villkorlig åtkomst genom alla inloggningsrapporter. Genom att klicka på fliken **Villkorlig åtkomst** för en inloggningspost kan kunderna granska statusen Villkorlig åtkomst och ange information om de principer som tillämpas på inloggningen och resultatet för varje princip.
> Mer information finns i vanliga [frågor om CA-information i alla inloggningar](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Filtrera inloggningsaktivitet

Först begränsa de rapporterade data till en nivå som fungerar för dig. För det andra filtrerar du inloggningsdata med datumfältet som standardfilter. Azure AD ger dig ett brett utbud av ytterligare filter som du kan ställa in:

![Inloggningsaktivitet](./media/concept-sign-ins/04.png "Inloggningsaktivitet")

**Begär ID** - ID för den begäran du bryr dig om.

**Användare** - Namnet eller användarens huvudnamn (UPN) för den användare du bryr dig om.

**Program** - Namnet på målprogrammet.
 
**Status** - Inloggningsstatus du bryr dig om:

- Lyckades

- Fel

- Avbruten


**IP-adress** - IP-adressen för den enhet som används för att ansluta till din klientorganisation.

**Platsen** - Platsen anslutningen initierades från:

- Ort

- Stat /Provins

- Land/region


**Resurs** - Namnet på den tjänst som används för inloggningen.


**Resurs-ID** - ID för den tjänst som används för inloggningen.


**Klientapp** - Den typ av klientapp som används för att ansluta till din klient:

![Filter för klientapp](./media/concept-sign-ins/client-app-filter.png)


|Namn|Modern autentisering|Beskrivning|
|---|:-:|---|
|Autentiserat SMTP| |Används av POP och IMAP-klientens för att skicka e-postmeddelanden.|
|Autodiscover| |Används av Outlook- och EAS-klienter för att hitta och ansluta till postlådor i Exchange Online.|
|Exchange ActiveSync| |Det här filtret visar alla inloggningsförsök där EAS-protokollet har gjorts.|
|Webbläsare|![Markera](./media/concept-sign-ins/check.png)|Visar alla inloggningsförsök från användare som använder webbläsare|
|Exchange ActiveSync| | Visar alla inloggningsförsök från användare med klientappar med Exchange ActiceSync för att ansluta till Exchange Online|
|Exchange Online PowerShell| |Används för att ansluta till Exchange Online med fjärr-PowerShell. Om du blockerar grundläggande autentisering för Exchange Online PowerShell måste du använda Exchange Online PowerShell-modulen för att ansluta. Instruktioner finns i [Anslut till Exchange Online PowerShell med multifaktorautentisering](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).|
|Exchange-webbtjänster| |Ett programmeringsgränssnitt som används av Outlook, Outlook för Mac och appar från tredje part.|
|IMAP4| |En äldre e-postklient som använder IMAP för att hämta e-post.|
|MAPI över HTTP| |Används av Outlook 2010 och senare.|
|Mobilappar och skrivbordsklienter|![Markera](./media/concept-sign-ins/check.png)|Visar alla inloggningsförsök från användare som använder mobilappar och skrivbordsklienter.|
|Offlineadressbok| |En kopia av adresslistsamlingar som hämtas och används av Outlook.|
|Outlook var som helst (RPC över HTTP)| |Används av Outlook 2016 och tidigare.|
|Outlook-tjänsten| |Används av appen E-post och Kalender för Windows 10.|
|POP3| |En äldre e-postklient som använder POP3 för att hämta e-post.|
|Rapportera webbtjänster| |Används för att hämta rapportdata i Exchange Online.|
|Övriga klienter| |Visar alla inloggningsförsök från användare där klientappen inte ingår eller är okänd.|



**Operativsystem** - Operativsystemet som körs på enheten använde inloggning till din klient. 


**Enhetswebbläsare** - Om anslutningen initierades från en webbläsare kan du i det här fältet filtrera efter webbläsarnamn.


**Korrelations-ID** - Aktivitetens korrelations-ID.




**Villkorlig åtkomst** - Status för de tillämpade reglerna för villkorlig åtkomst

- **Används inte**: Ingen princip tillämpas på användaren och programmet under inloggningen.

- **Framgång**: En eller flera principer för villkorlig åtkomst som tillämpas på användaren och programmet (men inte nödvändigtvis de andra villkoren) under inloggningen. 

- **Fel:** En eller flera principer för villkorlig åtkomst tillämpades och uppfylldes inte under inloggningen.









## <a name="download-sign-in-activities"></a>Ladda ned inloggningsaktivitet

Klicka på alternativet **Hämta** om du vill skapa en CSV- eller JSON-fil för de senaste 250 000 posterna. Börja med [att hämta inloggningsdata](quickstart-download-sign-in-report.md) om du vill arbeta med dem utanför Azure-portalen.  

![Ladda ned](./media/concept-sign-ins/71.png "Ladda ned")

> [!IMPORTANT]
> Antalet poster som du kan hämta begränsas av [Azure Active Directory-rapportlagringsprinciperna](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Inloggningar datagenvägar

Azure AD och Azure-portalen ger dig båda ytterligare startpunkter till inloggningsdata:

- Översikt över identitetsskydd
- Användare
- Grupper
- Företagsprogram

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Användare loggar in data i identitetsskydd

I diagrammet för användarloggning i översiktssidan för **identitetsskydd** visas veckovisa aggregeringar av inloggningar. Standardinställningen för tidsperioden är 30 dagar.

![Inloggningsaktivitet](./media/concept-sign-ins/06.png "Inloggningsaktivitet")

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
- Location
- IP-adress
- Datum
- MFA krävs
- Inloggningsstatus

> [!NOTE]
> IP-adresser utfärdas på ett sådant sätt att det inte finns något definitivt samband mellan en IP-adress och där datorn med den adressen är fysiskt belägen. Kartläggning IP-adresser kompliceras av det faktum att mobiloperatörer och VPN utfärda IP-adresser från centrala pooler som ofta är mycket långt från där klientenheten faktiskt används. För närvarande i Azure AD-rapporter, konvertera IP-adress till en fysisk plats är en bästa insats baserat på spår, registerdata, omvänd slå upp-enheter och annan information.

På sidan **Användare** visas en fullständig översikt över alla användarinloggningar om du klickar på **Inloggningar** i avsnittet **Aktivitet**.

![Inloggningsaktivitet](./media/concept-sign-ins/08.png "Inloggningsaktivitet")

## <a name="usage-of-managed-applications"></a>Användning av hanterade program

Med en programcentrerad vy över dina inloggningsuppgifter kan du få svar på frågor som:

* Vem använder mina program?
* Vilka är de tre bästa programmen i din organisation?
* Hur går det med min senaste ansökan?

Startpunkten för dessa data är de tre bästa programmen i organisationen. Data finns i rapporten de senaste 30 dagarna i avsnittet **Översikt** under **Företagsprogram**.

![Inloggningsaktivitet](./media/concept-sign-ins/10.png "Inloggningsaktivitet")

Appanvändningsdiagrammen varje vecka aggregeringar av inloggningar för dina tre bästa program under en viss tidsperiod. Standardvärdet för tidsperioden är 30 dagar.

![Inloggningsaktivitet](./media/concept-sign-ins/graph-chart.png "Inloggningsaktivitet")

Om du vill kan du ange att fokusera på ett visst program.

![Rapportering](./media/concept-sign-ins/single-app-usage-graph.png "Rapportering")

När du klickar på en dag i programanvändningsdiagrammet kan du få en detaljerad lista över inloggningsaktiviteterna.

Alternativet **Inloggningar** ger dig en fullständig översikt över alla inloggningshändelser för dina program.

## <a name="office-365-activity-logs"></a>Aktivitetsloggar för Office 365

Du kan visa Aktivitetsloggar för Office 365 från [administrationscentret för Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Tänk på den punkt som, Office 365 aktivitet och Azure AD aktivitetsloggar delar ett betydande antal av katalogresurser. Endast Microsoft 365-administrationscentret ger en fullständig bild av office 365-aktivitetsloggarna. 

Du kan också komma åt Office 365-aktivitetsloggarna programmässigt med hjälp av [Api:erna för Office 365-hantering](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Nästa steg

* [Felkoder för inloggningsaktivitetsrapport](reference-sign-ins-error-codes.md)
* [Azure AD-principer för lagring av data](reference-reports-data-retention.md)
* [Svarstider för Azure AD-rapport](reference-reports-latencies.md)

