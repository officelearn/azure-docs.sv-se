---
title: 'Exportera data: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen exportera data i Azure Machine Learning för att spara resultat, mellanliggande data och arbeta data från dina pipelines till moln lagrings mål utanför Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: 904b3ce1c2d05d713ee1ae99662148217f2a358e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337831"
---
# <a name="export-data-module"></a>Exportera datamodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att spara resultat, mellanliggande data och arbeta data från dina pipelines till moln lagrings mål. 

Den här modulen stöder export av data till följande moln data tjänster:

- Azure Blob-behållare
- Azure-filresurs
- Azure Data Lake
- Azure Data Lake Gen2
- Azure SQL-databas

Innan du exporterar dina data måste du först registrera ett data lager på din Azure Machine Learning-arbetsyta. Mer information finns i [få åtkomst till data i Azure Storage-tjänster](../how-to-access-data.md).

## <a name="how-to-configure-export-data"></a>Så här konfigurerar du export data

1. Lägg till modulen **Exportera data** till din pipeline i designern. Du hittar den här modulen i kategorin för **indata och utdata** .

1. Anslut **Exportera data** till modulen som innehåller de data som du vill exportera.

1. Välj **Exportera data** för att öppna fönstret **Egenskaper** .

1. För **data lager**väljer du ett befintligt data lager i list rutan. Du kan också skapa ett nytt data lager. Se hur du [använder åtkomst data i Azure Storage-tjänster](../how-to-access-data.md).

    > [!NOTE]
    > Export av data för en viss datatyp till en SQL Database-kolumn som har angetts som en annan datatyp stöds inte.

1. Kryss rutan **skapa utdata**igen, bestämmer om modulen ska köras för att återskapa utdata vid körning. 

    Den är som standard omarkerad, vilket innebär att om modulen har körts med samma parametrar tidigare kommer systemet att återanvända utdata från senaste körning för att minska körnings tiden. 

    Om den är markerad kommer systemet att köra modulen igen för att återskapa utdata.

1. Definiera sökvägen i data lagret där datan finns. Sökvägen är en relativ sökväg. Tomma sökvägar eller URL-sökvägar är inte tillåtna.


1. I **fil format**väljer du det format som data ska lagras i.
 
1. Skicka pipelinen.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
