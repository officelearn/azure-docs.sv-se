---
title: Importera eller exportera data med Azure App-konfiguration | Microsoft Docs
description: Lär dig hur du importerar eller exporterar data till eller från Azure App konfiguration
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 64fcc8396fc1b771d0095ee595fd177d7fe99b58
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899510"
---
# <a name="import-or-export-configuration-data"></a>Importera eller exportera konfigurationsdata

Azure App konfiguration stöder åtgärder för import och export av data. Använd dessa åtgärder för att arbeta med konfigurations data i Mass-och Exchange-data mellan appens konfigurations lagring och kod projekt. Du kan till exempel konfigurera ett konfigurations lager för en app för testning och en annan för produktion. Du kan sedan kopiera program inställningarna mellan dem via en fil så att du inte behöver ange data två gånger.

Den här artikeln innehåller en guide för att importera och exportera data med app-konfiguration.

## <a name="import-data"></a>Importera data

Import hämtar konfigurations data till ett konfigurations lager för appar från en befintlig källa, i stället för att manuellt ange den. Använd funktionen Importera för att migrera data till ett konfigurations lager för appar eller samla in data från flera källor. Konfiguration av appar stöder import från en JSON-, YAML-eller egenskaps fil.

Importera data med hjälp av antingen [Azure Portal](https://portal.azure.com) eller [Azure CLI](./scripts/cli-import.md). Följ de här stegen i Azure Portal:

1. Bläddra till appens konfigurations Arkiv och välj **Importera/exportera**.

2. På fliken **Importera** väljer du **käll tjänst** > **konfigurations fil**.

3. Välj **för språk** > **filtyp**.

4. Välj **mappikonen** och bläddra till den fil som ska importeras.

    ![Importera fil](./media/import-file.png)

5. Välj en **avgränsare**och ange ett **prefix** som ska användas för importerade nyckel namn.

6. Du kan också välja en **etikett**.

7. Välj **tillämpa** för att slutföra importen.

    ![Import filen är färdig](./media/import-file-complete.png)

## <a name="export-data"></a>Exportera data

Exportera skriver konfigurations data som lagras i app-konfigurationen till ett annat mål. Använd funktionen export, till exempel för att spara data i ett konfigurations lager för appar till en fil som är inbäddad med program koden under distributionen.

Exportera data med hjälp av antingen [Azure Portal](https://portal.azure.com) eller [Azure CLI](./scripts/cli-export.md). Följ de här stegen i Azure Portal:

1. Bläddra till appens konfigurations Arkiv och välj **Importera/exportera**.

2. På fliken **Exportera** väljer du **konfigurations filen**för **mål tjänst** > .

3. Du kan också ange ett **prefix** och välja en **etikett** och en tidpunkt för nycklar som ska exporteras.

4. Välj en **filtypen** > **avgränsare**.

5. Välj **tillämpa** för att slutföra exporten.

    ![Export filen är färdig](./media/export-file-complete.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core webbapp](./quickstart-aspnet-core-app.md)  
