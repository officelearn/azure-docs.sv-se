---
title: Lägga till en Azure IoT Edge-enhet i Azure IoT Central | Microsoft Docs
description: Som operatör lägger du till en Azure IoT Edge enhet till ditt Azure IoT Central-program
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e5d60c77e9bdc0733c12bca891eb6c3e33a1fceb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979078"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Självstudie: Lägg till en Azure IoT Edge enhet till ditt Azure IoT Central-program

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här självstudien visar hur du lägger till och konfigurerar en Azure IoT Edge-enhet till ditt Azure IoT Central-program. I den här självstudien valde vi en IoT Edge-aktiverad virtuell Linux-dator från Azure Marketplace.

Den här självstudien består av två delar:

* För det första, som en operatör, lär du dig att göra molnets första etablering av en IoT Edge enhet.
* Sedan kan du lära dig att "enhetens första etablering" av en IoT Edge enhet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägg till en ny IoT Edge enhet
> * Konfigurera IoT Edge enheten så att den kan etableras med hjälp av en nyckel för signatur för delad åtkomst (SAS)
> * Visa instrument paneler och modulens hälso tillstånd i IoT Central
> * Skicka kommandon till en modul som körs på den IoT Edge enheten
> * Ange egenskaper för en modul som körs på den IoT Edge enheten

## <a name="prerequisites"></a>Krav

Du behöver ett Azure IoT Central-program för att kunna genomföra den här självstudien. Följ [den här snabb starten för att skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Aktivera Azure IoT Edge registrerings grupp
På sidan **Administration** aktiverar du SAS-nycklar för Azure IoT Edge registrerings grupp.

![Skärm bild av administrations sidan med enhets anslutning markerad](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>Etablera en Azure IoT Edge enhet med "första molnet"  
I det här avsnittet skapar du en ny IoT Edge enhet med hjälp av miljö sensor mal len och du etablerar en enhet. Välj **enheter** > **miljö sensor mal len**. 

![Skärm bild av sidan enheter, där miljö sensor mal len är markerad](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Välj **+ ny**och ange ett enhets-ID och ett namn som du väljer. Välj **Skapa**.

![Skärm bild av dialog rutan skapa ny enhet med enhets-ID och skapa markerat](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Enheten övergår i **registrerat** läge.

![Skärm bild av sidan för miljö sensor med enhets status markerad](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>Distribuera en IoT Edge aktive rad virtuell Linux-dator

> [!NOTE]
> Du kan välja att använda valfri dator eller enhet. Operativ systemet kan vara Linux eller Windows.

I den här självstudien använder vi en Azure IoT-aktiverad virtuell Linux-dator som skapats på Azure. I [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)väljer **du Hämta nu**. 

![Skärm bild av Azure Marketplace, med Hämta nu markerat](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Välj **Fortsätt**.

![Skärm bild av dialog rutan skapa den här appen i Azure med Fortsätt markerat](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Du kommer till Azure Portal. Välj **Skapa**.

![Skärm bild av Azure Portal, med skapa markerat](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Välj **prenumeration**, skapa en ny resurs grupp och välj **(US) västra USA 2** för tillgänglighet för virtuella datorer. Ange sedan användar-och lösen ords information. De krävs för framtida steg, så kom ihåg dem. Välj **Granska + skapa**.

![Skärm bild av sidan Skapa en virtuell dator information med olika alternativ markerade](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

När du har validerat väljer du **skapa**.

![Skärm bild av sidan Skapa en virtuell dator med verifieringen klar och skapa markerad](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Det tar några minuter att skapa resurserna. Välj **Gå till resurs**.

![Skärm bild av sidan distributions slut för ande med gå till resurs markerad](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>Etablera virtuell dator som en IoT Edge enhet 

Under **support + fel sökning**väljer du **seriell konsol**.

![Skärm bild av support och fel söknings alternativ med Seriell konsol markerat](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Du ser en skärm som liknar följande:

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Tryck på RETUR, Ange användar namnet och lösen ordet som uppmaning och tryck sedan på RETUR igen. 

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Om du vill köra ett kommando som administratör (användarens rot) anger du: **sudo su –**

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Kontrol lera IoT Edge runtime-versionen. När detta skrivs är den aktuella GA-versionen 1.0.8.

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Installera vim-redigeraren eller Använd nano om du föredrar det. 

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Redigera filen IoT Edge config. yaml.

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Rulla nedåt och kommentera ut anslutnings sträng delen av yaml-filen. 

**Före**

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Efter** (tryck på ESC och tryck på gemener, för att börja redigera.)

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Ta bort kommentaren till den symmetriska nyckel delen av yaml-filen. 

**Före**

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Efter**

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Gå till IoT Central. Hämta omfångs-ID, enhets-ID och symmetrisk nyckel för den IoT Edge enheten.
![skärm bild av IoT Central med olika anslutnings alternativ för enheter markerade](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Gå till Linux-datorn och ersätt omfångs-ID och registrerings-ID med enhets-ID och symmetrisk nyckel.

Tryck på ESC och skriv **: Wq!** . Spara ändringarna genom att trycka på RETUR.

Starta om IoT Edge för att bearbeta ändringarna och tryck på RETUR.

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Skriv **iotedge-lista**. Efter några minuter ser du att tre moduler har distribuerats.

![Skärm bild av konsolen](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Device Explorer 

I IoT Central flyttas enheten till etablerad status.

![Skärm bild av alternativen för IoT Central enheter med enhets status markerad](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

På fliken **moduler** visas status för enheten och modulen på IoT Central. 

![Skärm bild av fliken IoT Central moduler](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Du ser moln egenskaper i ett formulär från den enhets mall som du skapade i föregående steg. Ange värden och välj **Spara**. 

![Skärm bild av mitt Linux Edge Device-formulär](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Här är en vy som presenteras i form av en panel för instrument paneler.

![Skärm bild av mina Linux Edge-paneler på instrument panelen](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du arbetar med och hanterar IoT Edge enheter i IoT Central, är följande det föreslagna nästa steg:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Konfigurera transparent Gateway](../../iot-edge/how-to-create-transparent-gateway.md)
