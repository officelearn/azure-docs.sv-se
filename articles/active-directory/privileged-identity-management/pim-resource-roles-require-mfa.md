---
title: Kräv multifaktorautentisering för Azure-resursroller i PIM | Microsoft Docs
description: Lär dig mer om att kräva multifaktorautentisering (MFA) för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 171d79856cf67dae9573dd1076c2ae4617cf86d1
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190576"
---
# <a name="require-multi-factor-authentication-for-azure-resource-roles-in-pim"></a>Kräv multifaktorautentisering för Azure-resursroller i PIM

Privileged Identity Management (PIM) för Azure-resursroller kan resurs-administratörer och identitetsadministratörer att skydda viktiga Azure-infrastrukturen med tidsbegränsade medlemskap och just-in-time-åtkomst. Dessutom tillhandahåller PIM valfritt tillämpning av Azure Multi-Factor Authentication för två olika scenarier.

## <a name="require-multi-factor-authentication-to-activate"></a>Kräv Multi-Factor Authentication att aktivera

Resurs-administratörer kan kräva berättigade medlemmar i en roll att köra Azure Multi-Factor Authentication innan du kan aktivera. Den här processen säkerställer att den användare som begär aktivering är de säger att de är med rimlig säkerhet. Tillämpa det här alternativet skyddar kritiska resurser i situationer när användarkonton kan ha komprometterats. 

Välj en resurs i listan över hanterade resurser om du vill framtvinga detta krav. Från den [Översiktsinstrumentpanel](pim-resource-roles-overview-dashboards.md), Välj en roll i listan över roller på den nedre högra delen av skärmen.

Dessutom kan du får rollinställningar från antingen den **roller** eller **rollinställningar** flikarna i den vänstra rutan.

>[!Note]
>Om alternativen i den vänstra rutan är nedtonad och du ser en banderoll överst på sidan med texten ”du har berättigade roller som kan aktiveras”, men du är inte administratör active. Det innebär att du måste [aktivera](pim-resource-roles-activate-your-roles.md) innan du fortsätter.

![”Roller” och ”inställningar” flikar ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Om du vill visa en rollmedlemskap, Välj **rollinställningar** från fältet högst upp på skärmen för att öppna den **rollen inställningen detalj**.

Om du vill ändra rollinställningarna för, Välj den **redigera** längst upp.

I avsnittet under **aktivera**, Välj kryssrutan för att **Multi-Factor Authentication vid aktivering**. Välj sedan **Spara**.

![Kräv Multi-Factor Authentication vid aktivering](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Kräv Multifaktorautentisering för tilldelning

I vissa fall kanske en resursadministratör vill tilldela en medlem till en roll under en kort period (en dag, till exempel). I det här fallet behöver de inte de tilldelade medlemmarna ska kunna begära aktivering. I det här scenariot kan inte PIM använda Multifaktorautentisering när medlemmen använder sina rolltilldelningen, eftersom de redan är aktiv i rollen från den tidpunkt då de är tilldelade.

För att säkerställa att resursadministratören uppfyller tilldelningen är de säger att de är, kan du använda Multifaktorautentisering vid uppgift.

Från samma roll inställningen informationsskärmen, markerar du kryssrutan **Multi-Factor Authentication på direkttilldelning**.

![Kräv Multifaktorautentisering vid direkt uppgift](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure-resurs rollinställningar i PIM](pim-resource-roles-configure-role-settings.md)
- [Konfigurera säkerhetsaviseringar för Azure-resursroller i PIM](pim-resource-roles-configure-alerts.md)


