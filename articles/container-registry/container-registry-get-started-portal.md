---
title: Snabbstart - skapa ett privat Docker-register i Azure med Azure-portalen
description: "Snabbt lära dig skapa ett privat Docker behållare register med Azure-portalen."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 514fa3490e480647f0923c99bd9606a3726d4d30
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Skapa ett behållarregister med hjälp av Azure Portal

En Azure-behållaren är en privat Docker-registret i Azure kan du lagra och hantera dina privata Docker behållare avbildningar. I Snabbstart skapar du en behållare registret med Azure-portalen.

För att slutföra den här snabbstarten, måste du ha Docker installeras lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac-](https://docs.docker.com/docker-for-mac/), [Windows-](https://docs.docker.com/docker-for-windows/) eller [Linux-](https://docs.docker.com/engine/installation/#supported-platforms)dator.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på https://portal.azure.com.

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

Välj **nya** > **behållare** > **Azure-behållaren registret**.

![Skapa en behållare registret i Azure-portalen][qs-portal-01]

Ange värden för **registrets** och **resursgruppen**. Registret namnet måste vara unikt i Azure och innehålla 5 50 alfanumeriska tecken. Skapa en ny resursgrupp med namnet `myResourceGroup`, och för **SKU**, Välj ”Basic”. Välj **skapa** att distribuera ACR-instans.

![Skapa en behållare registret i Azure-portalen][qs-portal-03]

I den här snabbstarten skapar vi en *grundläggande* registret. Azure Container registret är tillgänglig i flera olika SKU: er, beskrivs kortfattat i följande tabell. Utökad information om varje finns [behållare registret SKU: er](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

När den **distributionen lyckades** meddelandet som visas väljer du behållare registret i portalen och markerar **åtkomstnycklar**.

![Skapa en behållare registret i Azure-portalen][qs-portal-05]

Under **administratörsanvändare**väljer **aktivera**. Anteckna följande värden:

* Inloggningsserver
* Användarnamn
* lösenord

Du kan använda dessa värden i följande steg när du arbetar med registret med Docker CLI.

![Skapa en behållare registret i Azure-portalen][qs-portal-06]

## <a name="log-in-to-acr"></a>Logga in på ACR

Innan du skickar och hämtar behållaravbildningar måste du logga in på ACR-instansen. Det gör du genom att använda den [docker inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommando. Ersätt den *användarnamn*, *lösenord*, och *inloggningsserver* värden med de som du antecknade i föregående steg.

```bash
docker login --username <username> --password <password> <login server>
```

Kommandot returnerar `Login Succeeded` när den har slutförts. Du kan också se en säkerhetsvarning rekommenderar användning av den `--password-stdin` parameter. När användningen är utanför omfånget för den här artikeln, rekommenderar vi följande denna bästa praxis. Finns det [docker inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot referens för mer information.

## <a name="push-image-to-acr"></a>Push-avbildningen till ACR

Du måste ha en bild för att vidarebefordra en bild i registret för Azure-behållare. Om det behövs, kör du följande kommando för att hämta en befintlig avbildning från Docker-hubb.

```bash
docker pull microsoft/aci-helloworld
```

Innan du trycker på bilden i registret, måste du tagga avbildningen med ACR server inloggningsnamnet. Taggen bild med hjälp av den [docker-taggen](https://docs.docker.com/engine/reference/commandline/tag/) kommando. Ersätt *inloggningsserver* med inloggningsnamnet för servern som du antecknade tidigare.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Använd slutligen [docker push](https://docs.docker.com/engine/reference/commandline/push/) att skicka bilden till ACR-instans. Ersätt *inloggningsserver* med inloggningen servernamnet för din ACR-instans.

```
docker push <login server>/aci-helloworld:v1
```

Utdata från en lyckad `docker push` kommandot liknar:

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Visa lista över behållaravbildningar

Om du vill visa bilder i din ACR-instans, navigera till registret i portalen och välj **databaser**, sedan markerar du databasen som du skapat med `docker push`.

I det här exemplet väljer vi den **aci helloworld** databasen, och vi kan se den `v1`-tagged image under **taggar**.

![Skapa en behållare registret i Azure-portalen][qs-portal-09]

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort den **myResourceGroup** resursgruppen. Detta tar bort resursgruppen, ACR-instans och alla bilder i behållaren.

![Skapa en behållare registret i Azure-portalen][qs-portal-08]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapat du ett Azure Container registret med Azure CLI. Om du vill använda Azure Container registret med Azure Container instanser fortsätta att Azure Behållarinstanser kursen.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudier](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png