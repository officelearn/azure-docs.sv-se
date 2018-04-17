---
title: Lägga till en princip för tjänsten i Azure Analysis Services-serverrollen admin | Microsoft Docs
description: Lär dig hur du lägger till en princip för automation-tjänsten till rollen Administratör
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9c6417e069bbed38b1f6e9317636a10834ce7197
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="add-a-service-principle-to-the-server-administrator-role"></a>Lägga till en princip för tjänsten server-administratör 

 För att automatisera uppgifter för obevakad PowerShell, en tjänst-princip måste ha **serveradministratören** behörighet för Analysis Services-servern hanteras. Den här artikeln beskriver hur du lägger till en princip för tjänsten administratörer serverrollen på en Azure AS-server.

## <a name="before-you-begin"></a>Innan du börjar
Innan du slutför den här uppgiften, måste du ha en princip för tjänsten som registrerats i Azure Active Directory.

[Skapa service - principen i Azure-portalen](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Nödvändiga behörigheter
För att slutföra den här uppgiften, måste du ha [serveradministratören](analysis-services-server-admins.md) behörigheter på Azure AS-servern. 

## <a name="add-service-principle-to-server-administrators-role"></a>Lägg till tjänsten principen i serverrollen för administratörer

1. Anslut till din Azure AS-server i SSMS.
2. I **serveregenskaper** > **säkerhet**, klickar du på **Lägg till**.
3. I **väljer en användare eller grupp**, Sök efter appen registrerade av namnet, Välj och klicka sedan på **Lägg till**.

    ![Sök efter tjänstkontot för principen](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Kontrollera tjänsten principen konto-ID och klicka sedan på **OK**.
    
    ![Sök efter tjänstkontot för principen](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> För servern med hjälp av AzureRm cmdlets, service-princip med Schemaläggaren måste också tillhöra den **ägare** roll för resursen i [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Relaterad information

* [Hämta SQL Server PowerShell-modulen](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Hämta SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


