---
title: Vad är HR driven-etablering med Azure Active Directory? | Microsoft Docs
description: Beskriver översikt över HR driven etablering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b8ed75a87d1ac638e5ca55058a3d1ad7fcdffa
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135388"
---
# <a name="what-is-hr-driven-provisioning"></a>Vad är HR driven etablering?

![HR-etablering](./media/what-is-hr-driven-provisioning/cloud2a.png)

HR driven etablering är en process för att skapa digitala identiteter baserade på ett personal system.  HR-systemen blir auktoritets start för dessa nyligen skapade digitala identiteter och är ofta start punkten för flera etablerings processer.  Om en ny medarbetare till exempel ansluter till företaget, skapas de i det mänskliga resurs systemet.  Skapandet, utlöser etableringen av ett användar konto till Active Directory och Azure AD Connect etablerar det här kontot till Azure AD osv.

HR driven etablering kan antingen vara lokalt baserad eller molnbaserad.

## <a name="on-premises-based-hr-provisioning"></a>Lokal baserad HR-etablering
Lokal baserad HR-etablering sker med hjälp av ett lokalt HR-system och ett sätt att tillhandahålla nya digitala identiteter.

HR-system levereras i flera olika paket, program varu paket och kan använda SQL-servrar, LDAP-kataloger osv.

För närvarande använder Microsofts lokala HR-etablerings lösningar Microsoft Identity Manager för att utlösa etablering när en ny identitet skapas i de här HR-systemen.

Med MIM kan du etablera användare från dina lokala HR-system för att Active Directory eller Azure AD.

Information om Microsoft Identity Manager och de system som stöds av finns i [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) -dokumentationen.

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Nästa steg 
- [Vad är hantering av identitets livs cykel](what-is-identity-lifecycle-management.md)
- [Vad är etablering?](what-is-provisioning.md)
- [Vad är app-etablering?](what-is-app-provisioning.md)
- [Vad är etablering mellan kataloger?](what-is-inter-directory-provisioning.md)
- [Vad är katalog etablering?](what-is-inter-directory-provisioning.md)