---
title: Självstudie för att filtrera, analysera data med beräknings Azure Data Box Edge | Microsoft Docs
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
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277234"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Självstudier: Transformera data med Azure Data Box Edge

I den här självstudien beskrivs hur du konfigurerar en beräkningsroll på din Azure Data Box Edge-enhet. Efter att du konfigurerat beräkningsrollen så kan Data Box Edge transformera data innan de skickas till Azure.

Den här proceduren kan ta cirka 10 till 15 minuter att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägg till resurser
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

 
## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar en beräkningsroll på din Data Box Edge-enhet, ser du till att:

- Du har aktiverat din Data Box Edge-enhet enligt beskrivningen i [Anslut, konfigurera och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurera beräkning

Om du vill konfigurera beräkning på Data Box Edge skapar du en IoT Hub resurs.

1. I Azure Portal av din Data Box Edge-resurs går du till översikt. Välj **Kom igång**i den högra rutan på **beräknings** panelen.

    ![Kom igång med Compute](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. På panelen **Konfigurera Edge Compute** väljer du **Konfigurera beräkning**.
3. Ange följande på bladet **Konfigurera Edge Compute** :

   
    |Fält  |Value  |
    |---------|---------|
    |IoT-hubb     | Välj från **ny** eller **befintlig**. <br> Som standard används en standard-nivå (S1) för att skapa en IoT-resurs. Om du vill använda en IoT-resurs på kostnads fri nivå skapar du en och väljer sedan den befintliga resursen. <br> I varje fall använder IoT Hub resursen samma prenumeration och resurs grupp som används av Data Box Edge resursen.     |
    |Name     |Ange ett namn för din IoT Hub-resurs.         |

    ![Kom igång med Compute](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Välj **Skapa**. Det tar några minuter att skapa en IoT Hub-resurs. När IoT Hub resursen har skapats kan du **Konfigurera** Compute-panelen för att Visa beräknings konfigurationen. Bekräfta att Edge Compute-rollen har kon figurer ATS genom att välja **Visa beräkning** på panelen **Konfigurera beräkning** .
    
    ![Kom igång med Compute](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Om dialog rutan **Konfigurera beräkning** stängs innan IoT Hub är kopplad till data Box Edge enheten, skapas IoT Hub men visas inte i beräknings konfigurationen. 
    
    När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen. En IoT Edge runtime körs också på den här IoT Edge enheten. För tillfället är det bara Linux-plattformen tillgänglig för din IoT Edge-enhet.


## <a name="add-shares"></a>Lägg till resurser

För den enkla distributionen i den här självstudien behöver du två resurser: en Edge-resurs och en annan Edge-lokal resurs.

1. Lägg till en Edge-resurs på enheten genom att utföra följande steg:

    1. I din Data Box Edge-resurs går du till **Edge compute > kom igång**.
    2. Välj **Lägg till**på panelen **Lägg till resurs (er)** .
    3. På bladet **Lägg till resurs** anger du resurs namnet och väljer resurs typ.
    4. Om du vill montera Edge-resursen markerar du kryss rutan för att **använda resurs med Edge Compute**.
    5. Välj **lagrings kontot**, **lagrings tjänsten**, en befintlig användare och välj sedan **skapa**.

        ![Lägg till en Edge-resurs](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Om du skapade en lokal NFS-resurs så använder du följande kommandoalternativ för fjärrsynkronisering (rsync) för att kopiera filer till resursen:

    `rsync <source file path> < destination file path>`

    Mer information om rsync-kommandot finns i [Rsync-dokumentationen](https://www.computerhope.com/unix/rsync.htm).

    Edge-resursen skapas och du får ett meddelande om att det har skapats. Resurslistan kan vara uppdaterad men du måste vänta tills resursen har skapats.

2. Lägg till en lokal Edge-resurs på gräns enheten genom att upprepa alla steg i föregående steg och markera kryss rutan för att **Konfigurera som Edge-lokal resurs**. Data i den lokala resursen förblir på enheten.

    ![Lägg till en lokal Edge-resurs](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Välj **Lägg till resurs (er)** om du vill se den uppdaterade listan över resurser.

    ![Uppdaterad lista över resurser](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Lägg till en modul

Du kan lägga till en anpassad eller en fördefinierad modul. Det finns inga anpassade moduler på den här Edge-enheten. Om du vill veta hur du skapar en anpassad modul går du till [Utveckla en C#-modul för din Data Box Edge-enhet](data-box-edge-create-iot-edge-module.md).

I det här avsnittet så lägger du till en anpassad modul till den IoT Edge-enhet som du skapade i [Utveckla en C#-modul för din Data Box Edge](data-box-edge-create-iot-edge-module.md). Den här anpassade modulen tar filer från en lokal Edge-resurs på gräns enheten och flyttar dem till en gräns (moln) resurs på enheten. Molnresursen pushar sedan filerna till det Azure-lagringskonto som är associerat med molnresursen.

1. Gå till **Edge compute > kom igång**. På panelen **Lägg till moduler** väljer du scenario typen **enkel**. Välj **Lägg till**.
2. Ange följande värden i bladet **Konfigurera och Lägg till modul** :

    
    |Fält  |Value  |
    |---------|---------|
    |Name     | Ett unikt namn för modulen. Den här modulen är en Docker-behållare som du kan distribuera till den IoT Edge enhet som är kopplad till din Data Box Edge.        |
    |URI för avbildning     | Avbildnings-URI för motsvarande behållar avbildning för modulen.        |
    |Autentiseringsuppgifter krävs     | Om det här alternativet markeras används användar namn och lösen ord för att hämta moduler med en matchande URL.        |
    |Inmatad resurs     | Välj en inmatad resurs. Den lokala Edge-resursen är den ingående resursen i det här fallet. Modulen som används här flyttar filer från den lokala Edge-resursen till en Edge-resurs där de överförs till molnet.        |
    |Utgående resurs     | Välj en utgående resurs. Edge-resursen är den utgående resursen i det här fallet.        |
    |Utlösartyp     | Välj från **fil** eller **schema**. En fil utlösare utlöses när en fil händelse inträffar, till exempel att en fil skrivs till den angivna resursen. En schemalagd utlösare aktive ras baserat på ett schema som definierats av dig.         |
    |Namn på utlösare     | Ett unikt namn för utlösaren.         |
    |Miljövariabler| Valfri information som hjälper dig att definiera miljön där modulen ska köras.   |

    ![Lägg till och konfigurera modul](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Välj **Lägg till**. Modulen läggs till. Panelen **Lägg till modul** uppdateras för att indikera att modulen har distribuerats. 

    ![Modulen har distribuerats](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verifiera datatransformering och överföring

Det slutliga steget är att se till att modulen är ansluten och körs som förväntat. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

Verifiera att modulen körs genom att göra följande:

1. Välj panelen **Lägg till modul** . Då går du till bladet **moduler** . Identifiera den modul som du har distribuerat i listan över moduler. Körnings status för modulen som du har lagt till ska *köras*.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  I Utforskaren ansluter du till både de lokala resurserna och kant resurserna som du skapade tidigare.

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
> * Lägg till resurser
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

Läs hur du administrerar din Data Box Edge-enhet i:

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera en Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md)
