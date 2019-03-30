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
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670990"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Skapa sedan en enhetsidentitet och spara nyckeln för framtida bruk. Den är enhetsidentiteten används av simuleringsprogrammet för att skicka meddelanden till IoT-hubben. Den här funktionen är inte tillgänglig i PowerShell eller när du använder en Azure Resource Manager-mall. Följande information om hur du skapar en simulerad enhet med hjälp av den [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure Portal](https://portal.azure.com) och logga in på ditt Azure-konto.

2. Välj **resursgrupper** och välj sedan din resursgrupp. I den här självstudien används **ContosoResources**.

3. Välj din IoT-hubb i listan över resurser. I självstudien används **ContosoTestHub**. Välj **IoT-enheter** från hubbfönstret.

4. Välj **+ Lägg till**. I rutan Lägg till enhet fyller du i enhets-ID. I den här självstudien används **Contoso-Test-Device**. Lämna nycklarna tomma och markera **Generera nycklar automatiskt**. Kontrollera att **Connect device to IoT hub** (Anslut enhet till IoT-hubb) är aktiverat. Välj **Spara**.

   ![Lägg till enhetens skärm](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Nu när det har skapats, väljer du enheten för att se de genererade nycklarna. Välj Kopiera-ikonen på den primära nyckeln och spara den någonstans, till exempel Anteckningar testfasen av den här självstudiekursen.

   ![Information om enheten, inklusive nycklarna](./media/iot-hub-include-create-simulated-device-portal/device-details.png)