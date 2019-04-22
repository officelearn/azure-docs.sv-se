---
title: Hantera enheter i ditt program med Azure IoT Central | Microsoft Docs
description: Som operatör, Lär dig att hantera enheter i Azure IoT Central programmet.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a723ca6d69d45613636c7659e6566cec19b31d65
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679048"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Hantera enheter i ditt program med Azure IoT Central

Den här artikeln beskrivs hur du som en operatör att hantera enheter i Azure IoT Central programmet. Som en operatör kan du:

- Använd den **Device Explorer** att visa, lägga till och ta bort enheter som är anslutna till Azure IoT Central programmet.
- Upprätthålla en uppdaterad förteckning över dina enheter.
- Håll din enhetsmetadata uppdaterad genom att ändra de värden som lagras i egenskaperna för enheten.
- Styra beteendet för dina enheter genom att uppdatera en inställning i en specifik enhet från den **inställningar** sidan.

## <a name="view-your-devices"></a>Visa dina enheter

Visa en enskild enhet:

1. Välj **Device Explorer** på den vänstra navigeringsmenyn. Här visas en lista över dina [enheten mallar](howto-set-up-template.md).

1. Välj en mall för enheten i den **mallar** lista.

1. I den högra rutan i den **Device Explorer** kan du se en lista över enheter som skapats med mallen för enheten. Välj en enskild enhet att visa sidan med enhetsinformation för enheten:

    ![Enhetens informationssida](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Lägg till en enhet

Lägga till en enhet till Azure IoT Central programmet:

1. Välj **Device Explorer** på den vänstra navigeringsmenyn.

1. Välj mallen enhet som du vill skapa en enhet.

1. Välj + **nya**.

1. Välj **verkliga** eller **simulerade**. Det är en riktig enhet för en fysisk enhet som du ansluter till Azure IoT Central programmet. En simulerad enhet innehåller exempeldata som genererades av Azure IoT Central.

## <a name="import-devices"></a>Importera enheter

Om du vill ansluta stort antal enheter i ditt program, kan du massimportera importera enheter från en CSV-fil. CSV-filen ska innehålla följande kolumner och rubriker:

* **IOTC_DeviceID** -enhets-ID ska skrivas med små bokstäver.
* **IOTC_DeviceName** -den här kolumnen är valfritt.

Massinläsning registrerar enheter i ditt program:

1. Välj **Device Explorer** på den vänstra navigeringsmenyn.

1. Välj enhet mallen som du vill massregistrera skapar enheterna på den vänstra panelen.

    > [!NOTE]
    > Om du inte har en mall för enheten, men du kan sedan importera enheter under **oassocierade enheter** och registrera dem utan en mall. När enheter har importerats, kan du sedan koppla dem till en mall.

1. Välj **Import**.

    ![Importera åtgärd](./media/howto-manage-devices/BulkImport1.png)

1. Välj en CSV-fil med listan över enhets-ID som ska importeras.

1. Import av enheten börjar när filen har laddats upp. Du kan spåra importstatus överst i rutnätet för enheten.

1. När importen är klar visas ett meddelande på enheten rutnätet.

    ![Importresultat](./media/howto-manage-devices/BulkImport3.png)

Om enheten importerar misslyckas, visas ett felmeddelande på enheten rutnätet. En loggfil samla in alla fel genereras som du kan hämta.

**Kopplar enheter till en mall**

Om du registrerar enheter genom att starta importen under **oassocierade enheter**, och sedan enheterna som har skapats utan någon enhetsassociering för mallen. Enheter måste vara associerad med en mall för att utforska data och annan information om enheten. Följ dessa steg om du vill associera enheter med en mall:

1. Välj **Device Explorer** på den vänstra navigeringsmenyn.

1. I den vänstra panelen, väljer **oassocierade enheter**:

    ![Olänkade enheter](./media/howto-manage-devices/UnassociatedDevices1.png)

1. Markera de enheter som du vill associera med en mall:

1. Välj **associera**:

    ![Koppla enheter](./media/howto-manage-devices/UnassociatedDevices2.png)

1. Välj mallen i listan över tillgängliga mallar och välj **associera**.

1. De valda enheterna är associerad med mallen för enheten som du har valt.

> [!NOTE]
> När en enhet har associerats med en mall inte oassocierade eller som är associerade med en annan mall.

## <a name="export-devices"></a>Exportera enheter

Om du vill ansluta en verklig enhet till IoT Central, behöver du sin anslutningssträng. Du kan exportera enhetsinformation gruppvis att få den information du behöver att skapa anslutningssträngar för enheten. Exportprocessen skapar en CSV-fil med enhetens identitet, enhetsnamn och nycklar för alla valda enheter.

Massregistrera enheter för export från ditt program:

1. Välj **Device Explorer** på den vänstra navigeringsmenyn.

1. Välj mallen enhet som du vill exportera enheterna på den vänstra panelen.

1. Markera de enheter som du vill exportera och välj sedan den **exportera** åtgärd.

    ![Exportera](./media/howto-manage-devices/Export1.png)

1. Exportprocessen startar. Du kan spåra status överst i rutnätet.

1. När exporten är klar visas ett meddelande tillsammans med en länk till ladda ned den genererade filen.

1. Välj den **meddelande** att ladda ned filen till en lokal mapp på disken.

    ![Exporten lyckades](./media/howto-manage-devices/Export2.png)

1. Den exporterade CSV-filen innehåller följande kolumner: enhets-ID, enhetsnamn, enhetsnycklar och X509 certifikattumavtryck:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Se [enhetsanslutning i Azure IoT Central](concepts-connectivity.md), mer information om anslutningssträngar och verkliga enheter som ansluter till din IoT Central-program.

## <a name="delete-a-device"></a>Ta bort en enhet

Ta bort antingen en faktiska eller simulerade enhet från Azure IoT Central programmet:

1. Välj **Device Explorer** på navigeringsmenyn.

1. Välj mallen enhet på den enhet som du vill ta bort.

1. Markera kryssrutan bredvid enheten för att ta bort.

1. Välj **ta bort**.

## <a name="change-a-device-setting"></a>Ändra inställningen för en enhet

Inställningar styr beteendet för en enhet. Med andra ord kan du ange indata till din enhet. Du kan visa och uppdatera enhetsinställningarna på den **enhetsinformation** sidan.

1. Välj **Device Explorer** på navigeringsmenyn.

1. Välj mallen enhet på den enhet som du vill ändra inställningarna.

1. Välj den **inställningar** fliken. Här visas alla inställningar som enheten har och deras aktuella värden. Du kan se om enheten synkronisering för varje inställning.

1. Ändra inställningarna till de värden som du behöver. Du kan ändra flera inställningar i taget och uppdatera dem på samma gång.

1. Välj **uppdatering**. Värdena ska skickas till din enhet. När enheten bekräftar ändringen, status för inställningen går tillbaka till **synkroniserats**.

## <a name="change-a-property"></a>Ändra en egenskap

Egenskaperna är enhetens metadata som hör till enheten, till exempel city och serienummer. Du kan visa och uppdatera egenskaperna på den **enhetsinformation** sidan.

1. Välj **Device Explorer** på navigeringsmenyn.

1. Välj mallen enhet på den enhet vars egenskaper du vill ändra.

1. Välj den **egenskaper** fliken där du ser alla egenskaper.

1. Ändra programegenskaper till de värden som du behöver. Du kan ändra flera egenskaper på samma gång och uppdatera dem på samma gång. Välj **uppdatering**.

> [!NOTE]
> Du kan inte ändra värdet för _enhetsegenskaper_. Egenskaper för enhet sätts av enheten och är skrivskyddade i programmet Azure IoT Central.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar enheter i ditt program med Azure IoT Central, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hur du använder Enhetsuppsättningar](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
