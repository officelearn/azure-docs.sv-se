---
title: Svarstider för Azure Active Directory-rapportering | Microsoft-dokument
description: Läs mer om hur lång tid det tar för att rapportera händelser som visas i din Azure-portal
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
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d031546bb4f1f05e9ea2abb5b74fe911b0b507f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74007711"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory-rapporteringssvarstider

Svarstid är den tid det tar för Azure Active Directory (Azure AD) att rapportera data som visas i [Azure-portalen](https://portal.azure.com). I den här artikeln visas den förväntade svarstiden för de olika typerna av rapporter. 

## <a name="activity-reports"></a>Aktivitetsrapporter

Det finns två typer av aktivitetsrapporter:

- [Inloggningar](concept-sign-ins.md) – Ger information om användningen av hanterade program och användarloggningsaktiviteter
- [Granskningsloggar](concept-audit-logs.md) - Ger information om systemaktivitet om användare och grupper, hanterade program och katalogaktiviteter

I följande tabell visas svarstidsinformationen för aktivitetsrapporter. 

> [!NOTE]
> **Latens (95:e percentilen)** refererar till den tidpunkt då 95 % av loggarna kommer att rapporteras och **Latens (99:e percentilen)** refererar till den tidpunkt då 99 % av loggarna rapporteras. 
>

| Rapport | Latens (95:e percentilen) |Latens (99:e percentilen)|
| :-- | --- | --- |
| Granskningsloggar | 2 min  | 5 min  |
| Inloggningar | 2 min  | 5 min |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hur snart kan jag se aktivitetsdata efter att ha fått en premiumlicens?

Om du redan har aktiviteter data med din gratis licens, då kan du se det omedelbart vid uppgradering. Om du inte har några data tar det en eller två dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens.

## <a name="security-reports"></a>Säkerhetsrapporter

Det finns två typer av säkerhetsrapporter:

- [Riskfyllda inloggningar](concept-risky-sign-ins.md) – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 
- [Användare som har flaggats för risk](concept-user-at-risk.md) – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

I följande tabell visas svarstidsinformationen för säkerhetsrapporter.

| Rapport | Minimum | Medel | Maximal |
| :-- | --- | --- | --- |
| Användare i riskzonen          | 5 minuter   | 15 minuter  | 2 timmar  |
| Riskfyllda inloggningar         | 5 minuter   | 15 minuter  | 2 timmar  |

## <a name="risk-detections"></a>Riskidentifieringar

Azure AD använder adaptiva maskininlärningsalgoritmer och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användarkonton. Varje identifierad misstänkt åtgärd lagras i en post som kallas **en riskidentifiering**.

I följande tabell visas svarstidsinformationen för riskidentifieringar.

| Rapport | Minimum | Medel | Maximal |
| :-- | --- | --- | --- |
| Inloggningar från anonyma IP-adresser |5 minuter |15 minuter |2 timmar |
| Inloggningar från okända platser |5 minuter |15 minuter |2 timmar |
| Användare med läckta autentiseringsuppgifter |2 timmar |4 timmar |8 timmar |
| Omöjliga resor till ovanliga platser |5 minuter |1 timme |8 timmar  |
| Inloggningar från angripna enheter |2 timmar |4 timmar |8 timmar  |
| Inloggningar från IP-adresser med misstänkt aktivitet |2 timmar |4 timmar |8 timmar  |


## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure AD-rapporter](overview-reports.md)
* [Programmatisk åtkomst till Azure AD-rapporter](concept-reporting-api.md)
* [Azure Active Directory-riskidentifieringar](concept-risk-events.md)
