---
title: "Hantera behörigheter till resurser per användare i Azure-stacken | Microsoft Docs"
description: "Lär dig hur du hantera RBAC behörigheter som en tjänstadministratör eller innehavare."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: dfec5648ff383fd98965c1918cdab6472bb3c17f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
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


