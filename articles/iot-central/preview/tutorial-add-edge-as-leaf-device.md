---
title: Lägga till en Azure IoT Edge-enhet i Azure IoT Central | Microsoft Docs
description: Lägg till en Azure IoT Edge enhet till Azure-IoT Central som operatör
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893482"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Självstudie: Lägg till en Azure IoT Edge enhet till ditt Azure IoT Central-program (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

I den här självstudien lär du dig hur du lägger till och konfigurerar en *Azure IoI Edge-enhet* i ditt Microsoft Azure IoT Central program. I den här självstudien valde vi en Azure IoT Edge aktive rad Linux-dator från Azure Marketplace.

Den här självstudien består av två delar:

* För det första, som en operatör, lär du dig att göra molnets första etablering av en Azure IoT Edge enhet.
* Sedan får du lära dig hur du utför enheten första etablering av en Azure IoT Edge enhet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägg till en ny Azure IoT Edge enhet
> * Konfigurera den Azure IoT Edge enheten så att den kan etableras med SAS-nyckel
> * Visa instrument paneler, modul hälsa i IoT Central
> * Skicka kommandon till en modul som körs på den Azure IoT Edge enheten
> * Ange egenskaper för en modul som körs på den Azure IoT Edge enheten

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver ett Azure IoT Central-program för att kunna genomföra den här självstudien. Följ den här snabb starten för att [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Aktivera Azure IoT Edge registrerings grupp
Aktivera SAS-nycklar för Azure IoT Edge registrerings grupp från sidan Administration.

![Enhets mall – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>Molnets första Azure IoT Edge enhets etablering   
I det här avsnittet ska du skapa en ny Azure IoT Edge enhet med hjälp av **miljö sensor mal len** och tillhandahålla en enhet. Klicka på enheter i den vänstra navigeringen och klicka på mallen för miljö sensorn. 

![Enhets mall – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Klicka på **+ ny** och ange ett enhets-ID och namn som passar dig. 

![Enhets mall – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Enheten övergår i **registrerat** läge.

![Enhets mall – Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Distribuera en Azure IoT Edge aktive rad virtuell Linux-dator

>Obs: du kan välja att använda valfri dator eller enhet. OS: Linux eller Windows)

I den här självstudien valde vi en Azure IoT-aktiverad virtuell Linux-dator, som kan skapas på Azure. Du kommer att gå till [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) och klicka på **Hämta nu** . 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Klicka på **Fortsätt**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Du kommer att Azure Portal. Klicka på knappen **skapa**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Välj prenumeration, skapa en ny resurs grupp helst, Välj USA, väst 2 för VM-tillgänglighet, ange användare och lösen ord. Kom ihåg att användare, lösen ord krävs för kommande steg. Klicka på **Granska + skapa**

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

När du har validerat klickar du på **skapa**

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Det tar några minuter att skapa resurserna. Klicka på gå till **resurs**

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Etablera virtuell dator som Azure IoT Edge enhet 

Klicka på Seriell konsol under support + fel sökning i det vänstra navigerings fönstret

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

En skärm visas som nedan

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Tryck på RETUR och ange användar namn och lösen ord som uppmaning och tryck på RETUR. 

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Köra ett kommando som administratör/rot kör kommandot: **sudo su –**

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Kontrol lera Azure IoT Edge körnings version. Den aktuella GA-versionen är 1.0.8

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Installera vim-redigeraren eller Använd nano om det är din preferens. 

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Redigera Azure IoT Edge config. yaml-fil

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Rulla ned och kommentera ut anslutnings sträng delen av yaml-filen. 

**Framför**

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Efter** (tryck på ESC och tryck på gemener a för att börja redigera)

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Avkommentera symmetrisk nyckel del av yaml-filen. 

**Framför**

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**När**

![Virtuell Ubuntu-dator](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Gå till IoT Central och hämta omfångs-ID, enhets-ID och symmetrisk nyckel för Azure IoT Edge enhets ![enhet Connect](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Gå till rutan Linux och ersätt scope-ID, registrerings-ID med enhets-ID och symmetrisk nyckel

Tryck på **ESC** och skriv **: Wq!** och tryck på **RETUR** för att spara ändringarna

Starta om Azure IoT Edge för att bearbeta ändringarna och tryck på **RETUR**

![Anslut enhet](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Skriv: **iotedge List**, det tar några minuter. du ser tre moduler distribuerade

![Anslut enhet](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Device Explorer 

I IoT Central enheten flyttas till ett tillstånd som är etablerad

![Anslut enhet](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

Fliken moduler visar status för enheten och modulen på IoT Central 

![Anslut enhet](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Moln egenskaper visas i ett formulär (från den enhets mall som du skapade i föregående steg). Ange värden och klicka på **Spara**. 

![Anslut enhet](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Instrument panels panel

![Anslut enhet](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

I den här självstudiekursen lärde du dig att:

* Lägg till en ny Azure IoT Edge enhet
* Konfigurera den Azure IoT Edge enheten så att den kan etableras med SAS-nyckel
* Visa instrument paneler, modul hälsa i IoT Central
* Skicka kommandon till en modul som körs på den Azure IoT Edge enheten
* Ange egenskaper för en modul som körs på den Azure IoT Edge enheten

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du arbetar med Hantera Azure IoT Edge enheter i IoT Central, är det här det föreslagna nästa steg:

<!-- Next how-tos in the sequence -->

Konfigurera en transparent Gateway genom att följa den här självstudien

> [!div class="nextstepaction"]
> [Konfigurera transparent Gateway](../../iot-edge/how-to-create-transparent-gateway.md)
