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
ms.openlocfilehash: 33ba01440f599e2f019db46c304b0658632d9342
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885286"
---
# <a name="import-or-export-configuration-data"></a>Importera eller exportera konfigurationsdata

Azure App stöder konfigurationsdata importera och exportera åtgärder. De här alternativen kan du arbeta med konfigurationsdata gruppvis som samt exchange-data mellan din appkonfiguration lagra och code projekt. Du kan till exempel konfigurera en configuration appbutik för testning och en annan för produktion och kopiera programinställningar mellan dem via en fil så att du inte behöver ange data två gånger.

Den här artikeln innehåller en guide för import och export av data med Appkonfiguration.

## <a name="import-data"></a>Importera data

Importera ger konfiguration i konfiguration av datalager från en befintlig datakälla i stället för att ange dem manuellt. Du kan använda funktionen Importera om du vill migrera data till en appbutik för konfiguration eller samla in data från flera källor. Konfiguration av stöd för import från en JSON, egenskaper eller YAML-fil.

Du kan importera data med hjälp av antingen den [Azure-portalen](https://aka.ms/azconfig/portal) eller [Azure CLI](./scripts/cli-import.md). Följ dessa steg från Azure-portalen:

1. Bläddra till din app konfiguration av lagring och på **Import/Export**.

2. I den **Import** fliken **Source service** och **konfigurationsfilen**.

3. Välj **för språket** och **filtypen**.

4. Klicka på den **mappen** ikon och bläddra till filen som ska importeras.

    ![Importera fil](./media/import-file.png)

5. Välj en **avgränsare** och du kan också ange en **prefixet** ska användas för importerade nyckelnamn.

6. Välja en **etikett**.

7. Klicka på **tillämpa** att slutföra importen.

    ![Importera fil klar](./media/import-file-complete.png)

## <a name="export-data"></a>Exportera data

Export skriver till en annan plats som lagras i Appkonfiguration. Du kan använda funktionen Exportera, till exempel att spara data i en appbutik konfiguration till en fil som ska bäddas in med din programkod under distributionen.

Du kan exportera data med hjälp av antingen den [Azure-portalen](https://aka.ms/azconfig/portal) eller [Azure CLI](./scripts/cli-export.md). Följ dessa steg från Azure-portalen:

1. Bläddra till din app konfiguration av lagring och på **Import/Export**.

2. I den **exportera** fliken **rikta service** och **konfigurationsfilen**.

3. Du kan också ange en **prefixet** och välj en **etikett** och en punkt i tiden för nycklar som ska exporteras.

4. Välj en **filtypen** och **avgränsare**.

5. Klicka på **tillämpa** att slutföra exporten.

    ![Exportfilen är klar](./media/export-file-complete.png)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa en ASP.NET-webbapp](quickstart-aspnet-core-app.md)  
