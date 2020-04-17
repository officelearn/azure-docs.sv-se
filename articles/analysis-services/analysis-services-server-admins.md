---
title: Hantera serveradministratörer i Azure Analysis Services | Microsoft-dokument
description: I den här artikeln beskrivs hur du hanterar serveradministratörer för en Azure Analysis Services-server med hjälp av Azure-portalen, PowerShell- eller REST-API:erna.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 944a84405bd7e03b72b2610278f9f0e4d3cfaf38
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454245"
---
# <a name="manage-server-administrators"></a>Hantera serveradministratörer

Serveradministratörer måste vara en giltig användar- eller säkerhetsgrupp i Azure Active Directory (Azure AD) för den klient där servern finns. Du kan använda **Analysis Services-administratörer** för servern i Azure portal, Serveregenskaper i SSMS, PowerShell eller REST API för att hantera serveradministratörer. 

När du lägger `obj:groupid@tenantid`till en **säkerhetsgrupp**använder du .

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Så här lägger du till serveradministratörer med Hjälp av Azure Portal

1. Klicka på **Analysis Services Admins**för servern i portalen.
2. Klicka ** \<på** **Lägg till**i servernamn>sadministratörer - Analysis Services Admins .
3. Välj användarkonton från din Azure AD eller bjud in externa användare via e-postadress i **Lägg till serveradministratörer.**

    ![Serveradministratörer i Azure-portalen](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Så här lägger du till serveradministratörer med hjälp av SSMS

1. Högerklicka på servern > **egenskaper**.
2. Klicka på **Säkerhet**i **egenskaper för Analysserver.**
3. Klicka på **Lägg till**och ange sedan e-postadressen för en användare eller grupp i din Azure AD.
   
    ![Lägga till serveradministratörer i SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd [Cmdlet New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) för att ange parametern Administratör när du skapar en ny server. <br>
Använd [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) cmdlet för att ändra administratörsparametern för en befintlig server.

## <a name="rest-api"></a>REST-API

Använd [Skapa](https://docs.microsoft.com/rest/api/analysisservices/servers/create) för att ange egenskapen asAdministrator när du skapar en ny server. <br>
Använd [Uppdatera](https://docs.microsoft.com/rest/api/analysisservices/servers/update) för att ange egenskapen asAdministrator när du ändrar en befintlig server. <br>



## <a name="next-steps"></a>Nästa steg 

[Autentisering och användarbehörigheter](analysis-services-manage-users.md)  
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)  

