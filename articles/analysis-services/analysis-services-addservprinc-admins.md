---
title: Lägg till ett huvudnamn för tjänsten i Azure Analysis Services-serverrollen admin | Microsoft Docs
description: Lär dig hur du lägger till en automation tjänstens huvudnamn i rollen Administratör
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8840dd9a8a8e8ba1f78d461824ac0cdce32b2ac9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596778"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Lägg till ett huvudnamn för tjänsten server-administratör 

 För att automatisera uppgifter för obevakad PowerShell ett huvudnamn för tjänsten måste ha **serveradministratören** behörighet för Analysis Services-servern hanteras. Den här artikeln beskriver hur du lägger till ett huvudnamn för tjänsten administratörer serverrollen på en Azure AS-server.

## <a name="before-you-begin"></a>Innan du börjar
Innan du slutför den här uppgiften, måste du ha ett huvudnamn för tjänsten registreras i Azure Active Directory.

[Skapa tjänstens huvudnamn - Azure-portalen](../azure-resource-manager/resource-group-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="required-permissions"></a>Nödvändiga behörigheter
För att slutföra den här uppgiften, måste du ha [serveradministratören](analysis-services-server-admins.md) behörigheter på Azure AS-servern. 

## <a name="add-service-principal-to-server-administrators-role"></a>Lägga till tjänstens huvudnamn i serverrollen för administratörer

1. Anslut till din Azure AS-server i SSMS.
2. I **serveregenskaper** > **säkerhet**, klickar du på **Lägg till**.
3. I **väljer en användare eller grupp**, Sök efter appen registrerade av namnet, Välj och klicka sedan på **Lägg till**.

    ![Sök efter tjänstens objektkonto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Kontrollera service principal konto-ID och klicka sedan på **OK**.
    
    ![Sök efter tjänstens objektkonto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> För servern med hjälp av AzureRm cmdlets, service principal körs Schemaläggaren måste också tillhöra den **ägare** roll för resursen i [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Relaterad information

* [Hämta SQL Server PowerShell-modulen](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Hämta SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


