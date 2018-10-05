---
title: Manuellt installera eller uppdatera tillägg av bindning för Azure Functions
description: Lär dig mer om att installera eller uppdatera tillägg av Azure Functions-bindning för distribuerade funktionsappar.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktion, bindande tillägg, NuGet, uppdateringar
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: 77b863bc32442261e220b5dd3f11c0bd33b4fa7a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811310"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Manuellt installera eller uppdatera tillägg av Azure Functions-bindning från portalen

Azure Functions version 2.x-körningen använder tillägg av bindning för att implementera kod för utlösare och bindningar. Tillägg av bindning finns i NuGet-paket. Om du vill registrera ett tillägg i stort sett installation av ett paket. När du utvecklar funktioner, beror hur du installerar tillägg av bindning på development environment. Mer information finns i [registrera tillägg av bindning](functions-triggers-bindings.md#register-binding-extensions) i utlösare och bindningar artikeln.

Ibland måste du manuellt installera eller uppdatera tillägg av bindning i Azure-portalen. Du kan behöva uppdatera en registrerad bindning till en nyare version. Du kan också behöva registrera en stöds bindning som inte kan installeras i den **integrera** i portalen.

## <a name="install-a-binding-extension"></a>Installera ett bindningstillägg

Använd följande steg för att manuellt installera eller uppdatera tillägg från portalen.

1. I den [Azure-portalen](https://portal.azure.com), leta upp din funktionsapp och markera den. Välj den **översikt** fliken och markera **stoppa**.  Stoppas funktionsappen låser upp filer så att ändringar kan göras.

1. Välj den **plattformsfunktioner** fliken och under **utvecklingsverktyg** Välj **avancerade verktyg (Kudu)**. Kudu-slutpunkten (`https://<APP_NAME>.scm.azurewebsites.net/`) öppnas i ett nytt fönster.

1. I fönstret Kudu väljer **Felsökningskonsolen** > **CMD**.  

1. Navigera till i kommandofönstret `D:\home\site\wwwroot` och välj ikonen Ta bort bredvid `bin` ta bort mappen. Välj **OK** att bekräfta borttagningen.

1. Välj redigeringsikonen bredvid den `extensions.csproj` -fil som definierar tillägg av bindning för funktionsappen. Projektfilen öppnas i redigeraren online.

1. Göra nödvändiga tillägg och uppdateringar av **PackageReference** objekt i den **ItemGroup**och välj sedan **spara**. Den aktuella listan över stöds paketversioner finns i den [vilka paket behöver jag?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) wiki-artikeln. Alla tre Azure Storage-bindningar kräver att Microsoft.Azure.WebJobs.Extensions.Storage-paketet.

1. Från den `wwwroot` mapp, kör följande kommando för att återskapa de refererade sammansättningarna i den `bin` mapp.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. I den **översikt** fliken i portalen **starta** att starta om appen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
