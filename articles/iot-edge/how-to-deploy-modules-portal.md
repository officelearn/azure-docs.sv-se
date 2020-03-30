---
title: Distribuera moduler från Azure Portal - Azure IoT Edge
description: Använd din IoT-hubb i Azure-portalen för att skicka en IoT Edge-modul från din IoT Hub till din IoT Edge-enhet, som konfigurerats av ett distributionsmanifest.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271452"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Distribuera Azure IoT Edge-moduler från Azure-portalen

När du har skapat IoT Edge-moduler med din affärslogik vill du distribuera dem till dina enheter för att fungera på gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem alla på en gång och deklarera routningsreglerna som ansluter dem.

Den här artikeln visar hur Azure-portalen guidar dig genom att skapa ett distributionsmanifest och skicka distributionen till en IoT Edge-enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns i [Distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Krav

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.
* En [IoT Edge-enhet](how-to-register-device.md#register-in-the-azure-portal) med IoT Edge-körningen installerad.

## <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan modulerna och önskade egenskaper för modultvillingarna. Mer information om hur distributionsmanifest fungerar och hur du skapar dem finns i [Förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).

Azure-portalen har en guide som hjälper dig att skapa distributionsmanifestet i stället för att skapa JSON-dokumentet manuellt. Den har tre steg: **Lägg till moduler,** **Ange vägar**och Granska **distribution**.

### <a name="select-device-and-add-modules"></a>Välj enhet och lägga till moduler

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
1. Välj **IoT Edge** på menyn i den vänstra rutan.
1. Klicka på målenhetens ID från listan över enheter.
1. Välj **Ange moduler**i det övre fältet .
1. I avsnittet **Inställningar för behållarregister på** sidan anger du autentiseringsuppgifter för att komma åt alla privata behållarregister som innehåller modulavbildningarna.
1. I avsnittet **IoT-kantmoduler** på sidan väljer du **Lägg till**.
1. Titta på vilka typer av moduler från rullgardinsmenyn:

   * **IoT Edge Module** - Du anger modulnamn och behållaravbildning URI. Bild-URI för exempel modulen SimulatedTemperatureSensor är `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`till exempel . Om modulavbildningen lagras i ett privat behållarregister lägger du till autentiseringsuppgifterna på den här sidan för att komma åt avbildningen.
   * **Marketplace-modul** – moduler som finns på Azure Marketplace. Vissa marketplace-moduler kräver ytterligare konfiguration, så granska modulinformationen i azure [Marketplace IoT Edge Modules-listan.](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)
   * **Azure Stream Analytics Module** – moduler som genereras från en Azure Stream Analytics-arbetsbelastning.

1. När du har lagt till en modul väljer du modulnamnet i listan för att öppna modulinställningarna. Fyll i de valfria fälten om det behövs. Mer information om alternativ för att skapa behållare, starta om principen och önskad status finns i [Önskade egenskaper för EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Mer information om modultvillingen finns [i Definiera eller uppdatera önskade egenskaper](module-composition.md#define-or-update-desired-properties).
1. Om det behövs upprepar du steg 5 till 8 för att lägga till ytterligare moduler i distributionen.
1. Välj **Nästa: Rutter** för att fortsätta till flödet.

### <a name="specify-routes"></a>Ange rutter

På fliken **Rutter definierar** du hur meddelanden skickas mellan moduler och IoT Hub. Meddelanden skapas med namn-/värdepar. Som standard kallas en väg **för flöde** och definieras som **FRÅN /messages/\* INTO $upstream**, vilket innebär att alla meddelanden som matas ut av alla moduler skickas till din IoT-hubb.  

Lägg till eller uppdatera flödena med information från [Deklarera vägar](module-composition.md#declare-routes)och välj sedan **Nästa: Granska + skapa** för att fortsätta till nästa steg i guiden.

### <a name="review-deployment"></a>Granska distribution

Granskningsavsnittet visar det JSON-distributionsmanifest som skapades baserat på dina val i de två föregående avsnitten. Observera att det finns två moduler som deklarerats som du inte har lagt till: **$edgeAgent** och **$edgeHub**. Dessa två moduler utgör [IoT Edge-körningen](iot-edge-runtime.md) och krävs standardvärden i varje distribution.

Granska distributionsinformationen och välj sedan **Skapa**.

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till enheten kan du visa dem alla på enhetsinformationssidan i din IoT Hub. På den här sidan visas namnet på varje distribuerad modul samt användbar information som distributionsstatus och avslutningskod.

## <a name="deploy-modules-from-azure-marketplace"></a>Distribuera moduler från Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) är en marknadsplats för onlineprogram och tjänster där du kan bläddra igenom ett brett utbud av företagsprogram och lösningar som är certifierade och optimerade för att köras på Azure, inklusive [IoT Edge-moduler](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

Du kan distribuera en IoT Edge-modul från Azure Marketplace och från din IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Distribuera från Azure Marketplace

Läs IoT Edge-modulerna på marknadsplatsen och när du hittar den du vill kan du distribuera den genom att välja **Skapa** eller **Hämta den nu**. Fortsätt med distributionsguidens steg som kan variera beroende på vilken IoT Edge-modul du valde:

1. Bekräfta leverantörens användarvillkor och sekretesspolicy genom att välja **Fortsätt**. Du kan först behöva ange kontaktuppgifter.
1. Välj din prenumeration och IoT Hub som målenheten är ansluten till.
1. Välj **Distribuera till en enhet**.
1. Ange namnet på enheten eller välj **Sök enhet** för att bläddra bland de enheter som är registrerade med navet.
1. Välj **Skapa** om du vill fortsätta standardprocessen för att konfigurera ett distributionsmanifest, inklusive att lägga till andra moduler om så önskas. Information om den nya modulen, till exempel bild-URI, skapa alternativ och önskade egenskaper, är fördefinierade men kan ändras.

Kontrollera att modulen har distribuerats i din IoT-hubb i Azure-portalen. Välj din enhet, välj **Ange moduler** och modulen ska visas i avsnittet **IoT Edge Modules.**

### <a name="deploy-from-azure-iot-hub"></a>Distribuera från Azure IoT Hub

Du kan snabbt distribuera en modul från Azure Marketplace till din enhet i din IoT-hubb i Azure-portalen.

1. Navigera till din IoT Hub i Azure-portalen.
1. Välj **IoT Edge**under **Automatisk enhetshantering**i den vänstra rutan .
1. Välj den IoT Edge-enhet som ska ta emot distributionen.
1. Välj **Ange moduler**i det övre fältet .
1. Klicka på **Lägg till**i avsnittet **IoT-kantmoduler** och välj **Marketplace-modul** på den nedrullningsbara menyn.

![Lägg till modul i IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Välj en modul på **marketplace-sidan IoT Edge Module** Marketplace. Modulen du väljer konfigureras automatiskt för din prenumeration, resursgrupp och enhet. Den visas sedan i din lista över IoT Edge-moduler. Vissa moduler kan kräva ytterligare konfiguration.

> [!TIP]
> Informationen om IoT Edge-moduler från Azure IoT Hub är begränsad. Du kan först läsa mer om [IoT Edge-modulerna](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) på Azure Marketplace.

Välj **Nästa: Vägar** och fortsätt med distributionen enligt beskrivningen av [Ange vägar](#specify-routes) och granska [distribution](#review-deployment) tidigare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du distribuerar och övervakar IoT Edge-moduler i stor skala](how-to-deploy-monitor.md)
