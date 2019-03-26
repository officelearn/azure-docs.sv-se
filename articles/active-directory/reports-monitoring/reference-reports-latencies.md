---
title: Azure Active Directory-rapporteringssvarstider | Microsoft Docs
description: Lär dig mer om hur lång tid det tar för rapporteringshändelser ska visas i Azure portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64bd2247a3437a2cc960da1820d9be417eedff8e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438657"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory-rapporteringssvarstider

Svarstiden är tiden det tar för Azure Active Directory (Azure AD) rapporterar data visas i den [Azure-portalen](https://portal.azure.com). Den här artikeln visar den förväntade svarstiden för olika typer av rapporter. 

## <a name="activity-reports"></a>Aktivitetsrapporter

Det finns två typer av aktivitetsrapporter:

- [Inloggningar](concept-sign-ins.md) – innehåller information om användningen av hanterade program och användare inloggningsaktiviteter
- [Granskningsloggar](concept-audit-logs.md) -ger information om användare och grupper, hanterade program och katalogaktiviteter systemaktivitet

I följande tabell visas latensinformation för aktivitetsrapporter. 

> [!NOTE]
> **Svarstid (95: e percentilen)** refererar till den tid som 95% av loggarna rapporteras, och **svarstid (99: e percentilen)** refererar till den tid som 99% av loggarna rapporteras. 
>

| Rapport | Svarstid (95: e percentilen) |Svarstid (99: e percentilen)|Tidsintervall inom vilket loggar rapporteras|
| :-- | --- | --- | --- |
| Granskningsloggar | 2 min  | 5 min  | 2 – 60 minuter |
| Inloggningar | 2 min  | 5 min | 2-120 minuter |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hur snart ser aktiviteter data när du har fått en premiumlicens?

Om du redan har data för aktiviteter med din kostnadsfri licens, kan då du se det omedelbart vid uppgradering. Om du inte har några data, kan det ta en eller två dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens.

## <a name="security-reports"></a>Säkerhetsrapporter

Det finns två typer av säkerhetsrapporter:

- [Riskfyllda inloggningar](concept-risky-sign-ins.md) – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 
- [Användare som har flaggats för risk](concept-user-at-risk.md) – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

I följande tabell visas latensinformation för säkerhetsrapporter.

| Rapport | Minimum | Medel | Maximal |
| :-- | --- | --- | --- |
| Användare i riskzonen          | 5 minuter   | 15 minuter  | 2 timmar  |
| Riskfyllda inloggningar         | 5 minuter   | 15 minuter  | 2 timmar  |

## <a name="risk-events"></a>Riskhändelser

Azure AD använder anpassningsbar machine learning-algoritmer och heuristik för att identifiera misstänkta åtgärder relaterade till dina användarkonton. Varje identifierad misstänkt åtgärd lagras i en post som kallas en **riskhändelsen**.

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

* [Översikt över rapporter i Azure AD](overview-reports.md)
* [Programmatisk åtkomst till Azure AD-rapporter](concept-reporting-api.md)
* [Azure Active Directory-riskhändelser](concept-risk-events.md)
