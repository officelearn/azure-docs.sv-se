---
title: Identitetsdatalagring för kunder i Australien och Nya Zeeland – Azure AD
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
ms.openlocfilehash: 850298719d5636e964b0c338d7a2a4cc9bb8aece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370289"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Identitetsdatalagring för kunder i Australien och Nya Zeeland i Azure Active Directory

Identitetsdata lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst som Office 365 och Azure. Om du vill ha information om var dina identitetskunddata lagras kan du använda avsnittet [Var finns dina data i](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Microsoft Trust Center.

> [!NOTE]
> Tjänster och program som integreras med Azure AD har åtkomst till identitetskunddata. Utvärdera varje tjänst och program som du använder för att avgöra hur identitetskunddata bearbetas av den specifika tjänsten och programmet och om de uppfyller företagets datalagringskrav. Läs mer om Microsoft-tjänsternas datahemvist i avsnittet Var finns dina data? på Microsoft Trust Center.

För kunder som har angett en adress i Australien eller Nya Zeeland behåller Azure AD identitetsdata för dessa tjänster i australiska datacenter: 
- Hantering av Azure AD-katalog 
- Autentisering

Alla andra Azure AD-tjänster lagrar kunddata i globala datacenter. Information om hur du hittar datacentret för en tjänst finns i [Azure Active Directory – Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multifaktorautentisering (MFA)

MFA lagrar identitetskunddata i globala datacenter. Mer information om användarinformation som samlas in och lagras av molnbaserade Azure MFA- och Azure MFA Server finns i [azure multifaktorautentisering användardatainsamling](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

## <a name="next-steps"></a>Nästa steg
Mer information om någon av de funktioner som beskrivs ovan finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
