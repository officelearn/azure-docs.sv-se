---
title: "Använd Azure IoT Hub etablering tjänst för att etablera enheter över belastningen belastningsutjämnade IoT-hubbar | Microsoft Docs"
description: "DP automatisk enhetsetableringen över belastningen belastningsutjämnade IoT-hubbar i Azure Portal"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4842944cd0d980fb7e817165da23b9c3c4037e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Etablera enheter över Utjämning av nätverksbelastning IoT-hubbar

Den här kursen visar hur du etablerar enheter för flera belastningsutjämnade IoT Hub genom att använda enheten etablering Service (DP). I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Använda Azure-portalen för att etablera en andra enhet till en andra IoT-hubb 
> * Lägga till en post för registrering listan andra enhet
> * Anger den DP principen **även distribution**
> * Länka ny IoT-hubben till DP

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Den här kursen bygger på den tidigare [etablera enheter till ett nav](tutorial-provision-device-to-hub.md) kursen.

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Använda Azure-portalen för att etablera en andra enhet till en andra IoT-hubb

Följ stegen i den [etablera enheter till ett nav](tutorial-provision-device-to-hub.md) självstudiekursen för att etablera en andra enhet till en annan IoT-hubben.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Lägga till en post för registrering listan andra enhet

Registrering listan visar DP vilken metod för (metod för att bekräfta en enhetsidentitet) som används med enheten. Nästa steg är att lägga till en registrering lista post för annan enhet. 

1. Klicka på sidan för din dp **hantera registreringar**. Den **Lägg till registrering listpost** visas. 
2. Överst på sidan, klickar du på **Lägg till**.
2. Fyll i fälten och klicka sedan på **spara**.

## <a name="set-the-dps-allocation-policy"></a>Ange att principen på DP

Princip för resursallokering är en DP-inställning som avgör hur enheter har tilldelats en IoT-hubb. Det finns tre resursallokeringsprinciper som stöds: 

1. **Lägsta fördröjningen**: enheter har etablerats till en IoT-hubb som baseras på hubben med den lägsta fördröjningen till enheten.
2. **Jämnt viktas distribution** (standard): länkade IoT-hubbar sannolikt lika har enheter som har etablerats till dem. Det här är standardinställningen. Om du etablerar enheter till endast en IoT-hubb kan du behålla den här inställningen. 
3. **Statisk konfiguration via listan registrering**: specificering av önskade IoT-hubben i listan över registrering har högre prioritet än princip på DP-nivå.

Följ dessa steg om du vill ange princip för resursallokering:

1. Om du vill ange princip för resursallokering i DP-sidan klickar du på **Hantera princip**.
2. Ange allokeringsprincip för **jämnt viktas distribution**.
3. Klicka på **Spara**.

## <a name="link-the-new-iot-hub-to-dps"></a>Länka ny IoT-hubben till DP

Länka DP- och IoT-hubb så att DP kan registrera enheter till att hubben.

1. I den **alla resurser** klickar du på DP som du skapade tidigare.
2. Klicka på DP-sidan **länkade IoT-hubbar**.
3. Klicka på **Lägg till**.
4. I den **Lägg till länk till IoT-hubb** använder alternativknapparna för att ange om länkade IoT-hubben är placerad i den aktuella prenumerationen eller i en annan prenumeration. Välj namnet på IoT-hubben från den **IoT-hubb** rutan.
5. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använda Azure-portalen för att etablera en andra enhet till en andra IoT-hubb 
> * Lägga till en post för registrering listan andra enhet
> * Anger den DP principen **även distribution**
> * Länka ny IoT-hubben till DP

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
