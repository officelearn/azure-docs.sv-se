---
title: Konfigurera en anpassad Linux-behållare
description: Lär dig hur du konfigurerar en anpassad Linux-behållare i Azure App Service. Den här artikeln visar de vanligaste konfigurations åtgärderna.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280149"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Konfigurera en anpassad Linux-behållare för Azure App Service

Den här artikeln visar hur du konfigurerar en anpassad Linux-behållare som ska köras på Azure App Service.

Den här guiden innehåller viktiga begrepp och instruktioner för skapa behållare av Linux-appar i App Service. Om du aldrig har använt Azure App Service följer du snabb starten och [självstudien](tutorial-custom-docker-image.md) för [egen behållare](quickstart-docker-go.md) först. Det finns också en [snabb start](quickstart-multi-container.md) och [självstudier](tutorial-multi-container-app.md)för flera behållare.

## <a name="configure-port-number"></a>Konfigurera port nummer

Webb servern i din anpassade avbildning kan använda en annan port än 80. Du talar om för Azure om porten som din anpassade behållare använder genom att `WEBSITES_PORT` använda appens inställning. GitHub-sidan för [Python-exemplet i den här självstudien](https://github.com/Azure-Samples/docker-django-webapp-linux) visar att du behöver ställa in `WEBSITES_PORT` på _8000_. Du kan ställa in det genom [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) att köra kommandot i Cloud Shell. Ett exempel:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Din anpassade behållare kan använda miljövariabler som behöver anges externt. Du kan skicka dem genom att köra [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) kommandot i Cloud Shell. Ett exempel:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Den här metoden fungerar både för appar med en container eller flera behållare, där miljövariablerna anges i filen *filen Docker. yml* .

## <a name="use-persistent-shared-storage"></a>Använd beständig delad lagring

Du kan använda */Home* -katalogen i appens fil system för att spara filer mellan omstarter och dela dem över instanser. `/home` I din app kan du använda behållar appen för att få åtkomst till beständig lagring.

När beständig lagring är inaktive rad behålls inte skrivningar till `/home` katalogen i appens omstarter eller över flera instanser. Det enda undantaget är `/home/LogFiles` katalogen som används för att lagra Docker-och container-loggar. När beständig lagring är aktive rad är alla skrivningar `/home` till katalogen bestående och kan nås av alla instanser av en utskalad app.

Beständig lagring är *aktive rad* som standard och inställningen visas inte i program inställningarna. Om du vill inaktivera den anger `WEBSITES_ENABLE_APP_SERVICE_STORAGE` du appens inställning [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) genom att köra kommandot i Cloud Shell. Ett exempel:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Du kan också [Konfigurera en egen beständig lagring](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Aktivera SSH

SSH möjliggör säker kommunikation mellan en container och en klient. För att en anpassad behållare ska stödja SSH måste du lägga till den i själva Dockerfile.

> [!TIP]
> Alla inbyggda Linux-behållare har lagt till SSH-instruktionerna i sina avbildnings databaser. Du kan gå igenom följande instruktioner med [Node. js 10,14-lagringsplatsen](https://github.com/Azure-App-Service/node/blob/master/10.14) för att se hur den är aktive rad där.

- Använd [Kör](https://docs.docker.com/engine/reference/builder/#run) -instruktionen för att installera SSH-servern och ange lösen ordet för rot kontot `"Docker!"`till. För en avbildning som baseras på [Alpine Linux](https://hub.docker.com/_/alpine)behöver du till exempel följande kommandon:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Den här konfigurationen tillåter inte externa anslutningar till behållaren. SSH är endast tillgängligt via `https://<app-name>.scm.azurewebsites.net` och autentiseras med autentiseringsuppgifterna för publicering.

- Lägg till [den här sshd_config-filen](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) till avbildnings lagrings platsen och Använd [kopierings](https://docs.docker.com/engine/reference/builder/#copy) instruktionen för att kopiera filen till */etc/ssh/* -katalogen. Mer information om *sshd_config* -filer finns i [OpenBSD-dokumentationen](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Filen *sshd_config* måste innehålla följande objekt:
    > - `Ciphers`måste innehålla minst ett objekt i listan: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`måste innehålla minst ett objekt i listan: `hmac-sha1,hmac-sha1-96`.

- Använd instruktionen [exponera](https://docs.docker.com/engine/reference/builder/#expose) för att öppna port 2222 i behållaren. Även om rot lösen ordet är känt är port 2222 inte tillgänglig från Internet. Den är endast tillgänglig för behållare i brygga nätverket i ett privat virtuellt nätverk.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Starta SSH-servern i Start skriptet för din behållare.

    ```bash
    /usr/sbin/sshd
    ```

    Ett exempel finns i hur [noden default Node. js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) startar SSH-servern.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurera appar med flera behållare

- [Använd beständigt lagrings utrymme i Docker Compose](#use-persistent-storage-in-docker-compose)
- [Begränsningar för förhandsversion](#preview-limitations)
- [Docker Skriv alternativ](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Använd beständigt lagrings utrymme i Docker Compose

Appar med flera behållare som WordPress behöver beständig lagring för att fungera korrekt. För att aktivera den måste Docker-konfigurationen peka på en lagrings plats *utanför* din behållare. Lagrings platser i din behållare har inte kvar ändringar än starta om appar.

Aktivera beständig lagring genom att ställa `WEBSITES_ENABLE_APP_SERVICE_STORAGE` in appens inställning med hjälp av kommandot [AZ webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

I filen *filen Docker. yml* mappar du `volumes` alternativet till `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` är en miljövariabel i App Service som är mappad till beständig lagring för din app. Ett exempel:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Begränsningar för förhandsversion

Multi-container är för närvarande en för hands version. Följande App Service plattforms funktioner stöds inte:

- Autentisering/auktorisering
- Hanterade identiteter

### <a name="docker-compose-options"></a>Docker Skriv alternativ

I följande listor visas en Docker-konfigurations alternativ som stöds och inte stöds:

#### <a name="supported-options"></a>Alternativ som stöds

- command
- entrypoint
- miljö
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Alternativ som inte stöds

- build (inte tillåtet)
- depends_on (ignoreras)
- networks (ignoreras)
- secrets (ignoreras)
- andra portar än 80 och 8080 (ignoreras)

> [!NOTE]
> Andra alternativ som inte uttryckligen anropas ignoreras i den offentliga för hands versionen.

## <a name="configure-vnet-integration"></a>Konfigurera VNet-integrering

Användning av en anpassad behållare med VNet-integrering kan kräva ytterligare container konfiguration. Se [integrera din app med en Azure-Virtual Network](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Distribuera från privat container-lagringsplats](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Självstudie: WordPress-app med flera behållare](tutorial-multi-container-app.md)
