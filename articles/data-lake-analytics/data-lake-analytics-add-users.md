---
title: Lägga till användare i ett Azure Data Lake Analytics-konto
description: Lär dig hur du ska lägga till användare i ditt Data Lake Analytics-konto
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 58b78c7d9769069f36c9f01c2a7650878a6c5ec9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34717234"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Lägger till en användare i Azure-portalen

## <a name="start-the-add-user-wizard"></a>Starta i guiden Lägg till användare
1. Öppna din Azure Data Lake Analytics via https://portal.azure.com.
2. Klicka på **guiden Lägg till användare**.
3. I den **väljer användaren** steg, Sök efter den användare som du vill lägga till. Klicka på **Välj**.
4. den **Välj rolltjänster** steg, Välj **Data Lake Analytics Developer**. Den här rollen har en minsta uppsättning behörigheter som krävs för att skicka/övervaka/hantera U-SQL-jobb. Tilldela rollen om gruppen inte är avsedd för att hantera Azure-tjänster.
5. I den **Välj katalogen behörigheter** steg, Välj alla nya databaser som användare behöver åtkomst till. Läs- och skrivåtkomst till master-databasen krävs för att skicka jobb. Klicka på **OK** när du är klar.
6. I det sista steget kallas **tilldela behörigheter för markerade** granska ändringar guiden gör. Klicka på **OK**.


## <a name="configure-acls-for-data-folders"></a>Konfigurera ACL: er för mappar
Bevilja ”R-X” eller ”RWX” på mappar som innehåller indata och utdata.


## <a name="optionally-add-the-user-to-the-azure-data-lake-store-role-reader-role"></a>Du kan också lägga till användaren i rollen Azure Data Lake Store **Reader** roll.
1.  Hitta din Azure Data Lake Store-konto.
2.  Klicka på **användare**.
3. Klicka på **Lägg till**.
4.  Välj en roll med Azure RBAC att tilldela den här gruppen.
5.  Tilldela rollen läsare. Den här rollen har en minsta uppsättning behörigheter som krävs för att bläddra/hantera data som lagras i ADLS. Tilldela rollen om gruppen inte är avsedd för att hantera Azure-tjänster.
6.  Ange namnet på gruppen.
7.  Klicka på **OK**.

## <a name="adding-a-user-using-powershell"></a>Lägger till en användare med hjälp av PowerShell

1. Följ instruktionerna i den här guiden: [hur du installerar och konfigurerar du Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Hämta den [Lägg till AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell-skript.
3. Kör PowerShell-skript. 

Exempelkommando för att ge användaråtkomst till skicka jobb, visa nya jobb metadata och visa gamla metadata är:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure portal](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

