---
title: Skapa anpassade varningar för Azure Security Center för förhandsversionen av IoT | Microsoft Docs
description: Skapa och tilldela anpassade aviseringar för Azure Security Center för IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: d793b105e6d73c98739cd05d6e19a218413d7813
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862003"
---
# <a name="quickstart-create-custom-alerts"></a>Snabbstart: Skapa anpassade varningar

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Använda anpassade säkerhetsgrupper och aviseringar kan dra full nytta av säkerhetsinformation från slutpunkt till slutpunkt och kategoriska enheten kunskap för att säkerställa bättre säkerhet över din IoT-lösning. 

## <a name="why-use-custom-alerts"></a>Varför använda anpassade aviseringar? 

Du vet att dina IoT-enheter som är bäst.

För kunder som reklamklickningar sina förväntade enhetsbeteende kan Azure Security Center (ASC) för IoT kan du att översätta denna kunskap när en princip för beteende och en avisering för varje avvikelse från förväntat, normalt.

## <a name="security-groups"></a>Säkerhetsgrupper

Säkerhetsgrupper kan du definiera logiska grupper av enheter och hantera deras säkerhetstillstånd i ett centraliserat sätt.

Dessa grupper kan representera enheter med specifik maskinvara enheter som distribueras i en viss plats eller någon annan grupp som är lämpligt för dina specifika behov.

Säkerhetsgrupper definieras av en säkerhet modulen twin tag-egenskapen med namnet **Tilldelningsmodulen**. Ändra värdet för den här egenskapen att ändra gruppen för en enhet.  

Varje IoT-lösning i IoT Hub har som standard en säkerhetsgrupp med namnet **standard**.

Använda säkerhetsgrupper för att gruppera dina enheter i logiska kategorier. När du har skapat grupperna som du tilldela dem till anpassade aviseringar för för de mest effektiva slutpunkt till slutpunkt-lösningen. 

## <a name="customize-an-alert"></a>Anpassa en avisering

1. Öppna din IoT-hubb. 
2. Välj **Security**och välj sedan **anpassade aviseringar**. 
3. Välj de säkerhetsgrupper som du vill tillämpa anpassningen till. 
4. Klicka på **lägga till en anpassad avisering**
5. Ange ett namn på avisering (Observera att aviseringen namn inte kan ändras efter skapandet). 
6. Välj beteende för en anpassad aviseringar från den nedrullningsbara listan. 
7. Redigera de obligatoriska egenskaperna, klickar du på **OK**.
8. Se till att klicka på **spara**. Aviseringen tas bort nästa gång du stänger IoT Hub utan att spara den nya aviseringen.

 
## <a name="alerts-available-for-customization"></a>Aviseringar som är tillgängliga för anpassning

Följande tabell innehåller en sammanfattning av aviseringar som är tillgängliga för anpassning.

| Severity | Namn                                                                                                    | Datakälla | Beskrivning                                                                                                                                     |
|----------|---------------------------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Låg      | Anpassad avisering – antal cloud enhet-meddelanden i AMQP-protokollet är inte i det tillåtna intervallet          | IoT Hub     | Molnet för att meddelanden från enheten (AMQP-protokollet) under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                  |
| Låg      | Anpassad avisering – antal avvisade cloud enhet-meddelanden i AMQP-protokollet är inte i det tillåtna intervallet | IoT Hub     | Mängden cloud enhet-meddelanden (AMQP-protokollet) som avvisats av enheten under en tidsperiod är inte i den konfigurerade det tillåtna intervallet |
| Låg      | Anpassad avisering – antal enhet till moln-meddelanden i AMQP-protokollet är inte i det tillåtna intervallet          | IoT Hub     | Hur mycket av enheten till molnet meddelanden (AMQP-protokollet) under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                  |
| Låg      | Anpassad avisering – antal direkt metod anropar är inte i det tillåtna intervallet                              | IoT Hub     | Mängden direkt metod som anropar i taget fönstret inte är i den konfigurerade det tillåtna intervallet                                                     |
| Låg      | Anpassad avisering – antal filöverföringar är inte i det tillåtna intervallet                                       | IoT Hub     | Mängden filöverföringar under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                                              |
| Låg      | Anpassad avisering – antal cloud enhet-meddelanden i HTTP-protokollet är inte i det tillåtna intervallet          | IoT Hub     | Molnet för att meddelanden från enheten (HTTP-protokollet) under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                  |
| Låg      | Anpassad avisering – antal avvisade cloud enhet-meddelanden i HTTP-protokollet är inte i det tillåtna intervallet | IoT Hub     | Mängden cloud enhet-meddelanden (HTTP-protokollet) som avvisats av enheten under en tidsperiod är inte i den konfigurerade det tillåtna intervallet |
| Låg      | Anpassad avisering – antal enhet till moln-meddelanden i HTTP-protokollet är inte i det tillåtna intervallet          | IoT Hub     | Hur mycket av enheten till molnet meddelanden (HTTP-protokollet) under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                  |
| Låg      | Anpassad avisering – antal cloud enhet-meddelanden i MQTT-protokollet är inte i det tillåtna intervallet          | IoT Hub     | Molnet för att meddelanden från enheten (MQTT-protokollet) under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                  |
| Låg      | Anpassad avisering – antal avvisade cloud enhet-meddelanden i MQTT-protokollet är inte i det tillåtna intervallet | IoT Hub     | Mängden cloud enhet-meddelanden (MQTT-protokollet) som avvisats av enheten under en tidsperiod är inte i den konfigurerade det tillåtna intervallet |
| Låg      | Anpassad avisering – antal enhet till moln-meddelanden i MQTT-protokollet är inte i det tillåtna intervallet          | IoT Hub     | Hur mycket av enheten till molnet meddelanden (MQTT-protokollet) under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                  |
| Låg      | Anpassad avisering – antal kommandot kö återställningspunkter är inte i det tillåtna intervallet                               | IoT Hub     | Mängden kommandot kö rensar i taget fönstret inte är i den konfigurerade det tillåtna intervallet                                                      |
| Låg      | Anpassad avisering – antal uppdateringar för enhetstvilling är inte i det tillåtna intervallet                                       | IoT Hub     | Uppdateringar för enhetstvilling under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                                              |
| Låg      | Anpassad avisering – antal obehöriga åtgärder är inte i det tillåtna intervallet                            | IoT Hub     | Mängden obehöriga åtgärder under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                                   |
| Låg      | Anpassad avisering – antal aktiva anslutningar som inte finns i det tillåtna intervallet                                        | Agent       | Mängden aktiva anslutningar under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                                        |
| Låg      | Anpassad avisering – utgående anslutning till en IP-adress som inte är tillåtet har skapats                              | Agent       | En utgående anslutning till en IP-adress som inte är tillåtet har skapats                                                                                  |
| Låg      | Anpassad avisering – antal misslyckade inloggningar som lokala inte är i det tillåtna intervallet                                | Agent       | Misslyckade inloggningar som lokala under en tidsperiod är inte i den konfigurerade det tillåtna intervallet                                                       |
| Låg      | Anpassad avisering - inloggningen för en användare som inte är tillåtet                                                      | Agent       | En lokal användare som inte tillåts inloggad på enheten                                                                                        |
| Låg      | Anpassad avisering - körning av en process som inte är tillåtet                                               | Agent       | En process som inte är tillåtet utfördes på enheten |          |

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du distribuerar en säkerhetsagenten...

> [!div class="nextstepaction"]
> [Distribuera en säkerhetsagenten](how-to-deploy-agent.md)
