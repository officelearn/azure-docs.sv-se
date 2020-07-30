---
title: Snabb start – distribuera Docker-behållare till container instance – Portal
description: I den här snabb starten använder du Azure Portal för att snabbt distribuera en container-webbapp som körs i en isolerad Azure Container instance
ms.topic: quickstart
ms.date: 03/09/2020
ms.custom: seodec18, mvc, devx-track-javascript
ms.openlocfilehash: 5f36c81abd462f6dfd59c42da71e096e07206cae
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408075"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Snabb start: Distribuera en behållar instans i Azure med hjälp av Azure Portal

Använd Azure Container Instances för att köra Server lös Docker-behållare i Azure med enkelhet och hastighet. Distribuera ett program till en behållar instans på begäran när du inte behöver en fullständig plattform för behållar dirigering som Azure Kubernetes-tjänsten.

I den här snabb starten använder du Azure Portal för att distribuera en isolerad Docker-behållare och göra dess program tillgängligt med ett fullständigt kvalificerat domän namn (FQDN). När du har konfigurerat några inställningar och distribuerat containern kan gå du till programmet som körs:

![Program som distribuerats till Azure Container Instances visas i en webbläsare][aci-portal-07]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto][azure-free-account] innan du börjar.

## <a name="create-a-container-instance"></a>Skapa en containerinstans

Välj **skapa en resurs**  >  **behållare**  >  **container instances**.

![Skapa en ny containerinstans i Azure Portal][aci-portal-01]

På sidan **grundläggande** anger du följande värden i text rutorna **resurs grupp**, **behållar namn**och **behållar avbildning** . Lämna de övriga standardvärdena oförändrade och välj **OK**.

* Resurs grupp: **Skapa ny** > `myresourcegroup`
* Containernamn: `mycontainer`
* Avbildnings Källa: **snabb starts avbildningar**
* Behållar avbildning: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

![Konfigurera grundläggande inställningar för en ny containerinstans i Azure Portal][aci-portal-03]

I den här snabb starten använder du standardinställningar för att distribuera den offentliga Microsoft- `aci-helloworld` avbildningen. Det här exemplet på Linux-avbildningar är en liten webbapp som skrivits i Node.js som hanterar en statisk HTML-sida. Du kan också ta med dina egna behållar avbildningar lagrade i Azure Container Registry, Docker Hub eller andra register.

På sidan **nätverk** anger du en **DNS-** benämning på din behållare. Namnet måste vara unikt inom den Azure-region där du skapar behållar instansen. Din container kan nås offentligt på `<dns-name-label>.<region>.azurecontainer.io`. Om du får felmeddelandet ”DNS name label not available” (DNS-namnetikett inte tillgänglig) kan du prova en annan DNS-namnetikett.

![Konfigurera nätverks inställningar för en ny behållar instans i Azure Portal][aci-portal-04]

Lämna de andra inställningarna till standardinställningarna och välj sedan **Granska + skapa**.

När verifieringen är klar visas en sammanfattning av containerinställningarna. Välj **skapa** för att skicka begäran om distribution av behållare.

![Sammanfattning av inställningar för en ny containerinstans i Azure Portal][aci-portal-05]

När distributionen startar visas ett meddelande som anger att distributionen pågår. Ett nytt meddelande visas när containergruppen har distribuerats.

Öppna översikten för behållar gruppen genom att gå till **resurs grupper**  >  **myresourcegroup**  >  **-behållare**. Anteckna den **FQDN** (fullständigt kvalificerade domännamn) på containerinstansen samt dess **Status**.

![Översikt över gruppcontainer i Azure-portalen][aci-portal-06]

När dess **Status***Körs*, navigera till containerns FQDN i webbläsaren.

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-portal-07]

Grattis! Du har distribuerat ett offentligt tillgängligt program i Azure Container Instances genom att konfigurera några inställningar.

## <a name="view-container-logs"></a>Visa containerloggar

Att visa loggar för en containerinstans är användbart när du felsöker problem med din container eller det program som den kör.

Om du vill visa behållarens loggar väljer du **behållare**under **Inställningar**och **loggar**sedan. Du bör se HTTP GET-begäran som genereras när du har granskat programmet i webbläsaren.

![Containerloggar i Azure-portalen][aci-portal-11]

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med behållaren väljer du **Översikt** för behållarinstansen *mycontainer* och väljer sedan **Ta bort**.

![Ta bort containerinstansen i Azure Portal][aci-portal-09]

Välj **Ja** i bekräftelsedialogrutan när den visas.

![Borttagningsbekräftelse för en containerinstans i Azure Portal][aci-portal-10]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Azure Container instance från en offentlig Microsoft-avbildning. Om du vill skapa en container på egen hand och distribuera den från ett privat Azure-containerregister går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Självstudie för Azure Container Instances](./container-instances-tutorial-prepare-app.md)

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