---
title: Självstudie för att filtrera och analysera data med beräkning på Azure Data Box Edge | Microsoft Docs
description: Lär dig hur du konfigurerar en beräkningsroll på Data Box Edge och använder den för att transformera data innan de skickas till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 7b3d725eb05c811d3fdd44516c1bde9a8dfbaaac
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924346"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Självstudier: Transformera data med Azure Data Box Edge

I den här självstudien beskrivs hur du konfigurerar en beräkningsroll på din Azure Data Box Edge-enhet. Efter att du konfigurerat beräkningsrollen så kan Data Box Edge transformera data innan de skickas till Azure.

Den här proceduren kan ta upp till 10 – 15 minuter för att slutföra.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera beräkning
> * Lägg till resurser
> * Lägga till en beräkningsmodul
> * Verifiera datatransformering och överföring

 
## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du konfigurerar en beräkningsroll på din Data Box Edge-enhet, ser du till att:

- Du har aktiverat din Data Box Edge-enhet enligt beskrivningen i [Anslut, konfigurera och aktivera Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Konfigurera beräkning

Om du vill konfigurera beräkning på din Data Box-Edge, ska du skapa en IoT Hub-resurs.

1. Gå till Översikt i Azure-portalen för din Data Box Edge-resurs. I den högra rutan på den **Compute** panelen, väljer **börjar**.

    ![Kom igång med beräkning](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. På den **konfigurera Edge-beräkning** panelen, väljer **konfigurera beräkna**.
3. På den **konfigurera Edge-beräkning** bladet, ange följande:

   
    |Fält  |Värde  |
    |---------|---------|
    |IoT Hub     | Välj bland **nya** eller **befintliga**. <br> Som standard används en Standard-nivå (S1) för att skapa en IoT-resurs. Om du vill använda en kostnadsfri nivå IoT-resurs, skapa en och välj sedan den befintliga resursen. <br> I båda fallen använder IoT Hub-resurs i samma prenumeration och resursgrupp som används av Data Box Edge-resursen.     |
    |Namn     |Ange ett namn för din IoT Hub-resurs.         |

    ![Kom igång med beräkning](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Välj **Skapa**. Resursskapande IoT-hubb tar ett par minuter. När IoT Hub-resursen har skapats kan den **konfigurera beräkna** panelen uppdateringar att visa att beräkningskonfigurationen. För att bekräfta att rollen Edge-beräkning har konfigurerats, Välj **visa Compute** på den **konfigurera beräkna** panelen.
    
    ![Kom igång med beräkning](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    När Edge-beräkningsrollen har konfigurerats på Edge-enheten så skapas två enheter: en IoT-enhet och en IoT Edge-enhet. Bägge enheter kan visas i IoT Hub-resursen. En IoT Edge-körningen körs även på den här IoT Edge-enhet. För tillfället är det bara Linux-plattformen tillgänglig för din IoT Edge-enhet.


## <a name="add-shares"></a>Lägg till resurser

För enkel distribution i den här självstudien, behöver du två resurser: en Edge-resurs och en annan lokal Edge-resursen.

1. Lägg till en Edge-resurs på enheten genom att göra följande:

    1. I din Data Box Edge-resurs går du till **Edge-beräkning > Kom igång**.
    2. På den **Lägg till resurs/er** panelen, väljer **Lägg till**.
    3. På den **Lägg till resurs** bladet ange resursnamnet och välj resurstypen.
    4. Om du vill montera filresursen Edge, markerar du kryssrutan för **använder resursen med Edge-beräkning**.
    5. Välj den **lagringskonto**, **lagringstjänst**, en befintlig användare och välj sedan **skapa**.

        ![Lägg till en Edge-resurs](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Om du skapade en lokal NFS-resurs så använder du följande kommandoalternativ för fjärrsynkronisering (rsync) för att kopiera filer till resursen:

    `rsync <source file path> < destination file path>`

    Mer information om rsync-kommandot finns i [Rsync-dokumentationen](https://www.computerhope.com/unix/rsync.htm).

    Edge-resursen har skapats och du får ett meddelande har skapats. Resurslistan kan vara uppdaterad men du måste vänta tills resursen har skapats.

2. Lägg till en lokal resurs Edge på Edge-enheten genom att upprepa alla stegen i föregående steg och markera kryssrutan för **konfigurera som Edge lokal nätverksresurs**. Data i den lokala resursen kvar på enheten.

    ![Lägg till en lokal resurs Edge](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Välj **Lägg till resurs/er** att se den uppdaterade listan med resurser.

    ![Uppdaterad lista över resurser](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Lägga till en modul

Du kan lägga till en anpassad eller en färdiga modul. Det finns inga anpassade moduler på den här Edge-enhet. Om du vill veta hur du skapar en anpassad modul går du till [Utveckla en C#-modul för din Data Box Edge-enhet](data-box-edge-create-iot-edge-module.md).

I det här avsnittet så lägger du till en anpassad modul till den IoT Edge-enhet som du skapade i [Utveckla en C#-modul för din Data Box Edge](data-box-edge-create-iot-edge-module.md). Den här anpassade modulen tar filer från en lokal filresurs Edge på en Edge-enhet och flyttar dem till en filresurs för Edge (moln) på enheten. Molnresursen pushar sedan filerna till det Azure-lagringskonto som är associerat med molnresursen.

1. Gå till **Edge-beräkning > Kom igång**. På den **Lägg till moduler** panelen, Välj scenariotyp som **enkel**. Välj **Lägg till**.
2. I den **konfigurera och Lägg till modulen** bladet, ange följande värden:

    
    |Fält  |Värde  |
    |---------|---------|
    |Namn     | Ett unikt namn för modulen. Denna modul är en docker-behållare som du kan distribuera till IoT Edge-enheten som är associerat med din Data Box-Edge.        |
    |URI för avbildning     | Du bildens URI för motsvarande behållaravbildningen för modulen.        |
    |Autentiseringsuppgifter som krävs     | Om alternativet är markerat används användarnamn och lösenord för att hämta moduler med en matchande URL.        |
    |Inkommande resurs     | Välj en filresurs som indata. Den lokala Edge-resursen är inkommande resursen i det här fallet. Modulen som används här flyttar filer från den lokala Edge-resursen till en Edge-filresurs med där de överförs till molnet.        |
    |Utdata-resurs     | Välj en filresurs för utdata. Edge-resursen är utdata-resursen i det här fallet.        |
    |Utlösartyp     | Välj ett värde från **filen** eller **schema**. En fil-utlösaren utlöses när en fil-händelse inträffar, till exempel när en fil skrivs till inkommande resursen. En schemalagd utlösaren utlöses dig enligt ett schema som du definierar.         |
    |Namn på utlösare     | Ett unikt namn för utlösaren.         |
    |Miljövariabler| Valfri information som hjälper dig att definiera den miljö som din modul ska köras.   |

    ![Lägga till och konfigurera modulen](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Välj **Lägg till**. Modulen hämtar har lagts till. Den **Lägg till modulen** panelen uppdateras för att visa att modulen distribueras. 

    ![Modulen som distribuerats](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Verifiera datatransformering och överföring

Det slutliga steget är att se till att modulen är ansluten och körs som förväntat. Körningsstatusen för modulen bör vara aktiv för din IoT Edge-enhet i IoT Hub-resursen.

Verifiera att modulen körs genom att göra följande:

1. Välj den **Lägg till modulen** panelen. Då kommer du till den **moduler** bladet. Identifiera den modul som du distribuerat i listan över moduler. Körningsstatus för modulen som du har lagt till ska vara *kör*.

    ![Verifiera datatransformering](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  Anslut till både lokala Edge och Edge-resurser som du skapade tidigare i Utforskaren.

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
