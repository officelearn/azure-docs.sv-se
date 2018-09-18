---
title: Azure Active Directory-rapporteringssvarstider | Microsoft Docs
description: Lär dig mer om hur lång tid det tar för rapporteringshändelser ska visas i Azure portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 12/15/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: b81c66acc0a90ba9b74cf1f4fb34ef7a545837f9
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736614"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory-rapporteringssvarstider

Med [reporting](../active-directory-preview-explainer.md) i Azure Active Directory får du all information du behöver för att fastställa hur din miljö klarar. Hur lång tid det tar för rapporteringsdata att visas i Azure-portalen kallas även svarstid. 

Det här avsnittet listar latensinformation för alla reporting kategorierna i Azure-portalen. 


## <a name="activity-reports"></a>Aktivitetsrapporter

Det finns två områden för aktivitetsrapportering:

- **Inloggningsaktiviteter** – Information om användningen av hanterade program och användares inloggningsaktiviteter
- **Granskningsloggar** – Granska information om systemaktivitet för användare och grupphantering, dina hanterade program och katalogaktiviteter

I följande tabell visas latensinformation för aktivitetsrapporter.

| Rapport | Svarstid (95%) |Svarstid (99%)|
| :-- | --- | --- | 
| Granskningsloggar | 2 min  | 5 min  |
| Inloggningar | 2 min  | 5 min |


## <a name="security-reports"></a>Säkerhetsrapporter

Det finns två delar av säkerhetsrapportering:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 
- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

I följande tabell visas latensinformation för säkerhetsrapporter.

| Rapport | Minimum | Medel | Maximal |
| :-- | --- | --- | --- |
| Användare i farozonen          | 5 minuter   | 15 minuter  | 2 timmar  |
| Riskfyllda inloggningar         | 5 minuter   | 15 minuter  | 2 timmar  |

## <a name="risk-events"></a>Riskhändelser

Azure Active Directory använder anpassningsbara maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder relaterade till dina användarkonton. Varje identifierad misstänkt åtgärd lagras i en post kallas riskhändelse.

I följande tabell visas latensinformation för riskhändelser.

| Rapport | Minimum | Medel | Maximal |
| :-- | --- | --- | --- |
| Inloggningar från anonyma IP-adresser |5 minuter |15 minuter |2 timmar |
| Inloggningar från okända platser |5 minuter |15 minuter |2 timmar |
| Användare med läckta autentiseringsuppgifter |2 timmar |4 timmar |8 timmar |
| Omöjliga resor till ovanliga platser |5 minuter |1 timme |8 timmar  |
| Inloggningar från angripna enheter |2 timmar |4 timmar |8 timmar  |
| Inloggningar från IP-adresser med misstänkt aktivitet |2 timmar |4 timmar |8 timmar  |



## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om aktivitetsrapporter i Azure-portalen, se:

- [Logga in aktivitetsrapporter i Azure Active Directory-portalen](concept-sign-ins.md)
- [Granska aktivitetsrapporter i Azure Active Directory-portalen](concept-audit-logs.md)

Om du vill veta mer om säkerhetsrapporter i Azure-portalen, se:

- [Användare i farozonen i Azure Active Directory-portalen](concept-user-at-risk.md)
- [Rapporten om riskfyllda inloggningar i Azure Active Directory-portalen](concept-risky-sign-ins.md)

Om du vill veta mer om riskhändelser [Azure Active Directory-riskhändelser](concept-risk-events.md).
