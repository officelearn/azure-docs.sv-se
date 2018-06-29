---
title: Distribuera Azure IoT kant-moduler (portal) | Microsoft Docs
description: Använd Azure-portalen för att distribuera moduler till en IoT-enhet
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4082189d451f670c1ae3f76b8ec785d8bd0518b3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036488"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Distribuera Azure IoT kant moduler från Azure-portalen

När du skapar IoT kant moduler med affärslogik som du vill distribuera dem till dina enheter fungerar med gränsen. Om du har flera moduler som arbetar tillsammans för att samla in och bearbeta data kan du distribuera dem på samma gång och deklarera routningsregler som ansluter dem. 

Den här artikeln visar hur Azure-portalen visar hur du skapar en distributionsmanifestet och skicka distributionen till en IoT-enhet. Information om hur du skapar en distribution som riktar sig till flera enheter baserat på deras delade taggar finns [distribuera och övervaka IoT kant moduler i skala](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>Förutsättningar

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration. 
* En [IoT gränsenheten](how-to-register-device-portal.md) med den IoT kant som är installerad. 

## <a name="select-your-device"></a>Välj enhet

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
2. Välj **IoT kant** på menyn.
3. Klicka på ID för målenhet från listan över enheter. 
4. Välj **Ange moduler**.

## <a name="configure-a-deployment-manifest"></a>Konfigurera en distributionsmanifestet

En distributionsmanifestet är en JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modulen twins. Mer information om hur distributionen visar fungerar och hur du skapar dem finns [förstå hur IoT kant moduler kan användas, konfigurerats och återanvändas](module-composition.md).

Azure-portalen har en guide som vägleder dig genom att skapa distributionsmanifestet, i stället för att skapa JSON-dokumentet manuellt. Den har tre steg: **lägga till moduler**, **ange vägar**, och **granska distribution**. 

### <a name="add-modules"></a>Lägg till moduler

1. I den **registerinställningar** avsnitt på sidan Ange autentiseringsuppgifter för att komma åt alla privata behållare register som innehåller modulen-avbildningar. 
2. I den **distribution moduler** avsnitt på sidan Välj **Lägg till**. 
3. Välj typ av modul från den nedrullningsbara listan: 
   * **IoT kant modulen** -alternativet.
   * **Azure Stream Analytics-modulen** -endast moduler som genereras från ett Azure Stream Analytics-arbetsbelastning. 

4. Ange ett namn för modulen och sedan ange avbildningen som behållare. Exempel: 
   * **Namnet** -tempSensor
   * **Bild URI** -mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
5. Fyll i de valfria fälten om det behövs. För mer information om behållaren skapa alternativ för princip för omstart, och önskad status Se [EdgeAgent önskade egenskaper](module-edgeagent-edgehub.md#edgeagent-desired-properties). Läs mer om modulen dubbla [definiera eller uppdatera önskade egenskaper](module-composition.md#define-or-update-desired-properties).
6. Välj **Spara**.
7. Upprepa steg 2 till 6 för att lägga till ytterligare moduler för din distribution. 
8. Välj **nästa** fortsätta till avsnittet vägar.

### <a name="specify-routes"></a>Ange vägar

Som standard i guiden får du en väg kallas **väg** och definierad som **FROM /* i $uppströms **, vilket innebär att alla meddelanden som utdata genom alla moduler som skickas till din IoT-hubb.  

Lägg till eller uppdatera vägar med information från [deklarera vägar](module-composition.md#declare-routes)och välj **nästa** fortsätta till avsnittet granskning.

### <a name="review-deployment"></a>Granska distribution

Granska avsnittet visas i du JSON-distributionen manifest som har skapats utifrån dina val i föregående två avsnitt. Observera att det finns två moduler har deklarerats som du inte lägger till: **$edgeAgent** och **$edgeHub**. Dessa två moduler som utgör den [IoT kant runtime](iot-edge-runtime.md) och nödvändiga standardvärden i varje distribution. 

Granska information om din distribution, och välj sedan **skicka**. 

## <a name="view-modules-on-your-device"></a>Visa moduler på enheten

När du har distribuerat moduler till din enhet kan du visa dem i den **enhetsinformation** i portalen. Den här sidan visar namnet på varje distribuerad modul, samt användbar information som distributionskod status och avsluta. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuera och övervaka IoT kant moduler i skala](how-to-deploy-monitor.md)
