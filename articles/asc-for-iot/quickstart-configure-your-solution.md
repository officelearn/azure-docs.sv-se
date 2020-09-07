---
title: 'Snabb start: Konfigurera din lösning'
description: I den här snabb starten får du lära dig hur du konfigurerar din IoT-lösning från slut punkt till slut punkt med hjälp av Azure Security Center för IoT.
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
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e6ab713715cacc799d2b980c2bce2a2a15b76887
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505462"
---
# <a name="quickstart-configure-your-iot-solution"></a>Snabb start: Konfigurera din IoT-lösning

Den här artikeln innehåller en förklaring av hur du utför inledande konfiguration av din IoT-säkerhetslösning med Azure Security Center för IoT.

## <a name="azure-security-center-for-iot"></a>Azure Security Center för IoT

Azure Security Center for IoT ger omfattande säkerhet från slut punkt till slut punkt för Azure-baserade IoT-lösningar.

Med Azure Security Center för IoT kan du övervaka hela IoT-lösningen på en instrument panel, Visa alla IoT-enheter, IoT-plattformar och Server dels resurser i Azure.

När den här inställningen är aktive rad för din Azure Security Center IoT Hub identifierar IoT automatiskt andra Azure-tjänster, även anslutna till din IoT Hub och relaterat till din IoT-lösning.

Förutom automatisk Relations identifiering kan du också välja vilka andra Azure-resurs grupper som ska tagga som en del av din IoT-lösning.

Med dina val kan du lägga till hela prenumerationer, resurs grupper eller enskilda resurser.

När du har definierat alla resurs relationer använder Azure Security Center för IoT Azure Security Center för att ge dig säkerhets rekommendationer och aviseringar för dessa resurser.

## <a name="add-azure-resources-to-your-iot-solution"></a>Lägg till Azure-resurser i din IoT-lösning

Gör så här om du vill lägga till en ny resurs i IoT-lösningen:

1. Öppna din **IoT Hub** i Azure Portal.
1. Välj och öppna **Inställningar** från avsnittet **säkerhet** i den vänstra menyn och välj sedan **övervakade resurser**.
1. Välj **Redigera** och välj de övervakade resurser som hör till din IoT-lösning.
1. Klicka på **Lägg till**.

Grattis! Du har lagt till en ny resurs grupp i IoT-lösningen.

Azure Security Center för IoT övervakar nu att du nyligen har lagt till resurs grupper och viktiga säkerhets rekommendationer och aviseringar som en del av din IoT-lösning.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du skapar säkerhetsmoduler...

> [!div class="nextstepaction"]
> [Skapa säkerhetsmoduler](quickstart-create-security-twin.md)
