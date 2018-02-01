---
title: "Snabbstart – skapa ett privat Docker-register i Azure med Azure-portalen"
description: "Lär dig snabbt att skapa ett privat Docker-behållarregister med Azure-portalen."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/06/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: eaf935c1060e53673351936111083d8bb44f05e7
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Skapa ett behållarregister med hjälp av Azure Portal

Ett Azure-behållarregister är ett privat Docker-register i Azure där du kan lagra och hantera dina privata Docker-behållaravbildningar. I den här snabbstarten skapar du ett behållarregister med Azure-portalen.

För att kunna slutföra den här snabbstarten måste du ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

Välj **Ny** > **Behållare** > **Azure Container Registry**.

![Skapa ett behållarregister i Azure-portalen][qs-portal-01]

Ange värden för **Registernamn** och **Resursgrupp**. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Skapa en ny resursgrupp med namnet `myResourceGroup` och välj ”Grundläggande” för **SKU**. Välj **Skapa** för att distribuera ACR-instansen.

![Skapa ett behållarregister i Azure-portalen][qs-portal-03]

I den här snabbstarten skapar vi ett *Grundläggande* register. Azure Container Registry finns i flera olika SKU:er, som beskrivs kortfattat i följande tabell. Mer information om var och en finns i [SKU:er för Container Registry][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

När meddelandet **Distribueringen lyckades** väljer du behållarregistret i portalen och väljer sedan **Åtkomstnycklar**.

![Skapa ett behållarregister i Azure-portalen][qs-portal-05]

Under **Administratörsanvändare** väljer du **Aktivera**. Anteckna följande värden:

* Inloggningsserver
* Användarnamn
* lösenord

Du använder de här värdena i kommande steg när du arbetar med registret med Docker CLI.

![Skapa ett behållarregister i Azure-portalen][qs-portal-06]

## <a name="log-in-to-acr"></a>Logga in på ACR

Innan du skickar och hämtar behållaravbildningar måste du logga in på ACR-instansen. Det gör du med hjälp av kommandot [docker login][docker-login]. Ersätt värdena *användarnamn*, *lösenord* och *inloggningsserver* med de värden du antecknade i föregående steg.

```bash
docker login --username <username> --password <password> <login server>
```

Kommandot returnerar `Login Succeeded` när det har slutförts. Det kan även hända att du ser en säkerhetsvarning som rekommenderar att parametern `--password-stdin` används. Även om användning av denna ligger utanför vad som tas upp i denna artikel rekommenderar vi att du följer denna bästa metod. Se kommandoreferensen [docker login][docker-login] om du vill ha mer information.

## <a name="push-image-to-acr"></a>Push-överföra avbildning till ACR

Du måste först ha en avbildning om du ska kunna push-överföra en avbildning till Azure Container Registry. Kör följande kommando vid behov för att hämta en befintlig avbildning från Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Innan du överför avbildningen till registret måste du tagga avbildningen med namnet på ACR-inloggningsservern. Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt *inloggningsserver* med det inloggningsservernamn du antecknade tidigare.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Använd slutligen [docker push][docker-push] för att överföra avbildningen till ACR-instansen. Ersätt *inloggningsserver* med inloggningsservernamnet för ACR-instansen.

```
docker push <login server>/aci-helloworld:v1
```

Utdata från ett lyckat `docker push`-kommando ser ut ungefär så här:

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

Om du vill visa en lista över avbildningarna i ACR-instansen navigerar du till registret i portalen och väljer **Centrallager**. Välj sedan det lager du skapade med `docker push`.

I det här exemplet har vi valt lagret **aci-helloworld**, och vi kan se avbildningen som är taggad med `v1` under **TAGGAR**.

![Skapa ett behållarregister i Azure-portalen][qs-portal-09]

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resursgruppen **myResourceGroup**. Då raderas resursgruppen, ACR-instansen och alla behållaravbildningar.

![Skapa ett behållarregister i Azure-portalen][qs-portal-08]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Container Registry med Azure-portalen. Om du vill använda Azure Container Registry med Azure Container Instances fortsätter du till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudier][container-instances-tutorial-prepare-app]

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

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
