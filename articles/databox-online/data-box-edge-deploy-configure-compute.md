---
title: Självstudiekurs för att filtrera, analysera data med beräkning på Azure Data Box Edge | Microsoft-dokument
description: Lär dig hur du konfigurerar en beräkningsroll på Data Box Edge och använder den för att transformera data innan de skickas till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239023"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Självstudiekurs: Omvandla data med Azure Data Box Edge

I den här självstudien beskrivs hur du konfigurerar en beräkningsroll på din Azure Data Box Edge-enhet. Efter att du konfigurerat beräkningsrollen så kan Data Box Edge transformera data innan de skickas till Azure.

Den här proceduren kan ta cirka 10 till 15 minuter att slutföra.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägga till resurser
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

 
## <a name="prerequisites"></a>Krav

Innan du konfigurerar en beräkningsroll på din Data Box Edge-enhet, ser du till att:

- Du har aktiverat din Data Box Edge-enhet enligt beskrivningen i [Anslut, konfigurera och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurera beräkning

Om du vill konfigurera beräkning på din Data Box Edge skapar du en IoT Hub-resurs.

1. Gå till Översikt i Azure-portalen för din Data Box Edge-resurs. Välj **Kom igång**på panelen **Beräkna** i den högra rutan.

    ![Komma igång med beräkning](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. På **beräkningspanelen Konfigurera kant** väljer du **Konfigurera beräkning**.
3. Mata in följande på **beräkningsbladet Konfigurera kant:**

   
    |Field  |Värde  |
    |---------|---------|
    |IoT Hub     | Välj mellan **Ny** eller **Befintlig**. <br> Som standard används en standardnivå (S1) för att skapa en IoT-resurs. Om du vill använda en kostnadsfri nivå IoT-resurs skapar du en och väljer sedan den befintliga resursen. <br> I varje enskilt fall använder IoT Hub-resursen samma prenumerations- och resursgrupp som används av databoxkantsresursen.     |
    |Namn     |Ange ett namn på din IoT Hub-resurs.         |

    ![Komma igång med beräkning](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Välj **Skapa**. Det tar ett par minuter att skapa IoT Hub-resursen. När IoT Hub-resursen har skapats uppdateras uppdatering av **beräkningspanelen** för att visa beräkningskonfigurationen. Om du vill bekräfta att edge-beräkningsrollen har konfigurerats väljer du **Visa beräkning** på **beräkningspanelen Konfigurera.**
    
    ![Komma igång med beräkning](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Om dialogrutan **Konfigurera beräkning** stängs innan IoT Hub associeras med Data Box Edge-enheten skapas IoT Hub men visas inte i beräkningskonfigurationen. 
    
    När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen. En IoT Edge Runtime körs också på den här IoT Edge-enheten. För tillfället är det bara Linux-plattformen tillgänglig för din IoT Edge-enhet.


## <a name="add-shares"></a>Lägga till resurser

För den enkla distributionen i den här självstudien behöver du två resurser: en Edge-resurs och en annan lokal Edge-resurs.

1. Lägg till en Edge-resurs på enheten genom att göra följande steg:

    1. Gå till **Edge-beräkning > Komma igång i**databoxens edgeresurs.
    2. Välj **Lägg till**på panelen Lägg **till delar.**
    3. Ange resursnamnet och välj resurstypen på bladet **Lägg till resurs.**
    4. Om du vill montera Edge-resursen markerar du kryssrutan **Använd resursen med Edge-beräkning**.
    5. Välj **lagringskonto,** **lagringstjänst**, en befintlig användare och välj sedan **Skapa**.

        ![Lägga till en Edge-resurs](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Om du skapade en lokal NFS-resurs så använder du följande kommandoalternativ för fjärrsynkronisering (rsync) för att kopiera filer till resursen:

    `rsync <source file path> < destination file path>`

    Mer information om rsync-kommandot finns i [Rsync-dokumentationen](https://www.computerhope.com/unix/rsync.htm).

    Edge-resursen skapas och du får ett meddelande om att skapa dem har skapats. Resurslistan kan vara uppdaterad men du måste vänta tills resursen har skapats.

2. Lägg till en lokal edge-resurs på Edge-enheten genom att upprepa alla steg i föregående steg och markera kryssrutan konfigurera **som kant lokal resurs**. Data i den lokala resursen finns kvar på enheten.

    ![Lägga till en lokal edge-resurs](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Välj **Lägg till resurser om** du vill visa den uppdaterade listan med resurser.

    ![Uppdaterad lista över resurser](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Lägga till en modul

Du kan lägga till en anpassad eller en färdig modul. Det finns inga anpassade moduler på den här Edge-enheten. Om du vill veta hur du skapar en anpassad modul går du till [Utveckla en C#-modul för din Data Box Edge-enhet](data-box-edge-create-iot-edge-module.md).

I det här avsnittet så lägger du till en anpassad modul till den IoT Edge-enhet som du skapade i [Utveckla en C#-modul för din Data Box Edge](data-box-edge-create-iot-edge-module.md). Den här anpassade modulen tar filer från en lokal Edge-resurs på Edge-enheten och flyttar dem till en Edge-resurs (molnresurs) på enheten. Molnresursen pushar sedan filerna till det Azure-lagringskonto som är associerat med molnresursen.

1. Gå till **Edge-beräkning > Kom igång**. På panelen **Lägg till moduler** väljer du scenariotypen som **enkel**. Välj **Lägg till**.
2. Mata in följande värden i **modulbladet Konfigurera och lägg till:**

    
    |Field  |Värde  |
    |---------|---------|
    |Namn     | Ett unikt namn för modulen. Den här modulen är en dockerbehållare som du kan distribuera till IoT Edge-enheten som är associerad med din Data Box Edge.        |
    |Bild URI     | Bild-URI för motsvarande behållaravbildning för modulen.        |
    |Autentiseringsuppgifter krävs     | Om markerad används användarnamn och lösenord för att hämta moduler med en matchande URL.        |
    |Ingångsandel     | Välj en indataresurs. Den lokala edge-resursen är indataresursen i det här fallet. Modulen som används här flyttar filer från den lokala Edge-resursen till en Edge-resurs där de överförs till molnet.        |
    |Utdataaktie     | Välj en utdataresurs. Edge-resursen är utdataresursen i det här fallet.        |
    |Utlösartyp     | Välj från **Arkiv** eller **Schema**. En filutlösare utlöses när en filhändelse inträffar, till exempel en fil skrivs till indataresursen. En schemalagd utlösare utlöses baserat på ett schema som definierats av dig.         |
    |Utlösarnamn     | Ett unikt namn på din utlösare.         |
    |Miljövariabler| Valfri information som hjälper till att definiera den miljö där modulen ska köras.   |

    ![Lägga till och konfigurera modul](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Välj **Lägg till**. Modulen läggs till. **Uppdateringar av lägg till modulpanelen** för att ange att modulen har distribuerats. 

    ![Modul har distribuerats](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verifiera datatransformering och överföring

Det slutliga steget är att se till att modulen är ansluten och körs som förväntat. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

Verifiera att modulen körs genom att göra följande:

1. Välj panelen **Lägg till modul.** Detta tar dig till **modulens** blad. Identifiera modulen som du distribuerade i listan över moduler. Körningsstatusen för modulen som du lade till ska *köras*.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  I Utforskaren ansluter du till både Edge local- och Edge-resurser som du skapade tidigare.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Lägg till data i den lokala resursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Data flyttas till molnresursen.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    Data pushas sedan från molnresursen till lagringskontot. Visa data genom att gå till Storage Explorer.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Du har slutfört valideringsprocessen.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägga till resurser
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

Läs hur du administrerar din Data Box Edge-enhet i:

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera en Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md)
