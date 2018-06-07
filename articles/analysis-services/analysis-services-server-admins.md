---
title: Hantera server-administratörer i Azure Analysis Services | Microsoft Docs
description: Lär dig hur du hanterar server-administratörer för en Analysis Services-server i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 59aaf07edf387d40f47d7d82dcb116802a003dc1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596176"
---
# <a name="manage-server-administrators"></a>Hantera server-administratörer
Server-administratörer måste vara en giltig användare eller grupp i Azure Active Directory (Azure AD) för klienten som servern finns. Du kan använda **Analysis Services-administratörer** för servern i Azure-portalen eller serveregenskaper i SSMS att hantera serveradministratörer. 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Lägg till server-administratörer med Azure-portalen
1. I den portal för servern, klicka på **Analysis Services-administratörer**.
2. I  **\<servername >-Analysis Services-administratörer**, klickar du på **Lägg till**.
3. I **lägga till serveradministratörer**väljer du användarkonton från din Azure AD eller bjuda in externa användare efter e-postadress.

    ![Server-administratörer i Azure-portalen](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Lägg till server-administratörer med SSMS
1. Högerklicka på servern > **egenskaper**.
2. I **Analysis Server-egenskaper**, klickar du på **säkerhet**.
3. Klicka på **Lägg till**, och sedan ange den e-postadressen för en användare eller grupp i din Azure AD.
   
    ![Lägg till server-administratörer i SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Nästa steg 
[Autentisering och användarbehörigheter](analysis-services-manage-users.md)  
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)  

