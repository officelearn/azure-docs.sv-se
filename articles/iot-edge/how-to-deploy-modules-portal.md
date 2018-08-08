---
title: Distribuera Azure IoT Edge-moduler (portal) | Microsoft Docs
description: Använd Azure-portalen för att distribuera moduler till en IoT Edge-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 83f199c49209210ec577017534f93e36d05bd70a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620373"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Distribuera Azure IoT Edge-moduler från Azure portal

När du skapar IoT Edge moduler med din affärslogik som du vill distribuera dem till dina enheter att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på en gång och deklarera routningsregler som förenar dem. 

Den här artikeln visar hur Azure-portalen visar hur du skapar ett manifest för distribution och skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som är inriktad på flera enheter baserat på deras delade taggar finns i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen. 
* En [IoT Edge-enhet](how-to-register-device-portal.md) med IoT Edge-körningen installerad. 

## <a name="select-your-device"></a>Välj din enhet

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
2. Välj **IoT Edge** på menyn.
3. Klicka på ID för målenhet i listan över enheter. 
4. Välj **Ange moduler**.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Mer information om hur distribution manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Azure-portalen har en guide som visar hur du skapar distributionen manifestet, i stället för att skapa JSON-dokumentet manuellt. Den har tre steg: **Lägg till moduler**, **ange vägar**, och **granska distribution**. 

### <a name="add-modules"></a>Lägg till moduler

1. I den **registerinställningar** avsnitt på sidan Ange autentiseringsuppgifter för att komma åt alla privata behållarregister som innehåller dina avbildningar för modulen. 
2. I den **distribution moduler** på sidan, väljer **Lägg till**. 
3. Titta på typerna av moduler från den nedrullningsbara listan: 
   * **IoT Edge-modul** -alternativet.
   * **Azure Stream Analytics-modulen** -endast moduler som har genererats från en Azure Stream Analytics-arbetsbelastning. 
4. Välj den **IoT Edge-modul**.
5. Ange ett namn för modulen och sedan ange behållaravbildningen. Exempel: 
   * **Namn på** -tempSensor
   * **Bild-URI: N** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
6. Fyll i valfritt fält om det behövs. För mer information om behållare alternativ, omstartsprincip, för att skapa och önskad status finns i [EdgeAgent önskade egenskaper](module-edgeagent-edgehub.md#edgeagent-desired-properties). Läs mer om modultvillingen [definiera eller uppdatera önskade egenskaper](module-composition.md#define-or-update-desired-properties).
7. Välj **Spara**.
8. Upprepa steg 2 till 6 för att lägga till ytterligare moduler för din distribution. 
9. Välj **nästa** att fortsätta till avsnittet vägar.

### <a name="specify-routes"></a>Ange vägar

Som standard i guiden får du en väg kallas **väg** och definieras som **FROM /* i $uppströms **, vilket innebär att alla meddelanden som utdata av alla moduler som skickas till din IoT-hubb.  

Lägg till eller uppdatera vägar med information från [deklarera vägar](module-composition.md#declare-routes)och välj sedan **nästa** att fortsätta till avsnittet granskning.

### <a name="review-deployment"></a>Granska distribution

Granska avsnittet visas i du JSON-distributionen manifest som har skapats baserat på dina val i de föregående två avsnitt. Observera att det finns två moduler deklarerade att du inte har lagt till: **$edgeAgent** och **$edgeHub**. Dessa två moduler som utgör den [IoT Edge-körningen](iot-edge-runtime.md) och är obligatoriskt standardvärden i varje distribution. 

Granska information om din distribution, och välj sedan **skicka**. 

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat modulerna till din enhet kan du visa dem i alla de **enhetsinformation** i portalen. Den här sidan visar namnet på varje distribuerade modulen, samt användbar information som distribution status och avsluta kod. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)
