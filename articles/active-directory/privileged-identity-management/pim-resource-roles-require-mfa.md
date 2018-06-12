---
title: Använda Azure Multifaktorautentisering i Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Det här dokumentet beskriver hur du aktiverar multifaktorautentisering för PIM-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 0c50a17b651fc1fb5d49915da5da4a37d40a0d3e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260071"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Använda Azure Multifaktorautentisering i Azure-resurser med hjälp av Privileged Identity Management

Privileged Identity Management (PIM) för Azure-resursroller kan resurs-administratörer och identitet administratörer att skydda viktiga Azure-infrastrukturen med Tidsbundna medlemskap och just-in-time-åtkomst. Dessutom ger PIM valfria tvingande Azure Multi-Factor Authentication för två olika scenarier.

## <a name="require-multi-factor-authentication-to-activate"></a>Kräv Multi-Factor Authentication att aktivera

Resurs-administratörer kan kräva berättigade medlemmarna i en roll för att köra Azure Multi-Factor Authentication innan de kan aktivera. Den här processen säkerställer att den användare som begär aktivering är som göra detta med rimlig säkerhet. När det här alternativet skyddar viktiga resurser i situationer när användarkonton kan ha drabbats. 

Om du vill framtvinga detta krav, väljer du en resurs från listan över hanterade resurser. Från den [översikt över instrumentpanelen](pim-resource-roles-overview-dashboards.md), Välj en roll i listan över roller på den nedre högra delen av skärmen.

Dessutom kan du inställningar för från antingen den **roller** eller **rollinställningar** flikar i den vänstra rutan.

>[!Note]
>Om alternativen i den vänstra rutan är nedtonad och du ser en banderoll överst på sidan med texten ”du har berättigade roller som kan aktiveras”, men du är inte administratör active. Det innebär att du måste [aktivera](pim-resource-roles-activate-your-roles.md) innan du fortsätter.

![”Roller” och ”inställningar” flikar ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Om du vill visa en rollmedlemskap **rollinställningar** från fältet längst upp på skärmen för att öppna den **roll inställningen detalj**.

Om du vill ändra rollinställningarna för, Välj den **redigera** längst upp.

I avsnittet under **aktivera**, markera kryssrutan för att **Multi-Factor Authentication vid aktivering**. Välj sedan **Spara**.

![Kräv Multi-Factor Authentication vid aktivering](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Kräv Multi-Factor Authentication för tilldelning

I vissa fall kan en resursadministratör vill tilldela en medlem i en roll för en kort tid (en dag, till exempel). I det här fallet behöver de tilldelade medlemmarna att begära aktivering. I det här scenariot kan inte PIM använda Multifaktorautentisering när medlemmen använder sina rolltilldelning eftersom de redan aktiv i rollen från den tidpunkt då de är tilldelade.

Se till att resursadministratören uppfyller tilldelningen som göra detta, kan du använda Multifaktorautentisering för tilldelning.

Markera kryssrutan för från samma roll inställningen information skärm **Multi-Factor Authentication på direkttilldelning**.

![Kräv Multi-Factor Authentication på direkttilldelning](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Nästa steg

[Kräv godkännande för att aktivera](pim-resource-roles-approval-workflow.md)

[Använda granskningsloggen](pim-resource-roles-use-the-audit-log.md)



