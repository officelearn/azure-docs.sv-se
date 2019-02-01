---
title: Importera data från fil
titleSuffix: Azure Machine Learning Studio
description: Lär dig hur du överför en utbildning datafil från hårddisken till Azure Machine Learning Studio. Detta skapar en datauppsättning modul på arbetsytan.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: fe09eabe399af2d3ddc776843f39742774e6ea3c
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495895"
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-azure-machine-learning-studio"></a>Importera utbildningsdata från en fil på din hårddisk till Azure Machine Learning Studio

Lär dig hur du överför en datafil från hårddisken ska användas som träningsdata i Azure Machine Learning Studio. Genom att importera filen har du en datauppsättning modul som är redo för användning i din arbetsyta.

## <a name="steps-to-import-data-from-a-local-file"></a>Steg för att importera data från en lokal fil
Om du vill importera data från en lokal hårddisk, gör du följande:

1. Klicka på **+ ny** längst ned i Machine Learning Studio-fönstret.
2. Välj **DATAUPPSÄTTNING** och **från lokal fil**.
3. I den **ladda upp en ny datauppsättning** dialogrutan, bläddra till den fil som du vill ladda upp
4. Ange ett namn, identifiera datatypen och även ange en beskrivning. En beskrivning rekommenderas – du kan registrera alla egenskaper om de data som du vill komma ihåg när du använder data i framtiden.
5. Kryssrutan **det här är den nya versionen av en befintlig datamängd** gör att du kan uppdatera en befintlig datamängd med nya data. Klicka på den här kryssrutan och ange namnet på en befintlig datamängd.

![Ladda upp en ny datauppsättning](./media/import-data-from-local-file/upload-dataset.png)

Under uppladdningen visas ett meddelande om att din fil överförs. Ladda upp tiden beror på storleken på dina data och hastigheten på din anslutning till tjänsten. Om du vet att filen tar lång tid, kan du göra andra saker i Machine Learning Studio medan du väntar. Dock gör webbläsaren stängs att överföringen kommer data att misslyckas.

## <a name="dataset-module-is-ready-for-use"></a>Datauppsättning modulen är redo för användning
När dina data har överförts, lagras i en datauppsättning modul och är tillgänglig för alla experiment på arbetsytan.

När du redigerar ett experiment, hittar du de datauppsättningar som du har skapat i den **Mina datauppsättningar** listan den **sparade datauppsättningar** lista på modulpaletten. Du kan dra och släpp datauppsättningen till experimentets arbetsyta när du vill använda datauppsättningen för ytterligare analys och maskininlärning.
