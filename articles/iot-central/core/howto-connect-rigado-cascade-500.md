---
title: Anslut en Rigado Cascade 500 i Azure IoT Central | Microsoft-dokument
description: Lär dig hur du ansluter en Rigado Cascade 500 gateway-enhet till ditt IoT Central-program.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 3e6026e683d46ca9062c5c139fcd7febd7df9bd1
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758241"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Ansluta en Rigado Cascade 500-gatewayenhet till ditt Azure IoT Central-program

*Den här artikeln gäller lösningsbyggare och enhetsutvecklare.*

I den här artikeln beskrivs hur du som lösningsbyggare kan ansluta en Rigado Cascade 500-gatewayenhet till ditt Microsoft Azure IoT Central-program. 

## <a name="what-is-cascade-500"></a>Vad är Cascade 500?

Cascade 500 IoT gateway är ett hårdvaruerbjudande från Rigado som ingår som en del av deras Cascade Edge-as-a-Service-lösning. Det ger kommersiella IoT-projekt- och produktteam flexibel edge computing-kraft, en robust containermiljö och ett brett utbud av trådlösa enhetsanslutningsalternativ, inklusive Bluetooth 5, LTE & Wi-Fi.

Cascade 500 är förcertifierad för Azure IoT Plug and Play (förhandsversion) så att våra lösningsbyggare enkelt kan gå in på enheten i sina helhetslösningar. Cascade-gatewayen gör att du trådlöst kan ansluta till en mängd olika tillståndsövervakningssensorer som är i närheten av gateway-enheten. Dessa sensorer kan vara inbyggda i IoT Central via gateway-enheten.

## <a name="prerequisites"></a>Krav
Om du vill gå igenom den här programguiden behöver du följande resurser:

* En Rigado Cascade 500 enhet. För mer information, besök [Rigado](https://www.rigado.com/).
* Ett Azure IoT Central-program. Mer information finns i [skapa ett nytt program](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Lägga till en enhetsmall

För att kunna använda en Cascade 500-gatewayenhet till din Azure IoT Central-programinstans måste du konfigurera en motsvarande enhetsmall i ditt program.

Så här lägger du till en Kaskad 500-enhetsmall: 

1. Navigera till fliken ***Enhetsmallar*** i den vänstra ![rutan, välj **+ Ny:** Skapa ny enhetsmall](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. Sidan ger dig möjlighet att ***skapa en anpassad mall*** eller Använda en ***förkonfigurerad enhetsmall***
1. Välj C500-enhetsmallen i listan över förkonfigurerade ![enhetsmallar som visas nedan: Välj C500-enhetsmall](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Välj ***Nästa: Anpassa*** för att fortsätta till nästa steg. 
1. På nästa skärm väljer du ***Skapa*** för att gå in på C500-enhetsmallen i ditt IoT Central-program.

## <a name="retrieve-application-connection-details"></a>Hämta information om programanslutning

Du måste nu hämta **scope-ID:et** och **primärnyckeln** för ditt Azure IoT Central-program för att kunna ansluta Cascade 500-enheten. 

1. Navigera till **Administration** i den vänstra rutan och klicka på **Enhetsanslutning**. 
2. Anteckna **scope-ID:et** för ditt IoT Central-program.
![Id för appomfattning](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Klicka nu på **Visa tangenter** och anteckna **primärnyckeln**
![primärnyckel](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Kontakta Rigado för att ansluta gatewayen 

För att ansluta Cascade 500-enheten till din IoT Central-applikation måste du kontakta Rigado och förse dem med information om programanslutningen från stegen ovan. 

När enheten är ansluten till Internet, kommer Rigado att kunna trycka ner en konfigurationsuppdatering ner till Cascade 500 gateway-enheten via en säker kanal. 

Den här uppdateringen kommer att tillämpa IoT Central-anslutningsinformationen på Cascade 500-enheten och den visas i listan över enheter. 

![Primärnyckel](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Du är nu redo att använda din C500-enhet i din IoT Central ansökan!

## <a name="next-steps"></a>Nästa steg

Om du är enhetsutvecklare är några föreslagna nästa steg att:

- Läs om [enhetsanslutning i Azure IoT Central](./concepts-get-connected.md)
- Lär dig hur du [övervakar enhetsanslutning med Azure CLI](./howto-monitor-devices-azure-cli.md)
