---
title: "Rapportkvarhållningsregler i Azure Active Directory | Microsoft Docs"
description: "Bevarandeprinciper på rapportdata i Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 933d251903f4ca74902e984e7a1581a75345ee7f
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="azure-active-directory-report-retention-policies"></a>Rapportkvarhållningsregler i Azure Active Directory


Det här avsnittet ger svar på de vanligaste frågorna tillsammans med datalagring för olika aktivitetsrapporter i Azure Active Directory. 

**F: hur kan du få insamlingen av aktivitetsdata starta?**

**S:**

| Azure AD-version | Samlingen Start |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | När du registrerar dig för en prenumeration |
| Azure AD Kostnadsfri | Första gången du öppnar den [Azure Active Directory-bladet](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) eller använda den [reporting API: er](https://aka.ms/aadreports)  |

---
**F: när är aktivitetsdata i Azure portal?**

**S:**

- **Omedelbart** - om du redan har arbetat med rapporter i den klassiska Azure-portalen
- **Inom två timmar** - om du inte har aktiverat rapportering i den klassiska Azure-portalen

---
**F: hur kan du få insamling av säkerhet signaler igång?**  

**S:** för säkerhet signaler samling processen startar när du delta i använda Identity Protection Center. 


---
**F: hur länge insamlade data lagras?**

**S:**

**Aktivitetsrapporter**    

| Rapport                 | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Kataloggranskning        | 7 dagar        | 30 dagar             | 30 dagar             |
| Inloggningsaktivitet       | Saknas           | 30 dagar             | 30 dagar             |
| Azure MFA-användning        | 90 dagar       | 90 dagar             | 90 dagar             |

**Säkerhet signaler**

| Rapport         | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Användare i farozonen  | 7 dagar        | 30 dagar             | 90 dagar             |
| Riskfyllda inloggningar | 7 dagar        | 30 dagar             | 90 dagar             |

---
