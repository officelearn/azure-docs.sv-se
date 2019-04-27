---
title: Lägga till användare i ett Azure Data Lake Analytics-konto
description: Lär dig hur du ska lägga till användare i ditt Data Lake Analytics-konto
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 8323c4e1b236444f55dab826d2567491f5f0f736
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629330"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Om du lägger till en användare i Azure portal

## <a name="start-the-add-user-wizard"></a>Starta i guiden Lägg till användare
1. Öppna din Azure Data Lake Analytics via https://portal.azure.com.
2. Klicka på **guiden Lägg till användare**.
3. I den **Välj användare** steg, hitta den användare som du vill lägga till. Klicka på **Välj**.
4. den **Välj roll** steg, Välj **Data Lake Analytics-utvecklare**. Den här rollen har den minsta uppsättningen behörigheter som krävs för att skicka/övervaka/hantera U-SQL-jobb. Tilldela rollen om gruppen inte är avsedd för att hantera Azure-tjänster.
5. I den **Välj katalog-behörigheter** steg, välj eventuella ytterligare databaserna som användaren behöver åtkomst till. Läs- och skrivåtkomst till master-databasen krävs för att skicka jobb. Klicka på **OK** när du är klar.
6. I det sista steget kallas **tilldela valda behörigheter** granska ändringarna kommer att göra i guiden. Klicka på **OK**.


## <a name="configure-acls-for-data-folders"></a>Konfigurera åtkomstkontrollistor för mappar
Ge ”R-X” eller ”RWX” på mappar som innehåller indata och utdata.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Du kan också lägga till användaren till rollen Azure Data Lake Storage Gen1 **läsare** roll.
1.  Hitta din Azure Data Lake Storage Gen1-konto.
2.  Klicka på **användare**.
3. Klicka på **Lägg till**.
4.  Välj en Azure RBAC-roll att tilldela den här gruppen.
5.  Tilldela rollen läsare. Den här rollen har den minsta uppsättningen behörigheter som krävs för att bläddra/hantera data som lagras i ADLSGen1. Tilldela rollen om gruppen inte är avsedd för att hantera Azure-tjänster.
6.  Ange namnet på gruppen.
7.  Klicka på **OK**.

## <a name="adding-a-user-using-powershell"></a>Att lägga till en användare med hjälp av PowerShell

1. Följ instruktionerna i den här guiden: [Hur du installerar och konfigurerar du Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Ladda ned den [Lägg till AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell-skript.
3. Kör PowerShell-skript. 

Exempelkommando för att ge användaråtkomst till att skicka jobb, visa nya jobbmetadata och visa gamla metadata är:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

