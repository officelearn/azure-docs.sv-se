---
title: Hantera enheter i ditt program med Azure IoT Central | Microsoft Docs
description: Som operatör, Lär dig att hantera enheter i Azure IoT Central programmet.
author: ellenfosborne
ms.author: elfarber
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 21c29d90d8ed149062f24b470603b0e0e03cc6ab
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315613"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Hantera enheter i ditt program med Azure IoT Central

Den här artikeln beskrivs hur du som en operatör att hantera enheter i Azure IoT Central programmet. Som en operatör kan du:

- Använd den **Device Explorer** att visa, lägga till och ta bort enheter som är anslutna till Azure IoT Central programmet.
- Upprätthålla en uppdaterad förteckning över dina enheter.
- Håll din enhetsmetadata uppdaterad genom att ändra de värden som lagras i egenskaperna för enheten.
- Styra beteendet för dina enheter genom att uppdatera en inställning i en specifik enhet från den **inställningar** sidan.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="view-your-devices"></a>Visa dina enheter

Visa en enskild enhet:

1. Välj **Device Explorer** på den vänstra navigeringsmenyn. Här visas en lista över dina [enheten mallar](howto-set-up-template.md).

1. Välj en mall för enhet under mallar i det vänstra fönstret.

1. I rutan till höger på sidan Device Explorer kan du se en lista över enheter som skapats med mallen för enheten som visas nedan. Välj en enskild enhet att visa sidan med enhetsinformation för enheten:

    [![Enhetens informationssida](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Lägg till en enhet

Lägga till en enhet till Azure IoT Central programmet:

1. Välj **Device Explorer** på den vänstra navigeringsmenyn.

1. Välj mallen enhet som du vill skapa en enhet.

1. Välj + **nya**.

1. Välj **verkliga** eller **simulerade**. Det är en riktig enhet för en fysisk enhet som du ansluter till Azure IoT Central programmet. En simulerad enhet innehåller exempeldata som genererades av Azure IoT Central. Det här exemplet används en riktig enhet. Välj **verkliga** att navigera till den **enhetsinformation** för den nya enheten.


## <a name="import-devices"></a>Importera enheter

Om du vill ansluta stort antal enheter i ditt program, Azure IoT Central massetablerings erbjuder-importera enheter via en CSV-fil. CSV-filen ska innehålla följande kolumner (och rubriker)
1.  IOTC_DeviceID  **<span style="color:Red">(bör vara gemener)</span>**
1.  IOTC_DeviceName (valfritt)


Massinläsning registrerar enheter i ditt program:

1. Välj **Device Explorer** på den vänstra navigeringsmenyn.

1. Välj enhet mallen som du vill massregistrera skapar enheterna på den vänstra panelen.

 >   [!NOTE] 
    Om du inte har en mall för enheten, men du kan sedan importera enheter under **oassocierade enheter** och registrera dem utan någon mall. När enheterna har importerats, kan därefter du associera dem med en mall som ett senare skede.

1. Välj **Import**.

    [![Importera åtgärd](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Välj en CSV-fil med listan över enhets-ID som ska importeras.

1. Import av enheten börjar när filen har laddats upp. Du kan spåra importstatus överst i rutnätet för enheten.

1. När importen är klar visas ett meddelande på enheten rutnätet.

    [![Importresultat](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Om enheten importerar misslyckas, visas ett felmeddelande visas i rutnätet för enheten. En loggfil samla in alla fel genereras och kan hämtas genom att välja ett felmeddelande.


**Kopplar enheter till en mall**

Om du registrerar enheter genom att starta importen under **oassocierade enheter**, och sedan enheterna som har skapats utan någon enhetsassociering för mallen. Enheten måste vara associerad med en mall för att utforska data och annan information om enheten. Följ dessa steg om du vill associera enheter med en mall:
1. Välj **Device Explorer** på den vänstra navigeringsmenyn.
1. I den vänstra panelen, väljer **oassocierade enheter**.
    [![Olänkade enheter](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Välj de enheter som du vill associera med en mall.
1. Välj **associera** alternativet.
    [![Koppla enheter](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Välj mallen i listan över tillgängliga mallar och välj **associera**.
1. De valda enheterna kommer att flyttas under respektive enhet-mall.

 >   [!NOTE] 
    När en enhet har associerats med en mall inte oassocierade eller som är associerade med en annan mall.

## <a name="export-devices"></a>Exportera enheter

För att etablera enheter ansluter till IoT Central, behöver du anslutningssträngen för den enhet som har genererats av IoT Central. Du kan använda funktionen Exportera för att hämta anslutningssträngar och andra egenskaper för enheter i grupp från ditt program. Export skapar en CSV-fil med enhetens identitet, enhetsnamn och primär anslutningssträng för de valda enheterna.

Massregistrera enheter för export från ditt program:
1. Välj **Device Explorer** på den vänstra navigeringsmenyn.

1. Välj mallen enhet som du vill exportera enheterna på den vänstra panelen.

1. Markera de enheter som du vill exportera och välj sedan den **exportera** åtgärd.

    [![Exportera](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Exportprocessen startar och du kan spåra status överst i rutnätet. 

1. När exporten är klar visas ett meddelande tillsammans med en länk till ladda ned den genererade filen.

1. Välj den **meddelande** att ladda ned filen till en lokal mapp på disken.

    [![Exporten lyckades](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. Den exporterade CSV-filen innehåller följande kolumner: **Enhets-Id, enhetsnamn, Priamry/sekundär enhetsnycklar och primära och sekundära certifikat thumbrpints**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY

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

1. Ändra inställningarna till din önskade värden. Du kan ändra inställningarna för flera samtidigt och uppdatera dem på samma gång.

1. Välj **uppdatering**. Värdena ska skickas till din enhet. När enheten om ändringen, status för inställningen går tillbaka till **synkroniserats**.

## <a name="change-a-property"></a>Ändra en egenskap

Egenskaperna är enhetens metadata som hör till enheten, till exempel city och serienummer. Du kan visa och uppdatera egenskaperna på den **enhetsinformation** sidan.

1. Välj **Device Explorer** på navigeringsmenyn.

1. Välj mallen enhet på den enhet vars egenskaper du vill ändra.

1. Välj den **egenskaper** fliken där du ser alla egenskaper.

1. Ändra egenskaper för programmet till din önskade värden. Du kan ändra flera egenskaper på samma gång och uppdatera dem på samma gång. Välj **uppdatering**.

> [!NOTE]
> Du kan inte ändra värdet för _enhetsegenskaper_. Egenskaper för enhet sätts av enheten och är skrivskyddade i programmet Azure IoT Central.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att hantera enheter i Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hur du använder Enhetsuppsättningar](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
