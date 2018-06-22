---
title: Hantera server-administratörer i Azure Analysis Services | Microsoft Docs
description: Lär dig hur du hanterar server-administratörer för en Analysis Services-server i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ec1630f4de70f77c13e335c68aff16180e524c12
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307816"
---
# <a name="manage-server-administrators"></a>Hantera server-administratörer
Server-administratörer måste vara en giltig användare eller säkerhetsgrupp grupp i Azure Active Directory (Azure AD) för klienten som servern finns. Du kan använda **Analysis Services-administratörer** för servern i Azure-portalen eller serveregenskaper i SSMS att hantera serveradministratörer. 

> [!NOTE]
> Säkerhetsgrupper måste ha den `MailEnabled` egenskapen `True`.

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

