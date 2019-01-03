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
ms.openlocfilehash: 5afc434ccd7a41c6fa1f4fec300941458c84889e
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53629583"
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

Använd [New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) cmdlet för att ange parametern administratör när du skapar en ny server. <br>
Använd [Set-azurermanalysisservicesserver har](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver) cmdlet för att ändra parametern administratör för en befintlig server.

## <a name="rest-api"></a>REST-API

Använd [skapa](https://docs.microsoft.com/rest/api/analysisservices/servers/create) att ange egenskapen asAdministrator när du skapar en ny server. <br>
Använd [uppdatering](https://docs.microsoft.com/rest/api/analysisservices/servers/update) att ange egenskapen asAdministrator när du ändrar en befintlig server. <br>



## <a name="next-steps"></a>Nästa steg 

[Autentisering och användarbehörigheter](analysis-services-manage-users.md)  
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)  

