---
title: Hantera serveradministratörer i Azure Analysis Services | Microsoft Docs
description: Lär dig hur du hanterar serveradministratörer för en Analysis Services-server i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 25eb111871bbe3b18f59b0d6c123c72f3e55c859
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60498720"
---
# <a name="manage-server-administrators"></a>Hantera serveradministratörer

Server-administratörer måste vara en giltig användare eller säkerhetsgrupp grupp i Azure Active Directory (Azure AD) för klienten som servern finns. Du kan använda **Analysis Services-administratörer** för din server i Azure-portalen serveregenskaper i SSMS, PowerShell eller REST API för att hantera serveradministratörer. 

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

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd [New AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) cmdlet för att ange parametern administratör när du skapar en ny server. <br>
Använd [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) cmdlet för att ändra parametern administratör för en befintlig server.

## <a name="rest-api"></a>REST-API

Använd [skapa](https://docs.microsoft.com/rest/api/analysisservices/servers/create) att ange egenskapen asAdministrator när du skapar en ny server. <br>
Använd [uppdatering](https://docs.microsoft.com/rest/api/analysisservices/servers/update) att ange egenskapen asAdministrator när du ändrar en befintlig server. <br>



## <a name="next-steps"></a>Nästa steg 

[Autentisering och användarbehörigheter](analysis-services-manage-users.md)  
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)  

