---
title: 'Exportera data: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Exportera data i Azure Machine Learning för att spara resultat, mellanliggande data och arbetsdata från dina pipelines i molnlagringsmål utanför Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456309"
---
# <a name="export-data-module"></a>Exportera datamodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att spara resultat, mellanliggande data och arbetsdata från dina pipelines i molnlagringsmål. 

Den här modulen stöder export av data till följande molndatatjänster:

- Azure Blob-behållare
- Azure-filresurs
- Azure Data Lake
- Azure Data Lake Gen2

Innan du exporterar dina data måste du först registrera ett datalager på arbetsytan Azure Machine Learning. Mer information finns [i Access-data i Azure storage services](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Konfigurera exportdata

1. Lägg till modulen **Exportera data** i pipelinen i designern. Du hittar den här modulen i kategorin **In- och utdata.**

1. Anslut **exportera data** till modulen som innehåller de data som du vill exportera.

1. Välj **Exportera data** om du vill öppna fönstret **Egenskaper.**

1. För **Datastore**väljer du ett befintligt datalager i listrutan. Du kan också skapa ett nytt datalager. Kontrollera hur du besöker [Access-data i Azure storage services](../how-to-access-data.md).

1. Kryssrutan, **Regenerate output**, avgör om modulen ska köras för att återskapa utdata vid drift. 

    Det är som standard omarkerat, vilket innebär att om modulen har körts med samma parametrar tidigare, kommer systemet att återanvända utdata från senaste körningen för att minska körningstiden. 

    Om det väljs kommer systemet att köra modulen igen för att återskapa utdata.

1. Definiera sökvägen i datalagret där data finns. Banan är en relativ sökväg. Tomma sökvägar eller url-sökvägar är inte tillåtna.


1. För **Filformat**väljer du det format i vilket data ska lagras.
 
1. Skicka pipelinen.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
