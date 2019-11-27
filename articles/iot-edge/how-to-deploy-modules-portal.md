---
title: Distribuera moduler från Azure portal – Azure IoT Edge | Microsoft Docs
description: Använd Azure-portalen för att distribuera moduler till en IoT Edge-enhet
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6d915145e64a5f1a097f38cf79b19426c3acbaf2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457437"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Distribuera Azure IoT Edge-moduler från Azure portal

När du skapar IoT Edge moduler med din affärslogik som du vill distribuera dem till dina enheter att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på en gång och deklarera routningsregler som förenar dem.

Den här artikeln visar hur Azure-portalen visar hur du skapar ett manifest för distribution och skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns i [distribuera och övervaka IoT Edge moduler i skala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Krav

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.
* En [IoT Edge-enhet](how-to-register-device.md#register-in-the-azure-portal) med IoT Edge runtime installerad.

## <a name="select-your-device"></a>Välj din enhet

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.
1. Välj **IoT Edge** på menyn.
1. Klicka på ID för målenhet i listan över enheter.
1. Välj **Ange moduler**.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Mer information om hur distributions manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge moduler kan användas, konfigureras och återanvändas](module-composition.md).

Azure-portalen har en guide som visar hur du skapar distributionen manifestet, i stället för att skapa JSON-dokumentet manuellt. Det finns tre steg: **Lägg till moduler**, **Ange vägar**och **Granska distribution**.

### <a name="add-modules"></a>Lägg till moduler

1. I avsnittet **container Registry inställningar** på sidan anger du autentiseringsuppgifterna för åtkomst till alla privata behållar register som innehåller dina modulblad.

1. I avsnittet **distributions moduler** på sidan väljer du **Lägg till**.

1. Titta på typerna av moduler från den nedrullningsbara listan:

   * **IoT Edge modul** – standard alternativet.
   * **Azure Stream Analytics modulbaserade** moduler som genereras från en Azure Stream Analytics arbets belastning.
   * **Azure Machine Learning modul** – endast modell avbildningar som genereras från en Azure Machine Learning arbets yta.

1. Välj **IoT Edge-modulen**.

1. Ange ett namn för modulen och sedan ange behållaravbildningen. Till exempel:

   * **Namn** – SimulatedTemperatureSensor
   * **Avbildnings-URI** – MCR.Microsoft.com/azureiotedge-Simulated-temperature-sensor:1.0

1. Fyll i valfritt fält om det behövs. För ytterligare information om behållar skapande alternativ, starta om princip och önskad status, se [EdgeAgent önskade egenskaper](module-edgeagent-edgehub.md#edgeagent-desired-properties). Mer information om modulen finns i [definiera eller uppdatera önskade egenskaper](module-composition.md#define-or-update-desired-properties).

1. Välj **Spara**.

1. Upprepa steg 2 till 6 för att lägga till ytterligare moduler för din distribution.

1. Välj **Nästa** för att fortsätta till avsnittet vägar.

### <a name="specify-routes"></a>Ange vägar

Som standard ger guiden en väg som kallas **väg** och definieras som **från/* till $upstream * *, vilket innebär att alla meddelanden som skickas av alla moduler skickas till din IoT-hubb.  

Lägg till eller uppdatera vägarna med information från [deklarera vägar](module-composition.md#declare-routes), och välj sedan **Nästa** för att fortsätta till gransknings avsnittet.

### <a name="review-deployment"></a>Granska distribution

Granska avsnittet visas i du JSON-distributionen manifest som har skapats baserat på dina val i de föregående två avsnitt. Observera att det finns två moduler som har deklarerats att du inte har lagt till: **$edgeAgent** och **$edgeHub**. Dessa två moduler utgör den [IoT Edge körningen](iot-edge-runtime.md) och måste vara standardvärden i varje distribution.

Granska distributions informationen och välj sedan **Skicka**.

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till din enhet kan du Visa alla på **enhets informations** sidan i portalen. Den här sidan visar namnet på varje distribuerade modulen, samt användbar information som distribution status och avsluta kod.

## <a name="deploy-modules-from-azure-marketplace"></a>Distribuera moduler från Azure Marketplace

Azure Marketplace är ett online-program och tjänster för tjänster där du kan bläddra igenom en rad olika företags program och-lösningar som är certifierade och optimerade för att köras på Azure, inklusive [IoT Edge moduler](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure Marketplace kan också nås via Azure Portal under **skapa en resurs**.

Du kan installera en IoT Edge modul från antingen Azure Marketplace eller Azure Portal:

1. Hitta en modul och påbörja distributions processen.

   * Azure Portal: Leta reda på en modul och välj **skapa**.

   * Azure Marketplace:

     1. Hitta en modul och välj **Hämta den nu**.
     1. Bekräfta providerns användnings villkor och sekretess policy genom att välja **Fortsätt**.

1. Välj din prenumeration och den IoT Hub som mål enheten är ansluten till.

1. Välj **distribuera till en enhet**.

1. Ange namnet på enheten eller Välj **hitta enhet** för att bläddra bland de enheter som är registrerade i hubben.

1. Välj **skapa** för att fortsätta med standard processen att konfigurera ett distributions manifest, inklusive att lägga till andra moduler om du vill. Information om den nya modulen, till exempel bild-URI, skapa alternativ och önskade egenskaper är fördefinierade, men kan ändras.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar och övervakar IoT Edge moduler i stor skala](how-to-deploy-monitor.md)
