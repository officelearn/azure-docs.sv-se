---
title: Datavetenskap med den Deep Learning Data Science Virtual Machine på Azure | Microsoft Docs
description: Hur du utför flera vanliga datavetenskapsuppgifter med Deep Learning Data Science VM.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 91c1727907c9a134a257e8f7d7c1efb6721c1906
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993767"
---
# <a name="using-the-deep-learning-virtual-machine"></a>Med hjälp av virtuell dator för Djupinlärning

När du har etablerat din Deep Learning Virtual Machine (DLVM) kan börja du skapa modeller för djupa neurala nätverk för att skapa AI-program i domäner som visuellt och språkförståelse. 

Det finns ett antal verktyg som finns på den virtuell dator för Djupinlärning för AI. Den [Djupinlärning och AI-ramverk sidan](dsvm-deep-learning-ai-frameworks.md) innehåller information om hur du använder dessa verktyg. 

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Deep Learning självstudier och genomgångar

Förutom framework-baserad-exemplen finns också en uppsättning heltäckande genomgångar som har verifierats på DLVM. Dessa genomgångar dig rivstarta din utvecklingen av deep learning-program i domäner som bild och text/språkförståelse. Fler självstudier för slutpunkt till slutpunkt i olika domäner och teknik fortsätter som ska läggas till.   


- [Köra neurala nätverk för olika ramverk](https://github.com/ilkarman/DeepLearningFrameworks): en omfattande genomgång som visar hur du migrerar kod från en ramverket till en annan. Den visar även hur du jämför modellen och köra tar över ramverk. 

- [En instruktionsguide för att skapa en lösning för slutpunkt till slutpunkt för att identifiera produkterna i avbildningar](https://github.com/Azure/cortana-intelligence-product-detection-from-images): identifiering av avbildningen är en teknik som kan hitta och klassificera objekt i bilder. Den här tekniken finns risken för att ge enorma fördelar i många verkliga livet är vanligtvis affärsdomäner. Återförsäljare kan till exempel använda den här tekniken för att avgöra vilken produkt en kund har hämtas från hyllan. Den här informationen hjälper i sin tur butiker hantera produktlager. 

- [Djupinlärning för ljud](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) den här kursen visar hur du tränar en modell för djupinlärning för ljud händelse på den [urban ljud datauppsättning](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) och ger en översikt över hur du arbetar med ljuddata.

- [Klassificering av textdokument](https://github.com/anargyri/lstm_han): den här genomgången visar hur du skapar och träna två olika neuralt nätverksarkitekturer: hierarkisk uppmärksamhet nätverks- och lång kort sikt minne (LSTM) nätverk. Dessa neurala nätverk använder Keras-API för djupinlärning för att klassificera textdokument. Keras är en klientdel till tre av de mest populära ramverk för djupinlärning: Microsoft Cognitive Toolkit, TensorFlow och Theano.

## <a name="next-steps"></a>Nästa steg

Den [sidan med kodexempel](dsvm-samples-and-walkthroughs.md) innehåller länkar till kodexemplen förinstallerade på den virtuella datorn för var och en av ramverk som hjälper dig att komma igång snabbt. 
