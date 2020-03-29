---
title: Hantera hastighet och samtidighet för din kodning med Azure Media Services | Microsoft-dokument
description: Den här artikeln ger en kort översikt över hur du kan hantera hastighet och samtidighet för dina kodningsjobb/uppgifter med Azure Media Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463760"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Hantera hastighet och samtidighet för kodningen  

Den här artikeln ger en kort översikt över hur du kan hantera hastighet och samtidighet för dina kodningsjobb/uppgifter.

## <a name="overview"></a>Översikt

I Media Services avgör en **reserverad enhetstyp** hur snabbt dina mediebearbetningsuppgifter bearbetas. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. Avsnittet [skalningskodningsenheter](media-services-scale-media-processing-overview.md) visar en tabell som hjälper dig att fatta beslut när du väljer mellan olika kodningshastigheter.

Förutom att ange den reserverade **enhetstypen**kan du ange att ditt konto ska etableras med reserverade enheter . Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Om ditt konto till exempel har fem reserverade enheter körs fem medieuppgifter samtidigt så länge det finns uppgifter som ska bearbetas. De återstående uppgifterna väntar i kön och hämtas för bearbetning sekventiellt när en aktivitet som körs slutförs. Om ett konto inte har några etablerade reserverade enheter hämtas aktiviteter sekventiellt. I det här fallet beror väntetiden mellan en aktivitetsavbehandling och nästa start på tillgängligheten för resurser i systemet.

Detaljerad information och exempel som visar hur du skalar kodningsenheter finns i [det här](media-services-scale-media-processing-overview.md) avsnittet.

## <a name="next-step"></a>Nästa steg

[Skala kodningsenheter](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

