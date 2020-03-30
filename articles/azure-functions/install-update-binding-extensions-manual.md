---
title: Installera eller uppdatera bindningstillägg för Azure Functions manuellt
description: Lär dig hur du installerar eller uppdaterar Bindningstillägg för Azure Functions för distribuerade funktionsappar.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768868"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Installera eller uppdatera Azure Functions-bindningstillägg manuellt från portalen

Från och med version 2.x använder Azure Functions-körningen bindningstillägg för att implementera kod för utlösare och bindningar. Bindningstillägg finns i NuGet-paket. För att registrera ett tillägg installerar du i huvudsak ett paket. När du utvecklar funktioner beror hur du installerar bindningstillägg på utvecklingsmiljön. Mer information finns i [Registrera bindningstillägg](./functions-bindings-register.md) i artikeln utlösare och bindningar.

Ibland måste du installera eller uppdatera bindningstilläggen manuellt i Azure-portalen. Du kan till exempel behöva uppdatera en registrerad bindning till en nyare version. Du kan också behöva registrera en bindning som stöds som inte kan installeras på fliken **Integrera** i portalen.

## <a name="install-a-binding-extension"></a>Installera ett bindningstillägg

Följ följande steg för att manuellt installera eller uppdatera tillägg från portalen.

1. Leta upp din funktionsapp i [Azure-portalen](https://portal.azure.com)och välj den. Välj fliken **Översikt** och välj **Stoppa**.  Om du stoppar funktionsappen låses upp filer så att ändringar kan göras.

1. Välj fliken **Plattformsfunktioner** och under **Utvecklingsverktyg** väljer du **Avancerade verktyg (Kudu).** Kudu-ändpunkten`https://<APP_NAME>.scm.azurewebsites.net/`( ) öppnas i ett nytt fönster.

1. Välj **Felsökkonsol** > **CMD**i Kudu-fönstret .  

1. I kommandofönstret navigerar `D:\home\site\wwwroot` du till och `bin` väljer borttagningsikonen bredvid för att ta bort mappen. Välj **OK** för att bekräfta borttagningen.

1. Välj redigeringsikonen `extensions.csproj` bredvid filen, som definierar bindningstilläggen för funktionsappen. Projektfilen öppnas i onlineredigeraren.

1. Gör nödvändiga tillägg och uppdateringar av **PackageReference-objekt** i **ItemGroup**och välj sedan **Spara**. Den aktuella listan över paketversioner som stöds finns i vilken [paket behöver jag?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) Alla tre Azure Storage-bindningar kräver paketet Microsoft.Azure.WebJobs.Extensions.Storage.

1. Kör `wwwroot` följande kommando i mappen för att återskapa de refererade sammansättningarna i `bin` mappen.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Tillbaka på fliken **Översikt** i portalen väljer **du Start** för att starta om funktionsappen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)
