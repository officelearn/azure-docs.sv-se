---
title: Lägga till användare till ett Azure Data Lake Analytics konto
description: Lär dig hur du lägger till användare i ditt Data Lake Analytics konto på rätt sätt med hjälp av guiden Lägg till användare och Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: 16ec54bb2a41927a92d953586af59ae3653a29c0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117465"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Lägga till en användare i Azure-portalen

## <a name="start-the-add-user-wizard"></a>Starta guiden Lägg till användare
1. Öppna din Azure Data Lake Analytics via https://portal.azure.com .
2. Klicka på **guiden Lägg till användare**.
3. Leta upp den användare som du vill lägga till i steget **Välj användare** . Klicka på **Välj**.
4. steget **Välj roll** , Välj **data Lake Analytics utvecklare**. Den här rollen har den lägsta uppsättning behörigheter som krävs för att skicka/övervaka/hantera U-SQL-jobb. Tilldela den här rollen om gruppen inte är avsedd för hantering av Azure-tjänster.
5. I steget **Välj katalog behörigheter** väljer du ytterligare databaser som användaren behöver åtkomst till. Läs-och Skriv behörighet till huvud databasen krävs för att skicka jobb. När du är klar klickar du på **OK**.
6. I det sista steget som kallas **Tilldela markerade behörigheter** granskar du de ändringar som görs i guiden. Klicka på **OK**.


## <a name="configure-acls-for-data-folders"></a>Konfigurera ACL: er för datamappar
Bevilja "R-X" eller "RWX", om det behövs, i mappar som innehåller indata och utdata.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Du kan också lägga till användaren i rollen Azure Data Lake Storage Gen1 roll **läsare** .
1.  Hitta ditt Azure Data Lake Storage Gen1-konto.
2.  Klicka på **Användare**.
3. Klicka på **Lägg till**.
4.  Välj en Azure RBAC-roll för att tilldela den här gruppen.
5.  Tilldela till läsar roll. Den här rollen har den minsta uppsättning behörigheter som krävs för att bläddra/hantera data som lagras i ADLSGen1. Tilldela den här rollen om gruppen inte är avsedd för hantering av Azure-tjänster.
6.  Skriv in namnet på gruppen.
7.  Klicka på **OK**.

## <a name="adding-a-user-using-powershell"></a>Lägga till en användare med hjälp av PowerShell

1. Följ anvisningarna i den här guiden: [så här installerar och konfigurerar du Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Ladda ned [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell-skriptet.
3. Kör PowerShell-skriptet. 

Exempel kommandot för att ge användarna åtkomst till att skicka jobb, Visa nya metadata för jobb och Visa gamla metadata:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

