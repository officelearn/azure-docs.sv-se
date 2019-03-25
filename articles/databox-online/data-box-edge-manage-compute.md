---
title: Azure Data Box Edge beräkningshantering | Microsoft Docs
description: Beskriver hur du hanterar inställningar för Edge-beräkning som utlösare, moduler, visa beräkning konfiguration, ta bort konfiguration via Azure portal på din Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/24/2019
ms.author: alkohli
ms.openlocfilehash: de8ddd0d2886cd7798160e830094b295c62e17e9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400684"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Hantera beräkning på Azure Data Box-Edge

Den här artikeln beskriver hur du hanterar databearbetning på din Azure Data Box Edge. Du kan hantera beräkning via Azure portal eller via lokalt webbgränssnitt. Använda Azure-portalen för att hantera moduler, utlösare, och konfiguration och det lokala webbgränssnittet att hantera inställningar för beräkning.

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen.


I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hantera utlösare
> * Hantera beräkningskonfigurationen


## <a name="manage-triggers"></a>Hantera utlösare

Händelser är saker som händer i molnmiljön eller på en enhet som du kanske vill vidta åtgärder för. Det är till exempel en händelse när en fil skapas i en resurs. Utlösare är svar på dessa händelser. Utlösare kan utföra en funktion när händelsen utlöses. Utlösare för din Data Box-Edge kan vara som svar på filhändelser eller ett schema.

- **Filen**: Dessa utlösare är som svar på händelser för filen, till exempel skapar en fil, ändring av en fil.
- **Schemalagd**: Dessa utlösare är reaktion på ett schema som du kan definiera med ett startdatum och starttid upprepningsintervallet.


### <a name="add-a-trigger"></a>Lägg till en utlösare

Vidta följande steg i Azure portal för att skapa en utlösare.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Edge-beräkning > utlösaren**. Välj **+ Lägg till utlösare** i kommandofältet.

    ![Välj Lägg till utlösare](media/data-box-edge-manage-compute/add-trigger-1.png)

2. I **Lägg till utlösare** bladet, ange ett unikt namn för utlösaren.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Välj en **typ** för utlösaren. Välj **filen** när utlösaren är som svar på en händelse i filen. Välj **schemalagd** när du vill att utlösaren ska starta vid angiven tidpunkt och köra med ett specifikt Upprepa intervall. En annan uppsättning alternativ visas beroende på ditt val.

    - **Filen utlösaren** – Välj i listrutan en ansluten resurs. När en fil händelse skickas i den här resursen, skulle utlösaren anropa en Azure-funktion.

        ![Lägga till SMB-resurs](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Schemalagda utlösaren** – ange den startdatum /-tid och upprepningsintervallet i timmar, minuter och sekunder. Dessutom anger du namnet för ett ämne. Ett ämne ger dig flexibiliteten att dirigera utlösaren till en modul som distribuerats på enheten.

        Ett exempel route-strängen är: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`.

        ![Lägga till NFS-resurs](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Välj **Lägg till** att skapa utlösaren. Ett meddelande visar att skapa utlösaren pågår. När utlösaren har skapats, uppdaterar bladet för att återspegla den nya utlösaren.
 
    ![Uppdaterade utlösaren lista](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Ta bort en utlösare

Vidta följande steg i Azure portal för att ta bort en utlösare.

1. Välj utlösaren som du vill ta bort från listan över utlösare.

    ![Välj utlösare](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Högerklicka och välj sedan **ta bort**.

    ![Välj Ta bort](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Klicka på **Ja** när du uppmanas att bekräfta åtgärden.

    ![Bekräfta borttagning](media/data-box-edge-manage-compute/add-trigger-1.png)

Listan över utlösare uppdateringar borttagningen.

## <a name="manage-compute-configuration"></a>Hantera beräkningskonfigurationen

Använd Azure-portalen att visa att beräkningskonfigurationen, ta bort en befintlig beräknings-konfiguration eller uppdatera beräkningskonfigurationen att synkronisera åtkomstnycklarna för IoT-enheter och IoT Edge-enhet för din Data Box-Edge.

### <a name="view-compute-configuration"></a>Visa beräkning konfiguration

Vidta följande steg i Azure portal för att visa att beräkningskonfigurationen för din enhet.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Edge-beräkning > moduler**. Välj **visa beräkning** i kommandofältet.

    ![Välj Visa beräkning](media/data-box-edge-manage-compute/view-compute-1.png)

2. Anteckna att beräkningskonfigurationen på din enhet. När du har konfigurerat beräkning, skapade du en IoT Hub-resurs. En IoT-enhet och en IoT Edge-enhet har konfigurerats under den IoT Hub-resursen. Endast Linux-moduler stöds för att köras på IoT Edge-enhet.

    ![Visa konfiguration](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Ta bort beräkningskonfigurationen

Vidta följande steg i Azure portal för att ta bort den befintliga Edge beräkningskonfigurationen för din enhet.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Edge-beräkning > Kom igång**. Välj **ta bort beräkning** i kommandofältet.

    ![Välj Ta bort beräkning](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Om du tar bort beräkningskonfigurationen av behöver du konfigurera om din enhet om du vill använda beräkning igen. När du uppmanas att bekräfta väljer **Ja**.

    ![Välj Ta bort beräkning](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synkronisera IoT-enheter och IoT Edge-enhet åtkomstnycklar

När du konfigurerar beräkning på din Data Box-Edge, skapas en IoT-enhet och en IoT Edge-enhet. Dessa enheter tilldelas automatiskt symmetriska åtkomstnycklar. Som en säkerhetsåtgärd nycklarna roteras regelbundet via IoT Hub-tjänsten.

För att rotera nycklarna kan du gå till IoT Hub-tjänsten som du skapade och välja IoT-enhet eller IoT Edge-enhet. Varje enhet har en primär åtkomstnyckel och en sekundär åtkomstnycklar. Tilldela den primära åtkomstnyckeln till den sekundära åtkomstnyckeln och sedan återskapa den primära åtkomstnyckeln.

Om dina IoT-enheter och IoT Edge enhetsnycklar har roterats, måste du uppdatera konfigurationen på din Data Box Edge för att hämta de senaste åtkomstnycklarna. Synkroniseringen kan enheten Hämta de senaste nycklarna för dina IoT-enheter och IoT Edge-enhet. Data Box Edge använder bara de primära åtkomstnycklarna.

Vidta följande steg i Azure portal för att synkronisera åtkomstnycklarna för din enhet.

1. Gå till din Data Box Edge-resurs i Azure-portalen och gå sedan till **Edge-beräkning > Kom igång**. Välj **uppdatering configuration** i kommandofältet.

    ![Välj uppdatering-konfiguration](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Välj **Ja** när du uppmanas att bekräfta.

     ![Välj Ja om du uppmanas att göra](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. Avsluta dialogrutan när synkroniseringen är klar.

## <a name="enable-a-network-interface-for-compute"></a>Aktivera ett nätverksgränssnitt för beräkning

Du kan behöva åtkomst till en modul som körs på din Data Box Edge-enhet. För att komma åt modulen externt, behöver du tilldela en IP-adress till ett nätverksgränssnitt på din enhet. Du kan hantera dessa compute inställningar från din lokala webbgränssnittet.

Gör följande i din lokala webbgränssnitt att konfigurera inställningarna för beräkning.

1. I det lokala webbgränssnittet går du till **Configuration > Compute inställningar**.  

2. **Aktivera** nätverksgränssnittet som du vill använda för att ansluta till compute-moduler på enheten. 

    - Om du använder statiska IP-adresser anger du en IP-adress för nätverksgränssnittet.
    - Om du använder DHCP, ska sedan IP-adresser tilldelas automatiskt.

3. Välj **tillämpa** att tillämpa inställningarna.

    ![Aktivera inställningarna för beräkning](media/data-box-edge-manage-compute/compute-settings-1.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar användare via Azure-portalen](data-box-edge-manage-users.md).
