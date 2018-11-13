---
title: Snabbstart – skapa ett privat Docker-register i Azure med Azure-portalen
description: Lär dig snabbt att skapa ett privat Docker-containerregister med Azure-portalen.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 1e039c465bf37e0ee5ca1db5837798680e27463d
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278675"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-portal"></a>Snabbstart: Skapa ett containerregister med hjälp av Azure Portal

Ett Azure-containerregister är ett privat Docker-register i Azure där du kan lagra och hantera dina privata Docker-containeravbildningar. I den här snabbstarten skapar du ett behållarregister med Azure-portalen, push-överföra en behållaravbildning till registret och slutligen distribuera behållaren från ditt register till Azure Container Instances (ACI).

För att kunna slutföra den här snabbstarten måste du ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-container-registry"></a>Skapa ett containerregister

Välj **Skapa en resurs** > **Containers** > **Container Registry**.

![Skapa ett containerregister i Azure-portalen][qs-portal-01]

Ange värden för **Registernamn** och **Resursgrupp**. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. För den här snabbstarten skapar du en ny resursgrupp på platsen `West US` med namnet `myResourceGroup`, och för **SKU** väljer du ”Basic”. Välj **Skapa** för att distribuera ACR-instansen.

![Skapa ett containerregister i Azure-portalen][qs-portal-03]

I den här snabbstarten skapar vi ett *Grundläggande* register. Azure Container Registry finns i flera olika SKU:er, som beskrivs kortfattat i följande tabell. Mer information om var och en finns i [SKU:er för Container Registry][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

När meddelandet **Distribueringen lyckades** visas väljer du containerregistret i portalen och väljer sedan **Åtkomstnycklar**.

![Skapa ett containerregister i Azure-portalen][qs-portal-05]

Under **Administratörsanvändare** väljer du **Aktivera**. Anteckna följande värden:

* Inloggningsserver
* Användarnamn
* lösenord

Du använder de här värdena i kommande steg när du arbetar med registret med Docker CLI.

![Skapa ett containerregister i Azure-portalen][qs-portal-06]

## <a name="log-in-to-acr"></a>Logga in på ACR

Innan du skickar och hämtar containeravbildningar måste du logga in på ACR-instansen. Det gör du med hjälp av kommandot [docker login][docker-login]. Ersätt värdena *användarnamn*, *lösenord* och *inloggningsserver* med de värden du antecknade i föregående steg.

```bash
docker login --username <username> --password <password> <login server>
```

Kommandot returnerar `Login Succeeded` när det har slutförts. Det kan även hända att du ser en säkerhetsvarning som rekommenderar att parametern `--password-stdin` används. Även om användning av denna ligger utanför vad som tas upp i denna artikel rekommenderar vi att du följer denna bästa metod. Se kommandoreferensen [docker login][docker-login] om du vill ha mer information.

## <a name="push-image-to-acr"></a>Push-överföra avbildning till ACR

Du måste först ha en avbildning om du ska kunna push-överföra en avbildning till Azure Container Registry. Kör följande kommando vid behov för att hämta en befintlig avbildning från Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Innan du överför avbildningen till registret måste du tagga avbildningen med namnet på ACR-inloggningsservern. Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt *inloggningsserver* med det inloggningsservernamn du antecknade tidigare. Lägg till ett *lagringsplatsnamn* som **`myrepo`** för att placera din bild i en databas.

```bash
docker tag microsoft/aci-helloworld <login server>/<repository name>/aci-helloworld:v1
```

Använd slutligen [docker push][docker-push] för att överföra avbildningen till ACR-instansen. Ersätt *inloggningsservern* med namnet på inloggningsservern för ACR-instansen och ersätt *databasnamnet* med namnet på lagringsplatsen du använde i föregående kommando.

```bash
docker push <login server>/<repository name>/aci-helloworld:v1
```

Utdata från ett lyckat `docker push`-kommando ser ut ungefär så här:

```
The push refers to repository [specificregistryname.azurecr.io/myrepo/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

## <a name="list-container-images"></a>Visa lista över containeravbildningar

Om du vill visa en lista över avbildningarna i ACR-instansen navigerar du till registret i portalen och väljer **Centrallager**. Välj sedan det lager du skapade med `docker push`.

I det här exemplet har vi valt lagret **aci-helloworld**, och vi kan se avbildningen som är taggad med `v1` under **TAGGAR**.

![Skapa ett containerregister i Azure-portalen][qs-portal-09]

## <a name="deploy-image-to-aci"></a>Distribuera avbildningen till ACI

För att distribuera till en instans från registret måste vi navigera till lagringsplatsen (aci-helloworld) och sedan klicka på de tre punkterna bredvid v1.

![Starta en Azure Container Instance från portalen][qs-portal-10]

En snabbmeny visas. Välj **Kör instans**:

![Starta ACI-snabbmeny][qs-portal-11]

Fyll i **Containernamn**, se till att rätt prenumeration är vald och välj befintlig **Resursgrupp**: ”myResourceGroup”. Kontrollera att alternativen för ”offentlig IP-adress” är aktiverat genom att ange **Ja** och klicka sedan på **OK** att starta Azure Container-instansen.

![Starta ACI-distributionsalternativ][qs-portal-12]

När distributionen inleds visas en förloppsindikator i form av en ikon på instrumentpanelen i portalen. När distributionen är klar panelen uppdateras ikonen och indikerar istället behållargruppen **mycontainer**.

![Status för ACI-distribution][qs-portal-13]

Markera behållargruppen mycontainer för att visa egenskaperna för behållargruppen. Anteckna containergruppens **IP-adress** och containerns **STATUS**.

![ACI-containerinformation][qs-portal-14]

## <a name="view-the-application"></a>Visa programmet

När containern är i tillståndet **Körs** kan du använda din favoritwebbläsare och navigera till IP-adressen som du antecknade i föregående steg om du vill visa programmet.

![Hello World-app i webbläsaren][qs-portal-15]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa dina resurser går du till resursgruppen **myResourceGroup** i portalen. När resursgruppen har lästs in klickar du på **Ta bort resursgrupp** för att ta bort resursgruppen, Azure Container Registry och alla Azure Container Instances.

![Ta bort resursgrupp i Azure-portalen][qs-portal-08]

## <a name="next-steps"></a>Nästa steg

I snabbstarten skapade du ett Azure Container Registry med Azure CLI och startade en instans av den via Azure Container Instances. Fortsätt till självstudien om Azure Container Instances om du vill titta närmare på ACI.

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
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

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
