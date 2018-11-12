---
title: Transformera data med Azure Data Box Edge | Microsoft Docs
description: Lär dig hur du konfigurerar en beräkningsroll på Data Box Edge och använder den för att transformera data innan de skickas till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: e970f7cc0d4c4620f2da69286be36d0c22e0d747
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260371"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-preview"></a>Självstudie: Transformera data med Azure Data Box Edge (förhandsversion)

I den här kursen beskrivs hur du konfigurerar en beräkningsroll på Data Box Edge. När beräkningsrollen har konfigurerats kan Data Box Edge transformera data innan de skickas till Azure.

Den här proceduren kan ta upp till 30-45 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en IoT Hub-resurs
> * Konfigurera beräkningsroll
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen.
 
## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du konfigurerar beräkning på Data Box Edge ser du till att:

* Data Box Edge-enheten har aktiverats enligt beskrivningen i [Ansluta och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="create-an-iot-hub-resource"></a>Skapa en IoT Hub-resurs

Innan du konfigurerar beräkningsrollen på Data Box Edge måste du skapa en IoT Hub-resurs.

Detaljerade instruktioner finns i [Skapa en IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub). Använd samma prenumeration och den resursgrupp du använde för din Data Box Edge-resurs.

![Skapa IoT Hub-resurs](./media/data-box-edge-deploy-configure-compute/create-iothub-resource-1.png)

När Edge-beräkningsrollen inte har konfigurerats gäller följande:

- IoT Hub-resursen har inga IoT-enheter eller IoT Edge-enheter.
- Du kan inte skapa lokala Edge-resurser. När du lägger till en resurs är alternativet att skapa en lokal resurs för Edge-beräkning inte aktiverat.


## <a name="set-up-compute-role"></a>Konfigurera beräkningsroll

När Edge-beräkningsrollen har konfigurerats på Edge-enheten skapas två enheter – den ena är en IoT-enhet och den andra är en IoT Edge-enhet. Båda dessa enheter kan visas i IoT Hub-resursen.

Konfigurerar beräkningsrollen på enheten genom att göra följande.

1. Gå till Data Box Edge-resursen och gå sedan till **Översikt** och klicka på **Ställ in beräkningsroll**. 

    ![Konfigurera beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-1.png)
   
    Du kan även gå till **Moduler** och klicka på **Konfigurera beräkning**.

    ![Konfigurera beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-2.png)
 
2. I den nedrullningsbara listan väljer du den **IoT Hub-resurs** du har skapat i det föregående steget. Bara Linux-plattformen är tillgänglig här för din IoT Edge-enhet. Klicka på **Skapa**.

    ![Konfigurera beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-3.png)
 
3. Beräkningsrollen tar några minuter att skapa. På grund av en bugg i den här utgåvan, även när beräkningsrollen har skapats, uppdateras inte skärmen. Gå till **Moduler** och du kan se att Edge-beräkningsrollen har konfigurerats.  

    ![Konfigurera beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-4.png)

4. Gå till **Översikt** igen och nu har skärmen uppdaterats för att ange att beräkningsrollen har konfigurerats.

    ![Konfigurera beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-5.png)
 
5. Gå till den IoT Hub du har använt när du skapar Edge-beräkningsrollen. Gå till **IoT-enheter**. Du kan se att en IoT-enhet nu har aktiverats. 

    ![Konfigurera beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-6.png)

6. Gå till **IoT Edge** så ser du att en IoT Edge-enhet också har aktiverats.

    ![Konfigurera beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-7.png)
 
7. Välj och klicka på IoT Edge-enheten. En Edge-agent körs på den här IoT Edge-enhet. 

    ![Konfigurera beräkningsroll](./media/data-box-edge-deploy-configure-compute/setup-compute-8.png) 

Men det finns inga anpassade moduler på den här Edge-enheten. Nu kan du lägga till en anpassad modul på den här enheten. Om du vill veta hur du skapar en anpassad modul går du till [Utveckla en C#-modul för Data Box Edge](data-box-edge-create-iot-edge-module.md).


## <a name="add-a-custom-module"></a>Lägga till en anpassad modul

I det här avsnittet lägger du till en anpassad modul till den IoT Edge-enhet som du skapade i [Utveckla en C#-modul för Data Box Edge](data-box-edge-create-iot-edge-module.md). 

I den här proceduren används ett exempel där den anpassade modulen som används tar filer från en lokal resurs på Edge-enheten och flyttar dem till en molnresurs på enheten. Molnresursen flyttar sedan filerna till Azure-lagringskontot som associeras med molnresursen. 

1. Det första steget är att lägga till en lokal resurs på Edge-enheten. I Data Box Edge-resursen går du till **Resurser**. Klicka på **+ Lägg till resurs**. Ange resursnamnet och välj resurstyp. Markera alternativet **Configure as Edge local share** (Konfigurera som lokal Edge-resurs) för att skapa en lokal resurs. Välj en **befintlig användare** eller **skapa en ny**. Klicka på **Skapa**.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-1.png) 

    Om du har skapat en lokal NFS-resurs använder du följande rsync-kommanoalternativ för att kopiera filer till resursen:

    `rsync --inplace <source file path> < destination file path>`

     Mer information om rsync-kommandot finns i [Rsync-dokumentationen](https://www.computerhope.com/unix/rsync.htm). 

 
2. När den lokala resursen har skapats och du har fått ett meddelande om detta (resurslistan kan uppdateras innan men du måste vänta på att resursen har skapats klart) går du till listan över resurser. 

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-2.png) 
 
3. Välj och klicka på den nya lokala resursen och visa dess egenskaper. Kopiera och spara den **lokala monteringspunkten för Edge-moduler** som motsvarar den här resursen.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-3.png) 
 
    Gå till den befintliga molnresursen du har skapat på Data Box Edge-enheten. Kopiera och spara återigen den lokala monteringspunkten för Edge-beräkningsmodeller för den här molnresursen. Dessa lokala monteringspunkter används när du distribuerar modulen.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-4.png)  

4. Om du har lägga till en anpassad modul till IoT Edge-enheten går du till din IoT Hub-resurs och går sedan till **IoT Edge-enheten**. Välj och klicka på enheten. I **Enhetsinformation**, i kommandofältet högst upp, klickar du på **Ange moduler**. 

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-5.png) 

5. Under **Lägg till moduler** utför du följande steg:

    1. Ange **namn**, **adress**, **användarnamn** och **lösenord** för **Containerregisterinställningar** för den anpassade modulen. Namnet, adressen och listade autentiseringsuppgifter används för att hämta moduler med matchande URL. Distribuera den här modulen genom att under **Distributionsmoduler** välja **IoT Edge-modul**. Denna IoT Edge-modul är en dockercontainer som du kan distribuera till en IoT Edge-enhet som är associerad med din Data Box Edge-enhet.

        ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-6.png) 
 
    2. Ange inställningarna för den anpassade IoT Edge-modulen. Ange modulens **Namn** och **bild-URI** för motsvarande containeravbildning. 
    
        ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-7.png) 

    3. I **Alternativ för att skapa container** anger du de lokala monteringspunkter för de Edge-moduler som kopierades i de föregående stegen för molnresursen och den lokala resursen (viktig att använda dessa vägar i stället för att skapa nya). De lokala monteringspunkterna mappas till motsvarande **InputFolderPath** och **OutputFolderPath** som du angav i modulen när du [uppdaterade modulen med anpassad kod](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code). 
    
        Du kan kopiera och klistra in det exempel som visas nedan i dina **alternativ för att skapa container**: 
        
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

        Ange också miljövariabler här för din modul.

        ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-8.png) 
 
    4. **Konfigurera avancerade Edge-körningsinställningar** om det behövs och klicka sedan på **Nästa**.

        ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-9.png) 
 
6.  Under **Ange rutter** anger du rutter mellan moduler. I det här fallet anger du namnet på den lokala resurs som skickar data till molnresursen. Klicka på **Nästa**.

    Du kan ersätta vägen med följande vägsträng:       "route": "FROM /* WHERE topic = 'mysmblocalshare' INTO BrokeredEndpoint(\"/modules/filemovemodule/inputs/input1\")"

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-10.png) 
 
7.  Under **Granska distributionen** granskar du alla inställningar. Om du är nöjd **skickar** du modulen för distribution.

    ![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-11.png) 
 
Det här startar moduldistributionen så som visas av den **anpassade IoT Edge-modulen** under **Moduler**.

![Lägga till anpassad modul](./media/data-box-edge-deploy-configure-compute/add-a-custom-module-12.png) 

### <a name="verify-data-transform-and-transfer"></a>Verifiera datatransformering och överföring

Det slutliga steget är att se till att modulen är ansluten och körs som förväntat. Utför följande steg för att kontrollera att modulen körs.

1. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-1.png) 
 
2. Välj och klicka på modulen och titta på **modulidentitetstvillingen**. Klientstatusen för Edge-enheten och modulen bör visas som **Ansluten**.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-2.png) 
 
3.  När modulen körs visas den även under listan över Edge-moduler i Data Box Edge-resursen. **Körningsstatusen** för modulen du har lagt till är **körs**.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-3.png) 
 
4.  Anslut till både den lokala resursen och molnresursen du har skapat via Utforskaren.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-4.png) 
 
5.  Lägg till data i den lokala resursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-5.png) 
 
6.  Data flyttas till molnresursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-6.png)  

7.  Data flyttas sedan från molnresursen till lagringskontot. Gå till Storage Explorer för att visa data.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-transform-7.png) 
 
Detta avslutar verifieringsprocessen.


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Data Box Edge-ämnen som att:

> [!div class="checklist"]
> * Skapa en IoT Hub-resurs
> * Konfigurera beräkningsroll
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

Gå vidare till nästa självstudie och lär dig hur du administrerar Data Box Edge.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera en Data Box Edge](https://aka.ms/dbg-docs)


