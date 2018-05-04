---
title: Azure IoT-hubb IP-anslutningsfilter | Microsoft Docs
description: Hur du använder IP-filtrering för som blockerar anslutningar från särskilda IP-adresser för dina Azure IoT-hubb. Du kan blockera anslutningar från enskilda eller intervall med IP-adresser.
services: iot-hub
documentationcenter: ''
author: BeatriceOltean
manager: timlt
editor: ''
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: boltean
ms.openlocfilehash: 0229b8b16de1fa04d953c90895b29e4fbad60ec3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-ip-filters"></a>IP-filter

Säkerhet är en viktig del av alla IoT-lösning baserad på Azure IoT Hub. Ibland måste uttryckligen ange IP-adresser från vilka enheter kan anslutas som en del av din konfiguration. Den _IP-filter_ funktionen kan du konfigurera regler för avvisa eller ta emot trafik från specifika IPv4-adresser.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användningsfall när det är praktiskt att blockera IoT-hubbslutpunkter för vissa IP-adresser:

- IoT-hubb bör endast ta emot trafik från ett angivet IP-adresser och avvisa allt annat. Till exempel du använder din IoT-hubb med [Azure Express Route] att skapa privata anslutningar mellan en IoT-hubb och lokal infrastruktur.
- Behöver du avvisa trafik från IP-adresser som har identifierats som misstänkt av IoT hub-administratör.

## <a name="how-filter-rules-are-applied"></a>Hur filter regler

IP-filterregler tillämpas på tjänstnivå IoT-hubb. Därför tillämpas IP-filterregler på alla anslutningar från enheter och backend-appar som använder alla protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en rejecting IP-regel i din IoT-hubb tar emot en obehörig 401 statuskoden och en beskrivning. Svarsmeddelandet nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-Filter** rutnätet för en IoT-hubb i portalen är tomt. Den här standardinställningen innebär att din hubb accepterar anslutningar IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

![IoT-hubb standardinställningarna IP-filter][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Lägg till eller redigera en regel för IP-filter

När du lägger till en regel för IP-filter tillfrågas du om följande värden:

- En **Regelnamn för IP-filter** som måste vara en unik, skiftlägeskänsliga, alfanumeriska sträng upp till 128 tecken. Endast de ASCII-7-bitars alfanumeriska tecken plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` accepteras.
- Välj en **avvisa** eller **acceptera** som den **åtgärd** för IP-filterregeln.
- Ange en IPv4-adress eller ett block av IP-adresser i CIDR-notation. Till exempel i CIDR representerar notation 192.168.100.0/22 1024 IPv4-adresser från adressen 192.168.100.0 192.168.103.255.

![Lägga till en regel för IP-filter i en IoT-hubb][img-ip-filter-add-rule]

När du har sparat regeln kan se du en avisering om att uppdateringen pågår.

![Meddelande om att spara en regel för IP-filter][img-ip-filter-save-new-rule]

Den **Lägg till** alternativet inaktiveras när du når högst 10 IP-filterregler.

Du kan redigera en befintlig regel genom att dubbelklicka på den rad som innehåller regeln.

> [!NOTE]
> Neka IP adresser kan förhindra att andra Azure-tjänster (till exempel Azure Stream Analytics, Azure virtuella datorer eller enheter Explorer i portalen) interagera med IoT-hubben.

> [!WARNING]
> Om du använder Azure Stream Analytics (ASA) för att läsa meddelanden från en IoT-hubb med IP-filtrering, Använd Event Hub-kompatibelt namn och slutpunkten för din IoT-hubb i anslutningssträngen ASA.

## <a name="delete-an-ip-filter-rule"></a>Ta bort en regel för IP-filter

Välj en eller flera regler i rutnätet och klicka om du vill ta bort en regel för IP-filter, **ta bort**.

![Ta bort en regel för IoT-hubb IP-filter][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>IP-filter regeln utvärdering

IP-filterregler tillämpas i ordning och den första regeln som matchar IP-adressen bestämmer åtgärden Godkänn eller avvisa.

Om du vill acceptera adresser i intervallet 192.168.100.0/22 och avvisa allt annat ska den första regeln i rutnätet acceptera adressintervallet 192.168.100.0/22. Nästa regel bör Ignorera alla adresser med hjälp av intervallet 0.0.0.0/0.

Du kan ändra ordning på IP-filter reglerna i rutnätet genom att klicka tre lodräta punkter i början av en rad med dra och släpp.

Om du vill spara din nya Regelordningen för IP-filter, klickar du på **spara**.

![Ändra ordning på din IoT-hubb IP filterregler][img-ip-filter-rule-order]

## <a name="next-steps"></a>Nästa steg

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

- [Åtgärder som övervakning][lnk-monitor]
- [IoT-hubb mått][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure Express Route]:  https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
