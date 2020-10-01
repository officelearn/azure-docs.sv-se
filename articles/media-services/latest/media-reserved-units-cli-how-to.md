---
title: Så här använder du CLI för skalning av reserverade enheter (MRUs) – Azure | Microsoft Docs
description: Det här avsnittet visar hur du använder CLI för att skala medie bearbetning med Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: bcbe5fe71e5a4d4d39a29d4a6828c104f6891c0d
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617153"
---
# <a name="how-to-scale-media-reserved-units"></a>Skala enhets reserverade enheter

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du skalar MRSs (Media reservered units) för snabbare kodning.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett Media Services-konto](./create-account-howto.md).

Förstå [reserverade enheter för media](concept-media-reserved-units.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>Skala medie reserverade enheter med CLI

Kör kommandot `mru`.

Följande [AZ AMS konto MRU](/cli/azure/ams/account/mru?view=azure-cli-latest) -kommando anger medie reserverade enheter på "amsaccount"-kontot med hjälp av **Count** -och **Type** -parametrarna.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet minuter som de reserverade enheterna är etablerade i ditt konto. Detta inträffar oberoende av om det finns jobb som körs i ditt konto. En detaljerad förklaring finns i avsnittet Vanliga frågor och svar på sidan [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-step"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Se även

* [Kvoter och begränsningar](limits-quotas-constraints.md)
