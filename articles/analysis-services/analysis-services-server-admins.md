---
title: Hantera Server administratörer i Azure Analysis Services | Microsoft Docs
description: 'Den här artikeln beskriver hur du hanterar Server administratörer för en Azure Analysis Services-server med hjälp av Azure Portal-, PowerShell-eller REST-API: er.'
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bc17d27837d5b96f06b5172fb019db873418db94
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922957"
---
# <a name="manage-server-administrators"></a>Hantera serveradministratörer

Server administratörer måste vara en giltig användare, tjänstens huvud namn eller säkerhets grupp i Azure Active Directory (Azure AD) för den klient där-servern finns. Du kan använda **Analysis Services administratörer** för servern i Azure Portal, Server egenskaper i SSMS, PowerShell eller REST API för att hantera Server administratörer. 

Använd om du vill lägga till en **säkerhets grupp** `obj:groupid@tenantid` . Tjänstens huvud namn stöds inte i säkerhets grupper som har lagts till i Server administratörs rollen.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Lägga till Server administratörer med hjälp av Azure Portal

1. Klicka på **Analysis Services administratörer**på din server i portalen.
2. ** \<servername> Analysis Services administratörer**klickar du på **Lägg till**.
3. I **Lägg till Server administratörer**väljer du användar konton från din Azure AD eller bjuder in externa användare via e-postadress.

    ![Server administratörer i Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Så här lägger du till Server administratörer med SSMS

1. Högerklicka på Server > **Egenskaper**.
2. I **Analysis Server egenskaper**klickar du på **säkerhet**.
3. Klicka på **Lägg till**och ange sedan e-postadressen för en användare eller grupp i din Azure AD.
   
    ![Lägga till Server administratörer i SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd cmdleten [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) för att ange parametern administratör när du skapar en ny server. <br>
Använd [set-AzAnalysisServicesServer-](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) cmdleten för att ändra parametern administratör för en befintlig server.

## <a name="rest-api"></a>REST-API

Använd [create](https://docs.microsoft.com/rest/api/analysisservices/servers/create) för att ange egenskapen för administratör när du skapar en ny server. <br>
Använd [Update](https://docs.microsoft.com/rest/api/analysisservices/servers/update) för att ange egenskapen för administratör när du ändrar en befintlig server. <br>



## <a name="next-steps"></a>Nästa steg 

[Autentisering och användarbehörigheter](analysis-services-manage-users.md)  
[Hantera databas roller och användare](analysis-services-database-users.md)  
[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md)  
