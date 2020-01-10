---
title: Identitets data lagring för australiska kunder – Azure AD
description: Lär dig mer om var Azure Active Directory lagrar identitets-relaterade data för sina australiska kunder.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbe4befa824990c2f8b2627e7709913f564c0486
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751424"
---
# <a name="identity-data-storage-for-australian-customers-in-azure-active-directory"></a>Identitets data lagring för australiska kunder i Azure Active Directory

Identitets data lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst, till exempel Office 365 och Azure. Information om var dina identitets kunddata lagras, kan du använda avsnittet var finns [dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) i Microsoft säkerhets Center.

> [!NOTE]
> Tjänster och program som integreras med Azure AD har åtkomst till identitets kund information. Utvärdera varje tjänst och program som du använder för att avgöra hur identitetens kund data bearbetas av den specifika tjänsten och programmet, och om de uppfyller företagets krav på data lagring. Mer information om Microsofts tjänsters data placering finns i avsnittet var finns dina data? avsnitt i Microsoft säkerhets Center.

För kunder som tillhandahöll en adress i Australien lagrar Azure AD identitets data för dessa tjänster i de australiska data centren: 
- Azure AD-katalog hantering 
- Autentisering

Alla andra Azure AD-tjänster lagrar kund information i globala data Center. Information om hur du hittar data centret för en tjänst finns i [Azure Active Directory – var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure Multi-Factor Authentication (MFA)

MFA lagrar identitets kund information i globala data Center. Mer information om den användar information som samlas in och lagras av molnbaserad Azure MFA-och Azure MFA-Server finns i [Azure Multi-Factor Authentication User Data Collection](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

## <a name="next-steps"></a>Nästa steg
Mer information om de funktioner och funktioner som beskrivs ovan finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
