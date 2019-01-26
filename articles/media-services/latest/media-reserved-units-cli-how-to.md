---
title: Använd CLI för att skala Mediereserverade enheter – Azure | Microsoft Docs
description: Det här avsnittet visar hur du använder CLI för att skala mediebearbetning med Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9f95be8a9d75065deedd3bd7c92907145e966494
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913062"
---
# <a name="scaling-media-processing"></a>Skala mediebearbetning

Azure Media Services kan du skala mediebearbetning i ditt konto genom att hantera Mediereserverade enheter (MRUs). Detaljerad översikt finns i [skala mediebearbetning](../previous/media-services-scale-media-processing-overview.md). 

Den här artikeln visar hur du använder [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) att skala MRUs.

> [!NOTE]
> För analys av ljud och Video Analysis jobb som utlöses av Media Services v3 eller Video Indexer kan rekommenderar vi starkt att etablera ditt konto med 10 S3 MRUs. <br/>Om du behöver fler än 10 S3 MRUs kan öppna en stöd biljett med den [Azure-portalen](https://portal.azure.com/).

## <a name="prerequisites"></a>Förutsättningar 

+ Installera [Azure CLI]( /cli/azure/install-azure-cli). Den här artikeln kräver Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. 

    Du kan också använda [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).
+ [Skapa ett Media Services-konto](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Skala Mediereserverade enheter med CLI

1. Kör `login`-kommandot. Kör det här kommandot om du använder Azure cloudshell eller lokala CLI-gränssnittet.

    ```azurecli
    az login
    ```
    
    Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. I annat fall måste du öppna en Webbläsarsida och följ anvisningarna på kommandoraden för att ange en auktoriseringskod när du har lämnat till [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) i webbläsaren.
2. Kör `mru`-kommandot.

    Följande [az ams-konto mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) kommandot anger Mediereserverade enheter på ”amsaccount”-konto med hjälp av den **antal** och **typ** parametrar.

    ```azurecli
    az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
    ```

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet, typen och tid som MRUs levereras i ditt konto. Avgifter oavsett om du köra några jobb. En detaljerad förklaring finns i avsnittet vanliga frågor och svar i den [prissättning för Media Services](https://azure.microsoft.com/pricing/details/media-services/) sidan.   

## <a name="next-step"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
