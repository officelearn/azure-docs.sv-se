---
title: Lägga till användare i ett Azure Data Lake Analytics-konto
description: Lär dig hur du lägger till användare på rätt sätt i ditt DataSjöanalyskonto med hjälp av guiden Lägg till användare och Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672733"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Lägga till en användare i Azure-portalen

## <a name="start-the-add-user-wizard"></a>Starta guiden Lägg till användare
1. Öppna din Azure Data https://portal.azure.comLake Analytics via .
2. Klicka på **Lägg till användarguide.**
3. Leta reda på den användare som du vill lägga till i steget **Välj användare.** Klicka på **Markera**.
4. Välj **rollsteg,** välj **Utvecklare av DataSjöanalys**. Den här rollen har den minsta uppsättning behörigheter som krävs för att skicka/övervaka/hantera U-SQL-jobb. Tilldela till den här rollen om gruppen inte är avsedd för hantering av Azure-tjänster.
5. I steget **Välj katalogbehörigheter** väljer du eventuella ytterligare databaser som användaren behöver åtkomst till. Läs- och skrivåtkomst till huvuddatabasen krävs för att skicka jobb. När du är klar klickar du på **OK**.
6. I det sista steget **med namnet Tilldela valda behörigheter** granska de ändringar som guiden kommer att göra. Klicka på **OK**.


## <a name="configure-acls-for-data-folders"></a>Konfigurera ACL:er för datamappar
Bevilja "R-X" eller "RWX", efter behov, på mappar som innehåller indata och utdata.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Du kan också lägga till användaren i **rollläsaren** Azure Data Lake Storage Gen1.
1.  Hitta ditt Azure Data Lake Storage Gen1-konto.
2.  Klicka på **Användare**.
3. Klicka på **Lägg till**.
4.  Välj en Azure RBAC-roll för att tilldela den här gruppen.
5.  Tilldela till rollen Läsare. Den här rollen har den minsta uppsättning behörigheter som krävs för att bläddra/hantera data som lagras i ADLSGen1. Tilldela till den här rollen om gruppen inte är avsedd för hantering av Azure-tjänster.
6.  Skriv in namnet på gruppen.
7.  Klicka på **OK**.

## <a name="adding-a-user-using-powershell"></a>Lägga till en användare med PowerShell

1. Följ instruktionerna i den här guiden: [Installera och konfigurera Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Ladda ned [PowerShell-skriptet Add-AdlaJobUser.ps1.](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1)
3. Kör PowerShell-skriptet. 

Exempelkommandot för att ge användaren åtkomst till att skicka jobb, visa nya jobbmetadata och visa gamla metadata är:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Komma igång med Data Lake Analytics med hjälp av Azure-portalen](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

