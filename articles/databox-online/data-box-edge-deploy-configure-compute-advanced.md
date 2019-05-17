---
title: Självstudie för att filtrera och analysera data för avancerad distribution med beräkning på Azure Data Box Edge | Microsoft Docs
description: Lär dig hur du konfigurerar beräkningsrollen på Data Box Edge och använda den för att omvandla data för avancerad distribution flow innan du skickar till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/13/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 836a3f3c17c4cf11ac972b7cc129fb3f83093024
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65793888"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Självstudier: Transformera data med Azure Data Box Edge för avancerad distribution flow

Den här självstudien beskrivs hur du konfigurerar en beräkning roll för en avancerad distribution flow på din Azure Data Box Edge-enhet. Efter att du konfigurerat beräkningsrollen så kan Data Box Edge transformera data innan de skickas till Azure.

Beräkning kan konfigureras för distribution av enkla eller avancerade flow på din enhet.

|                  | Enkel distribution                                | Avancerad distribution                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Avsedd för     | IT-administratörer                                | Utvecklare                            |
| Type             | Använd Data Box Edge-tjänsten för att distribuera moduler      | Använd IoT Hub-tjänsten för att distribuera moduler |
| Modulerna som distribueras | Single                                           | Kedjat eller flera moduler           |


Den här proceduren kan ta upp till 20 till 30 minuter för att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägg till resurser
> * Lägg till en utlösare
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

 
## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du konfigurerar en beräkningsroll på din Data Box Edge-enhet, ser du till att:

- Du har aktiverat din Data Box Edge-enhet enligt beskrivningen i [Anslut, konfigurera och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurera beräkning

Om du vill konfigurera beräkning på din Data Box-Edge, ska du skapa en IoT Hub-resurs.

1. I Azure-portalen för din Data Box Edge-resurs går du till **översikt**. I den högra rutan på den **Compute** panelen, väljer **börjar**.

    ![Kom igång med beräkning](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. På den **konfigurera Edge-beräkning** panelen, väljer **konfigurera beräkna**.

    ![Kom igång med beräkning](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. På den **konfigurera Edge-beräkning** bladet, ange följande:

   
    |Fält  |Value  |
    |---------|---------|
    |IoT Hub     | Välj bland **nya** eller **befintliga**. <br> Som standard används en Standard-nivå (S1) för att skapa en IoT-resurs. Om du vill använda en kostnadsfri nivå IoT-resurs, skapa en och välj sedan den befintliga resursen. <br> I båda fallen använder IoT Hub-resurs i samma prenumeration och resursgrupp som används av Data Box Edge-resursen.     |
    |Namn     |Ange ett namn för din IoT Hub-resurs.         |

    ![Kom igång med beräkning](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Välj **Skapa**. Resursskapande IoT-hubb tar ett par minuter. När IoT Hub-resursen har skapats kan den **konfigurera Edge-beräkning** panelen uppdateringar att visa att beräkningskonfigurationen. För att bekräfta att rollen Edge-beräkning har konfigurerats, Välj **visa config** på den **konfigurera beräkna** panelen.
    
    ![Kom igång med beräkning](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen. En IoT Edge-körningen körs även på den här IoT Edge-enhet.

    För tillfället är det bara Linux-plattformen tillgänglig för din IoT Edge-enhet.


## <a name="add-shares"></a>Lägg till resurser

För avancerade distributionen i den här självstudien behöver du två resurser: en Edge-resurs och en annan lokal Edge-resursen.

1. Lägg till en Edge-resurs på enheten genom att göra följande:

    1. I din Data Box Edge-resurs går du till **Edge-beräkning > Kom igång**.
    2. På den **Lägg till resurs/er** panelen, väljer **Lägg till**.
    3. På den **Lägg till resurs** bladet ange resursnamnet och välj resurstypen.
    4. Om du vill montera filresursen Edge, markerar du kryssrutan för **använder resursen med Edge-beräkning**.
    5. Välj den **lagringskonto**, **lagringstjänst**, en befintlig användare och välj sedan **skapa**.

        ![Lägg till en Edge-resurs](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    När Edge-resursen har skapats får du ett meddelande har skapats. Resurs-listan har uppdaterats för att återspegla den nya resursen.

2. Lägg till en lokal resurs Edge på Edge-enheten genom att upprepa alla stegen i föregående steg och markera kryssrutan för **konfigurera som Edge lokal nätverksresurs**. Data i den lokala resursen kvar på enheten.

    ![Lägg till en lokal resurs Edge](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. På den **resurser** bladet visas den uppdaterade listan med resurser.

    ![Uppdaterad lista över resurser](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Välj resursen i listan om du vill visa egenskaperna för den nyligen skapade lokala resursen. I rutan **Lokal monteringspunkt för Edge-beräkningsmoduler** kopierar du det värde som motsvarar den här resursen.

    Du använder den här lokala monteringspunkten när du distribuerar modulen.

    ![Rutan Lokal monteringspunkt för Edge-beräkningsmodul](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Välj resursen i listan om du vill visa egenskaperna för Edge-resursen som du skapade. I rutan **Lokal monteringspunkt för Edge-beräkningsmoduler** kopierar du det värde som motsvarar den här resursen.

    Du använder den här lokala monteringspunkten när du distribuerar modulen.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Lägg till en utlösare

1. Gå till **Edge-beräkning > utlösare**. Välj **+ Lägg till utlösare**.

    ![Lägg till utlösare](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. I den **Lägg till utlösare** bladet, ange följande värden.

    |Fält  |Value  |
    |---------|---------|
    |Namn på utlösare     | Ett unikt namn för utlösaren.         |
    |Utlösartyp     | Välj **filen** utlösaren. En fil-utlösaren utlöses när en fil-händelse inträffar, till exempel när en fil skrivs till inkommande resursen. En schemalagd utlösaren utlöses å andra sidan dig enligt ett schema som du definierar. I det här exemplet behöver vi en fil-utlösare.    |
    |Inkommande resurs     | Välj en filresurs som indata. Den lokala Edge-resursen är inkommande resursen i det här fallet. Modulen som används här flyttar filer från den lokala Edge-resursen till en Edge-filresurs med där de överförs till molnet.        |

    ![Lägg till utlösare](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. Du meddelas när utlösaren har skapats. Listan över utlösare uppdateras för att visa den nya utlösaren. Välj utlösaren som du nyss skapade.

    ![Lägg till utlösare](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Kopiera och spara exemplet vägen. Du ändrar den här vägen i exemplet och använder det senare i IoT Hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Lägg till utlösare](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Lägga till en modul

Det finns inga anpassade moduler på den här Edge-enhet. Du kan lägga till en anpassad eller en färdiga modul. Om du vill veta hur du skapar en anpassad modul går du till [Utveckla en C#-modul för din Data Box Edge-enhet](data-box-edge-create-iot-edge-module.md).

I det här avsnittet så lägger du till en anpassad modul till den IoT Edge-enhet som du skapade i [Utveckla en C#-modul för din Data Box Edge](data-box-edge-create-iot-edge-module.md). Den här anpassade modulen tar filer från en lokal filresurs Edge på en Edge-enhet och flyttar dem till en filresurs för Edge (moln) på enheten. Molnresursen pushar sedan filerna till det Azure-lagringskonto som är associerat med molnresursen.

1. Gå till **Edge-beräkning > Kom igång**. På den **Lägg till moduler** panelen, Välj scenariotyp som **avancerade**. Välj **går du till IoT Hub**.

    ![Välj avancerad distribution](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. I din IoT Hub-resurs går du till **IoT Edge-enhet** och välj sedan din IoT Edge-enhet.

    ![Gå till IoT Edge-enhet i IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. På **enhetsinformation**väljer **ange moduler**.

    ![Länken Ange moduler](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Under **Lägg till moduler** utför du följande:

    1. Ange namn, adress, användarnamn och lösenord för containerregisterinställningarna för den anpassade modulen.
    Namn, adress och de listade autentiseringsuppgifterna används för att hämta moduler med en matchande URL. Distribuera den här modulen genom att under **Distributionsmoduler** välja **IoT Edge-modul**. Den här IoT Edge-modulen är en dockercontainer som du kan distribuera till den IoT Edge-enhet som är associerad med din Data Box Edge-enhet.

        ![Länken Ange moduler](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Ange inställningarna för den anpassade IoT Edge-modulen. Ange följande värden.
     
        |Fält  |Value  |
        |---------|---------|
        |Namn     | Ett unikt namn för modulen. Denna modul är en docker-behållare som du kan distribuera till IoT Edge-enhet som är associerade med din Data Box-Edge.        |
        |URI för avbildning     | Du bildens URI för motsvarande behållaravbildningen för modulen.        |
        |Autentiseringsuppgifter som krävs     | Om alternativet är markerat används användarnamn och lösenord för att hämta moduler med en matchande URL.        |
    
        I den **behållare skapa alternativ** anger lokala monteringspunkterna för Edge-moduler som du kopierade i föregående steg för Edge resurs- och Edge lokala resurser.

        > [!IMPORTANT]
        > Sökvägar som används här är monterade i behållaren, så att de måste matcha funktioner i din behållare förväntar sig. Om du följer [skapa en anpassad modul](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code), den kod som anges i modulen förväntar sig de kopierade sökvägarna. Ändra inte dessa sökvägar.
    
        I rutan **Alternativ för containerskapande** så kan du klistra in följande exempel:
    
        ```
        {
            "HostConfig": {
            "Binds": [
            "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
            "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
            }
        }
        ```

        Ange alla miljövariabler som används för. Miljövariabler ange valfri information som hjälper att definiera den miljö där din modulen körs.

        ![Rutan Alternativ för containerskapande](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Konfigurera vid behov avancerade Edge-körningsinställningar och klicka sedan på **Nästa**.

        ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Under **Ange vägar** anger du vägar mellan moduler.  
    
    ![Ange vägarna](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    Du kan ersätta *väg* med följande väg sträng som du kopierade tidigare. I det här exemplet anger du namnet på den lokala resurs som skickar data till molnresursen. Ersätt den `modulename` med namnet på modulen. Välj **Nästa**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Avsnittet Ange vägar](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  Under **Granska distributionen** så granskar du alla inställningarna. Om du är nöjd så **skickar** du modulen för distribution.

    ![Länken Ange moduler](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Den här åtgärden startar distribution av principmodul. När distributionen är klar i **Körningsstatus** av modulen är **kör**.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Kontrollera data förvandla, överföra

Det slutliga steget är att se till att modulen är ansluten och körs som förväntat. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

Vidta följande steg för att verifiera Datatransformering och överför till Azure.
 
1.  Anslut till både lokala Edge och Edge-resurser som du skapade tidigare i Utforskaren.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Lägg till data i den lokala resursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    Data flyttas till molnresursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    Data pushas sedan från molnresursen till lagringskontot. Om du vill visa data, gå till ditt storage-konto och välj sedan **Lagringsutforskaren**. Du kan visa överförda data i ditt storage-konto.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
Du har slutfört valideringsprocessen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägg till resurser
> * Lägg till en utlösare
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

Läs hur du administrerar din Data Box Edge-enhet i:

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera en Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md)
