---
title: Azure Active Directory rapporterings fördröjning | Microsoft Docs
description: Lär dig mer om hur lång tid det tar för rapport händelser att visas i din Azure Portal
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74007711"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory-rapporteringssvarstider

Svars tid är hur lång tid det tar för Azure Active Directory (Azure AD) rapporterings data att visas i [Azure Portal](https://portal.azure.com). Den här artikeln innehåller den förväntade svars tiden för de olika typerna av rapporter. 

## <a name="activity-reports"></a>Aktivitetsrapporter

Det finns två typer av aktivitets rapporter:

- [Inloggningar](concept-sign-ins.md) – ger information om användningen av hanterade program och användar inloggnings aktiviteter
- [Gransknings loggar](concept-audit-logs.md) – innehåller information om system aktivitet för användare och grupper, hanterade program och katalog aktiviteter

I följande tabell visas latens information för aktivitets rapporter. 

> [!NOTE]
> **Latens (95 percentil)** avser den tid som 95% av loggarna rapporteras och **latens (99 percentil)** avser den tid som 99% av loggarna rapporteras. 
>

| Rapport | Latens (95 percentil) |Latens (99 percentil)|
| :-- | --- | --- |
| Granskningsloggar | 2 min  | 5 min  |
| Inloggningar | 2 min  | 5 min |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hur snart kan jag se aktivitets data när de har skaffat en Premium-licens?

Om du redan har aktivitets data med din kostnads fria licens kan du se den direkt vid uppgraderingen. Om du inte har några data, tar det en eller två dagar innan data visas i rapporterna efter att du har uppgraderat till en Premium-licens.

## <a name="security-reports"></a>Säkerhetsrapporter

Det finns två typer av säkerhetsrapporter:

- [Riskfyllda inloggningar](concept-risky-sign-ins.md) – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 
- [Användare som har flaggats för risk](concept-user-at-risk.md) – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

I följande tabell visas svars tids informationen för säkerhets rapporter.

| Rapport | Minimum | Medel | Maximal |
| :-- | --- | --- | --- |
| Användare i riskzonen          | 5 minuter   | 15 minuter  | 2 timmar  |
| Riskfyllda inloggningar         | 5 minuter   | 15 minuter  | 2 timmar  |

## <a name="risk-detections"></a>Riskidentifieringar

Azure AD använder anpassningsbara algoritmer för maskin inlärning och heuristik för att identifiera misstänkta åtgärder som är relaterade till dina användar konton. Varje misstänkt misstänkt åtgärd lagras i en post som kallas för **identifiering av risker**.

I följande tabell visas svars informationen för risk identifieringar.

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
* [Programmerings åtkomst till Azure AD-rapporter](concept-reporting-api.md)
* [Azure Active Directory risk identifieringar](concept-risk-events.md)
