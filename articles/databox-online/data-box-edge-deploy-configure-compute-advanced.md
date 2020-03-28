---
title: Självstudiekurs för att filtrera, analysera data för avancerad distribution med beräkning på Azure Data Box Edge | Microsoft-dokument
description: Lär dig hur du konfigurerar beräkningsrollen på Data Box Edge och använder den för att omvandla data för avancerat distributionsflöde innan du skickar till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239044"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Självstudiekurs: Omvandla data med Azure Data Box Edge för avancerat distributionsflöde

Den här självstudien beskriver hur du konfigurerar en beräkningsroll för ett avancerat distributionsflöde på din Azure Data Box Edge-enhet. Efter att du konfigurerat beräkningsrollen så kan Data Box Edge transformera data innan de skickas till Azure.

Beräkning kan konfigureras för enkelt eller avancerat distributionsflöde på din enhet.

|                  | Enkel distribution                                | Avancerad distribution                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Avsedd för     | IT-administratörer                                | Utvecklare                            |
| Typ             | Använda tjänsten Data Box Edge för att distribuera moduler      | Använda IoT Hub-tjänsten för att distribuera moduler |
| Distribuerade moduler | Enkel                                           | Kedjade eller flera moduler           |


Den här proceduren kan ta cirka 20 till 30 minuter att slutföra.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägga till resurser
> * Lägga till en utlösare
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

 
## <a name="prerequisites"></a>Krav

Innan du konfigurerar en beräkningsroll på din Data Box Edge-enhet, ser du till att:

- Du har aktiverat din Data Box Edge-enhet enligt beskrivningen i [Anslut, konfigurera och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurera beräkning

Om du vill konfigurera beräkning på din Data Box Edge skapar du en IoT Hub-resurs.

1. Gå till **Översikt**i Azure-portalen för din Data Box Edge-resurs . Välj **Kom igång**på panelen **Beräkna** i den högra rutan.

    ![Komma igång med beräkning](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. På **beräkningspanelen Konfigurera kant** väljer du **Konfigurera beräkning**.

    ![Komma igång med beräkning](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Mata in följande på **beräkningsbladet Konfigurera kant:**

   
    |Field  |Värde  |
    |---------|---------|
    |IoT Hub     | Välj mellan **Ny** eller **Befintlig**. <br> Som standard används en standardnivå (S1) för att skapa en IoT-resurs. Om du vill använda en kostnadsfri nivå IoT-resurs skapar du en och väljer sedan den befintliga resursen. <br> I varje enskilt fall använder IoT Hub-resursen samma prenumerations- och resursgrupp som används av databoxkantsresursen.     |
    |Namn     |Ange ett namn på din IoT Hub-resurs.         |

    ![Komma igång med beräkning](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Välj **Skapa**. Det tar ett par minuter att skapa IoT Hub-resursen. När IoT Hub-resursen har skapats uppdateras **configuration edge-beräkningspanelen** för att visa beräkningskonfigurationen. Om du vill bekräfta att edge-beräkningsrollen har konfigurerats väljer du **Visa config** på panelen **Konfigurera beräkning.**
    
    ![Komma igång med beräkning](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen. En IoT Edge Runtime körs också på den här IoT Edge-enheten.

    För tillfället är det bara Linux-plattformen tillgänglig för din IoT Edge-enhet.


## <a name="add-shares"></a>Lägga till resurser

För den avancerade distributionen i den här självstudien behöver du två resurser: en Edge-resurs och en annan lokal Edge-resurs.

1. Lägg till en Edge-resurs på enheten genom att göra följande steg:

    1. Gå till **Edge-beräkning > Komma igång i**databoxens edgeresurs.
    2. Välj **Lägg till**på panelen Lägg **till delar.**
    3. Ange resursnamnet och välj resurstypen på bladet **Lägg till resurs.**
    4. Om du vill montera Edge-resursen markerar du kryssrutan **Använd resursen med Edge-beräkning**.
    5. Välj **lagringskonto,** **lagringstjänst**, en befintlig användare och välj sedan **Skapa**.

        ![Lägga till en Edge-resurs](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    När Edge-resursen har skapats får du ett meddelande om att skapa dem har skapats. Aktielistan uppdateras för att återspegla den nya aktien.

2. Lägg till en lokal edge-resurs på Edge-enheten genom att upprepa alla steg i föregående steg och markera kryssrutan konfigurera **som kant lokal resurs**. Data i den lokala resursen finns kvar på enheten.

    ![Lägga till en lokal edge-resurs](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. På bladet **Aktier** visas den uppdaterade listan över resurser.

    ![Uppdaterad lista över resurser](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Om du vill visa egenskaperna för den nyskapade lokala resursen väljer du resursen i listan. I rutan **Lokal monteringspunkt för Edge-beräkningsmoduler** kopierar du det värde som motsvarar den här resursen.

    Du använder den här lokala monteringspunkten när du distribuerar modulen.

    ![Rutan Lokal monteringspunkt för Edge-beräkningsmodul](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Om du vill visa egenskaperna för edge-resursen som du skapade väljer du resursen i listan. I rutan **Lokal monteringspunkt för Edge-beräkningsmoduler** kopierar du det värde som motsvarar den här resursen.

    Du använder den här lokala monteringspunkten när du distribuerar modulen.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Lägga till en utlösare

1. Gå till **Edge-beräkning > utlösare**. Välj **+ Lägg till utlösare**.

    ![Lägg till utlösare](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Mata in följande värden i **bladet Lägg till utlösare.**

    |Field  |Värde  |
    |---------|---------|
    |Utlösarnamn     | Ett unikt namn på din utlösare.         |
    |Utlösartyp     | Välj **Filutlösaren.** En filutlösare utlöses när en filhändelse inträffar, till exempel en fil skrivs till indataresursen. En schemalagd utlösare däremot utlöses baserat på ett schema som definierats av dig. I det här exemplet behöver vi en filutlösare.    |
    |Ingångsandel     | Välj en indataresurs. Den lokala edge-resursen är indataresursen i det här fallet. Modulen som används här flyttar filer från den lokala Edge-resursen till en Edge-resurs där de överförs till molnet.        |

    ![Lägg till utlösare](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Du meddelas när utlösaren har skapats. Listan över utlösare uppdateras för att visa den nyskapade utlösaren. Välj den utlösare som du just skapade.

    ![Lägg till utlösare](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Kopiera och spara exempelvägen. Du kommer att ändra den här exempelvägen och använda den senare i IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Lägg till utlösare](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Lägga till en modul

Det finns inga anpassade moduler på den här Edge-enheten. Du kan lägga till en anpassad eller en färdig modul. Om du vill veta hur du skapar en anpassad modul går du till [Utveckla en C#-modul för din Data Box Edge-enhet](data-box-edge-create-iot-edge-module.md).

I det här avsnittet så lägger du till en anpassad modul till den IoT Edge-enhet som du skapade i [Utveckla en C#-modul för din Data Box Edge](data-box-edge-create-iot-edge-module.md). Den här anpassade modulen tar filer från en lokal Edge-resurs på Edge-enheten och flyttar dem till en Edge-resurs (molnresurs) på enheten. Molnresursen pushar sedan filerna till det Azure-lagringskonto som är associerat med molnresursen.

1. Gå till **Edge-beräkning > Kom igång**. På panelen **Lägg till moduler** väljer du scenariotypen som **avancerad**. Välj **Gå till IoT Hub**.

    ![Välj avancerad distribution](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. I din IoT Hub-resurs går du till **IoT Edge-enheten** och väljer sedan din IoT Edge-enhet.

    ![Gå till IoT Edge-enheten i IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Välj **Ange moduler**på **enhetsinformation**.

    ![Länken Ange moduler](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Under **Lägg till moduler** utför du följande:

    1. Ange namn, adress, användarnamn och lösenord för containerregisterinställningarna för den anpassade modulen.
    Namn, adress och de listade autentiseringsuppgifterna används för att hämta moduler med en matchande URL. Distribuera den här modulen genom att under **Distributionsmoduler** välja **IoT Edge-modul**. Den här IoT Edge-modulen är en dockercontainer som du kan distribuera till den IoT Edge-enhet som är associerad med din Data Box Edge-enhet.

        ![Länken Ange moduler](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Ange inställningarna för den anpassade IoT Edge-modulen. Mata in följande värden.
     
        |Field  |Värde  |
        |---------|---------|
        |Namn     | Ett unikt namn för modulen. Den här modulen är en dockerbehållare som du kan distribuera till IoT Edge-enheten som är associerad med databoxens edge.        |
        |Bild URI     | Bild-URI för motsvarande behållaravbildning för modulen.        |
        |Autentiseringsuppgifter krävs     | Om markerad används användarnamn och lösenord för att hämta moduler med en matchande URL.        |
    
        I rutan **Alternativ för behållar skapa** anger du de lokala monteringspunkterna för Edge-modulerna som du kopierade i föregående steg för den lokala edge-resursen och den lokala edge-resursen.

        > [!IMPORTANT]
        > De sökvägar som används här är monterade i behållaren, så de måste matcha vad funktionerna i behållaren förväntar sig. Om du följer [Skapa en anpassad modul](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)förväntar sig koden som anges i modulen de kopierade sökvägarna. Ändra inte dessa sökvägar.
    
        I rutan **Alternativ för containerskapande** så kan du klistra in följande exempel:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Ange eventuella miljövariabler som används för din modul. Miljövariabler ger valfri information som hjälper till att definiera i vilken miljö din modul körs.

        ![Rutan Alternativ för containerskapande](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Konfigurera vid behov avancerade Edge-körningsinställningar och klicka sedan på **Nästa**.

        ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Under **Ange vägar** anger du vägar mellan moduler.  
    
    ![Ange rutter](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Du kan ersätta *vägen* med följande flödessträng som du kopierade tidigare. I det här exemplet anger du namnet på den lokala resurs som skickar data till molnresursen. Ersätt `modulename` modulen med namnet på modulen. Välj **Nästa**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Avsnittet Ange vägar](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  Under **Granska distributionen** så granskar du alla inställningarna. Om du är nöjd så **skickar** du modulen för distribution.

    ![Länken Ange moduler](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Den här åtgärden startar modulen distribution. När distributionen är klar **körs** **körningsstatus** för modulen .

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Verifiera datatransformning, överföring

Det slutliga steget är att se till att modulen är ansluten och körs som förväntat. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

Gör så här för att verifiera datatransformering och överföring till Azure.
 
1.  I Utforskaren ansluter du till både Edge local- och Edge-resurser som du skapade tidigare.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Lägg till data i den lokala resursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Data flyttas till molnresursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Data pushas sedan från molnresursen till lagringskontot. Om du vill visa data går du till ditt lagringskonto och väljer sedan **Storage Explorer**. Du kan visa uppladdade data i ditt lagringskonto.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Du har slutfört valideringsprocessen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägga till resurser
> * Lägga till en utlösare
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

Läs hur du administrerar din Data Box Edge-enhet i:

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera en Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md)
