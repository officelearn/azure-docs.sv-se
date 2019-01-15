---
title: Hantera behörigheter till resurser per användare i Azure Stack (tjänstadministratör och klient) | Microsoft Docs
description: Lär dig hantera RBAC-behörigheter som en tjänstadministratör eller klient.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 26ce7139b856fc2f8d7d2cad549b3dd3c0f5e406
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304694"
---
# <a name="manage-role-based-access-control"></a>Hantera rollbaserad åtkomstkontroll

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

En användare i Azure Stack kan vara en läsare, ägare eller deltagare för varje instans av en prenumeration, resursgrupp eller tjänsten. Användare A kan exempelvis läsare åtkomstbehörighet till en prenumeration men har ägarbehörighet för virtuell dator sju.

 - Läsare: Användaren kan visa allt, men göra inte några ändringar.
 - Deltagare: Användaren kan hantera allt förutom åtkomst till resurser.
 - Ägare: Användaren kan hantera allt, inklusive åtkomst till resurser.

## <a name="set-access-permissions-for-a-user"></a>Ange åtkomstbehörighet för en användare

1. Logga in med ett konto som har ägarbehörighet för den resurs du vill hantera.
2. I bladet för resursen klickar du på den **åtkomst** ikonen ![](media/azure-stack-manage-permissions/image1.png).
3. I den **användare** bladet klickar du på **roller**.
4. I den **roller** bladet klickar du på **Lägg till** och lägga till behörigheter för användaren.

## <a name="set-access-permissions-for-a-universal-group"></a>Ange åtkomstbehörighet för en universell grupp 

> [!Note]  
Gäller endast för Active Directory Federation Services (AD FS).

1. Logga in med ett konto som har ägarbehörighet för den resurs du vill hantera.
2. I bladet för resursen klickar du på den **åtkomst** ikonen ![](media/azure-stack-manage-permissions/image1.png).
3. I den **användare** bladet klickar du på **roller**.
4. I den **roller** bladet klickar du på **Lägg till** och lägga till behörigheter för universell grupp Active Directory-gruppen.

## <a name="next-steps"></a>Nästa steg
[Lägga till en Azure Stack-klientorganisation](azure-stack-add-new-user-aad.md)

