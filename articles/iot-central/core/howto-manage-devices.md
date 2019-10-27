---
title: Hantera enheterna i ditt Azure IoT Central-program | Microsoft Docs
description: Som operatör lär du dig hur du hanterar enheter i ditt Azure IoT Central-program.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c3d07c95592c1c43a070afbbf5091693ce0a6611
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950358"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Hantera enheter i ditt Azure IoT Central-program

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Den här artikeln beskriver hur, som en operatör, för att hantera enheter i ditt Azure IoT Central-program. Som operatör kan du:

- Använd **Device Explorer** sidan om du vill visa, lägga till och ta bort enheter som är anslutna till ditt Azure IoT Central-program.
- Underhåll en uppdaterad inventering av dina enheter.
- Se till att din enhets metadata är uppdaterade genom att ändra värdena som lagras i enhets egenskaperna.
- Styr hur enheterna fungerar genom att uppdatera en inställning på en enskild enhet från sidan **Inställningar** .

## <a name="view-your-devices"></a>Visa dina enheter

Så här visar du en enskild enhet:

1. Välj **Device Explorer** i det vänstra fönstret. Här visas en lista över dina [enhets mallar](howto-set-up-template.md).

1. Välj en enhets mal len i listan **mallar** .

1. I den högra rutan på sidan **Device Explorer** visas en lista över enheter som har skapats från den enhets mal len. Välj en enskild enhet för att Visa enhets informations sidan för enheten:

    ![Sidan enhets information](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Lägg till en enhet

Så här lägger du till en enhet till ditt Azure IoT Central-program:

1. Välj **Device Explorer** i det vänstra fönstret.

1. Välj den enhets mall som du vill skapa en enhet från.

1. Välj + **ny**.

1. Välj **verklig** eller **simulerad**. En riktig enhet är för en fysisk enhet som du ansluter till ditt Azure IoT Central-program. En simulerad enhet innehåller exempel data som genererats av Azure IoT Central.

## <a name="import-devices"></a>Importera enheter

Om du vill ansluta ett stort antal enheter till ditt program kan du Mass importera enheter från en CSV-fil. CSV-filen ska ha följande kolumner och rubriker:

* **IOTC_DeviceID** – enhets-ID: t bör vara i gemener.
* **IOTC_DeviceName** – den här kolumnen är valfri.

För att registrera enheter i ditt program:

1. Välj **Device Explorer** i det vänstra fönstret.

1. På den vänstra panelen väljer du den enhets mall som du vill använda för att skapa enheterna.

    > [!NOTE]
    > Om du inte har någon enhets mall än kan du importera enheter under ej **associerade enheter** och registrera dem utan en mall. När enheterna har importer ATS kan du koppla dem till en mall.

1. Välj **Importera**.

    ![Importera åtgärd](./media/howto-manage-devices/bulkimport1a.png)

1. Välj den CSV-fil som innehåller listan med enhets-ID: n som ska importeras.

1. Enhets importen startar när filen har laddats upp. Du kan spåra import status överst i enhets rutnätet.

1. När importen är klar visas ett meddelande om att det är klart i enhets rutnätet.

    ![Importen lyckades](./media/howto-manage-devices/bulkimport3a.png)

Om det inte går att importera enheten visas ett fel meddelande i enhets rutnätet. En loggfil som fångar alla fel genereras som du kan ladda ned.

**Associera enheter med en mall**

Om du registrerar enheter genom att starta importen under en **associerad**enhet, skapas enheterna utan någon Association mellan enhets mallar. Enheter måste vara kopplade till en mall för att utforska data och annan information om enheten. Följ dessa steg om du vill associera enheter med en mall:

1. Välj **Device Explorer** i det vänstra fönstret.

1. På den vänstra panelen väljer du **anslutna enheter**:

    ![Avassocierade enheter](./media/howto-manage-devices/unassociateddevices1a.png)

1. Välj de enheter som du vill associera med en mall:

1. Välj **associera**:

    ![Associera enheter](./media/howto-manage-devices/unassociateddevices2a.png)

1. Välj mallen i listan över tillgängliga mallar och välj **associera**.

1. De valda enheterna är associerade med den enhets mall som du har valt.

> [!NOTE]
> När en enhet har associerats med en mall går det inte att associera eller associera den med en annan mall.

## <a name="export-devices"></a>Exportera enheter

Om du vill ansluta en verklig enhet till IoT Central behöver du dess anslutnings sträng. Du kan exportera enhets information i bulk för att få den information du behöver för att skapa anslutnings strängar för enheter. Export processen skapar en CSV-fil med enhets identitet, enhets namn och nycklar för alla valda enheter.

För Mass export av enheter från ditt program:

1. Välj **Device Explorer** i det vänstra fönstret.

1. På den vänstra panelen väljer du den enhets mall från vilken du vill exportera enheterna.

1. Välj de enheter som du vill exportera och välj sedan åtgärden **Exportera** .

    ![Exportera](./media/howto-manage-devices/export1a.png)

1. Export processen startar. Du kan spåra status överst i rutnätet.

1. När exporten är klar visas ett meddelande med en länk för att ladda ned den genererade filen.

1. Välj **meddelandet lyckades** för att ladda ned filen till en lokal mapp på disken.

    ![Exporten lyckades](./media/howto-manage-devices/export2a.png)

1. Den exporterade CSV-filen innehåller följande kolumner: enhets-ID, enhets namn, enhets nycklar och X509-certifikat tumavtrycken:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Mer information om anslutnings strängar och hur du ansluter riktiga enheter till ditt IoT Central-program finns i [enhets anslutning i Azure IoT Central](concepts-connectivity.md).

## <a name="delete-a-device"></a>Ta bort en enhet

Ta bort en verklig eller simulerad enhet från ditt Azure IoT Central-program:

1. Välj **Device Explorer** i det vänstra fönstret.

1. Välj enhets mal len för den enhet som du vill ta bort.

1. Markera kryss rutan bredvid den enhet som ska tas bort.

1. Välj **ta bort**.

## <a name="change-a-device-setting"></a>Ändra en enhets inställning

Inställningar styr enhetens beteende. Med andra ord kan du ange indata till din enhet. Du kan visa och uppdatera enhets inställningar på **enhets informations** sidan.

1. Välj **Device Explorer** i det vänstra fönstret.

1. Välj enhets mal len för enheten vars inställningar du vill ändra.

1. Välj fliken **Inställningar** . Här ser du alla inställningar som enheten har och deras aktuella värden. För varje inställning kan du se om enheten fortfarande synkroniseras.

1. Ändra inställningarna till de värden du behöver. Du kan ändra flera inställningar i taget och samtidigt uppdatera alla på samma tidpunkt.

1. Välj **Uppdatera**. Värdena skickas till enheten. När enheten bekräftar inställnings ändringen går statusen för inställningen tillbaka till **synkroniserad**.

## <a name="change-a-property"></a>Ändra en egenskap

Egenskaper är enhetens metadata som är kopplade till enheten, till exempel stad och serie nummer. Du kan visa och uppdatera egenskaper på **enhets informations** sidan.

1. Välj **Device Explorer** i det vänstra fönstret.

1. Välj enhets mal len för enheten vars egenskaper du vill ändra.

1. Välj fliken **Egenskaper** där du ser alla egenskaper.

1. Ändra program egenskaperna till de värden du behöver. Du kan ändra flera egenskaper på samma gång och samtidigt uppdatera alla på samma gång. Välj **Uppdatera**.

> [!NOTE]
> Du kan inte ändra värdet för _enhets egenskaper_. Enhets egenskaper anges av enheten och är skrivskyddade i Azure IoT Central-programmet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar enheter i ditt Azure IoT Central-program, är här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Använda enhets uppsättningar](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
