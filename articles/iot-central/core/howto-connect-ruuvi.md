---
title: Anslut en RuuviTag i Azure IoT Central | Microsoft-dokument
description: Lär dig hur du ansluter en RuuviTag-miljösensor till ditt IoT Central-program.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: e8d1c4a605e8db2e9753bb80c9712dd6c2be7b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158270"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Ansluta en RuuviTag-sensor till ditt Azure IoT Central-program

I den här artikeln beskrivs hur du som lösningsbyggare kan ansluta en RuuviTag-sensor till ditt Microsoft Azure IoT Central-program.

Vad är en Ruuvi-tagg?

RuuviTag är en avancerad plattform för sensorfyr med öppen källkod utformad för att tillgodose behoven hos företagskunder, utvecklare, beslutsfattare, studenter och hobbyister. Enheten är inställd på att fungera så fort du tar den ur lådan och är redo för dig att distribuera den där du behöver den. Det är en Bluetooth LE beacon med en miljösensor och accelerometer inbyggd.

RuuviTag kommunicerar via BLE (Bluetooth Low Energy) och kräver en gateway-enhet för att prata med Azure IoT Central. Se till att du har en gateway-enhet, till exempel Rigado Cascade 500, installation för att aktivera en RuuviTag för att ansluta till IoT Central.

Följ [instruktionerna här](./howto-connect-rigado-cascade-500.md) om du vill ställa in en Rigado Cascade 500 gateway enhet.

## <a name="prerequisites"></a>Krav

För att ansluta RuuviTag-sensorer behöver du följande resurser:

* En RuuviTag-sensor. För mer information, besök [RuuviTag](https://ruuvi.com/).
* En Rigado Cascade 500 enhet eller en annan BLE gateway. För mer information, besök [Rigado](https://www.rigado.com/).
* Ett Azure IoT Central-program. Mer information finns i [skapa ett nytt program](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Lägga till en RuuviTag-enhetsmall

Om du vill gå in på en RuuviTag-sensor i din Azure IoT Central-programinstans måste du konfigurera en motsvarande enhetsmall i ditt program.

Så här lägger du till en RuuviTag-enhetsmall:

1. Navigera till fliken ***Enhetsmallar*** i den vänstra ![rutan,](./media/howto-connect-ruuvi/devicetemplate-new.png) välj **+ Ny:** Skapa ny enhetsmall Sidan ger dig ett alternativ för att ***skapa en anpassad mall*** eller Använda en ***förkonfigurerad enhetsmall***
1. Välj enhetsmallen RuuviTag i listan över förkonfigurerade ![enhetsmallar som visas nedan: Välj RuuviTag-enhetsmall](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Välj ***Nästa: Anpassa*** för att fortsätta till nästa steg.
1. På nästa skärm väljer du ***Skapa*** för att gå in på C500-enhetsmallen i ditt IoT Central-program.

## <a name="connect-a-ruuvitag-sensor"></a>Anslut en RuuviTag-sensor

Som tidigare nämnts, för att ansluta RuuviTag med din IoT Central ansökan, måste du ställa in en gateway-enhet. Stegen nedan förutsätter att du har konfigurerat en Rigado Cascade 500 gateway-enhet.  

1. Slå på din Rigado Cascade 500-enhet och anslut den till din nätverksanslutning (via Ethernet eller trådlöst)
1. Pop locket av RuuviTag och dra plast fliken för att säkra anslutningen med batteriet.
1. Placera RuuviTag nära en Rigado Cascade 500 gateway som redan är konfigurerad i din IoT Central ansökan.
1. På bara några sekunder ska din RuuviTag visas i din lista över enheter inom IoT Central.  
    ![RuuviTag enhetslista](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Du kan nu använda denna RuuviTag i din IoT Central ansökan.  

## <a name="create-a-simulated-ruuvitag"></a>Skapa en simulerad RuuviTag

Om du inte har en fysisk RuuviTag-enhet kan du skapa en simulerad RuuviTag-sensor som ska användas för testning i ditt Azure IoT Central-program.

Så här skapar du en simulerad RuuviTag:

1. Välj **enheter > RuuviTag**.
1. Välj **+ Nytt**.
1. Ange ett unikt **enhets-ID** och ett eget **enhetsnamn**.  
1. Aktivera den **simulerade** inställningen.
1. Välj **Skapa**.  

## <a name="next-steps"></a>Efterföljande moment

Nu när du har lärt dig hur du ansluter en RuuviTag till ditt Azure IoT Central-program, är det föreslagna nästa steget att lära dig hur du [anpassar ditt IoT Central-program](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) för att skapa en lösning från på.
