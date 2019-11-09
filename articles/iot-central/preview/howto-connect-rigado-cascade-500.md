---
title: Ansluta en Rigado kaskad 500 i Azure IoT Central | Microsoft Docs
description: Lär dig hur du ansluter en Rigado kaskad 500-gateway-enhet till ditt IoT Central-program.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: 4559bb87369309882ebdaa0d3b408786feb586b5
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896039"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Ansluta en Rigado kaskad 500-gateway-enhet till ditt Azure IoT Central-program


I den här artikeln beskrivs hur du kan ansluta en Rigado kaskad 500-gateway-enhet till ditt Microsoft Azure IoT Central-program i en Solution Builder. 

## <a name="what-is-cascade-500"></a>Vad är kaskad på 500?

Överlappande 500 IoT Gateway är ett maskin varu erbjudande från Rigado som ingår som en del av deras relaterade lösning för Edge-as-a-service. Det tillhandahåller kommersiella IoT-projekt och produkt team med flexibel gräns för data behandling, en robust program miljö för behållare och en mängd olika alternativ för trådlös enhets anslutning, inklusive Bluetooth 5, LTE, & Wi-Fi.

Cascade 500 är förcertifierat för Azure IoT Plug and Play (PnP) som gör det möjligt för våra lösnings byggare att enkelt publicera enheten i sina slut-till-slut-lösningar. Med överlappande Gateway kan du trådlöst ansluta till en mängd olika villkor som övervakar sensorer som är i närheten av gateway-enheten. Dessa sensorer kan registreras i IoT Central via gateway-enheten.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom den här instruktions guiden behöver du följande resurser:

* En Rigado kaskad 500-enhet. Mer information finns på [Rigado](https://www.rigado.com/).
* Ett Azure IoT Central-program som skapats från någon av Programmallarna för förhands granskning. Mer information finns i avsnittet [skapa ett nytt program](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Lägga till en enhetsmall

För att kunna publicera en överlappande 500-gateway-enhet i din Azure IoT Central program instans måste du konfigurera en motsvarande enhets mall i ditt program.

Lägga till en enhets mal len Cascade 500: 

1. Gå till fliken ***mallar för enheter*** i den vänstra rutan och välj **+ ny**: ![skapa ny enhets mal len](./media/howto-connect-rigado-cascade500/device-template-new.png)
1. Sidan ger dig ett alternativ för att ***skapa en anpassad mall*** eller ***använda en förkonfigurerad enhets mall***
1. Välj enhets mal len C500 i listan med förkonfigurerade enhetsspecifika mallar enligt nedan: ![välja C500 Device Template](./media/howto-connect-rigado-cascade500/device-template-preconfigured.png)
1. Välj ***Nästa: anpassa*** för att fortsätta till nästa steg. 
1. På nästa skärm väljer du ***skapa*** för att publicera enhets mal len C500 i ditt IoT Central-program.

## <a name="retrieve-application-connection-details"></a>Hämta information om program anslutning

Du måste nu hämta **omfångs-ID** och **primär nyckel** för ditt Azure IoT Central-program för att kunna ansluta kaskad 500-enheten. 

1. Navigera till **Administration** i det vänstra fönstret och klicka på **enhets anslutning**. 
2. Anteckna **omfångs-ID:** t för ditt IoT Central-program.
![app-scope-ID](./media/howto-connect-rigado-cascade500/app-scope-id.png)
3. Klicka nu på **Visa nycklar** och anteckna **primär nyckel**
![primär nyckel](./media/howto-connect-rigado-cascade500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Kontakta Rigado för att ansluta gatewayen 

För att du ska kunna ansluta kaskad 500-enheten till ditt IoT Central-program måste du kontakta Rigado och ge dem information om program anslutningen från ovanstående steg. 

När enheten är ansluten till Internet kommer Rigado att kunna push-överföra en konfigurations uppdatering till kaskad 500 gateway-enheten via en säker kanal. 

Den här uppdateringen kommer att tillämpa IoT Central anslutnings information på kaskad 500-enheten och den visas i enhets listan. 

![Primär nyckel](./media/howto-connect-rigado-cascade500/devices-list-c500.png)  

Du är nu redo att använda din C500-enhet i ditt IoT Central-program!

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en Rigado kaskad 500 till ditt Azure IoT Central-program, är det föreslagna nästa steg att lära dig hur du [skapar ett in-Store Analytics-program](../retail/tutorial-in-store-analytics-create-app-pnp.md) för att skapa en lösning för slut punkt till slut punkt. 