---
title: Azure IoT Hub IP-anslutningsfilter | Microsoft Docs
description: Så här använder IP-filtrering för blockerar anslutningar från specifika IP-adresser för till Azure IoT hub. Du kan blockera anslutningar från enskilda eller intervall av IP-adresser.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: 864af9cae35912d95f2c0bf0b574a5ca2404a608
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190649"
---
# <a name="use-ip-filters"></a>IP-filter

Säkerhet är en viktig aspekt av alla IoT-lösningar som baseras på Azure IoT Hub. Ibland måste du uttryckligen ange IP-adresser som innehåller de enheter som kan ansluta som en del av din säkerhetskonfiguration. Den _IP-adressfilter_ funktionen kan du konfigurera regler för avvisar eller tar emot trafik från specifika IPv4-adresser.

## <a name="when-to-use"></a>När du ska använda detta

Det finns två specifika användningsfall när det är bra att blockera IoT Hub-slutpunkter för vissa IP-adresser:

- IoT-hubben ska ta emot trafik från ett angivet intervall med IP-adresser och avvisa allt annat. Exempel: du använder IoT hub med [Azure Express Route] skapa privata anslutningar mellan en IoT-hubb och din lokala infrastruktur.
- Du måste avvisa trafik från IP-adresser som har identifierats som misstänkt av IoT hub-administratör.

## <a name="how-filter-rules-are-applied"></a>Hur filterregler tillämpas

IP-filterreglerna tillämpas på tjänstnivå IoT Hub. Därför gäller IP-filterreglerna för alla anslutningar från enheter och backend-appar med alla protokoll som stöds.

Alla anslutningsförsök från en IP-adress som matchar en rejecting IP-regel i din IoT-hubb tar emot en obehörig 401 statuskoden och en beskrivning. Svarsmeddelandet nämner inte IP-regeln.

## <a name="default-setting"></a>Standardinställningen

Som standard den **IP-adressfilter** rutnätet i portal för en IoT-hubb är tomt. Den här standardinställningen innebär att din hubb godtar anslutningar på IP-adresser. Den här standardinställningen motsvarar en regel som accepterar 0.0.0.0/0 IP-adressintervall.

![IoT Hub standardinställningarna IP-filter][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Lägg till eller redigera en IP-filterregeln

När du lägger till en IP-filterregeln uppmanas du att följande värden:

- En **Regelnamn för IP-filter** som måste vara en unik, skiftlägesokänslig, alfanumerisk sträng på högst 128 tecken. Endast de ASCII 7 bitar alfanumeriska tecken plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` accepteras.
- Välj en **avvisa** eller **acceptera** som den **åtgärd** för IP-filterregeln.
- Ange en IPv4-adress eller ett block med IP-adresser i CIDR-notation. Exempel: i CIDR notation 192.168.100.0/22 representerar 1024 IPv4-adresser från 192.168.100.0 till 192.168.103.255.

![Lägg till en IP-filterregeln till en IoT hub][img-ip-filter-add-rule]

När du har sparat regeln kan se du en avisering om att uppdateringen pågår.

![Meddelande om hur du sparar en IP-filterregeln][img-ip-filter-save-new-rule]

Den **Lägg till** alternativet inaktiveras när du når upp till 10 IP-filterreglerna.

Du kan redigera en befintlig regel genom att dubbelklicka på den rad som innehåller regeln.

> [!NOTE]
> Avvisa IP adresser kan förhindra att andra Azure-tjänster (till exempel Azure Stream Analytics, Azure Virtual Machines eller Device Explorer i portalen) interagera med IoT hub.

> [!WARNING]
> Om du använder Azure Stream Analytics (ASA) för att läsa meddelanden från en IoT-hubb med IP-filtrering, Använd Event Hub-kompatibla namnet och slutpunkten för din IoT-hubb i anslutningssträngen ASA.

## <a name="delete-an-ip-filter-rule"></a>Ta bort en regel för IP-filter

Välj en eller flera regler i rutnätet och klicka på för att ta bort en IP-filterregeln **ta bort**.

![Ta bort en regel för IoT Hub IP-filter][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>IP-filter rule utvärdering

IP-filterreglerna tillämpas i ordning och den första regeln som matchar IP-adressen anger åtgärden acceptera eller avvisa.

Om du vill acceptera adresserna i intervallet 192.168.100.0/22 och avvisa allt annat, bör den första regeln i rutnätet godkänna adressintervallet 192.168.100.0/22. Nästa regel ska avvisa alla adresser med hjälp av adressintervallet 0.0.0.0/0.

Du kan ändra ordning på IP-filterreglerna i rutnätet genom att klicka på de tre lodräta punkterna i början av en rad och med dra och släpp.

Om du vill spara den nya IP-filter regel för turordning, klickar du på **spara**.

![Ändra ordning på din IoT Hub IP-filterreglerna][img-ip-filter-rule-order]

## <a name="next-steps"></a>Nästa steg

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

- [Åtgärdsövervakning][lnk-monitor]
- [IoT Hub-mått][lnk-metrics]

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
