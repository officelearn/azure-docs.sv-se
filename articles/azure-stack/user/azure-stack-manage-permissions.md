---
title: "Hantera behörigheter till resurser per användare i Azure-stacken | Microsoft Docs"
description: "Lär dig hur du hantera RBAC behörigheter som en tjänstadministratör eller innehavare."
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 1e0ced4b2d65456034ba3f8543152702c089a0b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control"></a>Hantera rollbaserad åtkomstkontroll

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

En användare i Azure-stacken kan vara en läsare, ägare eller deltagare för varje instans av en prenumeration, resursgrupp eller tjänst. Användare A kanske exempelvis har reader-behörighet till prenumerationen 1 men har tillräckliga rättigheter till 7 för virtuell dator.

* Läsare: Användare kan visa allt, men göra inte några ändringar.
* Deltagare: Användare kan hantera allt utom åtkomst till resurser.
* Ägare: Användare kan hantera allt, inklusive åtkomst till resurser.

## <a name="set-access-permissions-for-a-user"></a>Ange åtkomstbehörighet för en användare
1. Logga in med ett konto som har ägarbehörighet för till resursen som du vill hantera.
2. I bladet för resursen klickar du på den **åtkomst** ikonen ![](media/azure-stack-manage-permissions/image1.png).
3. I den **användare** bladet, klickar du på **roller**.
4. I den **roller** bladet, klickar du på **Lägg till** att lägga till behörigheter för användaren.

## <a name="next-steps"></a>Nästa steg


