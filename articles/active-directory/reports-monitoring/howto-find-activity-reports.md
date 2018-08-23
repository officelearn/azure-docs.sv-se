---
title: Hitta aktivitetsrapporter för Azure Active Directory-användare i Azure-portalen | Microsoft Docs
description: Lär dig var aktivitetsrapporter för Azure Active Directory-användare finns i Azure-portalen.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 182537d6f07b624f2395f591681ed4596579bde0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42059519"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Hitta aktivitetsrapporter i Azure portal

I den här artikeln beskriver vi hur du hittar aktivitetsrapporter för Azure Active Directory-användare i Azure-portalen.

## <a name="activity-and-integrated-app-reports"></a>Aktivitet och integrerad app rapporter

För kontext-baserade rapportering i Azure-portalen, slås befintliga rapporter samman i en enda vy. Ett enda underliggande API: et tillhandahåller data till vyn.

Att se den här vyn på den **Azure Active Directory** bladet under **aktivitet**väljer **granskningsloggar**.

![Granskningsloggar](./media/howto-find-activity-reports/482.png "Granskningsloggar")

Följande rapporter konsolideras i den här vyn:

* Granskningsrapporten
* Lösenordsåterställningsaktivitet
* Lösenordsåterställningsaktivitet för registrering
* Aktiviteter för självbetjäningsgrupper
* Namnändringar för Office 365-grupp
* Kontot etablering aktivitet
* Status för förnyelse av lösenord
* Kontoetableringsfel


Programanvändning rapporten har förbättrats och ingår i den **inloggningar** vy. Att se den här vyn på den **Azure Active Directory** bladet under **aktivitet**väljer **inloggningar**.

![Visa inloggningar](./media/howto-find-activity-reports/483.png "visa inloggningar")

Den **inloggningar** vyn innehåller alla användarinloggningar. Du kan använda den här informationen för att hämta information om användningen. Du kan också visa information om användningen av program i den **företagsprogram** översikt i den **hantera** avsnittet.

![Företagsprogram](./media/howto-find-activity-reports/484.png "företagsprogram")

## <a name="access-a-specific-report"></a>Få åtkomst till en specifik rapport

Även om Azure-portalen erbjuder en enda vy kan kan du också titta på specifika rapporter.

### <a name="audit-logs"></a>Granskningsloggar

Som svar på kundfeedback i Azure-portalen kan du använda avancerade filter för att komma åt de data du vill. Ett filter som du kan använda är en *aktivitetskategori*, som visar en lista över de olika typerna av aktivitet loggar i Azure AD. Du kan välja en kategori för att begränsa resultaten till vad du letar efter.

Till exempel om du är intresserad av att endast aktiviteter relaterade till antalet återställningar av lösenord via självbetjäning kan du kan välja den **Self-service lösenordshantering** kategori. De kategorier som du ser baseras på den resurs som du arbetar i.  

![Kategori alternativen på sidan Filter granskningsloggar](./media/howto-find-activity-reports/06.png "kategori alternativen på sidan filtrera granskningsloggar")

Aktivitetskategorier är:

- Kärnkatalog
- Hantering av lösenord för självbetjäning
- Självbetjäning, grupphantering
- Kontoetablering

### <a name="application-usage"></a>Programanvändning

Visa information om program som körs på för alla appar eller för ett enda program under **aktivitet**väljer **inloggningar**. Om du vill begränsa resultaten kan filtrera du efter användarnamn eller programnamn.

![Filtrera inloggningshändelser sidan](./media/howto-find-activity-reports/07.png "filtrera inloggningshändelser sidan")

### <a name="security-reports"></a>Säkerhetsrapporter

#### <a name="azure-ad-anomalous-activity-reports"></a>Azure AD avvikande aktivitet rapporter

Azure AD avvikande aktivitet säkerhetsrapporter konsolideras så att du får en central vy. Den här vyn visar alla säkerhetsrelaterade riskhändelser att Azure AD kan identifiera och rapportera om.

Följande tabell visar Azure AD avvikande aktivitet säkerhetsrapporter och motsvarande typer av riskhändelser på Azure-portalen.

| Azure AD avvikande Aktivitetsrapport |  Identity protection riskhändelsetyp|
| :--- | :--- |
| Användare med läckta autentiseringsuppgifter | Läckta autentiseringsuppgifter |
| Oregelbunden inloggningsaktivitet | Omöjliga resor till ovanliga platser |
| Inloggningar från potentiellt infekterade enheter | Inloggningar från angripna enheter|
| Inloggningar från okända källor | Inloggningar från anonyma IP-adresser |
| Inloggningar från IP-adresser med misstänkt aktivitet | Inloggningar från IP-adresser med misstänkt aktivitet |
| - | Inloggningar från okända platser |

I följande Azure AD-säkerhetsgrupper för avvikande aktivitet rapporterar inte ingår som riskhändelser på Azure-portalen:

* Inloggningar efter flera fel
* Inloggningar från flera geografiska områden

Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](concept-risk-events.md).  


#### <a name="detected-risk-events"></a>Identifierade riskhändelserna

I Azure-portalen kan du komma åt rapporter om identifierad riskhändelser på den **Azure Active Directory** bladet under **SECURITY**. Identifierade riskhändelserna spåras i följande rapporter:   

- Användare i farozonen
- Riskfyllda inloggningar

![Säkerhetsrapporter](./media/howto-find-activity-reports/04.png "säkerhetsrapporter")

Mer information om säkerhetsrapporter finns:

- [Användare i farozonen i Azure Active Directory-portalen](concept-user-at-risk.md)
- [Rapport för riskfyllda inloggningar i Azure Active Directory-portalen](concept-risky-sign-ins.md)


Visa den **programanvändning** rapport genom att gå de **Azure Active Directory** bladet under **hantera**väljer **företagsprogram**, Välj sedan **inloggningar**.


![Rapporten Enterprise program inloggningar](./media/howto-find-activity-reports/199.png)

## <a name="next-steps"></a>Nästa steg

En översikt över rapportering finns i [Azure Active Directory-rapportering](overview-reports.md).
