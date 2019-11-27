---
title: Snabb start – distribuera Docker-behållare till container instance – Portal
description: I den här snabb starten använder du Azure Portal för att snabbt distribuera en container-webbapp som körs i en isolerad Azure Container instance
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: e0c5ba57c7664a64c1b11bed215f419f31630d39
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533532"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Snabb start: Distribuera en behållar instans i Azure med hjälp av Azure Portal

Använd Azure Container Instances för att köra Server lös Docker-behållare i Azure med enkelhet och hastighet. Distribuera ett program till en behållar instans på begäran när du inte behöver en fullständig plattform för behållar dirigering som Azure Kubernetes-tjänsten.

I den här snabb starten använder du Azure Portal för att distribuera en isolerad Docker-behållare och göra dess program tillgängligt med ett fullständigt kvalificerat domän namn (FQDN). När du har konfigurerat några inställningar och distribuerat containern kan gå du till programmet som körs:

![Program som distribuerats till Azure Container Instances visas i en webbläsare][aci-portal-07]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][azure-free-account] innan du börjar.

## <a name="create-a-container-instance"></a>Skapa en containerinstans

Välj **Skapa en resurs** > **containrar** > **Containerinstanser**.

![Skapa en ny containerinstans i Azure Portal][aci-portal-01]

På sidan **grundläggande** anger du följande värden i text rutorna **resurs grupp**, **behållar namn**och **behållar avbildning** . Lämna de övriga standardvärdena oförändrade och välj **OK**.

* Resursgrupp: **Skapa ny** > `myresourcegroup`
* Containernamn: `mycontainer`
* Containeravbildning: `mcr.microsoft.com/azuredocs/aci-helloworld`

![Konfigurera grundläggande inställningar för en ny containerinstans i Azure Portal][aci-portal-03]

I den här snabb starten använder du standard inställningen för **avbildnings typ** **offentlig** för att distribuera den offentliga Microsoft `aci-helloworld`-avbildningen. Den här Linux-avbildningen paketerar en liten webbapp som skrivits i Node. js och som hanterar en statisk HTML-sida.

På sidan **nätverk** anger du en **DNS-** benämning på din behållare. Namnet måste vara unikt inom den Azure-region där du skapar behållar instansen. Din container kan nås offentligt på `<dns-name-label>.<region>.azurecontainer.io`. Om du får felmeddelandet ”DNS name label not available” (DNS-namnetikett inte tillgänglig) kan du prova en annan DNS-namnetikett.

![Konfigurera en ny containerinstans i Azure Portal][aci-portal-04]

Lämna de andra inställningarna till standardinställningarna och välj sedan **Granska + skapa**.

När verifieringen är klar visas en sammanfattning av containerinställningarna. Välj **skapa** för att skicka begäran om distribution av behållare.

![Sammanfattning av inställningar för en ny containerinstans i Azure Portal][aci-portal-05]

När distributionen inleds visas ett meddelande som indikerar att distributionen pågår. Ett nytt meddelande visas när containergruppen har distribuerats.

Öppna översikten för behållar gruppen genom att gå till **resurs grupper** > myresourcegroup ** > ** behållaren. Anteckna den **FQDN** (fullständigt kvalificerade domännamn) på containerinstansen samt dess **Status**.

![Översikt över gruppcontainer i Azure-portalen][aci-portal-06]

När dess **Status***Körs*, navigera till containerns FQDN i webbläsaren.

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-portal-07]

Gratulerar! Du har distribuerat ett offentligt tillgängligt program i Azure Container Instances genom att konfigurera några inställningar.

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

I den här snabb starten skapade du en Azure Container instance från en offentlig Microsoft-avbildning. Om du vill skapa en container på egen hand och distribuera den från ett privat Azure-containerregister går du vidare till självstudien för Azure Container Instances.

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