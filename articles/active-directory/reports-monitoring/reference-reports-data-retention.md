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
ms.openlocfilehash: abd64b7d2fa7930f5b6177c7ac037840da34dc18
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333589"
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

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>När Azure AD startar samla in säkerhetsdata signalen?  

För säkerhets-signaler, samling processen startar när du anmäla sig till använder den **Identity Protection Center**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Hur länge sparas data i Azure AD?

**Aktivitetsrapporter**    

| Rapport                 | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Kataloggranskning        | 7 dagar        | 30 dagar             | 30 dagar             |
| Inloggningsaktivitet       | Gäller inte           | 30 dagar             | 30 dagar             |
| Azure MFA-användning        | 30 dagar       | 30 dagar             | 30 dagar             |

**Security signaler**

| Rapport         | Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Användare i riskzonen  | 7 dagar        | 30 dagar             | 90 dagar             |
| Riskfyllda inloggningar | 7 dagar        | 30 dagar             | 90 dagar             |

---
