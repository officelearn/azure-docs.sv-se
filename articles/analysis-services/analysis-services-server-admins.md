---
title: "Hantera server-administratörer i Azure Analysis Services | Microsoft Docs"
description: "Lär dig hur du hanterar server-administratörer för en Analysis Services-server i Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: d90f1e3df8f5934d5c334ec72b5726f105842ca1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
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
[Rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-what-is.md)  

