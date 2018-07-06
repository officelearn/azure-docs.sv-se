---
title: Hantera serveradministratörer i Azure Analysis Services | Microsoft Docs
description: Lär dig hur du hanterar serveradministratörer för en Analysis Services-server i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9d8f74bd66fc7c980c4fc5f83492aad7d8a4aa5c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866975"
---
# <a name="manage-server-administrators"></a>Hantera serveradministratörer
Server-administratörer måste vara en giltig användare eller säkerhetsgrupp grupp i Azure Active Directory (Azure AD) för klienten som servern finns. Du kan använda **Analysis Services-administratörer** för din server i Azure portal eller serveregenskaper i SSMS för att hantera serveradministratörer. 

> [!NOTE]
> Säkerhetsgrupper måste ha den `MailEnabled` egenskapen `True`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Att lägga till serveradministratörer med hjälp av Azure-portalen
1. I portalen för din server, klickar du på **Analysis Services-administratörer**.
2. I  **\<servername >-Analysis Services-administratörer**, klickar du på **Lägg till**.
3. I **Lägg till serveradministratörer**väljer du användarkonton från din Azure AD eller Bjud in extern användare av e-postadress.

    ![Serveradministratörer i Azure-portalen](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Att lägga till serveradministratörer med hjälp av SSMS
1. Högerklicka på servern > **egenskaper**.
2. I **serveregenskaper i Analysis**, klickar du på **Security**.
3. Klicka på **Lägg till**, och ange den e-postadressen för en användare eller grupp i Azure AD.
   
    ![Lägg till serveradministratörer i SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>Nästa steg 
[Autentisering och användarbehörigheter](analysis-services-manage-users.md)  
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)  

