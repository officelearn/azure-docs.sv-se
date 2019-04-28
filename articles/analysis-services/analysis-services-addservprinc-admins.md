---
title: Lägg till ett huvudnamn för tjänsten i Azure Analysis Services-serverrollen admin | Microsoft Docs
description: Lär dig hur du lägger till ett automation-tjänstens huvudnamn till administratörsrollen för servern
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 701be795ca217c4a2dc5a7dbaa3a3717d16c85bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024632"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Lägga till tjänstens huvudnamn till administratörsrollen för servern 

 Om du vill automatisera obevakad PowerShell-åtgärder, ett huvudnamn för tjänsten måste ha **serveradministratören** behörighet för Analysis Services-servern som hanteras. Den här artikeln beskriver hur du lägger till ett huvudnamn för tjänsten i administratörsrollen på ett Azure AS-servern.

## <a name="before-you-begin"></a>Innan du börjar
Innan du slutför den här uppgiften, måste du ha ett huvudnamn för tjänsten i Azure Active Directory.

[Skapa tjänstens huvudnamn – Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Nödvändiga behörigheter
För att slutföra den här uppgiften, måste du ha [serveradministratören](analysis-services-server-admins.md) behörigheter på Azure AS-servern. 

## <a name="add-service-principal-to-server-administrators-role"></a>Lägg till tjänstens huvudnamn till administratörsrollen för servern

1. I SSMS, ansluter du till din Azure AS-servern.
2. I **serveregenskaper** > **Security**, klickar du på **Lägg till**.
3. I **väljer en användare eller grupp**, Sök efter din registrerad app efter namn, Välj och klicka sedan på **Lägg till**.

    ![Sök efter konto för tjänstens huvudnamn](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Kontrollera tjänsten huvudkonto-ID och klicka sedan på **OK**.
    
    ![Sök efter konto för tjänstens huvudnamn](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> För åtgärder med hjälp av Azure PowerShell-cmdletar, service principal körs Schemaläggaren måste tillhöra den **ägare** för resursen i [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Relaterad information

* [Hämta PowerShell-modulen för SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ned SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


