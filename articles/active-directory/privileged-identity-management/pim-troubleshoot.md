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
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7fc3508f7f672e277577f92218ff1860b676cb
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72559481"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Felsöka ett problem med Privileged Identity Management

Har du problem med Privileged Identity Management (PIM) i Azure Active Directory (Azure AD)? Informationen som följer kan hjälpa dig att få saker att fungera igen.

## <a name="access-to-azure-resources-denied"></a>Åtkomst till Azure-resurser nekas

### <a name="problem"></a>Problem

Du får ett auktoriseringsfel när du försöker göra en användare tillgänglig för en administratörs roll för Azure AD och du inte kan komma åt Azure-resurser under Privileged Identity Management. Du kan inte komma åt Azure-resurser under Privileged Identity Management även om du är global administratör och Prenumerationens ägare.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa när rollen som administratör för användar åtkomst för PIM-tjänstens huvud namn har tagits bort av en prenumeration. För att Privileged Identity Managements tjänsten ska kunna få åtkomst till Azure-resurser, ska MS-PIM-tjänstens huvud namn alltid ha tilldelats [rollen administratör för användar åtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) i Azure-prenumerationen.

### <a name="resolution"></a>Upplösning

Tilldela rollen administratör för användar åtkomst till tjänsten Privileged Identity Management-tjänstens huvud namn (MS – PIM) på prenumerations nivå. Den här tilldelningen ska tillåta att Privileged Identity Management-tjänsten får åtkomst till Azure-resurserna. Rollen kan tilldelas på en hanterings grupps nivå eller på prenumerations nivån, beroende på dina behov. Mer information om tjänstens huvud namn finns i [tilldela ett program till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role).

## <a name="next-steps"></a>Nästa steg

- [Licens krav för att använda Privileged Identity Management](subscription-requirements.md)
- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Distribuera Privileged Identity Management](pim-deployment-plan.md)
