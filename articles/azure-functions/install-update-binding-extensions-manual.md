---
title: Installera eller uppdatera Azure Functions bindnings tillägg manuellt
description: Lär dig hur du installerar eller uppdaterar Azure Functions bindnings tillägg för distribuerade funktions appar.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768868"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Installera eller uppdatera Azure Functions bindnings tillägg manuellt från portalen

Från och med version 2. x använder Azure Functions runtime binding Extensions för att implementera kod för utlösare och bindningar. Bindnings tillägg finns i NuGet-paket. För att registrera ett tillägg installerar du i princip ett paket. När du utvecklar funktioner beror det på hur du installerar bindnings tillägg i utvecklings miljön. Mer information finns i avsnittet [Registrera bindnings tillägg](./functions-bindings-register.md) i artikeln utlösare och bindningar.

Ibland måste du manuellt installera eller uppdatera dina bindnings tillägg i Azure Portal. Du kan till exempel behöva uppdatera en registrerad bindning till en nyare version. Du kan också behöva registrera en bindning som stöds och som inte kan installeras på fliken **integrera** i portalen.

## <a name="install-a-binding-extension"></a>Installera ett bindnings tillägg

Använd följande steg för att manuellt installera eller uppdatera tillägg från portalen.

1. Leta upp din Function-app i [Azure Portal](https://portal.azure.com)och markera den. Välj fliken **Översikt** och välj **stoppa**.  Genom att stoppa funktionen i kan du låsa upp filer så att ändringar kan göras.

1. Välj fliken **plattforms funktioner** och under **utvecklingsverktyg** väljer du **Avancerade verktyg (kudu)** . Kudu-slutpunkten (`https://<APP_NAME>.scm.azurewebsites.net/`) öppnas i ett nytt fönster.

1. I fönstret kudu väljer du **Felsök konsol** > **cmd**.  

1. I kommando fönstret navigerar du till `D:\home\site\wwwroot` och väljer ikonen Ta bort bredvid `bin` för att ta bort mappen. Bekräfta borttagningen genom att klicka på **OK** .

1. Välj redigerings ikonen bredvid `extensions.csproj`-filen, som definierar bindnings tilläggen för Function-appen. Projekt filen öppnas i online-redigeraren.

1. Gör nödvändiga tillägg och uppdateringar av **PackageReference** -objekt i **ItemGroup**och välj sedan **Spara**. Den aktuella listan över paket versioner som stöds finns i artikeln [vilka paket behöver jag?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) wiki-artikeln. Alla tre Azure Storage-bindningar kräver paketet Microsoft. Azure. WebJobs. Extensions. Storage.

1. Kör följande kommando från `wwwroot`-mappen för att återskapa de refererade sammansättningarna i mappen `bin`.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Gå tillbaka till fliken **Översikt** i portalen och välj **Starta** för att starta om Function-appen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
