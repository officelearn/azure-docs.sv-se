---
title: Importera eller exportera data med Azure App-konfiguration
description: Lär dig hur du importerar eller exporterar data till eller från Azure App-konfiguration
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056871"
---
# <a name="import-or-export-configuration-data"></a>Importera eller exportera konfigurationsdata

Azure App Configuration stöder dataimport och exportåtgärder. Använd dessa åtgärder för att arbeta med konfigurationsdata i grupp och utbyta data mellan appkonfigurationsarkivet och kodprojektet. Du kan till exempel ställa in en App Configuration Store för testning och en annan för produktion. Du kan kopiera programinställningarna mellan dem så att du inte behöver ange data två gånger.

Den här artikeln innehåller en guide för import och export av data med appkonfiguration. Om du vill konfigurera en pågående synkronisering med din GitHub-repo, ta en titt på vår [GitHub-åtgärd](https://aka.ms/azconfig-gha1).

## <a name="import-data"></a>Importera data

Import ger konfigurationsdata till ett App Configuration-arkiv från en befintlig källa. Använd importfunktionen för att migrera data till ett appkonfigurationslager eller aggregerade data från flera källor. Appkonfigurationen stöder import från en JSON-, YAML- eller egenskapsfil.

Importera data med hjälp av antingen [Azure-portalen](https://portal.azure.com) eller [Azure CLI](./scripts/cli-import.md). Gör så här på Azure-portalen:

1. Bläddra till appkonfigurationsarkivet och välj **Importera/exportera** på **Operations-menyn.**

1. Välj**Konfigurationsfil**för **källtjänst** > på fliken **Importera** .

1. Välj **För språk** och välj önskad inmatningstyp.

1. Markera **mappikonen** och bläddra till den fil som ska importeras.

    ![Importera fil](./media/import-file.png)

1. Välj en **avgränsare**och ange eventuellt ett **prefix** som ska användas för importerade nyckelnamn.

1. Du kan också välja en **etikett**.

1. Välj **Använd** om du vill slutföra importen.

    ![Importera filen klar](./media/import-file-complete.png)

## <a name="export-data"></a>Exportera data

Exportera skrivkonfigurationsdata som lagras i appkonfiguration till ett annat mål. Använd exportfunktionen om du till exempel vill spara data i ett appkonfigurationsarkiv i en fil som är inbäddad med programkoden under distributionen.

Exportera data med hjälp av [azure-portalen](https://portal.azure.com) eller [Azure CLI](./scripts/cli-export.md). Gör så här på Azure-portalen:

1. Bläddra till appkonfigurationsarkivet och välj **Importera/exportera**.

1. Välj**Konfigurationsfil**för **måltjänst** > på fliken **Exportera** .

1. Du kan också ange ett **prefix** och välja en **etikett** och en punkt i tid för nycklar som ska exporteras.

1. Välj en > **filtypsavgränsare**. **File type**

1. Välj **Använd** för att slutföra exporten.

    ![Exportera filen klar](./media/export-file-complete.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-webbapp](./quickstart-aspnet-core-app.md)  
