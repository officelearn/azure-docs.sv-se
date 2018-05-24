---
title: Snabbstart – Skapa din första Azure Container Instances-behållare med Azure Portal
description: I den här snabbstarten använder du Azure Portal för att distribuera en behållare i Azure Container Instances
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6aa6fb27b2aa7c8b9614e5812fadc629b1e185f8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Snabbstart: Skapa din första behållare i Azure Container Instances

Azure Container Instances gör det enkelt att skapa och hantera Docker-behållare i Azure, utan att behöva etablera virtuella datorer eller gå upp till en högre tjänstnivå. I den här snabbstarten använder du Azure Portal för att skapa en behållare i Azure och gör den tillgänglig på Internet med ett fullständigt kvalificerat domännamn (FQDN). När du har konfigurerat några inställningar visas detta i webbläsaren:

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-portal-07]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][azure-free-account] konto innan du börjar.

## <a name="create-a-container-instance"></a>Skapa en behållarinstans

Välj **Skapa en resurs** > **Behållare** > **Behållarinstanser**.

![Skapa en ny behållarinstans i Azure Portal][aci-portal-01]

Ange följande värden i textfälten **Behållarnamn**, **Behållaravbildning** och **Resursgrupp**. Lämna de övriga standardvärdena oförändrade och välj **OK**.

* Behållarnamn: `mycontainer`
* Behållaravbildning: `microsoft/aci-helloworld`
* Resursgrupp: `myResourceGroup`

![Konfigurera grundläggande inställningar för en ny behållarinstans i Azure Portal][aci-portal-03]

Du kan skapa både Windows- och Linux-behållare i Azure Container Instances. I den här snabbstarten lämnar du standardinställningen för **Linux** för att distribuera den Linux-baserade `microsoft/aci-helloworld` avbildningen.

Under **Konfiguration** anger du en **DNS-namnetikett** för din behållare. Namnet måste vara unikt i den Azure-region där du skapar behållarinstansen. Din behållare kan nås offentligt på `<dns-name-label>.<region>.azurecontainer.io`.

Lämna de övriga standardinställningarna i **Konfiguration** oförändrade och klicka sedan på **OK** att bekräfta konfigurationen.

![Konfigurera en ny behållarinstans i Azure Portal][aci-portal-04]

När verifieringen är klar visas en sammanfattning av behållarinställningarna. Välj **OK** för att skicka din begäran om distribution av behållare.

![Sammanfattning av inställningar för en ny behållarinstans i Azure Portal][aci-portal-05]

När distributionen inleds visas en förloppsindikator i form av en ikon på instrumentpanelen i portalen. När den har distribuerats visar panelen din nya behållarinstans.

![Förloppsindikator under tillkomsten av en ny behållarinstans i Azure Portal][aci-portal-08]

Välj en **mycontainer**-behållarinstans för att visa dess egenskaper. Anteckna den **FQDN** (fullständigt kvalificerade domännamn) på behållarinstansen samt dess **Status**.

![Översikt över gruppbehållare i Azure-portalen][aci-portal-06]

När dess **Status** *Körs*, navigera till behållarens FQDN i webbläsaren.

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-portal-07]

Grattis! Du har distribuerat ett offentligt tillgängligt program i Azure Containe Instances genom att konfigurera några inställningar.

## <a name="view-container-logs"></a>Visa behållarloggar

Att visa loggar för en behållarinstans är användbart när du felsöker problem med din behållare eller det program som den kör.

Visa behållarens loggar under **INSTÄLLNINGAR** och välj sedan **Behållare** och **Loggar**. Du bör se HTTP GET-begäran som genereras när du har granskat programmet i webbläsaren.

![Behållarloggar i Azure-portalen][aci-portal-11]

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med behållaren väljer du **Översikt** för behållarinstansen *mycontainer* och väljer sedan **Ta bort**.

![Ta bort behållarinstansen i Azure Portal][aci-portal-09]

Välj **Ja** i bekräftelsedialogrutan när den visas.

![Borttagningsbekräftelse för en behållarinstans i Azure Portal][aci-portal-10]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en Azure-behållarinstans utifrån en avbildning som finns i det offentliga Docker Hub-registret. Om du vill skapa en behållare på egen hand och distribuera den till Azure Container Instances från ett privat Azure-behållarregister, går du vidare till självstudien för Azure Container Instances.

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