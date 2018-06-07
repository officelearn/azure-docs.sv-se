---
title: Azure Active Directory reporting svarstiderna | Microsoft Docs
description: Lär dig mer om hur lång tid det tar för rapporteringshändelser ska visas i din Azure-portalen
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 12/15/2017
ms.author: rolyon;dhanyahk
ms.reviewer: dhanyahk
ms.openlocfilehash: f27efab05dc8580735c58eed20e9b48d8003dd17
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589788"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory reporting svarstider

Med [reporting](active-directory-preview-explainer.md) i Azure Active Directory så, du får all information du behöver ta reda på hur du gör din miljö. Hur lång tid det tar för att rapportera data visas i Azure-portalen kallas även svarstid. 

Det här avsnittet innehåller information svarstid för alla reporting kategorier i Azure-portalen. 


## <a name="activity-reports"></a>Aktivitetsrapporter

Det finns två områden av aktiviteten reporting:

- **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
- **Granskningsloggar** – Granska information om systemaktivitet för användare och grupphantering, dina hanterade program och katalogaktiviteter

I följande tabell visas latensinformation för aktivitetsrapporter.

| Rapport | Svarstid (P95) |Svarstid (P99)|
| :-- | --- | --- | 
| Granskningsloggar | 2 minuter  | 5 minuter  |
| Inloggningar | 2 minuter  | 5 minuter |







## <a name="security-reports"></a>Säkerhetsrapporter

Det finns två områden av säkerhet reporting:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 
- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

I följande tabell visas latensinformation för säkerhetsrapporter.

| Rapport | Minimum | Medel | Maximal |
| :-- | --- | --- | --- |
| Användare i farozonen          | 5 minuter   | 15 minuter  | 2 timmar  |
| Riskfyllda inloggningar         | 5 minuter   | 15 minuter  | 2 timmar  |

## <a name="risk-events"></a>Riskhändelser

Azure Active Directory använder anpassningsbar maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användarkonton. Varje upptäckt misstänkt åtgärd lagras i en post kallas risk händelse.

I följande tabell visas latensinformation för riskhändelser.

| Rapport | Minimum | Medel | Maximal |
| :-- | --- | --- | --- |
| Inloggningar från anonyma IP-adresser |5 minuter |15 minuter |2 timmar |
| Inloggningar från okända platser |5 minuter |15 minuter |2 timmar |
| Används med läckta autentiseringsuppgifter |2 timmar |4 timmar |8 timmar |
| Omöjligt att resa till ovanliga platser |5 minuter |1 timme |8 timmar  |
| Inloggningar från angripna enheter |2 timmar |4 timmar |8 timmar  |
| Inloggningar från IP-adresser med misstänkt aktivitet |2 timmar |4 timmar |8 timmar  |



## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om aktivitetsrapporter i Azure-portalen, se:

- [Inloggningsaktivitet rapporterna i Azure Active Directory-portalen](active-directory-reporting-activity-sign-ins.md)
- [Granska aktivitetsrapporter i Azure Active Directory-portalen](active-directory-reporting-activity-audit-logs.md)

Om du vill veta mer om säkerhetsrapporter i Azure-portalen, se:

- [Användare på risk säkerhetsrapporten i Azure Active Directory-portalen](active-directory-reporting-security-user-at-risk.md)
- [Rapporten riskfyllda inloggningar i Azure Active Directory-portalen](active-directory-reporting-security-risky-sign-ins.md)

Om du vill veta mer om riskhändelser finns [Azure Active Directory riskhändelser](active-directory-reporting-risk-events.md).
