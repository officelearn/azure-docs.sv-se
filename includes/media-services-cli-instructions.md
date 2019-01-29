---
title: ta med fil
description: ta med fil
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104991"
---
## <a name="open-cli-shell"></a>Öppna CLI Shell

Det rekommenderas att använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) att köra CLI-kommandon. **Cloud Shell** är ett kostnadsfritt, interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Vanliga Azure-verktyg förinstalleras och konfigureras i Cloud Shell och kan användas med kontot. Markera bara knappen Kopiera för att kopiera koden, klistra in den i Cloud Shell och tryck sedan på RETUR för att köra den. Det finns flera olika sätt att öppna Cloud Shell:

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Inloggning

Att börja använda CLI-gränssnittet (i molnet eller lokalt), kör `az login` att skapa en anslutning till Azure.

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. I annat fall måste du öppna en Webbläsarsida och följ anvisningarna på kommandoraden för att ange en auktoriseringskod när du har lämnat till https://aka.ms/devicelogin i din webbläsare.

### <a name="specify-location-of-files"></a>Ange platsen för filer

Många Media Services CLI-kommandon kan du skicka en parameter med ett filnamn. 

Om du använder **Azure Cloud Shell**, ladda upp din fil till **Azure Cloud Shell**. Du hittar knappen uppladdning/nedladdning filer överst i fönstret shell. Sedan kan hänvisa till filen så här: `@{FileName}.` 

![Överföra filer]

Om du använder Azure CLI lokalt kan du ange hela sökvägen. Till exempel `@c:\tracks.json`.


[Överföra filer]: ./media/media-services-cli/upload-download-files.png
