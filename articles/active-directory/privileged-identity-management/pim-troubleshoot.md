---
title: Felsöka ett problem med privilegierad identitetshantering – Azure Active Directory | Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299694"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Felsöka ett problem med privilegierad identitetshantering

Har du problem med PIM (Privileged Identity Management) i Azure Active Directory (Azure AD)? Den information som följer kan hjälpa dig att få saker och ting att fungera igen.

## <a name="access-to-azure-resources-denied"></a>Åtkomst till Azure-resurser nekad

### <a name="problem"></a>Problem

Som aktiv ägare eller användaråtkomstadministratör för en Azure-resurs kan du se din resurs i Privilegierad identitetshantering men kan inte utföra några åtgärder som att göra en kvalificerad tilldelning eller visa en lista över rolltilldelningar från resursen översiktssida. Någon av dessa åtgärder resulterar i ett auktoriseringsfel.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa när rollen administratör för användaråtkomst för PIM-tjänstens huvudnamn togs bort av misstag från prenumerationen. För att tjänsten Privilegierad identitetshantering ska kunna komma åt Azure-resurser bör ms-PIM-tjänstens huvudnamn alltid ha tilldelats rollen Administratör för [användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) över Azure-prenumerationen.

### <a name="resolution"></a>Lösning

Tilldela rollen Administratör för användaråtkomst till huvudnamnet för tjänsten Privilegierad identitetshantering (MS–PIM) på prenumerationsnivå. Den här tilldelningen bör tillåta tjänsten Privilegierad identitetshantering att komma åt Azure-resurserna. Rollen kan tilldelas på hanteringsgruppsnivå eller på prenumerationsnivå, beroende på dina behov. Mer informationstjänsthuvudnamn finns i [Tilldela ett program till en roll](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>Nästa steg

- [Licenskrav för att använda privilegierad identitetshantering](subscription-requirements.md)
- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Distribuera Privileged Identity Management](pim-deployment-plan.md)
