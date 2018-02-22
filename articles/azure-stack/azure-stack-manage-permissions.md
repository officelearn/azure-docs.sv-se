---
title: "Hantera behörigheter till resurser per användare i Azure-stacken (tjänstadministratören och klient) | Microsoft Docs"
description: "Lär dig hur du hantera RBAC behörigheter som en tjänstadministratör eller innehavare."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: fenila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: mabrigg
ms.reviewer: thomas.roettinger
ms.openlocfilehash: 0e50ea44ebb0b0a7285dab04666dd55cad480c6a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="manage-role-based-access-control"></a>Hantera rollbaserad åtkomstkontroll

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

En användare i Azure-stacken kan vara en läsare, ägare eller deltagare för varje instans av en prenumeration, resursgrupp eller tjänst. Till exempel kan användare A läsaren behörighet till en prenumeration, men har tillräckliga rättigheter till sju till virtuell dator.

 - Läsare: Användare kan visa allt, men göra inte några ändringar.
 - Deltagare: Användare kan hantera allt utom åtkomst till resurser.
 - Ägare: Användare kan hantera allt, inklusive åtkomst till resurser.

## <a name="set-access-permissions-for-a-user"></a>Ange åtkomstbehörighet för en användare

1. Logga in med ett konto som har ägarbehörighet för till resursen som du vill hantera.
2. I bladet för resursen klickar du på den **åtkomst** ikonen ![](media/azure-stack-manage-permissions/image1.png).
3. I den **användare** bladet, klickar du på **roller**.
4. I den **roller** bladet, klickar du på **Lägg till** att lägga till behörigheter för användaren.

## <a name="set-access-permissions-for-a-universal-group"></a>Ange åtkomstbehörighet för en universell grupp 

> [!Note]  
Gäller endast för Active Directory Federation Services (AD FS).

1. Logga in med ett konto som har ägarbehörighet för till resursen som du vill hantera.
2. I bladet för resursen klickar du på den **åtkomst** ikonen ![](media/azure-stack-manage-permissions/image1.png).
3. I den **användare** bladet, klickar du på **roller**.
4. I den **roller** bladet, klickar du på **Lägg till** att lägga till behörigheter för universell grupp Active Directory-gruppen.

## <a name="next-steps"></a>Nästa steg
[Lägga till en Azure Stack-klientorganisation](azure-stack-add-new-user-aad.md)

