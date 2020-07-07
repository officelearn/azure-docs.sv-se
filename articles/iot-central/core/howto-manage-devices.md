---
title: Hantera enheterna i ditt Azure IoT Central-program | Microsoft Docs
description: Som operatör lär du dig hur du hanterar enheter i ditt Azure IoT Central-program.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80157950"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Hantera enheter i ditt Azure IoT Central-program



Den här artikeln beskriver hur, som en operatör, för att hantera enheter i ditt Azure IoT Central-program. Som operatör kan du:

- Använd sidan **enheter** för att visa, lägga till och ta bort enheter som är anslutna till ditt Azure IoT Central-program.
- Underhåll en uppdaterad inventering av dina enheter.
- Håll din enhets metadata uppdaterad genom att ändra värdena som lagras i enhets egenskaperna från dina vyer.
- Styr hur enheterna fungerar genom att uppdatera en inställning på en enskild enhet från dina vyer.

## <a name="view-your-devices"></a>Visa dina enheter

Så här visar du en enskild enhet:

1. Välj **enheter** i den vänstra rutan. Här visas en lista över alla enheter och dina enhets mallar.

1. Välj en enhets mall.

1. I den högra rutan på sidan **enheter** visas en lista över enheter som har skapats från den enhets mal len. Välj en enskild enhet för att Visa enhets informations sidan för enheten:

    ![Sidan enhets information](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Lägg till en enhet

Så här lägger du till en enhet till ditt Azure IoT Central-program:

1. Välj **enheter** i den vänstra rutan.

1. Välj den enhets mall som du vill skapa en enhet från.

1. Välj + **ny**.

1. Aktivera **eller** **inaktivera**den **simulerade** växlingen. En riktig enhet är för en fysisk enhet som du ansluter till ditt Azure IoT Central-program. En simulerad enhet innehåller exempel data som genererats av Azure IoT Central.

1. Klicka på **Skapa**.

1. Enheten visas nu i enhets listan för den här mallen. Välj enheten om du vill se sidan med enhets information som innehåller alla vyer för enheten.

## <a name="import-devices"></a>Importera enheter

Om du vill ansluta ett stort antal enheter till ditt program kan du Mass importera enheter från en CSV-fil. CSV-filen ska ha följande kolumner och rubriker:

* **IOTC_DeviceID** – enhets-ID: t bör vara i gemener.
* **IOTC_DeviceName** – den här kolumnen är valfri.

För att registrera enheter i ditt program:

1. Välj **enheter** i den vänstra rutan.

1. På den vänstra panelen väljer du den enhets mall som du vill använda för att skapa enheterna.

    > [!NOTE]
    > Om du inte har någon enhets mall än kan du importera enheter under **alla enheter** och registrera dem utan mall. När enheterna har importer ATS kan du migrera dem till en mall.

1. Välj **Importera**.

    ![Importera åtgärd](./media/howto-manage-devices/bulkimport1a.png)


1. Välj den CSV-fil som innehåller listan med enhets-ID: n som ska importeras.

1. Enhets importen startar när filen har laddats upp. Du kan spåra import statusen i enhets Åtgärds panelen. Den här panelen visas automatiskt när importen startar eller du kan komma åt den via klock ikonen i det övre högra hörnet.

1. När importen är klar visas ett meddelande om att ett meddelande visas i enhetens åtgärds panel.

    ![Importen lyckades](./media/howto-manage-devices/bulkimport3a.png)


Om det inte går att importera enheten visas ett fel meddelande på enhetens åtgärds panel. En loggfil som fångar alla fel genereras som du kan ladda ned.

**Migrera enheter till en mall**

Om du registrerar enheter genom att starta importen under **alla enheter**, skapas enheterna utan någon Association mellan enhets mallar. Enheter måste vara kopplade till en mall för att utforska data och annan information om enheten. Följ dessa steg om du vill associera enheter med en mall:

1. Välj **enheter** i den vänstra rutan.

1. Välj **alla enheter**i den vänstra panelen:

    ![Avassocierade enheter](./media/howto-manage-devices/unassociateddevices1a.png)


1. Använd filtret i rutnätet för att avgöra om värdet i kolumnen **enhets mal len** är "unassocierat" för någon av dina enheter.

1. Välj de enheter som du vill associera med en mall:

1. Välj **migrera**:

    ![Associera enheter](./media/howto-manage-devices/unassociateddevices2a.png)


1. Välj mallen i listan över tillgängliga mallar och välj **migrera**.

1. De valda enheterna är associerade med den enhets mall som du har valt.


## <a name="export-devices"></a>Exportera enheter

Om du vill ansluta en verklig enhet till IoT Central behöver du dess anslutnings sträng. Du kan exportera enhets information i bulk för att få den information du behöver för att skapa anslutnings strängar för enheter. Export processen skapar en CSV-fil med enhets identitet, enhets namn och nycklar för alla valda enheter.

För Mass export av enheter från ditt program:

1. Välj **enheter** i den vänstra rutan.

1. I den vänstra rutan väljer du den enhets mall från vilken du vill exportera enheterna.

1. Välj de enheter som du vill exportera och välj sedan åtgärden **Exportera** .

    ![Exportera](./media/howto-manage-devices/export1a.png)


1. Export processen startar. Du kan spåra statusen med hjälp av panelen enhets åtgärder.

1. När exporten är klar visas ett meddelande med en länk för att ladda ned den genererade filen.

1. Välj länken **Hämta fil** för att ladda ned filen till en lokal mapp på disken.

    ![Exporten lyckades](./media/howto-manage-devices/export2a.png)


1. Den exporterade CSV-filen innehåller följande kolumner: enhets-ID, enhets namn, enhets nycklar och X509-certifikat tumavtrycken:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Mer information om anslutnings strängar och hur du ansluter riktiga enheter till ditt IoT Central program finns i [enhets anslutning i Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Ta bort en enhet

Ta bort en verklig eller simulerad enhet från ditt Azure IoT Central-program:

1. Välj **enheter** i den vänstra rutan.

1. Välj enhets mal len för den enhet som du vill ta bort.

1. Använd filter verktygen för att filtrera och söka efter dina enheter. Markera kryss rutan bredvid de enheter som ska tas bort.

1. Välj **Ta bort**. Du kan spåra statusen för borttagningen i enhets Åtgärds panelen.

## <a name="change-a-property"></a>Ändra en egenskap

Moln egenskaper är enhetens metadata som är kopplade till enheten, till exempel stad och serie nummer. Skrivbara egenskaper styr enhetens beteende. Med andra ord kan du ange indata till din enhet.  Enhets egenskaperna anges av enheten och är skrivskyddade i IoT Central. Du kan visa och uppdatera egenskaper för vyn **enhets information** för din enhet.

1. Välj **enheter** i den vänstra rutan.

1. Välj enhets mal len för enheten vars egenskaper du vill ändra och välj mål enheten.

1. Välj den vy som innehåller egenskaper för enheten. i den här vyn kan du ange värden och välja **Spara** längst upp på sidan. Här ser du de egenskaper som enheten har och deras aktuella värden. Moln egenskaper och skrivbara egenskaper har redigerbara fält, medan enhets egenskaperna är skrivskyddade. För skrivbara egenskaper kan du se statusen för synkronisering längst ned i fältet. 

1. Ändra egenskaperna till de värden du behöver. Du kan ändra flera egenskaper i taget och samtidigt uppdatera alla på samma tidpunkt.

1. Välj **Spara**. Om du har sparat skrivbara egenskaper skickas värdena till din enhet. När enheten bekräftar ändringen av den skrivbara egenskapen återgår statusen tillbaka till **synkroniserad**. Om du har sparat en moln egenskap uppdateras värdet.


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar enheter i ditt Azure IoT Central-program, är här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Använda enhets grupper](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
