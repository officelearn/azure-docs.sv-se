---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945165"
---
## <a name="create-a-project-zip-file"></a>Skapa en ZIP-fil av projektet

>[!NOTE]
> Om du har laddat ned filerna i en ZIP-fil extraherar du filerna först. Om du till exempel har hämtat en ZIP-fil från GitHub kan du inte distribuera filen i befintligt skick. GitHub lägger till ytterligare kapslade kataloger som inte fungerar med App Service. 
>

I ett lokalt terminalfönster navigerar du till rot katalogen för ditt Apps-projekt. 

Den här katalogen ska innehålla post filen till din webbapp, till exempel _index. html_, _index. php_och _app. js_. Det kan också innehålla paket hanterings filer som _Project. JSON_, _Composer. JSON_, _Package. JSON_, _Bower. JSON_och _Requirements. txt_.

Om du inte vill att App Service ska köra distributions automatisering kan du köra alla Bygg aktiviteter (till exempel `npm`, `bower`, `gulp`, `composer`och `pip`) och kontrol lera att du har alla filer du behöver för att köra appen. Det här steget krävs om du vill [köra paketet direkt](../articles/app-service/deploy-run-package.md).

Skapa ett ZIP-arkiv med allt i projektet. Följande kommando använder standardverktyget i terminalen:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

