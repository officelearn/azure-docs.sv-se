---
title: Hantera enheterna i ditt Azure IoT Central-program | Microsoft-dokument
description: Som operatör kan du lära dig hur du hanterar enheter i ditt Azure IoT Central-program.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157950"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Hantera enheter i ditt Azure IoT Central-program



I den här artikeln beskrivs hur du som operatör hanterar enheter i ditt Azure IoT Central-program. Som operatör kan du:

- Använd sidan **Enheter** för att visa, lägga till och ta bort enheter som är anslutna till ditt Azure IoT Central-program.
- Underhåll en uppdaterad inventering av dina enheter.
- Håll enhetens metadata uppdaterade genom att ändra de värden som lagras i enhetsegenskaperna från dina vyer.
- Kontrollera enheternas beteende genom att uppdatera en inställning på en viss enhet från dina vyer.

## <a name="view-your-devices"></a>Visa dina enheter

Så här visar du en enskild enhet:

1. Välj **Enheter** i den vänstra rutan. Här visas en lista över alla enheter och enhetsmallar.

1. Välj en enhetsmall.

1. I den högra rutan på sidan **Enheter** visas en lista över enheter som skapats från enhetsmallen. Välj en enskild enhet för att se enhetsinformationssidan för den enheten:

    ![Sida om enhetsinformation](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Lägg till en enhet

Så här lägger du till en enhet i ditt Azure IoT Central-program:

1. Välj **Enheter** i den vänstra rutan.

1. Välj den enhetsmall som du vill skapa en enhet från.

1. Välj + **Nytt**.

1. Aktivera **eller** **inaktivera**den simulerade **växlingsknappen** . En riktig enhet är för en fysisk enhet som du ansluter till ditt Azure IoT Central-program. En simulerad enhet har exempeldata som genereras för dig av Azure IoT Central.

1. Klicka på **Skapa**.

1. Den här enheten visas nu i enhetslistan för den här mallen. Välj den enhet som ska visas på sidan enhetsinformation som innehåller alla vyer för enheten.

## <a name="import-devices"></a>Importera enheter

Om du vill ansluta ett stort antal enheter till ditt program kan du massimport importera enheter från en CSV-fil. CSV-filen bör ha följande kolumner och rubriker:

* **IOTC_DeviceID** - enhets-ID bör vara alla gemener.
* **IOTC_DeviceName** - den här kolumnen är valfri.

Så här massregistrerar du enheter i ditt program:

1. Välj **Enheter** i den vänstra rutan.

1. På den vänstra panelen väljer du den enhetsmall som du vill massskapa enheterna för.

    > [!NOTE]
    > Om du inte har någon enhetsmall ännu kan du importera enheter under **Alla enheter** och registrera dem utan mall. När enheter har importerats kan du sedan migrera dem till en mall.

1. Välj **Importera**.

    ![Importera åtgärd](./media/howto-manage-devices/bulkimport1a.png)


1. Markera den CSV-fil som innehåller listan över enhets-ID:n som ska importeras.

1. Enhetsimporten startar när filen har överförts. Du kan spåra importstatusen på panelen Enhetsoperationer. Den här panelen visas automatiskt när importen startar eller så kan du komma åt den via klockikonen i det övre högra hörnet.

1. När importen är klar visas ett meddelande om lyckade funktioner på panelen Enhetsoperationer.

    ![Importen har framgångsrikt](./media/howto-manage-devices/bulkimport3a.png)


Om enhetsimporten misslyckas visas ett felmeddelande på enhetsfunktionspanelen. En loggfil som fångar alla fel genereras som du kan hämta.

**Migrera enheter till en mall**

Om du registrerar enheter genom att starta importen under **Alla enheter**skapas enheterna utan någon enhetsmallsasociation. Enheter måste associeras med en mall för att utforska data och annan information om enheten. Så här associerar du enheter med en mall:

1. Välj **Enheter** i den vänstra rutan.

1. På den vänstra panelen väljer du **Alla enheter:**

    ![Oassocierade enheter](./media/howto-manage-devices/unassociateddevices1a.png)


1. Använd filtret på rutnätet för att avgöra om värdet i kolumnen **Enhetsmall** är "Ej associerad" för någon av dina enheter.

1. Markera de enheter som du vill associera med en mall:

1. Välj **Migrera:**

    ![Associera enheter](./media/howto-manage-devices/unassociateddevices2a.png)


1. Välj mallen i listan över tillgängliga mallar och välj **Migrera**.

1. De valda enheterna är associerade med den enhetsmall du valde.


## <a name="export-devices"></a>Exportera enheter

Om du vill ansluta en riktig enhet till IoT Central behöver du dess anslutningssträng. Du kan exportera enhetsinformation i grupp för att få den information du behöver för att skapa enhetsanslutningssträngar. Exportprocessen skapar en CSV-fil med enhetens identitet, enhetsnamn och nycklar för alla valda enheter.

Så här massexporterar enheter från ditt program:

1. Välj **Enheter** i den vänstra rutan.

1. Välj den enhetsmall som du vill exportera enheterna från i den vänstra rutan.

1. Markera de enheter som du vill exportera och välj sedan åtgärden **Exportera.**

    ![Exportera](./media/howto-manage-devices/export1a.png)


1. Exportprocessen startar. Du kan spåra statusen med hjälp av panelen Enhetsoperationer.

1. När exporten är klar visas ett meddelande om lyckade resultat tillsammans med en länk för att hämta den genererade filen.

1. Välj länken **Hämta fil** om du vill hämta filen till en lokal mapp på disken.

    ![Exportera framgång](./media/howto-manage-devices/export2a.png)


1. Den exporterade CSV-filen innehåller följande kolumner: enhets-ID, enhetsnamn, enhetsnycklar och X509-certifikattumavtryck:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Mer information om anslutningssträngar och anslutning av riktiga enheter till ditt IoT Central-program finns [i Enhetsanslutning i Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Ta bort en enhet

Så här tar du bort en verklig eller simulerad enhet från ditt Azure IoT Central-program:

1. Välj **Enheter** i den vänstra rutan.

1. Välj enhetsmallen för den enhet som du vill ta bort.

1. Använd filterverktygen för att filtrera och söka efter dina enheter. Markera rutan bredvid de enheter som ska tas bort.

1. Välj **Ta bort**. Du kan spåra status för den här borttagningen på enhetens driftpanel.

## <a name="change-a-property"></a>Ändra en egenskap

Molnegenskaper är enhetsmetadata som är associerade med enheten, till exempel ort och serienummer. Skrivbara egenskaper styr beteendet för en enhet. Med andra ord gör de att du kan ge indata till din enhet.  Enhetsegenskaperna ställs in av enheten och är skrivskyddade inom IoT Central. Du kan visa och uppdatera egenskaper i **enhetsinformationsvyerna** för enheten.

1. Välj **Enheter** i den vänstra rutan.

1. Välj enhetsmallen för den enhet vars egenskaper du vill ändra och välj målenheten.

1. Välj den vy som innehåller egenskaper för enheten, med den här vyn kan du mata in värden och välja **Spara** högst upp på sidan. Här ser du vilka egenskaper enheten har och deras aktuella värden. Molnegenskaper och skrivbara egenskaper har redigerbara fält, medan enhetsegenskaper är skrivskyddade. För skrivbara egenskaper kan du se deras synkroniseringsstatus längst ned i fältet. 

1. Ändra egenskaperna till de värden du behöver. Du kan ändra flera egenskaper åt gången och uppdatera dem alla samtidigt.

1. Välj **Spara**. Om du har sparat skrivbara egenskaper skickas värdena till enheten. När enheten bekräftar ändringen för egenskapen skrivbar återgår statusen till **synkroniserad**. Om du har sparat en molnegenskap uppdateras värdet.


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar enheter i ditt Azure IoT Central-program, här är det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Så här använder du enhetsgrupper](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
