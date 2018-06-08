---
title: Importera data från en fil till Azure Machine Learning Studio | Microsoft Docs
description: Lär dig mer om att överföra filen en utbildning data från hårddisken till Azure Machine Learning Studio. Detta skapar en dataset-modul i arbetsytan.
keywords: Importera data, dataformatet, datatyper, datakällor, träningsdata
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 0365492b2814d686dd0bfa099e94717137b51725
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834778"
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>Importera träningsdata från en fil på hårddisken till Machine Learning Studio
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Lär dig hur du överför en fil från hårddisken ska användas som utbildningsdata i Azure Machine Learning Studio. Genom att importera filen har du en dataset-modul som är redo för användning i din arbetsyta.

## <a name="steps-to-import-data-from-a-local-file"></a>Steg för att importera data från en lokal fil
Om du vill importera data från en lokal hårddisk måste du göra följande:

1. Klicka på **+ ny** längst ned i fönstret Machine Learning Studio.
2. Välj **DATASET** och **från en lokal fil**.
3. I den **ladda upp en ny datamängd** dialogrutan, bläddra till filen som du vill ladda upp
4. Ange ett namn, identifiera datatypen och även ange en beskrivning. En beskrivning rekommenderas – du kan registrera alla egenskaper om de data som du vill spara när du använder data i framtiden.
5. Kryssrutan **detta är den nya versionen av en befintlig datauppsättning** kan du uppdatera en befintlig datamängd med nya data. Klicka på den här kryssrutan och sedan ange namnet på en befintlig datauppsättning.

![Ladda upp en ny datamängd](./media/import-data-from-local-file/upload-dataset.png)

Under överföring visas ett meddelande om att filen överförs. Överför tiden beror på storleken på dina data och hastigheten på anslutningen till tjänsten. Om du vet att filen tar lång tid kan göra du andra saker i Machine Learning Studio medan du väntar. Dock gör stänger webbläsaren dataöverföringen misslyckas.

## <a name="dataset-module-is-ready-for-use"></a>DataSet-modulen är redo för användning
När data överförs lagras i en dataset-modul och är tillgänglig för alla experiment på arbetsytan.

När du redigerar ett experiment kan du hitta datauppsättningar som du har skapat i den **Mina datauppsättningar** listan den **sparade datauppsättningar** lista på modulpaletten. Du kan dra och släpp dataset till arbetsytan för experimentet när du vill använda datauppsättningen för ytterligare analys och maskininlärning.
