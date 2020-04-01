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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461222"
---
## <a name="use-cli-shell"></a>Använd CLI-skal

Vi rekommenderar att du använder [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) för att köra CLI-kommandon. **Cloud Shell** är ett kostnadsfritt, interaktivt skal som du kan använda för att köra stegen i den här artikeln. Vanliga Azure-verktyg förinstalleras och konfigureras i Cloud Shell och kan användas med kontot. Den ger dig flexibilitet att välja den skalupplevelse som passar bäst för ditt sätt att arbeta. Linux-användare kan välja en Bash-upplevelse och Windows-användare kan välja PowerShell.

Du kan också installera CLI lokalt. Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) för instruktioner för din plattform.

### <a name="sign-in"></a>Logga in

Att använda en lokal installation av CLI kräver inloggning till Azure. Det här steget krävs inte för Azure Cloud Shell. Logga in `az login` med kommandot.

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsarsida och följa instruktionerna på kommandoraden för https://aka.ms/devicelogin att ange en auktoriseringskod efter att du har navigerat till i webbläsaren.

### <a name="specify-location-of-files"></a>Ange plats för filer

Med många MEDIA Services CLI-kommandon kan du skicka en parameter med ett filnamn. Om du använder **Cloud Shell**kan du ladda upp filen till din clouddrive (med Bash eller PowerShell). 

![Överföra filer]

Oavsett om du använder en lokal CLI eller **Cloud Shell**måste du ange filsökvägen enligt det operativsystem eller molnskal (Bash eller PowerShell) som du använder. Nedan följer några exempel:

Relativ sökväg till filen (alla operativsystem)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Absolut filsökväg på Linux/Mac och Windows OS

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Använd `{file}` om kommandot ber om en sökväg till filen. Till exempel `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Använd `@{file}` om kommandot ska läsas in den angivna filen. Till exempel `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Överföra filer]: ./media/media-services-cli/upload-download-files.png
