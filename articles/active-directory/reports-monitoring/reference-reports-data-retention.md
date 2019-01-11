---
title: Rapportkvarhållningsregler i Azure Active Directory | Microsoft Docs
description: Principer för kvarhållning på rapportdata i Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0ed23c8efcf43213b288945c2910edf4c97bb63e
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214478"
---
# <a name="azure-active-directory-report-retention-policies"></a>Rapportkvarhållningsregler i Azure Active Directory

I den här artikeln får du lära dig om datalagringsprinciper för olika aktivitetsrapporter i Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>När Azure AD startar insamling av data?

| Azure AD-version | Samling Start |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | När du registrerar dig för en prenumeration |
| Azure AD Kostnadsfri <br /> Azure AD Basic | Första gången du öppnar den [Azure Active Directory-bladet](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) eller Använd den [reporting API: er](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>När är aktivitetsdata i Azure-portalen?

- **Omedelbart** – om du redan har arbetat med rapporter i Azure-portalen.
- **Inom 2 timmar** – om du inte har aktiverat rapportering i Azure-portalen.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hur snart ser aktiviteter data när du har fått en premiumlicens?

Om du redan har data för aktiviteter med din kostnadsfri licens, kan då du se det omedelbart vid uppgradering. Om du inte har några data, kan det ta en eller två dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Kan jag se förra månadens data när du har skaffat en Azure AD premium-licens?

Om du nyligen har bytt till en premium-version (inklusive en utvärderingsversion), kan du visa data upp till 7 dagar från början. När en publiceringskonfiguration ackumuleras data, kan du se data för de senaste 30 dagarna.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>När Azure AD startar samla in säkerhetsdata signalen?  

För säkerhets-signaler, samling processen startar när du anmäla sig till använder den **Identity Protection Center**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Hur länge sparas data i Azure AD?

**Aktivitetsrapporter**    

| Rapport                 | Azure AD Kostnadsfri | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Kataloggranskning        | 7 dagar        |  7 dagar        | 30 dagar             | 30 dagar             |
| Inloggningsaktivitet       | Gäller inte           |  Gäller inte           | 30 dagar             | 30 dagar             |
| Azure MFA-användning        | 30 dagar       |  30 dagar       | 30 dagar             | 30 dagar             |

Du kan behålla aktivitetsdata gransknings- och logga in under längre tid än loggperioden ovan genom att skicka det till ett Azure storage-konto med Azure Monitor. Mer information finns i [Arkiv Azure AD-loggar till ett Azure storage-konto](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Security signaler**

| Rapport         | Azure AD Kostnadsfri | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Användare i riskzonen  | 7 dagar        | 7 dagar         | 30 dagar             | 90 dagar             |
| Riskfyllda inloggningar | 7 dagar        | 7 dagar         |  30 dagar            | 90 dagar             |

---
