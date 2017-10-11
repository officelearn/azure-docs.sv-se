---
title: Hitta aktivitetsrapporter i Azure portal | Microsoft Docs
description: "Lär dig att hitta Azure Active Directory aktivitetsrapporter i Azure-portalen."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f1875582476c3817b9eb0082b6548cc15043cb98
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Hitta aktivitetsrapporter i Azure-portalen

Om du flyttar från den klassiska Azure-portalen till Azure portal, får du en ny titt på Azure Active Directory (AD Azure) aktivitetsloggar. I en senaste [blogginlägget](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/), förklarar vi hur du kan se aktiviteten loggar i kontexten för den resurs som du arbetar med i Azure-portalen. I den här artikeln beskrivs hur du hittar rapporter som du använde i den klassiska Azure-portalen i Azure-portalen.

## <a name="whats-new"></a>Nyheter

Rapporter i den klassiska Azure-portalen är uppdelade i kategorier:

1.  Säkerhetsrapporter
2.  Aktivitetsrapporter
3.  Integrerad apprapporter

### <a name="activity-and-integrated-app-reports"></a>Aktiviteten och integrerad apprapporter

För sammanhangsberoende rapportering i Azure-portalen, kombineras befintliga rapporter till en enda vy. En enda underliggande API ger data till vyn.

Visa den här vyn på den **Azure Active Directory** bladet under **AKTIVITETEN**väljer **granskningsloggar**.

![Granskningsloggar](./media/active-directory-reporting-migration/482.png "Granskningsloggar")

Följande rapporter konsolideras i den här vyn:

-   Granska rapporten
-   Lösenordsåterställningsaktivitet
-   Lösenordsåterställningsaktivitet för registrering
-   Självbetjäning grupper aktivitet
-   Ändringar av Office365 namn
-   Kontot etablering aktivitet
-   Status för förnyelse av lösenord
-   Kontoetableringsfel


Programanvändning rapporten har förbättrats och ingår i den **inloggningar** vyn. Visa den här vyn på den **Azure Active Directory** bladet under **AKTIVITETEN**väljer **inloggningar**.

![Inloggningar visa](./media/active-directory-reporting-migration/483.png "inloggningar visa")

Den **inloggningar** vyn innehåller alla användarinloggningar. Du kan använda den här informationen för att hämta information om användning. Du kan också visa information om användning av program i den **företagsprogram** översikt över i den **hantera** avsnitt.

![Företagsprogram](./media/active-directory-reporting-migration/484.png "företagsprogram")

## <a name="access-a-specific-report"></a>Åtkomst till en rapport

Även om Azure-portalen erbjuder en enda vy kan titta du också på specifika rapporter.

### <a name="audit-logs"></a>Granskningsloggar

Som svar på kundfeedback i Azure-portalen, kan du använda avancerade filter att komma åt data som du vill använda. Är ett filter som du kan använda en *aktivitetskategorin*, som listar de olika typerna av aktiviteten loggar i Azure AD. Du kan välja en kategori för att begränsa resultaten till vad du letar efter.

Till exempel om du är intresserad av att endast aktiviteter som rör lösenordsåterställning med självbetjäning kan du välja den **Self-service lösenordshantering** kategori. De kategorier som du ser baseras på den resurs som du arbetar med.  

![Kategori alternativen på sidan Filter granskningsloggar](./media/active-directory-reporting-migration/06.png "kategori alternativen på sidan Filter granskningsloggar")

Aktivitetskategorier inkluderar:

- Kärnkatalog
- Hantering av lösenord för självbetjäning
- Självbetjäning, grupphantering
- Kontoetablering

### <a name="application-usage"></a>Programanvändning

Visa information om programanvändning för alla appar eller för en enda app under **AKTIVITETEN**väljer **inloggningar**. Om du vill begränsa sökresultaten kan du filtrera efter användarnamn eller programnamn.

![Filter inloggning händelser sidan](./media/active-directory-reporting-migration/07.png "filtrera inloggning händelser sida")

### <a name="security-reports"></a>Säkerhetsrapporter

#### <a name="azure-ad-anomalous-activity-reports"></a>Azure AD avvikande aktivitetsrapporter

Säkerheten i Azure AD avvikande aktivitet har konsoliderade rapporter från den klassiska Azure-portalen för att ge dig en, centrala vy. Den här vyn visar alla riskhändelser som säkerhetsrelaterade att Azure AD kan identifiera och rapportera om.

Följande tabell listar Azure AD avvikande aktivitet säkerhetsrapporter och motsvarande risk händelsetyper i Azure-portalen.

| Azure AD avvikande Aktivitetsrapport |  Identity protection risk händelsetyp|
| :--- | :--- |
| Används med läckta autentiseringsuppgifter | Läckta autentiseringsuppgifter |
| Oregelbunden inloggningsaktivitet | Omöjligt att resa till ovanliga platser |
| Inloggningar från potentiellt infekterade enheter | Inloggningar från angripna enheter|
| Inloggningar från okända källor | Inloggningar från anonyma IP-adresser |
| Inloggningar från IP-adresser med misstänkt aktivitet | Inloggningar från IP-adresser med misstänkt aktivitet |
| - | Inloggningar från okända platser |

Följande Azure AD avvikande aktivitet säkerheten rapporterar inte ingår som riskhändelser i Azure-portalen:

* Inloggningar efter flera fel
* Inloggningar från flera geografiska områden

Dessa rapporter är fortfarande tillgängliga i den klassiska Azure-portalen, men de kommer att inaktualiseras i framtiden.

Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](active-directory-identity-protection-risk-events.md).  


#### <a name="detected-risk-events"></a>Identifierade riskhändelser

I Azure-portalen kan du komma åt rapporter om identifierad riskhändelser på den **Azure Active Directory** bladet under **säkerhet**. Identifierade riskhändelser spåras i följande rapporter:   

- Användare i riskzonen
- Riskfyllda inloggningar

![Säkerhetsrapporter](./media/active-directory-reporting-migration/04.png "säkerhetsrapporter")

Mer information om säkerhetsrapporter finns:

- [Användare på Risk säkerhetsrapporten i Azure Active Directory-portalen](active-directory-reporting-security-user-at-risk.md)
- [Riskfyllda inloggningar rapporten i Azure Active Directory-portalen](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Aktivitetsrapporter i den klassiska Azure-portalen och Azure-portalen

Tabellen i det här avsnittet innehåller befintliga rapporter i den klassiska Azure-portalen. Här beskrivs också hur du kan få samma information i Azure-portalen.

Visa alla granskning data på den **Azure Active Directory** bladet under **AKTIVITETEN**, gå till **granskningsloggar**.

![Granskningsloggar](./media/active-directory-reporting-migration/61.png "Granskningsloggar")

| Klassisk Azure-portal                 | Du hittar i Azure-portalen                                                         |
| ---                                  | ---                                                                        |
| Granskningsloggar                           | För **aktivitetskategorin**väljer **Core Directory**.                       |
| Lösenordsåterställningsaktivitet              | För **aktivitetskategorin**väljer **Self-service lösenordshantering**. |
| Lösenordsåterställningsaktivitet för registrering | För **aktivitetskategorin**väljer **Self-service lösenordshantering**.     |
| Självbetjäning grupper aktivitet         | För **aktivitetskategorin**väljer **Self-service Group Management**.        |
| Kontot etablering aktivitet        | För **aktivitetskategorin**väljer **konto Användaretablering**.         |
| Status för förnyelse av lösenord             | För **aktivitetskategorin**väljer **automatisk förnyelse av App lösenord**.      |
| Kontoetableringsfel          | För **aktivitetskategorin**väljer **konto Användaretablering**.        |
| Ändringar av Office365 namn         | För **aktivitetskategorin**väljer **Self-service lösenordshantering**. För **aktivitet resurstypen**väljer **gruppen**. För **Aktivitetskälla**väljer **O365 grupper**.|

Visa den **programanvändning** rapportera på den **Azure Active Directory** bladet under **hantera**väljer **företagsprogram**, och välj sedan **inloggningar**.


![Enterprise program inloggningar rapporten](./media/active-directory-reporting-migration/199.png "Enterprise program inloggningar rapport")

## <a name="next-steps"></a>Nästa steg

En översikt över rapportering finns i [Azure Active Directory-rapportering](active-directory-reporting-azure-portal.md).
