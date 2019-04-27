---
title: Importera eller exportera data med Azure-Appkonfiguration | Microsoft Docs
description: Lär dig att importera eller exportera data till eller från Azure-Appkonfiguration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 6557f3a4129076c1e8708208e5d4e0d4f53dcec3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741784"
---
# <a name="import-or-export-configuration-data"></a>Importera eller exportera konfigurationsdata

Azure App stöder konfigurationsdata importera och exportera åtgärder. Använda de här åtgärderna för att arbeta med konfigurationsdata i bulk och exchange-data mellan din app konfiguration av lagring och kod projekt. Du kan till exempel skapa en app konfigurationsarkiv för testning och ett annat för produktion. Du kan sedan kopiera programinställningar mellan dem via en fil så att du slipper ange data två gånger.

Den här artikeln innehåller en guide för import och export av data med Appkonfiguration.

## <a name="import-data"></a>Importera data

Importera ger konfiguration till en Appkonfiguration datalager från en befintlig datakälla i stället för att ange den manuellt. Funktionen Importera används för att migrera data till en appbutik för konfiguration eller samla in data från flera källor. Konfiguration av stöder importera från en fil för JSON eller YAML egenskaper.

Importera data genom att använda antingen den [Azure-portalen](https://aka.ms/azconfig/portal) eller [Azure CLI](./scripts/cli-import.md). Följ dessa steg från Azure-portalen:

1. Bläddra till din app konfiguration av lagring och markera **Import/Export**.

2. På den **Import** fliken **Source service** > **konfigurationsfilen**.

3. Välj **för språket** > **filtypen**.

4. Välj den **mappen** ikon, och bläddra till filen som ska importeras.

    ![Importera fil](./media/import-file.png)

5. Välj en **avgränsare**, och du kan också ange en **prefixet** ska användas för importerade nyckelnamn.

6. Alternativt kan du välja en **etikett**.

7. Välj **tillämpa** att slutföra importen.

    ![Importera fil klar](./media/import-file-complete.png)

## <a name="export-data"></a>Exportera data

Export skriver till en annan plats som lagras i Appkonfiguration. Använda funktionen Exportera, till exempel att spara data i en appbutik konfiguration till en fil som är inbäddad med din programkod under distributionen.

Exportera data genom att använda antingen den [Azure-portalen](https://aka.ms/azconfig/portal) eller [Azure CLI](./scripts/cli-export.md). Följ dessa steg från Azure-portalen:

1. Bläddra till din app konfiguration av lagring och markera **Import/Export**.

2. På den **exportera** fliken **rikta service** > **konfigurationsfilen**.

3. Du kan också ange en **prefixet** och välj en **etikett** och en punkt i tiden för nycklar som ska exporteras.

4. Välj en **filtypen** > **avgränsare**.

5. Välj **tillämpa** att slutföra exporten.

    ![Exportfilen är klar](./media/export-file-complete.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-webbapp](./quickstart-aspnet-core-app.md)  
