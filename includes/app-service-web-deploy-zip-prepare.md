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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945165"
---
## <a name="create-a-project-zip-file"></a>Skapa en ZIP-fil av projektet

>[!NOTE]
> Om du hämtade filerna i en ZIP-fil extraherar du filerna först. Om du till exempel har hämtat en ZIP-fil från GitHub kan du inte distribuera filen som den är. GitHub lägger till ytterligare kapslade kataloger som inte fungerar med App Service. 
>

I ett lokalt terminalfönster navigerar du till rotkatalogen för appprojektet. 

Den här katalogen ska innehålla postfilen till webbappen, till exempel _index.html_, _index.php_och _app.js_. Den kan också innehålla pakethanteringsfiler som _project.json_, _composer.json_, _package.json_, _bower.json_och _requirements.txt_.

Om du inte vill att App Service ska köra distributionsautomatisering `npm` `bower`åt `gulp` `composer`dig `pip`kör du alla bygguppgifter (till exempel , , , och ) och ) och se till att du har alla filer du behöver för att köra appen. Det här steget krävs om du vill [köra paketet direkt](../articles/app-service/deploy-run-package.md).

Skapa ett ZIP-arkiv med allt i projektet. Följande kommando använder standardverktyget i terminalen:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

