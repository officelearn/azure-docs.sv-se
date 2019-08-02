---
title: Lägg till tjänstens huvud namn i Azure Analysis Services server administratörs roll | Microsoft Docs
description: Lär dig hur du lägger till ett Automation-tjänstens huvud namn i Server administratörs rollen
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a8fbbdbd647aa3d733a26ceaf72629058ae3a274
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619267"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Lägg till ett huvud namn för tjänsten i Server administratörs rollen 

 För att automatisera obevakade PowerShell-aktiviteter måste ett huvud namn för tjänsten ha **Server administratörs** behörighet på den Analysis Services server som hanteras. Den här artikeln beskriver hur du lägger till ett huvud namn för tjänsten i rollen Server administratörer på en Azure AS-Server.

## <a name="before-you-begin"></a>Innan du börjar
Innan du slutför den här uppgiften måste du ha ett huvud namn för tjänsten som registrerats i Azure Active Directory.

[Skapa tjänstens huvud namn – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Nödvändiga behörigheter
För att slutföra den här uppgiften måste du ha [Server administratörs](analysis-services-server-admins.md) behörighet på Azure as Server. 

## <a name="add-service-principal-to-server-administrators-role"></a>Lägg till tjänstens huvud namn i rollen Server administratörer

1. I SSMS ansluter du till din Azure AS-Server.
2. I **Server egenskaper** > **säkerhet**klickar du på **Lägg till**.
3. I **Välj en användare eller grupp**söker du efter din registrerade app efter namn, väljer och klickar sedan på **Lägg till**.

    ![Sök efter tjänstens huvud konto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Kontrol lera konto-ID för tjänstens huvud namn och klicka sedan på **OK**.
    
    ![Sök efter tjänstens huvud konto](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> För Server åtgärder som använder Azure PowerShell-cmdletar måste tjänstens huvud namn som kör Scheduler också tillhöra **ägar** rollen för resursen i [Azure role-based Access Control (RBAC)](../role-based-access-control/overview.md). 

## <a name="related-information"></a>Relaterad information

* [Ladda ned SQL Server PowerShell-modul](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Ladda ned SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


