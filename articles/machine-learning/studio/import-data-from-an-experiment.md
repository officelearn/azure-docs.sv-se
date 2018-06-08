---
title: Importera data till Machine Learning Studio från en annan experiment | Microsoft Docs
description: Så här sparar utbildningsdata i Azure Machine Learning Studio och använda den i ett annat experiment.
keywords: Importera data, data, datakällor, träningsdata
services: machine-learning
documentationcenter: ''
author: bradsev
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
ms.author: bradsev
ms.openlocfilehash: 8279b13270d4bba990e6670acd714be19118e98c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834836"
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importera data till Azure Machine Learning Studio från ett annat experiment
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Det blir när du vill ta ett mellanliggande resultat från en experiment och använda den som en del av ett annat experiment. Om du vill göra detta måste spara du modulen som en datamängd:

1. Klicka på utdata från den modul som du vill spara som en datamängd.
2. Klicka på **Spara som Dataset**.
3. När du uppmanas, anger du ett namn och en beskrivning som du kan enkelt identifiera dataset.
4. Klicka på den **OK** markering.

När Spara har slutförts vara datauppsättningen tillgängliga för användning i alla experiment på arbetsytan. Du hittar den i den **sparade datauppsättningar** lista på modulpaletten.

