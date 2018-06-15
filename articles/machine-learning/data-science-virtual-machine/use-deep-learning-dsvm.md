---
title: Datavetenskap med den djupgående Learning datavetenskap virtuella datorn i Azure | Microsoft Docs
description: Hur du utför flera gemensamma datavetenskap med djup Learning datavetenskap VM.
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
ms.openlocfilehash: 2053ed8cc420183d493097eeb2cd2ad93c82c70c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167253"
---
# <a name="using-the-deep-learning-virtual-machine"></a>Med hjälp av djup Learning virtuell dator

När du har etablerat din djup Learning virtuell dator (DLVM), kan du börja skapa modeller djupa neurala nätverket för att skapa AI-program i domäner som datorn vision och förstå språk. 

Det finns ett antal verktyg som finns på djup Learning VM för AI. Den [djup inlärning och AI ramverk sidan](dsvm-deep-learning-ai-frameworks.md) innehåller information om hur du använder dessa verktyg. 

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Djupt Learning självstudier och genomgång

Förutom att framework-baserade-exempel finns också en uppsättning omfattande genomgång som har godkänts på DLVM. Dessa genomgång hjälpa dig att aldrig din utvecklingen av djup learning-program i domäner som bilden och förstå/språk. Flera självstudier för slutpunkt till slutpunkt mellan olika domäner och teknik fortsätter som ska läggas till.   


- [Kör neurala nätverk mellan olika ramverk](https://github.com/ilkarman/DeepLearningFrameworks): en omfattande genomgång som visar hur du migrerar kod från en framework till en annan. Här visas också hur du jämföra modellen och kör prestanda över ramverk. 

- [En instruktioner för att skapa en lösning för slutpunkt till slutpunkt för att identifiera produkterna inom bilder](https://github.com/Azure/cortana-intelligence-product-detection-from-images): identifiering av avbildningen är en teknik som kan hitta och klassificera objekt inom bilder. Den här tekniken har möjlighet att ta mycket stora fördelar i många verkligheten business domäner. Återförsäljare kan till exempel använda den här tekniken för att fastställa vilka produkter en kund har hämtats från hyllan. Den här informationen hjälper i sin tur lagrar hantera produktinformation. 

- [Med namnet entitet extrahering från PubMed sammanfattningar](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition) den här kursen visar hur man extraherar namngivna enheter som läkemedel namn eller sjukdom namn från Ostrukturerade text. Det tränar en anpassad word bädda in på en text Kristi av 18 miljoner PubMed sammanfattningar, använder den modellen för att bygga en lång kortvariga minne (LSTM) återkommande neurala nätverket modell för extrahering av entiteten och visar att bädda in modellen domänspecifika ordet kan överträffar en Allmänt ord inbäddning av entiteten extrahering.

- [Djup learning för ljud](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) den här kursen visar hur du tränar en djup learning modell för identifiering av ljud händelse på den [Urbant ljud dataset](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) och ger en översikt över hur du arbetar med ljuddata.

- [Klassificering av dokument](https://github.com/anargyri/lstm_han): den här genomgången visar hur du bygger och träna två olika neural nätverksarkitekturer: hierarkisk uppmärksamhet nätverks- och lång kort sikt minne (LSTM)-nätverk. Dessa neurala nätverk använder Keras API för djupa för att klassificera dokument. Keras är en klientdel på tre av de mest populära djup learning ramverk: Microsoft kognitiva Toolkit, TensorFlow och Theano.

## <a name="next-steps"></a>Nästa steg

Den [-exempel](dsvm-samples-and-walkthroughs.md) innehåller pekare till kodexemplen förinstallerade på den virtuella datorn för varje ramverk som hjälper dig att komma igång snabbt. 
