---
title: Skapa slutpunkter för webbtjänster
titleSuffix: Azure Machine Learning Studio
description: Skapa slutpunkter för webbtjänster i Azure Machine Learning. Varje slutpunkt i webbtjänsten är oberoende av varandra åtgärdas, begränsas och hanteras.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/12/2019
ms.openlocfilehash: a6945ac7bfb750916e229ae04376f895f2b3d506
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267283"
---
# <a name="creating-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Skapa slutpunkter för distribuerade Azure Machine Learning Studio-webbtjänster

> [!NOTE]
> Det här avsnittet beskrivs tekniker som kan användas till en **klassiska** Machine Learning-webbtjänster.

När en webbtjänst har distribuerats skapas en standardslutpunkt för tjänsten. Standardslutpunkten kan anropas med dess API-nyckel. Du kan lägga till fler slutpunkter med deras egna nycklar från Web Services-portalen.
Varje slutpunkt i webbtjänsten är oberoende av varandra åtgärdas, begränsas och hanteras. Varje slutpunkt är en unik URL med en auktoriseringsnyckeln som du kan distribuera till dina kunder.

## <a name="adding-endpoints-to-a-web-service"></a>Lägga till slutpunkter till en webbtjänst

Du kan lägga till en slutpunkt till en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen. När slutpunkten har skapats kan du använda via synkron API: er, batch API: er, och excel-kalkylblad.

> [!NOTE]
> Om du har lagt till ytterligare slutpunkter till webbtjänsten kan du inte ta bort standardslutpunkten.

1. Klicka på webbtjänster i Machine Learning Studio, på den vänstra kolumnen.
2. Längst ned i instrumentpanelen för webbtjänsten klickar du på **hantera slutpunkter för**. Azure Machine Learning Web Services-portalen öppnas på sidan slutpunkter för webbtjänsten.
3. Klicka på **Ny**.
4. Skriv ett namn och beskrivning för den nya slutpunkten. Slutpunktsnamn måste vara 24 tecken eller mindre långt och måste bestå av gemena bokstäver eller siffror. Välj loggningsnivån och om exempeldata är aktiverad. Mer information om loggning finns i [aktivera loggning för Machine Learning Web services](web-services-logging.md).

## <a name="next-steps"></a>Nästa steg

[Hur du använder en Azure Machine Learning-webbtjänst](consume-web-services.md).
