---
title: Hitta aktivitetsrapporter för Azure Active Directory-användare i Azure-portalen | Microsoft Docs
description: Lär dig mer om aktivitetsrapporter för Azure Active Directory-användare finns i Azure-portalen.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: users-groups-roles
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f22219a0e2ff342e25a2efdeb319f389250ecfef
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231329"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Hitta aktivitetsrapporter i Azure-portalen

I den här artikeln beskrivs hur du hittar aktivitetsrapporter för Azure Active Directory-användare i Azure-portalen.

## <a name="activity-and-integrated-app-reports"></a>Aktiviteten och integrerad apprapporter

För sammanhangsberoende rapportering i Azure-portalen, kombineras befintliga rapporter till en enda vy. En enda underliggande API ger data till vyn.

Visa den här vyn på den **Azure Active Directory** bladet under **AKTIVITETEN**väljer **granskningsloggar**.

![Granskningsloggar](./media/active-directory-reporting-migration/482.png "Granskningsloggar")

Följande rapporter konsolideras i den här vyn:

* Granska rapporten
* Lösenordsåterställningsaktivitet
* Lösenordsåterställningsaktivitet för registrering
* Självbetjäning grupper aktivitet
* Ändringar av Office365 namn
* Kontot etablering aktivitet
* Status för förnyelse av lösenord
* Kontoetableringsfel


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

Azure AD avvikande aktivitetssäkerhet rapporterar konsolideras för att ge dig en central vy. Den här vyn visar alla riskhändelser som säkerhetsrelaterade att Azure AD kan identifiera och rapportera om.

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

Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](active-directory-identity-protection-risk-events.md).  


#### <a name="detected-risk-events"></a>Identifierade riskhändelser

I Azure-portalen kan du komma åt rapporter om identifierad riskhändelser på den **Azure Active Directory** bladet under **säkerhet**. Identifierade riskhändelser spåras i följande rapporter:   

- Användare i riskzonen
- Riskfyllda inloggningar

![Säkerhetsrapporter](./media/active-directory-reporting-migration/04.png "säkerhetsrapporter")

Mer information om säkerhetsrapporter finns:

- [Användare på Risk säkerhetsrapporten i Azure Active Directory-portalen](active-directory-reporting-security-user-at-risk.md)
- [Riskfyllda inloggningar rapporten i Azure Active Directory-portalen](active-directory-reporting-security-risky-sign-ins.md)


Visa den **programanvändning** rapportera på den **Azure Active Directory** bladet under **hantera**väljer **företagsprogram**, och välj sedan **inloggningar**.


![Enterprise program inloggningar rapport](./media/active-directory-reporting-migration/199.png)

## <a name="next-steps"></a>Nästa steg

En översikt över rapportering finns i [Azure Active Directory-rapportering](active-directory-reporting-azure-portal.md).
