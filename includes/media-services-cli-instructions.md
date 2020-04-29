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
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79461222"
---
## <a name="use-cli-shell"></a>Använd CLI-gränssnitt

Vi rekommenderar att du använder [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) för att köra CLI-kommandon. **Cloud Shell** är ett kostnads fritt, interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Vanliga Azure-verktyg förinstalleras och konfigureras i Cloud Shell och kan användas med kontot. Den ger dig flexibilitet att välja den skalupplevelse som passar bäst för ditt sätt att arbeta. Linux-användare kan välja en Bash-upplevelse och Windows-användare kan välja PowerShell.

Du kan också installera CLI lokalt. Instruktioner för din plattform finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) .

### <a name="sign-in"></a>Logga in

Om du använder en lokal installation av CLI måste du logga in på Azure. Det här steget krävs inte för Azure Cloud Shell. Logga in med `az login` kommandot.

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webb sida och följa instruktionerna på kommando raden för att ange en auktoriseringskod efter att du har navigerat till https://aka.ms/devicelogin i webbläsaren.

### <a name="specify-location-of-files"></a>Ange plats för filer

Med många Media Services CLI-kommandon kan du skicka en parameter med ett fil namn. Om du använder **Cloud Shell**kan du överföra filen till din clouddrive (med bash eller PowerShell). 

![Överföra filer]

Oavsett om du använder en lokal CLI eller **Cloud Shell**, måste du ange fil Sök vägen enligt det OS-eller Cloud Shell (bash eller PowerShell) som du använder. Nedan visas några exempel:

Relativ sökväg till filen (alla operativ system)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absolut fil Sök väg på Linux/Mac och Windows OS

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Använd `{file}` om kommandot frågar efter sökvägen till filen. Till exempel `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Använd `@{file}` om kommandot ska läsa in den angivna filen. Till exempel `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Överföra filer]: ./media/media-services-cli/upload-download-files.png
