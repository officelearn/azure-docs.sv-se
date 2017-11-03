---
title: "Hantera hastighet och samtidighet på din encoding med Azure Media Services | Microsoft Docs"
description: "Den här artikeln ger en kort översikt över hur du kan hantera hastighet och samtidighet kodning jobb/aktiviteter med Azure Media Services."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 0463904fd9bf1138587d0d214e572ddd38cc2184
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Hantera hastighet och samtidighet på din kodning

Den här artikeln ger en kort översikt över hur du kan hantera hastighet och samtidighet kodning jobb/aktiviteter.

## <a name="overview"></a>Översikt

I Media Services en **reserverade enhetstyp** bestämmer hastighet som media bearbeta uppgifter bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. Den [skalning kodning enheter](media-services-scale-media-processing-overview.md) avsnittet visas en tabell som hjälper dig att fatta beslut om att välja mellan olika kodning hastigheter.

Ange vilken enhet, kan du ange att tillhandahålla ditt konto med **reserverade enheter**. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Till exempel om ditt konto har fem reserverade enheter fem media aktiviteter körs samtidigt så länge som det finns aktiviteter som ska bearbetas. De återstående uppgifterna kommer att vänta i kön och ska hämta tas upp för bearbetning i tur och ordning när en aktivitet är klar. Om ett konto inte har några reserverade enheter som har etablerats sedan hanteras uppgifter sekventiellt. I det här fallet beror väntetiden mellan en aktivitet avslutas och nästa start på tillgängligheten för resurser i systemet.

Detaljerad information och exempel som visar hur du kodning skalningsenheter finns [detta](media-services-scale-media-processing-overview.md) avsnittet.

## <a name="next-step"></a>Nästa steg

[Kodning skalningsenheter](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

