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
ms.openlocfilehash: db431d7815cfcc006563bd6da438154ef77ae6e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814714"
---
## <a name="cli-shell"></a>CLI-gränssnittet

Det rekommenderas att använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) att köra CLI-kommandon. **Cloud Shell** är ett kostnadsfritt, interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Vanliga Azure-verktyg förinstalleras och konfigureras i Cloud Shell och kan användas med kontot. Det ger dig flexibilitet att välja den skalupplevelse som passar bäst för ditt sätt att arbeta. Linux-användare kan välja Bash och Windows-användare kan välja PowerShell.

Du kan också installera CLI lokalt. Se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) anvisningar för din plattform.

### <a name="sign-in"></a>Logga in

Om du använder en lokal installation av CLI måste du logga in på Azure. Det här steget krävs inte för Azure Cloud Shell. Logga in med den `az login` kommando.

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

Använd `{file}` om kommandot frågar efter en sökväg till filen. Till exempel `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Använd `@{file}` om kommandot kommer att läsa in den angivna filen. Till exempel `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Överföra filer]: ./media/media-services-cli/upload-download-files.png
