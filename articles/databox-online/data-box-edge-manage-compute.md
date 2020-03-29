---
title: Beräkningshantering för Azure Data Box Edge | Microsoft-dokument
description: Beskriver hur du hanterar Edge-beräkningsinställningarna, till exempel utlösare, moduler, visa beräkningskonfiguration, ta bort konfiguration via Azure-portalen på din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: a9daf1d59b03d283be999aaab559c6d60f6405dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65953121"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Hantera beräkning på din Azure Data Box Edge

I den här artikeln beskrivs hur du hanterar beräkning på din Azure Data Box Edge. Du kan hantera beräkningen via Azure-portalen eller via det lokala webbgränssnittet. Använd Azure-portalen för att hantera moduler, utlösare och beräkningskonfiguration och det lokala webbgränssnittet för att hantera beräkningsinställningar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hantera utlösare
> * Hantera beräkningskonfiguration


## <a name="manage-triggers"></a>Hantera utlösare

Händelser är saker som händer i din molnmiljö eller på din enhet som du kanske vill vidta åtgärder för. När en fil till exempel skapas i en resurs är det en händelse. Triggers höjer händelserna. För din Data Box Edge kan utlösare vara ett svar på filhändelser eller ett schema.

- **Fil:** Dessa utlösare är ett svar på filhändelser som att skapa en fil, modifiering av en fil.
- **Schemalagda**: Dessa utlösare är ett svar på ett schema som du kan definiera med ett startdatum, starttid och upprepningsintervallet.


### <a name="add-a-trigger"></a>Lägga till en utlösare

Gör följande i Azure-portalen för att skapa en utlösare.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Edge-beräkning >-utlösare**. Välj **+ Lägg till utlösare** i kommandofältet.

    ![Välj lägg till utlösare](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Ange ett unikt namn för utlösaren i **Lägg till utlösarblad.**
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Välj en **typ** för utlösaren. Välj **Arkiv** när utlösaren är ett svar på en filhändelse. Välj **Schemalagt** när du vill att utlösaren ska starta vid en bestämd tidpunkt och köras med ett angivet upprepningsintervall. Beroende på ditt val visas en annan uppsättning alternativ.

    - **Filutlösaren** - Välj i listrutan en monterad resurs. När en filhändelse utlöses i den här resursen anropar utlösaren en Azure-funktion.

        ![Lägga till SMB-resurs](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Schemalagd utlösare** - Ange startdatum/-tid och upprepningsintervallet i timmar, minuter eller sekunder. Ange också namnet på ett ämne. Ett ämne ger dig flexibiliteten att dirigera utlösaren till en modul som distribueras på enheten.

        En exempelruttsträng `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`är: .

        ![Lägga till NFS-resurs](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Välj **Lägg till** om du vill skapa utlösaren. Ett meddelande visar att skapandet av utlösaren pågår. När utlösaren har skapats uppdateras bladet så att det återspeglar den nya utlösaren.
 
    ![Uppdaterad utlösarlista](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Ta bort en utlösare

Gör följande i Azure-portalen för att ta bort en utlösare.

1. Välj den utlösare som du vill ta bort i listan över utlösare.

    ![Välj utlösare](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Högerklicka och välj sedan **Ta bort**.

    ![Välj ta bort](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

    ![Bekräfta borttagning](media/data-box-edge-manage-compute/add-trigger-1.png)

Listan över utlösare uppdateras för att återspegla borttagningen.

## <a name="manage-compute-configuration"></a>Hantera beräkningskonfiguration

Använd Azure-portalen för att visa beräkningskonfigurationen, ta bort en befintlig beräkningskonfiguration eller uppdatera beräkningskonfigurationen för att synkronisera åtkomstnycklar för IoT-enheten och IoT Edge-enheten för din Data Box Edge.

### <a name="view-compute-configuration"></a>Visa beräkningskonfiguration

Gör följande i Azure-portalen för att visa beräkningskonfigurationen för din enhet.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Edge-beräkning > moduler**. Välj **Visa beräkning** i kommandofältet.

    ![Välj visa beräkning](media/data-box-edge-manage-compute/view-compute-1.png)

2. Anteckna beräkningskonfigurationen på enheten. När du konfigurerade beräkning skapade du en IoT Hub-resurs. Under den IoT Hub-resursen konfigureras en IoT-enhet och en IoT Edge-enhet. Endast Linux-modulerna stöds för att köras på IoT Edge-enheten.

    ![Visa konfiguration](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Ta bort beräkningskonfiguration

Gör följande i Azure-portalen för att ta bort den befintliga Edge-beräkningskonfigurationen för din enhet.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Edge-beräkning > Komma igång**. Välj **Ta bort beräkning** i kommandofältet.

    ![Välj Ta bort beräkning](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Om du tar bort beräkningskonfigurationen måste du konfigurera om enheten om du behöver använda beräkning igen. När du uppmanas att bekräfta väljer du **Ja**.

    ![Välj Ta bort beräkning](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synkronisera upp IoT-enhet och IoT Edge-enhetsåtkomsttangenter

När du konfigurerar beräkning på din Data Box Edge skapas en IoT-enhet och en IoT Edge-enhet. Dessa enheter tilldelas automatiskt symmetriska åtkomstnycklar. Som en säkerhetspraxis roteras dessa nycklar regelbundet via IoT Hub-tjänsten.

Om du vill rotera dessa tangenter kan du gå till tjänsten IoT Hub som du har skapat och välja IoT-enheten eller IoT Edge-enheten. Varje enhet har en primär åtkomstnyckel och en sekundär åtkomstnycklar. Tilldela den primära åtkomstnyckeln till den sekundära åtkomstnyckeln och återskapa sedan den primära åtkomstnyckeln.

Om knapparna för IoT-enheten och IoT Edge-enheten har roterats måste du uppdatera konfigurationen på databoxens kant för att få de senaste åtkomstnycklarna. Synkroniseringen hjälper enheten att få de senaste nycklarna för din IoT-enhet och IoT Edge-enhet. Data Box Edge använder bara de primära åtkomstnycklarna.

Gör följande i Azure-portalen för att synkronisera åtkomstnycklarna för din enhet.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Edge-beräkning > Komma igång**. Välj **Uppdatera konfiguration** i kommandofältet.

    ![Välj uppdateringskonfiguration](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Välj **Ja** när du uppmanas att bekräfta.

     ![Välj Ja när du uppmanas att göra det](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. Avsluta dialogrutan när synkroniseringen är klar.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar Edge-beräkningsnätverk via Azure Portal](data-box-edge-extend-compute-access-modules.md).
