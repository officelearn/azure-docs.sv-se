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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624920"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Hitta aktivitetsrapporter i Azure portal

I den här artikeln får lära du att hitta aktivitetsrapporter för Azure Active Directory (Azure AD)-användare i Azure-portalen.

## <a name="audit-logs-report"></a>Granskningsloggar

Granskningsloggar kombinerar flera rapporter kring program aktiviteter i en enda vy för kontext-baserade rapporter. Komma åt granskningsloggar:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj din katalog från det övre högra hörnet och välj sedan den **Azure Active Directory** bladet från det vänstra navigeringsfönstret.
3. Välj **granskningsloggar** från den **aktivitet** på bladet Azure Active Directory. 

    ![Granskningsloggar](./media/howto-find-activity-reports/482.png "Granskningsloggar")

Granskningsloggar konsoliderar följande rapporter:

* Granskningsrapport
* Lösenordsåterställningsaktivitet
* Lösenordsåterställningsaktivitet för registrering
* Aktiviteter för självbetjäningsgrupper
* Namnändringar för Office 365-grupp
* Kontot etablering aktivitet
* Status för förnyelse av lösenord
* Kontoetableringsfel

### <a name="filtering-on-audit-logs"></a>Filtrering på granskningsloggar

Du kan använda avancerade filter i granskningsrapporten för att få åtkomst till en särskild kategori av granskningsdata, genom att ange den i den **aktivitetskategori** filter. Om du vill visa alla aktiviteter som rör lösenordsåterställning via självbetjäning, väljer du exempelvis den **egen lösenordshantering** kategori. 

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

Aktivitetskategorier är:

- Kärnkatalog
- Hantering av lösenord för självbetjäning
- Självbetjäning, grupphantering
- Kontoetablering


## <a name="sign-ins-report"></a>Rapporten inloggningar 

Den **inloggningar** vyn innehåller alla användarinloggningar, samt de **programanvändning** rapporten. Du kan också visa information om användningen av program i den **hantera** delen av den **företagsprogram** Översikt.

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

Öppna rapporten inloggningar:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj din katalog från det övre högra hörnet och välj sedan den **Azure Active Directory** bladet från det vänstra navigeringsfönstret.
3. Välj **inloggningar** från den **aktivitet** på bladet Azure Active Directory. 

    ![Visa inloggningar](./media/howto-find-activity-reports/483.png "visa inloggningar")


### <a name="filtering-on-application-name"></a>Filtrera efter namn

Du kan använda rapporten inloggningar för att visa information om appanvändningen genom att filtrera efter användarnamn eller programnamn.

![Filtrera inloggningshändelser sidan](./media/howto-find-activity-reports/07.png "filtrera inloggningshändelser sidan")

## <a name="security-reports"></a>Säkerhetsrapporter

### <a name="anomalous-activity-reports"></a>Avvikande aktivitetsrapporter

Avvikande aktivitetsrapporter innehåller information om säkerhetsrelaterade riskhändelser som Azure AD kan identifiera och rapportera om.

Följande tabell visar Azure AD avvikande aktivitet säkerhetsrapporter och motsvarande typer av riskhändelser på Azure-portalen. Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](concept-risk-events.md).  


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


### <a name="detected-risk-events"></a>Identifierade riskhändelserna

Du kan komma åt rapporter om identifierade riskhändelserna i den **Security** delen av den **Azure Active Directory** -bladet i den [Azure-portalen](https://portal.azure.com). Identifierade riskhändelserna spåras i följande rapporter:   

- [Användare i riskzonen](concept-user-at-risk.md)
- [Riskfyllda inloggningar](concept-risky-sign-ins.md)

    ![Säkerhetsrapporter](./media/howto-find-activity-reports/04.png "säkerhetsrapporter")

## <a name="next-steps"></a>Nästa steg

* [Granska loggar översikt](concept-audit-logs.md)
* [Översikt över inloggningar](concept-sign-ins.md)
* [Översikt över riskfyllda händelser](concept-risk-events.md)
