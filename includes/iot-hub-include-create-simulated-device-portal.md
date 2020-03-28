---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67187965"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Skapa sedan en enhetsidentitet och spara nyckeln för framtida bruk. Den är enhetsidentiteten används av simuleringsprogrammet för att skicka meddelanden till IoT-hubben. Den här funktionen är inte tillgänglig i PowerShell eller när du använder en Azure Resource Manager-mall. Följande steg talar om för dig hur du skapar den simulerade enheten med [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure Portal](https://portal.azure.com) och logga in på ditt Azure-konto.

2. Välj **Resursgrupper** och välj sedan resursgruppen. I den här självstudien används **ContosoResources**.

3. Välj din IoT-hubb i listan över resurser. I självstudien används **ContosoTestHub**. Välj **IoT-enheter** från hubbfönstret.

4. Välj **+ Lägg till**. I rutan Lägg till enhet fyller du i enhets-ID. I den här självstudien används **Contoso-Test-Device**. Lämna nycklarna tomma och markera **Generera nycklar automatiskt**. Kontrollera att **Connect device to IoT hub** (Anslut enhet till IoT-hubb) är aktiverat. Välj **Spara**.

   ![Skärmen för tilläggsenhet](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Nu när den har skapats väljer du enheten för att se de genererade nycklarna. Välj ikonen Kopiera på primärnyckeln och spara den någonstans, till exempel Anteckningar för testfasen av den här självstudien.

   ![Enhetsinformationen, inklusive tangenterna](./media/iot-hub-include-create-simulated-device-portal/device-details.png)