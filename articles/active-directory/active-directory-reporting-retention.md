---
title: Rapportkvarhållningsregler i Azure Active Directory | Microsoft Docs
description: Bevarandeprinciper på rapportdata i Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/10/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: fac160d2d5916097afcbb9825bb82a52789e4a89
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589203"
---
# <a name="azure-active-directory-report-retention-policies"></a>Rapportkvarhållningsregler i Azure Active Directory


Det här avsnittet ger svar på de vanligaste frågorna tillsammans med datalagring för olika aktivitetsrapporter i Azure Active Directory. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>F: hur kan du få insamlingen av aktivitetsdata starta?

**S:**

| Azure AD-version | Samlingen Start |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | När du registrerar dig för en prenumeration |
| Azure AD Kostnadsfri | Första gången du öppnar den [Azure Active Directory-bladet](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) eller använda den [reporting API: er](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>F: när är aktivitetsdata i Azure portal?

**S:**

- **Omedelbart** - om du redan har arbetat med rapporter i Azure-portalen
- **Inom två timmar** - om du inte har aktiverat rapportering i Azure-portalen

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>F: hur kan du få insamling av säkerhet signaler igång?  

**S:** för säkerhet signaler samling processen startar när du delta i använda Identity Protection Center. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>F: hur länge insamlade data lagras?

**S:**

**Aktivitetsrapporter**    

| Rapport                 | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Kataloggranskning        | 7 dagar        | 30 dagar             | 30 dagar             |
| Inloggningsaktivitet       | Gäller inte           | 30 dagar             | 30 dagar             |
| Azure MFA-användning        | 30 dagar       | 30 dagar             | 30 dagar             |

**Säkerhet signaler**

| Rapport         | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Användare i farozonen  | 7 dagar        | 30 dagar             | 90 dagar             |
| Riskfyllda inloggningar | 7 dagar        | 30 dagar             | 90 dagar             |

---
