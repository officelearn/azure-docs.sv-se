---
title: Använda anpassade roller för Privileged Identity Management-Målinställningar för Azure-resurser | Microsoft Docs
description: Beskriver hur du använder anpassade roller för Azure-resurser med PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: e3df34e761f17aa3c1949af390e57360d84a304f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Använd anpassade roller till målet Privileged Identity Management-inställningar

Du kan behöva använda strikt Privileged Identity Management (PIM) inställningar för vissa medlemmar i en roll, och ger större självständigt ansvarar för andra. Överväg ett scenario där din organisation anlitar flera kontrakt har associerats till att utvecklingen av ett program som körs i en Azure-prenumeration.

Som administratör av en resurs kan vilja du att anställda ska vara tillämplig för åtkomst utan att kräva godkännande. Alla avtal har associerats måste vara godkända när de begär åtkomst till organisationens resurser.

Följ stegen som beskrivs i nästa avsnitt för att ställa in riktade PIM-inställningar för roller i Azure-resurs.

## <a name="create-the-custom-role"></a>Skapa den anpassade rollen

Om du vill skapa en anpassad roll för en resurs, Följ stegen som beskrivs i [skapa anpassade roller för rollbaserad åtkomstkontroll i](../role-based-access-control-custom-roles.md).

När du skapar en anpassad roll kan du inkludera ett beskrivande namn så att du lätt kan komma ihåg vilket inbyggd roll som du tänkt att duplicera.

> [!NOTE]
> Se till att den anpassade rollen är en dubblett av den inbyggda rollen som du vill duplicera, och att dess område matchar den inbyggda rollen.

## <a name="apply-pim-settings"></a>Tillämpa PIM-inställningar

När rollen skapas i din klient i Azure-portalen går du till den **Privileged Identity Management - Azure-resurser** fönstret. Välj den resurs som rollen gäller.

![”Privileged Identity Management - Azure-resurser” fönstret](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Konfigurera inställningarna för PIM](pim-resource-roles-configure-role-settings.md) som ska tillämpas på dessa medlemmar i rollen.

Slutligen [tilldela roller](pim-resource-roles-assign-roles.md) i gruppen distinkta medlemmar som du vill använda som mål med de här inställningarna.

## <a name="next-steps"></a>Nästa steg

[Granska prenumeration ägare och åtkomst](pim-resource-roles-perform-access-review.md)
