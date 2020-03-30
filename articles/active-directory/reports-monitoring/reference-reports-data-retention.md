---
title: Hur länge lagrar Azure AD rapporteringsdata? | Microsoft Docs
description: Läs om hur länge Azure lagrar de olika typerna av rapporteringsdata.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54636600c208f8f5df9fa2e25460c63dd9f46e85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239550"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Hur länge lagrar Azure AD rapporteringsdata?


I den här artikeln får du lära dig mer om datalagringsprinciperna för de olika aktivitetsrapporterna i Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>När börjar Azure AD samla in data?

| Azure AD-utgåva | Start av samling |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | När du registrerar dig för en prenumeration |
| Azure AD Kostnadsfri| Första gången du öppnar [Azure Active Directory-bladet](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) eller använder [rapporterar API:erna](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>När är aktivitetsdata tillgängliga i Azure-portalen?

- **Omedelbart** - Om du redan har arbetat med rapporter i Azure-portalen.
- **Inom 2 timmar** - Om du inte har aktiverat rapportering i Azure-portalen.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hur snart kan jag se aktivitetsdata efter att ha fått en premiumlicens?

Om du redan har aktiviteter data med din gratis licens, då kan du se det omedelbart vid uppgradering. Om du inte har några data tar det en eller två dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Kan jag se förra månadens data efter att ha fått en Azure AD-premiumlicens?

Om du nyligen har bytt till en premiumversion (inklusive en testversion) kan du se data upp till 7 dagar från början. När data ackumuleras kan du se data för de senaste 30 dagarna.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>När börjar Azure AD samla in säkerhetssignaldata?  

För säkerhetssignaler startar insamlingsprocessen när du väljer att använda **Identity Protection Center**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Hur länge lagrar Azure AD data?

**Aktivitetsrapporter**    

| Rapport                 | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Granskningsloggar             | 7 dagar        | 30 dagar             | 30 dagar             |
| Inloggningar               | 7 dagar        | 30 dagar             | 30 dagar             |
| Azure MFA-användning        | 30 dagar       | 30 dagar             | 30 dagar             |

Du kan behålla gransknings- och inloggningsaktivitetsdata längre än den standardlagringsperiod som beskrivs ovan genom att dirigera dem till ett Azure-lagringskonto med Azure Monitor. Mer information finns i [Arkivera Azure AD-loggar till ett Azure-lagringskonto](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Säkerhetssignaler**

| Rapport         | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Användare i riskzonen  | 7 dagar        | 30 dagar             | 90 dagar             |
| Riskfyllda inloggningar | 7 dagar        | 30 dagar             | 90 dagar             |

---
