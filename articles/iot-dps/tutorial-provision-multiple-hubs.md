---
title: Använd Azure IoT Hub Device Provisioning Service till att etablera enheter över belastningsutjämnade IoT-hubbar | Microsoft Docs
description: DPS automatiska enhetsetablering över belastningsutjämnade IoT-hubbar i Azure Portal
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: d0a3720fe729d5e260bbe5b0902460c8c7cfc7cb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629634"
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Etablera enheter över belastningsutjämnade IoT-hubbar

Den här självstudien visar hur du etablerar enheter för flera belastningsutjämnade IoT-hubbar genom att använda Device Provisioning Service (DPS). I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Använda Azure Portal till att etablera en andra enhet till en andra IoT-hubb 
> * Lägga till en post i registreringslistan för den andra enheten
> * Ange DPS-allokeringsprincipen till **jämn distribution**
> * Länka den nya IoT-hubben till DPS

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien bygger på den tidigare självstudien [Etablera enhet till en hubb](tutorial-provision-device-to-hub.md).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Använda Azure Portal till att etablera en andra enhet till en andra IoT-hubb

Följ stegen i självstudien [Etablera enhet till en hubb](tutorial-provision-device-to-hub.md) för att etablera en andra enhet till en annan IoT-hubb.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Lägga till en post i registreringslistan för den andra enheten

Registreringslistan visar DPS vilken attesteringsmetod (metod för att bekräfta en enhetsidentitet) som används med enheten. Nästa steg är att lägga till en post i registreringslistan för den andra enheten. 

1. På sidan för din DPS klickar du på **Hantera registreringar**. Sidan **Lägg till post i registreringslistan** visas. 
2. Klicka på **Lägg till** högst upp på sidan.
2. Fyll i fälten och klicka sedan på **Spara**.

## <a name="set-the-dps-allocation-policy"></a>Ange allokeringsprincip för DPS

Allokeringsprincipen är en DPS-inställning som fastställer hur enheter tilldelas till en IoT-hubb. Det finns tre allokeringsprinciper som stöds: 

1. **Kortast svarstid**: Enheter etableras till en IoT-hubb baserat på hubben med kortast svarstid till enheten.
2. **Jämnt viktad distribution** (standard): Det är lika sannolikt att länkade IoT-hubbar får enheter etablerade till sig. Det här är standardinställningen. Om du endast etablerar enheter till en IoT-hubb kan du behålla den här inställningen. 
3. **Statisk konfiguration via registreringslistan**: Specifikation av den önskade IoT-hubben på registreringslistan har högre prioritet än allokeringsprincipen på DPS-nivå.

Följ dessa steg om du vill ange allokeringsprincipen:

1. Om du vill ställa in allokeringsprincipen går du till DPS-sidan och klickar på **Hantera allokeringsprincip**.
2. Ange allokeringsprincipen till **Jämnt viktad distribution**.
3. Klicka på **Spara**.

## <a name="link-the-new-iot-hub-to-dps"></a>Länka den nya IoT-hubben till DPS

Länka DPS och IoT-hubben så att DPS kan registrera enheter på hubben.

1. På sidan **Alla resurser** klickar du på den DPS som du skapade tidigare.
2. På DPS-sidan klickar du på **Länkade IoT-hubbar**.
3. Klicka på **Lägg till**.
4. På sidan **Lägg till länk i IoT Hub** använder du alternativknapparna för att ange om den länkade IoT-hubben är placerad i den aktuella prenumerationen eller i en annan prenumeration. Välj sedan IoT-hubbens namn från rutan **IoT Hub**.
5. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda Azure Portal till att etablera en andra enhet till en andra IoT-hubb 
> * Lägga till en post i registreringslistan för den andra enheten
> * Ange DPS-allokeringsprincipen till **jämn distribution**
> * Länka den nya IoT-hubben till DPS

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
