---
title: Importera data från en fil – Azure Machine Learning Studio | Microsoft Docs
description: Lär dig hur du överför en utbildning datafil från hårddisken till Azure Machine Learning Studio. Detta skapar en datauppsättning modul på arbetsytan.
keywords: Importera data, dataformat, datatyper, datakällor, utbildningsdata
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 096d2d4a8c1ee1727b124a5416ec77e8281c97d7
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254868"
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
