---
title: ta med fil
description: ta med fil
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: c13ba8ab42d949e5752bfefd9b792b14a5a5ce59
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56408219"
---
## <a name="cli-shell"></a>CLI-gränssnittet

Det rekommenderas att använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) att köra CLI-kommandon. **Cloud Shell** är ett kostnadsfritt, interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Vanliga Azure-verktyg förinstalleras och konfigureras i Cloud Shell och kan användas med kontot. Det ger dig flexibilitet att välja den skalupplevelse som passar bäst för ditt sätt att arbeta. Linux-användare kan välja Bash och Windows-användare kan välja PowerShell.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Inloggning

Att börja använda CLI-gränssnittet (i molnet eller lokalt), kör `az login` att skapa en anslutning till Azure.

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. I annat fall måste du öppna en Webbläsarsida och följ anvisningarna på kommandoraden för att ange en auktoriseringskod när du har lämnat till https://aka.ms/devicelogin i din webbläsare.

### <a name="specify-location-of-files"></a>Ange platsen för filer

Många Media Services CLI-kommandon kan du skicka en parameter med ett filnamn. Om du använder **Cloud Shell**, du kan överföra filen till din clouddrive (med Bash eller PowerShell). 

![Överföra filer]

Om du använder en lokal CLI eller **Cloud Shell**, måste du ange sökvägen till filen enligt OS- eller Cloud Shell (Bash eller PowerShell) som du använder. Nedan visas några exempel:

Relativ sökväg till filen (alla OS)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absoluta sökvägen på Linux/Mac och Windows OS

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`


[Överföra filer]: ./media/media-services-cli/upload-download-files.png
