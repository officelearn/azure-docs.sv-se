---
title: 'Snabbstart: Konfigurera din Azure Security Center för IoT-lösning'
description: I den här snabbstarten kan du lära dig hur du konfigurerar din end-to-end IoT-lösning med Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: e670df359cc33c9eaca089d0ed8f9614ef8c0468
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73904139"
---
# <a name="quickstart-configure-your-iot-solution"></a>Snabbstart: Konfigurera din IoT-lösning

Den här artikeln innehåller en förklaring av hur du utför den första konfigurationen av din IoT-säkerhetslösning med Azure Security Center för IoT. 

## <a name="azure-security-center-for-iot"></a>Azure Security Center för IoT

Azure Security Center för IoT ger omfattande heltäckande säkerhet för Azure-baserade IoT-lösningar.

Med Azure Security Center för IoT kan du övervaka hela din IoT-lösning i en instrumentpanel, med alla dina IoT-enheter, IoT-plattformar och backend-resurser i Azure.

När azure Security Center for IoT har aktiverats på din IoT-hubb identifierar de automatiskt andra Azure-tjänster som är anslutna till din IoT-hubb och som är relaterade till din IoT-lösning.

Förutom automatisk relationsidentifiering kan du också välja och vraka vilka andra Azure-resursgrupper som ska taggas som en del av din IoT-lösning. 

Med dina val kan du lägga till hela prenumerationer, resursgrupper eller enskilda resurser. 

När du har definierat alla resursrelationer utnyttjar Azure Security Center för IoT Azure Security Center för att ge dig säkerhetsrekommendationer och aviseringar för dessa resurser.

## <a name="add-azure-resources-to-your-iot-solution"></a>Lägga till Azure-resurser i din IoT-lösning

Så här lägger du till ny resurs i IoT-lösningen: 

1. Öppna din **IoT Hub** i Azure-portalen. 
1. Välj och öppna **Resurser** från under **Säkerhet** i den vänstra menyn. 
1. Välj **Redigera** och välj de resursgrupper som tillhör din IoT-lösning.
1. Klicka på **Lägg till**. 

Grattis! Du har lagt till en ny resursgrupp i din IoT-lösning.

Azure Security Center för IoT övervakar nu att du nyligen har lagt till resursgrupper och visar relevanta säkerhetsrekommendationer och aviseringar som en del av din IoT-lösning.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel för att lära dig hur du skapar säkerhetsmoduler...

> [!div class="nextstepaction"]
> [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)