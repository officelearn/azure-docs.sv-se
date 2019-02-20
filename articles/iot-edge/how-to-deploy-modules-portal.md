---
title: Distribuera moduler från Azure portal – Azure IoT Edge | Microsoft Docs
description: Använd Azure-portalen för att distribuera moduler till en IoT Edge-enhet
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d7729dce5419c5813de3c4dfce55c40098f5988
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430122"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Distribuera Azure IoT Edge-moduler från Azure portal

När du skapar IoT Edge moduler med din affärslogik som du vill distribuera dem till dina enheter att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på en gång och deklarera routningsregler som förenar dem.

Den här artikeln visar hur Azure-portalen visar hur du skapar ett manifest för distribution och skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som är inriktad på flera enheter baserat på deras delade taggar finns i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen.
* En [IoT Edge-enhet](how-to-register-device-portal.md) med IoT Edge-körningen installerad.

## <a name="select-your-device"></a>Välj din enhet

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
1. Välj **IoT Edge** på menyn.
1. Klicka på ID för målenhet i listan över enheter.
1. Välj **Ange moduler**.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Mer information om hur distribution manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Azure-portalen har en guide som visar hur du skapar distributionen manifestet, i stället för att skapa JSON-dokumentet manuellt. Den har tre steg: **Lägg till moduler**, **ange vägar**, och **granska distribution**.

### <a name="add-modules"></a>Lägg till moduler

1. I den **registerinställningar** avsnitt på sidan Ange autentiseringsuppgifter för att komma åt alla privata behållarregister som innehåller dina avbildningar för modulen.

1. I den **distribution moduler** på sidan, väljer **Lägg till**.

1. Titta på typerna av moduler från den nedrullningsbara listan:

   * **IoT Edge-modul** -alternativet.
   * **Azure Stream Analytics-modulen** -endast moduler som har genererats från en Azure Stream Analytics-arbetsbelastning.
   * **Azure Machine Learning-modulen** -endast modellera bilder som skapas från en Azure Machine Learning-arbetsyta.

1. Välj den **IoT Edge-modul**.

1. Ange ett namn för modulen och sedan ange behållaravbildningen. Exempel:

   * **Namn på** -tempSensor
   * **Bild-URI: N** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. Fyll i valfritt fält om det behövs. För mer information om behållare alternativ, omstartsprincip, för att skapa och önskad status finns i [EdgeAgent önskade egenskaper](module-edgeagent-edgehub.md#edgeagent-desired-properties). Läs mer om modultvillingen [definiera eller uppdatera önskade egenskaper](module-composition.md#define-or-update-desired-properties).

1. Välj **Spara**.

1. Upprepa steg 2 till 6 för att lägga till ytterligare moduler för din distribution.

1. Välj **nästa** att fortsätta till avsnittet vägar.

### <a name="specify-routes"></a>Ange vägar

Som standard i guiden får du en väg kallas **väg** och definieras som **FROM /* i $uppströms **, vilket innebär att alla meddelanden som utdata av alla moduler som skickas till din IoT-hubb.  

Lägg till eller uppdatera vägar med information från [deklarera vägar](module-composition.md#declare-routes)och välj sedan **nästa** att fortsätta till avsnittet granskning.

### <a name="review-deployment"></a>Granska distribution

Granska avsnittet visas i du JSON-distributionen manifest som har skapats baserat på dina val i de föregående två avsnitt. Observera att det finns två moduler deklarerade att du inte har lagt till: **$edgeAgent** och **$edgeHub**. Dessa två moduler som utgör den [IoT Edge-körningen](iot-edge-runtime.md) och är obligatoriskt standardvärden i varje distribution.

Granska information om din distribution, och välj sedan **skicka**.

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat modulerna till din enhet kan du visa dem i alla de **enhetsinformation** i portalen. Den här sidan visar namnet på varje distribuerade modulen, samt användbar information som distribution status och avsluta kod.

## <a name="deploy-modules-from-azure-marketplace"></a>Distribuera moduler från Azure Marketplace

Azure Marketplace är en online-program och tjänster där du kan bläddra igenom ett brett utbud av företagsprogram och lösningar som är certifierade och optimerade för körning på Azure, inklusive [IoT Edge-moduler](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure Marketplace kan även nås via Azure-portalen under **skapa en resurs**.

Du kan installera en IoT Edge-modul från Azure Marketplace eller Azure-portalen:

1. Hitta en modul och påbörjar distributionen.

   * Azure-portalen: Hitta en modul och välj **skapa**.

   * Azure Marketplace:

     1. Hitta en modul och välj **Hämta nu**.
     1. Bekräfta leverantörens användningsvillkoren och sekretesspolicyn princip genom att välja **Fortsätt**.

1. Välj din prenumeration och IoT-hubben som målenheten är ansluten.

1. Välj **distribuera till en enhet**.

1. Ange namnet på den enhet eller välj **hitta enhet** att bläddra bland de enheter som registrerats på hubben.

1. Välj **skapa** fortsätta standard processen med att konfigurera ett manifest för distribution, inklusive att lägga till andra moduler om du vill. Information om den nya modulen, till exempel bild URI, användningsområden och önskade egenskaper är fördefinierade men kan ändras.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)
