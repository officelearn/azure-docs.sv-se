---
title: Distribuera moduler från Azure Portal-Azure IoT Edge
description: Använd din IoT Hub i Azure Portal för att skicka en IoT Edge-modul från din IoT Hub till din IoT Edge-enhet enligt konfigurationen i ett distributions manifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510540"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Distribuera Azure IoT Edge-moduler från Azure portal

När du skapar IoT Edge moduler med din affärslogik som du vill distribuera dem till dina enheter att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på en gång och deklarera routningsregler som förenar dem.

Den här artikeln visar hur Azure-portalen visar hur du skapar ett manifest för distribution och skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns i [distribuera och övervaka IoT Edge moduler i stor skala](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Krav

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen.
* En [IoT Edge-enhet](how-to-register-device.md#register-in-the-azure-portal) med IoT Edge-körningen installerad.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Mer information om hur distribution manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Azure-portalen har en guide som visar hur du skapar distributionen manifestet, i stället för att skapa JSON-dokumentet manuellt. Den har tre steg: **Lägg till moduler**, **ange vägar**, och **granska distribution**.

### <a name="select-device-and-add-modules"></a>Välj enhet och Lägg till moduler

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
1. Välj **IoT Edge** på menyn i den vänstra rutan.
1. Klicka på ID för målenhet i listan över enheter.
1. I den övre stapeln väljer du **Ange moduler**.
1. I avsnittet **container Registry inställningar** på sidan anger du autentiseringsuppgifterna för åtkomst till alla privata behållar register som innehåller dina modulblad.
1. I avsnittet **IoT Edge moduler** på sidan väljer du **Lägg till**.
1. Titta på olika typer av moduler på den nedrullningsbara menyn:

   * **IoT Edge modul** – du anger modulens namn och URI för behållar avbildningen. Till exempel är avbildnings-URI: n för SimulatedTemperatureSensor-modulen `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Om modulens avbildning lagras i ett privat behållar register, lägger du till autentiseringsuppgifterna på den här sidan för att få åtkomst till avbildningen.
   * **Marketplace-modul** – moduler som finns på Azure Marketplace. Vissa Marketplace-moduler kräver ytterligare konfiguration, så granska informationen i modulen i listan med [IoT Edge moduler för Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Azure Stream Analytics modul** – moduler som genereras från en Azure Stream Analytics arbets belastning.

1. När du har lagt till en modul väljer du modulnamnet i listan för att öppna inställningarna för modulen. Fyll i valfritt fält om det behövs. För mer information om behållare alternativ, omstartsprincip, för att skapa och önskad status finns i [EdgeAgent önskade egenskaper](module-edgeagent-edgehub.md#edgeagent-desired-properties). Läs mer om modultvillingen [definiera eller uppdatera önskade egenskaper](module-composition.md#define-or-update-desired-properties).
1. Om det behövs upprepar du steg 5 till 8 för att lägga till ytterligare moduler i distributionen.
1. Välj **Nästa: vägar** för att fortsätta till avsnittet vägar.

### <a name="specify-routes"></a>Ange vägar

På fliken **vägar** definierar du hur meddelanden ska skickas mellan moduler och IoT Hub. Meddelanden skapas med hjälp av namn/värde-par. Som standard kallas en väg **väg** och definieras som **från/messages/\* i $upstream**, vilket innebär att alla meddelanden som skickas av alla moduler skickas till IoT-hubben.  

Lägg till eller uppdatera vägarna med information från [deklarera vägar](module-composition.md#declare-routes)och välj sedan **Nästa: granska + skapa** för att fortsätta till nästa steg i guiden.

### <a name="review-deployment"></a>Granska distribution

Granska avsnittet visas i du JSON-distributionen manifest som har skapats baserat på dina val i de föregående två avsnitt. Observera att det finns två moduler deklarerade att du inte har lagt till: **$edgeAgent** och **$edgeHub**. Dessa två moduler som utgör den [IoT Edge-körningen](iot-edge-runtime.md) och är obligatoriskt standardvärden i varje distribution.

Granska distributions informationen och välj sedan **skapa**.

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till din enhet kan du Visa alla på sidan enhets information i din IoT Hub. Den här sidan visar namnet på varje distribuerade modulen, samt användbar information som distribution status och avsluta kod.

## <a name="deploy-modules-from-azure-marketplace"></a>Distribuera moduler från Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) är ett online-program och tjänster för tjänster där du kan bläddra igenom en rad olika företags program och-lösningar som är certifierade och optimerade för att köras på Azure, inklusive [IoT Edge moduler](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

Du kan distribuera en IoT Edge-modul från Azure Marketplace och från din IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Distribuera från Azure Marketplace

Utforska de IoT Edge modulerna på Marketplace och när du hittar den du vill använda kan du distribuera den genom att välja **skapa** eller **Hämta den nu**. Fortsätt med distributions guiden steg som kan variera beroende på vilken IoT Edge-modul du har valt:

1. Bekräfta providerns användnings villkor och sekretess policy genom att välja **Fortsätt**. Du kanske först måste ange kontakt information.
1. Välj din prenumeration och den IoT Hub som mål enheten är ansluten till.
1. Välj **distribuera till en enhet**.
1. Ange namnet på enheten eller Välj **hitta enhet** för att bläddra bland de enheter som är registrerade i hubben.
1. Välj **skapa** för att fortsätta med standard processen att konfigurera ett distributions manifest, inklusive att lägga till andra moduler om du vill. Information om den nya modulen, till exempel bild-URI, skapa alternativ och önskade egenskaper är fördefinierade, men kan ändras.

Kontrol lera att modulen har distribuerats i IoT Hub i Azure Portal. Välj din enhet, Välj **Ange moduler** och modulen ska visas i avsnittet **IoT Edge moduler** .

### <a name="deploy-from-azure-iot-hub"></a>Distribuera från Azure IoT Hub

Du kan snabbt distribuera en modul från Azure Marketplace till din enhet i din IoT Hub i Azure Portal.

1. I Azure Portal navigerar du till IoT Hub.
1. I den vänstra rutan under **Automatisk enhets hantering**väljer du **IoT Edge**.
1. Välj den IoT Edge enhet som ska ta emot distributionen.
1. I den övre stapeln väljer du **Ange moduler**.
1. I avsnittet **IoT Edge moduler** klickar du på **Lägg till**och väljer **Marketplace-modul** på den nedrullningsbara menyn.

![Lägg till modul i IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Välj en modul på sidan **IoT Edge modul Marketplace** . Den modul du väljer konfigureras automatiskt för din prenumeration, resurs grupp och enhet. Den visas sedan i listan med IoT Edge moduler. Vissa moduler kan kräva ytterligare konfiguration.

> [!TIP]
> Informationen om IoT Edge moduler från Azure-IoT Hub är begränsad. Du kan först läsa mer om [IoT Edge-modulerna](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) i Azure Marketplace.

Välj **Nästa: vägar** och fortsätt med distributionen enligt beskrivningen i [Ange vägar](#specify-routes) och [Granska distribution](#review-deployment) tidigare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)
