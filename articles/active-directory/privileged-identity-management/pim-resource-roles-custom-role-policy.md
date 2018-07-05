---
title: Använda anpassade roller för Privileged Identity Management Målinställningar för Azure-resurser | Microsoft Docs
description: Beskriver hur du använder anpassade roller för Azure-resurser med PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: f086d8038e6d27990c49749438ee05e3e39a5aec
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442917"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Använda anpassade roller för Privileged Identity Management Målinställningar

Du kan behöva gäller strikta inställningar för Privileged Identity Management (PIM) för vissa medlemmar i en roll, samtidigt som det ger större självständigt för andra. Tänk dig ett scenario där anlitar din organisation flera kontrakt associates att underlätta utvecklingen av ett program som körs i en Azure-prenumeration.

Som administratör av en resurs kan du anställda att vara berättigade till åtkomst utan att kräva godkännande. Alla avtal associates måste vara godkända när de begär åtkomst till organisationens resurser.

Följ stegen som beskrivs i nästa avsnitt för att konfigurera inställningar för riktade PIM för Azure-resursroller.

## <a name="create-the-custom-role"></a>Skapa anpassad roll

Om du vill skapa en anpassad roll för en resurs, följer du stegen som beskrivs i [skapa anpassade roller för rollbaserad åtkomstkontroll i](../role-based-access-control-custom-roles.md).

När du skapar en anpassad roll kan du inkludera ett beskrivande namn så att du lätt kan komma ihåg vilken inbyggd roll som du försöker att duplicera.

> [!NOTE]
> Kontrollera att den anpassade rollen är en dubblett av den inbyggda rollen som du vill duplicera och att den inbyggda rollen som överensstämmer med sitt omfång.

## <a name="apply-pim-settings"></a>Använda PIM-inställningar

När rollen skapas i din klient i Azure-portalen går du till den **Privileged Identity Management - Azure-resurser** fönstret. Välj den resurs som rollen gäller.

![”Privileged Identity Management - Azure-resurser” fönstret](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Konfigurera PIM rollinställningar](pim-resource-roles-configure-role-settings.md) som ska tillämpas på dessa medlemmar i rollen.

Slutligen [tilldela roller](pim-resource-roles-assign-roles.md) till distinkta grupp med medlemmar som du vill använda med de här inställningarna.

## <a name="next-steps"></a>Nästa steg

[Granska prenumerationsägare och åtkomst](pim-resource-roles-perform-access-review.md)
