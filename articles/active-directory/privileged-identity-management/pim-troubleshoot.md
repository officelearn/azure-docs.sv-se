---
title: Felsöka ett problem med Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Lär dig hur du felsöker systemfel med roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299694"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Felsöka ett problem med Privileged Identity Management

Har du problem med Privileged Identity Management (PIM) i Azure Active Directory (Azure AD)? Informationen som följer kan hjälpa dig att få saker att fungera igen.

## <a name="access-to-azure-resources-denied"></a>Åtkomst till Azure-resurser nekas

### <a name="problem"></a>Problem

Som en aktiv ägare eller administratör för användar åtkomst för en Azure-resurs kan du se din resurs inuti Privileged Identity Management men inte utföra några åtgärder som att göra en berättigad tilldelning eller Visa en lista över roll tilldelningar från resursen översikts sida. Alla dessa åtgärder resulterar i ett auktoriseringsfel.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa när rollen som administratör för användar åtkomst för PIM-tjänstens huvud namn har tagits bort av en prenumeration. För att Privileged Identity Managements tjänsten ska kunna få åtkomst till Azure-resurser, ska MS-PIM-tjänstens huvud namn alltid ha tilldelats [rollen administratör för användar åtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) i Azure-prenumerationen.

### <a name="resolution"></a>Lösning

Tilldela rollen administratör för användar åtkomst till tjänsten Privileged Identity Management-tjänstens huvud namn (MS – PIM) på prenumerations nivå. Den här tilldelningen ska tillåta att Privileged Identity Management-tjänsten får åtkomst till Azure-resurserna. Rollen kan tilldelas på en hanterings grupps nivå eller på prenumerations nivån, beroende på dina behov. Mer information om tjänstens huvud namn finns i [tilldela ett program till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>Nästa steg

- [Licens krav för att använda Privileged Identity Management](subscription-requirements.md)
- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Distribuera Privileged Identity Management](pim-deployment-plan.md)
