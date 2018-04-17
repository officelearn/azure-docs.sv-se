---
title: Privileged Identity Management resurser för Azure - Använd anpassade roller till målet PIM-inställningar | Microsoft Docs
description: Beskriver hur du använder anpassade roller i PIM för Azure-resurser.
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
ms.openlocfilehash: 6336d99df1bbdd71c66a9757af1d9fb356a91bf6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="use-custom-roles-to-target-pim-settings"></a>Använd anpassade roller till målet PIM-inställningar

Det kan vara nödvändigt att använda strikt PIM-inställningar för vissa medlemmar i en roll, och ger större självständigt ansvarar för andra. Föreställ dig ett scenario där din organisation anlitar sevral kontraktet har associerats till att utvecklingen av ett program som körs i en Azure-prenumeration. 

Som administratör av en resurs kan du vill att anställda i din organisation ha berättigade åtkomst utan godkännande krävs, men alla kontraktet har associerats begära godkännande när de begär aktivering. Följ stegen nedan disposition processen för att aktivera riktade PIM-inställningar för roller i Azure-resurs.

## <a name="create-the-custom-role"></a>Skapa den anpassade rollen

[Använd den här guiden för att skapa en anpassad roll för en resurs](../../role-based-access-control/custom-roles.md).

Innehåller ett beskrivande namn så att du lätt kan komma ihåg vilket inbyggd roll som du tänkt att duplicera.

>[!NOTE]
>Se till att den anpassade rollen är en dubblett av den roll som du tänkt och dess område matchar den inbyggda rollen.

## <a name="apply-pim-settings"></a>Tillämpa PIM-inställningar

När rollen har skapats i din klientorganisation, navigera till PIM och välj den resurs som rollen är begränsad till.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[Konfigurera inställningarna för PIM](pim-resource-roles-configure-role-settings.md) som ska tillämpas på dessa medlemmar

Slutligen [tilldela roller](pim-resource-roles-assign-roles.md) i gruppen distinkta medlemmar som du vill rikta med de här inställningarna.

## <a name="next-steps"></a>Nästa steg

[Granska ägarna av prenumeration](pim-resource-roles-perform-access-review.md)
