---
title: "Datavetenskap med den djupgående Learning datavetenskap virtuella datorn i Azure | Microsoft Docs"
description: "Hur du utför flera gemensamma datavetenskap med djup Learning datavetenskap VM."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma
ms.openlocfilehash: 9bc56d242abde919f6150b88c81921d409e1d94c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-deep-learning-virtual-machine"></a>Med hjälp av djup Learning virtuell dator

När du har etablerat din djup Learning virtuell dator (DLVM), kan du börja skapa modeller djupa neurala nätverket för att skapa AI-program i domäner som datorn vision, Förstå språk. 

Det finns ett antal verktyg som finns på DLVM för AI. Den [djup inlärning och AI ramverk sidan](dsvm-deep-learning-ai-frameworks.md) innehåller information om hur du använder dessa verktyg. Den [-exempel](dsvm-samples-and-walkthroughs.md) innehåller pekare till kodexemplen förinstallerade på den virtuella datorn för varje ramverk som hjälper dig att komma igång snabbt. 

# <a name="deep-learning-tutorials-and-walkthroughs"></a>Djupt Learning självstudier och genomgång

Förutom att framework-baserade-exempel erbjuder vi även en uppsättning omfattande genomgång som har godkänts på DLVM. Dessa hjälpfiler aldrig av du utvecklar djup learning-program i domäner som bilden och förstå/språk. Vi kan fortsätta att lägga till fler självstudier för slutpunkt till slutpunkt mellan olika domäner och teknik.   

1. [Kör neurala nätverk mellan olika ramverk](https://github.com/ilkarman/DeepLearningFrameworks): Detta är en omfattande genomgång som visar hur du migrerar kod från en framework till en annan. Här visas också hur du jämföra modellen och kör prestanda över ramverk. 

2. [En instruktioner för att skapa en lösning för slutpunkt till slutpunkt för att identifiera produkterna inom bilder](https://github.com/Azure/cortana-intelligence-product-detection-from-images): identifiering av avbildningen är en teknik som kan hitta och klassificera objekt inom bilder. Den här tekniken har möjlighet att ta mycket stora fördelar i många verkligheten business domäner. Återförsäljare kan till exempel använda den här tekniken för att fastställa vilka produkter en kund har hämtats från hyllan. Den här informationen hjälper i sin tur lagrar hantera produktinformation. 

3. [Klassificering av dokument](https://github.com/anargyri/lstm_han): den här genomgången visar hur du bygger och träna två olika neurala nätverksarkitekturer (hierarkisk uppmärksamhet nätverks- och lång kort sikt minne (LSTM)-nätverk) för klassificering av dokument med hjälp av Keras API för djup. Keras är en klientdel på tre av de mest populära djup learning ramverk, Microsoft kognitiva Toolkit, TensorFlow och Theano.



