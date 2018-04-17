---
title: Hantera server-administratörer i Azure Analysis Services | Microsoft Docs
description: Lär dig hur du hanterar server-administratörer för en Analysis Services-server i Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: def09f2853f761f3fefca80f341e6cc0557bac86
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
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

