---
title: Hantera hastighet och samtidighet för kodningen med Azure Media Services | Microsoft Docs
description: Den här artikeln ger en kort översikt över hur du kan hantera hastighet och samtidighet för dina jobb/kodningsuppgifter med Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bcaadc8dd61899aff860ad246e30170c99ec0f6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188636"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Hantera hastighet och samtidighet för kodningen  

Den här artikeln ger en kort översikt över hur du kan hantera hastighet och samtidighet för dina jobb/kodningsuppgifter.

## <a name="overview"></a>Översikt

I Media Services, en **typ av reserverad enhet** bestämmer hur snabbt mediebearbetningsuppgifter som din mediebearbetning uppgifter ska bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** eller **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. Den [skalning kodningsenheter](media-services-scale-media-processing-overview.md) avsnittet visas en tabell som hjälper dig att fatta beslut när du väljer mellan olika kodning hastigheter.

Förutom att ange typ av reserverad enhet kan du ange att etablera ditt konto med **reserverade enheter**. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Till exempel om ditt konto har fem reserverade enheter, och sedan fem medieuppgifter körs samtidigt så länge som det finns aktiviteter som ska bearbetas. De återstående aktiviteterna ska vänta i kön och ska få hämtas för bearbetning av sekventiellt när en aktivitet är klar. Om ett konto inte har några mediereserverade enheter etablerade sedan hämtas uppgifter sekventiellt. I det här fallet beror väntetiden mellan en uppgift slutförs och nästa start på tillgängligheten för resurser i systemet.

Detaljerad information och exempel som visar hur du skalar kodningsenheter finns [detta](media-services-scale-media-processing-overview.md) avsnittet.

## <a name="next-step"></a>Nästa steg

[Skala kodningsenheter](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

