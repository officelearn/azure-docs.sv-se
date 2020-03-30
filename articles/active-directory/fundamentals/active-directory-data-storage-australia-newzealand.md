---
title: Identitetsdatalagring för australiska kunder - Azure AD
description: Läs om var Azure Active Directory lagrar identitetsrelaterade data för sina australiska kunder.
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
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460542"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identitetsdatalagring för kunder i Australien och Nya Zeeland i Azure Active Directory

Identitetsdata lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst som Office 365 och Azure. Om du vill ha information om var dina identitetskunddata lagras kan du använda avsnittet [Var finns dina data i](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Microsoft Trust Center.

> [!NOTE]
> Tjänster och program som integreras med Azure AD har åtkomst till identitetskunddata. Utvärdera varje tjänst och program som du använder för att avgöra hur identitetskunddata bearbetas av den specifika tjänsten och programmet och om de uppfyller företagets datalagringskrav. Läs mer om Microsoft-tjänsternas datahemvist i avsnittet Var finns dina data? på Microsoft Trust Center.

För kunder som har angett en adress i Australien och Nya Zeeland och använder Azure AD-kostnadsfri utgåva håller Azure AD PII-data vila i australiska datacenter. 

Alla andra Azure AD-premiumtjänster lagrar kunddata i globala datacenter. Information om hur du hittar datacentret för en tjänst finns i [Azure Active Directory – Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multifaktorautentisering (MFA)

MFA-tjänst i Azure AD lagrar identitetskunddata i globala datacenter i vila. Mer information om användarinformation som samlas in och lagras av molnbaserade Azure MFA- och Azure MFA Server finns i [azure multifaktorautentisering användardatainsamling](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency). Om kunder använder MFA kommer deras data att lagras utanför Australien datacenter i vila. 

## <a name="next-steps"></a>Nästa steg
Mer information om någon av de funktioner som beskrivs ovan finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
