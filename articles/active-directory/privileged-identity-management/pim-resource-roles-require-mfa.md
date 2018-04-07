---
title: Privileged Identity Management för Azure-resurser - MFA | Microsoft Docs
description: Det här dokumentet beskriver hur du aktiverar multifaktorautentisering för PIM-resurser.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - resursroller - MFA

PIM för Azure-resursroller kan resurs-administratörer och identitet administratörer att skydda viktiga Azure-infrastrukturen med Tidsbundna medlemskap och just-in-time-åtkomst. Dessutom tillhandahåller PIM valfria tvingande för Azure Multi-Factor Authentication (MFA) för två olika scenarier.

## <a name="require-mfa-to-activate"></a>Kräv MFA för att aktivera

Resurs-administratörer kan kräva medlemmar av en roll lyckas Azure MFA innan du kan aktivera. Den här processen säkerställer användaren begär aktivering som göra detta med rimlig säkerhet. När det här alternativet skyddar viktiga resurser i situationer när användarkonton har komprometterats. 

Om du vill framtvinga detta krav, väljer du en resurs från listan över hanterade resurser. Från den [översikt över instrumentpanelen](pim-resource-roles-overview-dashboards.md), Välj en roll i listan över roller i nederkant höger på skärmen.

Dessutom kan du inställningar från antingen ”roller” eller ”inställningar” flikar i den vänstra navigeringsmenyn.

>[!Note]
>Om alternativen i den vänstra navigeringsmenyn är nedtonad och du ser en banderoll överst på sidan med texten ”du har berättigade roller som kan aktiveras” är inte administratör active och måste [aktivera](pim-resource-roles-activate-your-roles.md) innan du fortsätter.

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Om du visar en rollmedlemskap Välj ”inställningar” längst upp på skärmen för att öppna ”roll inställningen information”.

Klicka på den **redigera** längst upp för att ändra rollinställningarna.

I avsnittet under **aktivera**, klickar du på kryssrutan till **Multi-Factor Authentication att aktivera**, klicka på Spara.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>Kräva MFA för tilldelning

En resursadministratör kan i vissa fall vill tilldela en medlem i en roll för en kort tid (till exempel en dag) och inte behöver de tilldelade medlemmarna att begära aktivering. I det här scenariot kan inte PIM tvinga MFA när medlemmen använder sina rolltilldelning eftersom de redan aktiv i rollen från den tidpunkt då de är tilldelade.

För att säkerställa resursadministratören uppfyller tilldelningen som göra detta ska använda du Multifaktorautentisering för tilldelning.

Markera kryssrutan för ”kräver Multi-Factor Authentication för tilldelning” från samma roll inställningen information skärm.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Nästa steg

[Kräv godkännande för att aktivera](pim-resource-roles-approval-workflow.md)

[Använda granskningsloggen](pim-resource-roles-use-the-audit-log.md)



