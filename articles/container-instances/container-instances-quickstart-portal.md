---
title: Snabbstart – distribuera Docker-behållare till Azure Container Instances - Portal
description: I den här snabbstarten använder du Azure portal att snabbt distribuera en behållarbaserad webbapp som körs i en isolerad Azure container-instans
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 008d6d2a9a4a20e9fd083e9e2f009396a7f14df2
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995923"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Snabbstart: Distribuera en behållarinstans i Azure med hjälp av Azure-portalen

Använd Azure Container Instances för att köra serverlös Docker-behållare i Azure med enkelhet och hastighet. Distribuera ett program till en container-instans på begäran när du inte behöver en fullständig behållarplattform orchestration som Azure Kubernetes Service.

I den här snabbstarten använder du Azure-portalen för att distribuera en isolerad dockerbehållare och tillgängliggöra dess program med ett fullständigt kvalificerat domännamn (FQDN). När du har konfigurerat några inställningar och distribuerat containern kan gå du till programmet som körs:

![App som distribuerats via Azure Container Instances visas i webbläsaren][aci-portal-07]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][azure-free-account] konto innan du börjar.

## <a name="create-a-container-instance"></a>Skapa en containerinstans

Välj **Skapa en resurs** > **Behållare** > **Behållarinstanser**.

![Skapa en ny containerinstans i Azure Portal][aci-portal-01]

På den **grunderna** anger du följande värden i den **resursgrupp**, **behållarnamn**, och **behållaravbildning** textrutor. Lämna de övriga standardvärdena oförändrade och välj **OK**.

* Resursgrupp: **Skapa ny** > `myresourcegroup`
* Containernamn: `mycontainer`
* Containeravbildning: `mcr.microsoft.com/azuredocs/aci-helloworld`

![Konfigurera grundläggande inställningar för en ny containerinstans i Azure Portal][aci-portal-03]

I den här snabbstarten använder du standardvärdet **bildtyp** inställningen för den **offentliga** att distribuera offentliga Microsoft `aci-helloworld` bild. Den här Linux-avbildning paket en liten webbapp som skrivits i Node.js som har en statisk HTML-sida.

På den **nätverk** anger en **DNS-Namnetiketten** för behållaren. Namnet måste vara unikt inom Azure-region där du skapar behållarinstansen. Din container kan nås offentligt på `<dns-name-label>.<region>.azurecontainer.io`. Om du får felmeddelandet ”DNS name label not available” (DNS-namnetikett inte tillgänglig) kan du prova en annan DNS-namnetikett.

![Konfigurera en ny containerinstans i Azure Portal][aci-portal-04]

Lämna de andra inställningarna oförändrade och välj sedan **granska + skapa**.

När verifieringen är klar visas en sammanfattning av containerinställningarna. Välj **skapa** att skicka din begäran om distribution av behållare.

![Sammanfattning av inställningar för en ny containerinstans i Azure Portal][aci-portal-05]

När distributionen inleds visas ett meddelande som indikerar att distributionen pågår. Ett nytt meddelande visas när containergruppen har distribuerats.

Öppna översikten för behållargruppen genom att gå till **resursgrupper** > **myresourcegroup** > **mycontainer**. Anteckna den **FQDN** (fullständigt kvalificerade domännamn) på containerinstansen samt dess **Status**.

![Översikt över gruppcontainer i Azure-portalen][aci-portal-06]

När dess **Status***Körs*, navigera till containerns FQDN i webbläsaren.

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-portal-07]

Grattis! Du har distribuerat ett offentligt tillgängligt program i Azure Container Instances genom att konfigurera några inställningar.

## <a name="view-container-logs"></a>Visa containerloggar

Att visa loggar för en containerinstans är användbart när du felsöker problem med din container eller det program som den kör.

Visa containerns loggar under **Inställningar** och välj sedan **Containrar** och **Loggar**. Du bör se HTTP GET-begäran som genereras när du har granskat programmet i webbläsaren.

![Containerloggar i Azure-portalen][aci-portal-11]

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med behållaren väljer du **Översikt** för behållarinstansen *mycontainer* och väljer sedan **Ta bort**.

![Ta bort containerinstansen i Azure Portal][aci-portal-09]

Välj **Ja** i bekräftelsedialogrutan när den visas.

![Borttagningsbekräftelse för en containerinstans i Azure Portal][aci-portal-10]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett Azure container-instans från en offentlig avbildning av Microsoft. Om du vill skapa en container på egen hand och distribuera den från ett privat Azure-containerregister går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudie](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/