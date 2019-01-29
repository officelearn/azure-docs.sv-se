---
title: Transformera data med Azure Data Box Edge | Microsoft Docs
description: Lär dig hur du konfigurerar en beräkningsroll på Data Box Edge och använder den för att transformera data innan de skickas till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c52c311f1e1cd1335ea5797eadacd0bc89e1b36c
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402123"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Självstudier: Transformera data med Azure Data Box Edge (förhandsversion)

I den här självstudien beskrivs hur du konfigurerar en beräkningsroll på din Azure Data Box Edge-enhet. Efter att du konfigurerat beräkningsrollen så kan Data Box Edge transformera data innan de skickas till Azure.

Den här proceduren kan ta 30 till 45 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Azure IoT Hub-resurs
> * Konfigurera beräkningsroll
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen.
 
## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du konfigurerar en beräkningsroll på din Data Box Edge-enhet, ser du till att:

* Du har aktiverat din Data Box Edge-enhet enligt beskrivningen i [Anslut, konfigurera och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Skapa en IoT Hub-resurs

Innan du konfigurerar en beräkningsroll på Data Box Edge så måste du skapa en IoT Hub-resurs.

Detaljerade instruktioner finns i [Skapa en IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Använd samma prenumeration och resursgrupp som du använde för din Data Box Edge-resurs.

![Skapa en IoT Hub-resurs](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

Om en Edge-beräkningsroll inte har konfigurerats så gäller följande förbehåll:

- IoT Hub-resursen har inga Azure IoT-enheter eller Azure IoT Edge-enheter.
- Du kan inte skapa lokala Edge-resurser. När du lägger till en resurs är alternativet att skapa en lokal resurs för Edge-beräkning inte aktiverat.


## <a name="set-up-compute-role"></a>Konfigurera beräkningsroll

När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen.

Konfigurera beräkningsrollen på enheten genom att göra följande:

1. Gå till Data Box Edge-resursen, välj **Översikt** och klicka sedan på **Ställ in beräkningsroll**. 

    ![Översiktslänken i den vänstra rutan](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Alternativt så kan du gå till **Moduler** och välja **Konfigurera beräkning**.

    ![Länkarna Moduler och Konfigurera beräkning](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
1. I listmenyn så väljer du **IoT Hub-resursen** som du skapade i det föregående steget.  
    För tillfället är det bara Linux-plattformen tillgänglig för din IoT Edge-enhet. 
    
1. Klicka på **Skapa**.

    ![Skapa-knappen](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
    Beräkningsrollen tar några minuter att skapa. På grund av en bugg i den här utgåvan, även när beräkningsrollen har skapats, uppdateras inte skärmen. För att bekräfta att Edge-beräkningsrollen har konfigurerats, gå till **Moduler**.  

    ![Enhetslistan Konfigurera Edge-beräkning](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

1. Gå till **Översikt** igen.  
    Skärmen är uppdaterad och visar att beräkningsrollen har konfigurerats.

    ![Konfigurera en beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
1. I den IoT Hub som du använde när du skapade Edge-beräkningsrollen, går du till **IoT-enheter**.  
    En IoT-enhet är nu aktiverad. 

    ![Sidan IoT-enheter](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

1. I den vänstra rutan väljer du **IoT Edge**.  
    En IoT Edge-enhet är också aktiverad.

    ![Konfigurera en beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
1. Välj och klicka på IoT Edge-enheten.  
    En Edge-agent körs på den här IoT Edge-enhet. 

    ![Enhetsinformationssidan](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

    Det finns inga anpassade moduler på den här Edge-enheten, men du kan nu lägga till en anpassad modul. Om du vill veta hur du skapar en anpassad modul går du till [Utveckla en C#-modul för din Data Box Edge-enhet](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Lägga till en anpassad modul

I det här avsnittet så lägger du till en anpassad modul till den IoT Edge-enhet som du skapade i [Utveckla en C#-modul för din Data Box Edge](data-box-edge-create-iot-edge-module.md). 

Den här proceduren använder ett exempel där den anpassade modulen tar filer från en lokal resurs på Edge-enheten och flyttar dem till en molnresurs på enheten. Molnresursen pushar sedan filerna till det Azure-lagringskonto som är associerat med molnresursen. 

1. Lägg till en lokal resurs på Edge-enheten genom att göra följande:

    a. I Data Box Edge-resursen går du till **Resurser**. 
    
    b. Välj **Lägg till resurs** och ange därefter resursens namn och välj resurstyp. 
    
    c. Skapa en lokal resurs genom att markera kryssrutan **Konfigurera som lokal Edge-resurs**. 
    
    d. Välj **Skapa ny** eller **Använd befintlig** och välj sedan **Skapa**.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Om du skapade en lokal NFS-resurs så använder du följande kommandoalternativ för fjärrsynkronisering (rsync) för att kopiera filer till resursen:

    `rsync --inplace <source file path> < destination file path>`

    Mer information om rsync-kommandot finns i [Rsync-dokumentationen](https://www.computerhope.com/unix/rsync.htm). 

    Den lokala resursen skapas och du får ett meddelande att det har lyckats. Resurslistan kan vara uppdaterad men du måste vänta tills resursen har skapats.
    
1. Gå till listan över resurser. 

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
1. Visa egenskaperna för den nyligen skapade lokala resursen genom att markera den. 

1. I rutan **Lokal monteringspunkt för Edge-beräkningsmoduler** kopierar du det värde som motsvarar den här resursen.  
    Du använder den här lokala monteringspunkten när du distribuerar modulen.

    ![Rutan Lokal monteringspunkt för Edge-beräkningsmodul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
1. På en befintlig molnresurs som skapades på din Data Box Edge-enhet i rutan **Lokal monteringspunkt för Edge-beräkningsmoduler** så kopierar du den lokala monteringspunkten för Edge-beräkningsmoduler för den här molnresursen.  
    Du använder den här lokala monteringspunkten när du distribuerar modulen.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

1. Om du vill lägga till en anpassad modul till IoT Edge-enheten så går du till din IoT Hub-resurs och därefter till **IoT Edge-enheten**. 

1. Välj enheten och i **Enhetsinformation** väljer du **Ange moduler**. 

    ![Länken Ange moduler](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

1. Under **Lägg till moduler** utför du följande:

    a. Ange namn, adress, användarnamn och lösenord för containerregisterinställningarna för den anpassade modulen.  
    Namn, adress och de listade autentiseringsuppgifterna används för att hämta moduler med en matchande URL. Distribuera den här modulen genom att under **Distributionsmoduler** välja **IoT Edge-modul**. Den här IoT Edge-modulen är en dockercontainer som du kan distribuera till den IoT Edge-enhet som är associerad med din Data Box Edge-enhet.

    ![Länken Ange moduler](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    b. Ange inställningarna för den anpassade IoT Edge-modulen genom att ange namnet på din modul och URI för avbildningen för motsvarande containeravbildning. 
    
    ![Sidan Anpassade IoT Edge-moduler](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    c. I rutan **Alternativ för containerskapande** anger du de lokala monteringspunkterna för de Edge-moduler som du kopierade i föregående steg för molnet och den lokala resursen.
    > [!IMPORTANT]
    > Använd de kopierade sökvägarna. Skapa inte nya sökvägar. De lokala monteringspunkterna mappas till motsvarande **InputFolderPath** och **OutputFolderPath** som du angav i modulen när du [uppdaterade modulen med anpassad kod](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
    I rutan **Alternativ för containerskapande** så kan du klistra in följande exempel: 
    
    ```
    {
        "HostConfig": {
        "Binds": [
        "/home/hcsshares/mysmblocalshare:/home/LocalShare",
        "/home/hcsshares/mysmbshare1:/home/CloudShare"
        ]
        }
    }
    ```

    Ange också eventuella miljövariabler här för din modul.

    ![Rutan Alternativ för containerskapande](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    d. Konfigurera vid behov avancerade Edge-körningsinställningar och klicka sedan på **Nästa**.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
1.  Under **Ange vägar** anger du vägar mellan moduler.  
    I det här exemplet anger du namnet på den lokala resurs som skickar data till molnresursen.

    Du kan ersätta *väg* med följande vägsträng:       `"route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"`

    ![Avsnittet Ange vägar](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 

1. Välj **Nästa**. 

1.  Under **Granska distributionen** så granskar du alla inställningarna. Om du är nöjd så **skickar** du modulen för distribution.

    ![Länken Ange moduler](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
    Den här åtgärden startar moduldistributionen som det visas i följande bild:

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Verifiera datatransformering och överföring

Det slutliga steget är att se till att modulen är ansluten och körs som förväntat. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
Verifiera att modulen körs genom att göra följande:

1. Välj modulen och titta på Modulidentitetstvillingen.  
    Klientstatusen för Edge-enheten och modulen bör visas som *Ansluten*.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
    När modulen körs visas den även under listan över Edge-moduler i din Data Box Edge-resurs. Körningsstatusen för den modul du har lagt till är *körs*.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
1.  I Utforskaren så ansluter du till både den lokala resursen och molnresursen du skapade tidigare.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
1.  Lägg till data i den lokala resursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
    Data flyttas till molnresursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

    Data pushas sedan från molnresursen till lagringskontot. Visa data genom att gå till Storage Explorer.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Du har slutfört valideringsprocessen.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en IoT Hub-resurs
> * Konfigurera beräkningsroll
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

Läs hur du administrerar din Data Box Edge-enhet i:

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera en Data Box Edge](https://aka.ms/dbg-docs)


