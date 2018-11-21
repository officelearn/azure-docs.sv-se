---
title: Importera data till Machine Learning Studio från ett annat experiment | Microsoft Docs
description: Så här att spara träningsdata i Azure Machine Learning Studio och använda den i ett annat experiment.
keywords: Importera data, data, datakällor, utbildningsdata
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=deguhath, author=deguhath)
ms.author: amlstudiodocs
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: cb024f757772ec22adcf4513c422d41628ab7360
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52265159"
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importera data till Azure Machine Learning Studio från ett annat experiment

Det blir när du vill ta ett mellanliggande resultat från ett experiment och använda den som en del av ett annat experiment. Om du vill göra detta måste spara du modulen som en datauppsättning:

1. Klicka på utdata från modulen som du vill spara som en datauppsättning.
2. Klicka på **Spara som datamängd**.
3. När du uppmanas, anger du ett namn och en beskrivning som du kan enkelt identifiera datauppsättningen.
4. Klicka på den **OK** bockmarkeringen.

När du är klar spara blir datauppsättningen tillgänglig för användning i alla experiment på arbetsytan. Du hittar den i den **sparade datauppsättningar** lista på modulpaletten.

