---
title: Hantera enheter i din Azure IoT centralt program | Microsoft Docs
description: Som operatör, Lär dig att hantera enheter i din Azure IoT centralt program.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: cf803c03d266f2a400e47fc551dea62936456177
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937626"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Hantera enheter i din Azure IoT centralt program

Den här artikeln beskriver hur som en operatör att hantera enheter i Microsoft Azure IoT Central programmet. Som en operatör kan du:

- Använd den **Explorer** sidan Visa, lägga till och ta bort enheter som är anslutna till din Azure IoT centralt program.
- Upprätthåll en uppdaterad förteckning över dina enheter.
- Hålla enhetens metadata uppdaterad genom att ändra de värden som lagras i egenskaperna för enheten.
- Styr beteendet för dina enheter genom att uppdatera en inställning i en specifik enhet från den **inställningar** sidan.

## <a name="view-your-devices"></a>Visa dina enheter

Visa en enskild enhet:

1. Välj **Explorer** på den vänstra navigeringsmenyn. Här ser du en lista över dina [enheten mallar](howto-set-up-template.md).

1. Välj en **enheten mallen** i det vänstra fönstret.

1. I den högra rutan visas en lista över enheter som har skapats med mallen för enheten. Välj en enskild enhet för att se den **enhetsinformation** sidan för den enheten:

    [![Informationssidan för enheten](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Lägg till en enhet

Lägga till en enhet i Azure IoT centrala programmet:

1. Välj **Explorer** på den vänstra navigeringsmenyn.

1. Välj den mall för enheten som du vill skapa en enhet.

1. Välj + **nya**.

1. Välj **verkliga** eller **simulerade**. En verklig enhet är för en fysisk enhet som du ansluter till dina Azure IoT centralt program. En simulerad enhet har exempeldata som genereras av Azure IoT Central. Det här exemplet används en verklig enhet. Välj **verkliga** att navigera till den **enhetsinformation** för den nya enheten.


## <a name="import-devices"></a>Importera enheter

Om du vill ansluta stort antal enheter i tillämpningsprogrammet Azure IoT centrala bulk erbjudanden importera enheter via en CSV-fil. 

Krav för CSV-filen:
1. CSV-filen ska ha endast en kolumn som innehåller enhets-ID.

1. Filen bör inte ha någon rubrik.


För bulk-registrera enheter i ditt program:

1. Välj **Explorer** på den vänstra navigeringsmenyn.

1. Välj enhet mallen som du vill att Massredigera skapa enheterna på den vänstra panelen.

 >   [!NOTE] 
    Om du inte har en mall för enhet ännu kan du importera enheter under **oassocierade enheter** och registrera dem utan någon mall. När enheter har importerats kan därefter du associera dem med en mall som ett efterföljande steg.

1. Klicka på **Importera**.

    [![Åtgärd för import](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Välj den CSV-fil som innehåller listan över enhets-ID som ska importeras.

1. Importera enheten startar när filen har överförts. Du kan spåra statusen importera överst i rutnätet för enheten.

1. När importen är klar visas ett meddelande på enheten rutnätet.

    [![Importresultat](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Om enheten importerar misslyckas, visas ett felmeddelande visas i rutnätet för enheten. En loggfil som avbildar alla fel genereras och kan hämtas genom att klicka på ett felmeddelande.


**Koppla enheter till en mall**

Om du registrerar enheter genom att starta importen under **oassocierade enheter**, och sedan enheterna som har skapats utan någon mall enhetsassociering. Enheten måste vara kopplad till en mall för att utforska data och annan information om enheten. Följ dessa steg om du vill associera enheter med en mall:
1. Välj **Explorer** på den vänstra navigeringsmenyn.
1. I den vänstra panelen väljer **oassocierade enheter**.
    [![Olänkade enheter](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Markera de enheter som du vill associera med en mall.
1. Klicka på **associera** alternativet.
    [![Koppla enheter](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Välj mallen i listan över tillgängliga mallar och klickar på **associera** knappen.
1. De valda enheterna kommer att flyttas mall för respektive enhet.

 >   [!NOTE] 
    När en enhet har associerats med en mall inte oassocierade eller som är associerade med en annan mall.

## <a name="export-devices"></a>Exportera enheter

För att etablera enheter ansluter till IoT Central måste anslutningssträngen för den enhet som har genererats av IoT Central. Du kan använda exportfunktionen för att hämta anslutningssträngarna och andra egenskaper för enheterna i grupp från ditt program. Exportera skapar en CSV-fil med enhetens identitet, enhetsnamn och primära anslutningssträngen för de valda enheterna.

Att Massredigera export enheter från ditt program:
1. Välj **Explorer** på den vänstra navigeringsmenyn.

1. Välj enhet mallen som du vill exportera enheterna på den vänstra panelen.

1. Välj de enheter som du vill exportera och klicka sedan på den **exportera** åtgärd.

    [![Exportera](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Exportprocessen startar och du kan följa statusen överst i rutnätet. 

1. När exporten har slutförts visas ett meddelande att hämta den genererade filen tillsammans med en länk.

1. Klicka på den **meddelande** att ladda ned filen till en lokal mapp på disken.

    [![Exportera lyckades](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. Den exporterade CSV-filen har följande information:
    1. Namn
    1. Enhets-ID
    1. Primär anslutningssträngen


## <a name="delete-a-device"></a>Ta bort en enhet

Ta bort antingen en verklig eller simulerade enhet från dina Azure IoT centralt program:

1. Välj **Explorer** på navigeringsmenyn.

1. Välj mallen enhet på den enhet som du vill ta bort.

1. Markera kryssrutan bredvid enheten som ska tas bort.

1. Välj **ta bort**.

## <a name="change-a-device-setting"></a>Ändra inställningen för en enhet

Inställningar styr beteendet för en enhet. Med andra ord kan du ange indata till din enhet. Du kan visa och uppdatera inställningar för enheter på den **enhetsinformation** sidan.

1. Välj **Explorer** på navigeringsmenyn.

1. Välj mallen enhet på den enhet som du vill ändra inställningarna.

1. Välj den **inställningar** fliken. Här ser du alla inställningar som enheten har och deras aktuella värden. Du kan se om enheten fortfarande synkroniseringen för varje inställning.

1. Ändra inställningarna för din önskade värden. Du kan ändra inställningar för flera samtidigt och uppdatera dem på samma gång.

1. Välj **uppdatering**. Värden skickas till din enhet. När enheten har bekräftat principinställningens ändring, status för inställningen går tillbaka till **synkroniseras**.

## <a name="change-a-property"></a>Ändra en egenskap

Egenskaperna är enhetsmetadata som associeras med enhet, till exempel city och serienummer. Du kan visa och uppdatera egenskaper på den **enhetsinformation** sidan.

1. Välj **Explorer** på navigeringsmenyn.

1. Välj mallen enhet på den enhet vars egenskaper du vill ändra.

1. Välj den **egenskaper** fliken, där du kan se alla egenskaper.

1. Ändra egenskaperna för din önskade värden. Du kan ändra flera egenskaper på samma gång och uppdatera dem på samma gång.

1. Välj **uppdatering**.

> [!NOTE]
> Du kan inte ändra värdet för _enhetsegenskaper_. Egenskaper för enhet anges av enheten och är skrivskyddade i Azure IoT centrala programmet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar enheter i ditt Azure IoT centrala program, är här det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Hur du använder enheten anger](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
