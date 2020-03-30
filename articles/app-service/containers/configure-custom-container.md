---
title: Konfigurera en anpassad Linux-behållare
description: Lär dig hur du konfigurerar en anpassad Linux-behållare i Azure App Service. Den här artikeln visar de vanligaste konfigurationsuppgifterna.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280149"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Konfigurera en anpassad Linux-behållare för Azure App Service

Den här artikeln visar hur du konfigurerar en anpassad Linux-behållare för att köras på Azure App Service.

Den här guiden innehåller viktiga begrepp och instruktioner för containerisering av Linux-appar i App Service. Om du aldrig har använt Azure App Service följer du den [anpassade snabbstarten](quickstart-docker-go.md) och [självstudien för behållaren](tutorial-custom-docker-image.md) först. Det finns också en [multi-container app snabbstart](quickstart-multi-container.md) och [handledning](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Konfigurera portnummer

Webbservern i den anpassade avbildningen kan använda en annan port än 80. Du berättar för Azure om porten som `WEBSITES_PORT` din anpassade behållare använder med hjälp av appinställningen. GitHub-sidan för [Python-exemplet i den här självstudien](https://github.com/Azure-Samples/docker-django-webapp-linux) visar att du behöver ställa in `WEBSITES_PORT` på _8000_. Du kan ställa [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in den genom att köra kommandot i Cloud Shell. Ett exempel:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Din anpassade behållare kan använda miljövariabler som måste levereras externt. Du kan skicka in [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dem genom att köra kommandot i Cloud Shell. Ett exempel:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Den här metoden fungerar både för appar med en behållare eller appar med flera behållare, där miljövariablerna anges i *filen docker-compose.yml.*

## <a name="use-persistent-shared-storage"></a>Använda beständig delad lagring

Du kan använda *arbetskatalogen* i appens filsystem för att spara filer över omstarter och dela dem mellan instanser. I `/home` din app tillhandahålls för att göra det möjligt för behållarappen att komma åt beständig lagring.

När beständig lagring är inaktiverad `/home` sparas inte skrivningar till katalogen över omstarter av appar eller över flera instanser. Det enda undantaget `/home/LogFiles` är katalogen, som används för att lagra Docker och behållarloggar. När beständig lagring är aktiverat sparas alla skrivningar till katalogen `/home` och kan nås av alla instanser av en utskalad app.

Som standard är beständig lagring *aktiverad* och inställningen visas inte i programinställningarna. Om du vill `WEBSITES_ENABLE_APP_SERVICE_STORAGE` inaktivera den [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) ställer du in appinställningen genom att köra kommandot i Cloud Shell. Ett exempel:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Du kan också [konfigurera din egen beständiga lagring](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Aktivera SSH

SSH möjliggör säker kommunikation mellan en container och en klient. För att en anpassad behållare ska stödja SSH måste du lägga till den i själva Dockerfile.

> [!TIP]
> Alla inbyggda Linux-behållare har lagt till SSH-instruktionerna i sina avbildningsarkiv. Du kan gå igenom följande instruktioner med [nod.js 10.14-databasen](https://github.com/Azure-App-Service/node/blob/master/10.14) för att se hur den är aktiverad där.

- Använd [RUN-instruktionen](https://docs.docker.com/engine/reference/builder/#run) för att installera SSH-servern och `"Docker!"`ange lösenordet för rotkontot till . För en avbildning baserad på [Alpine Linux](https://hub.docker.com/_/alpine)behöver du till exempel följande kommandon:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Den här konfigurationen tillåter inte externa anslutningar till behållaren. SSH är endast `https://<app-name>.scm.azurewebsites.net` tillgängligt via och autentiserat med publiceringsautentiseringsuppgifterna.

- Lägg till [den här sshd_config filen](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) i bilddatabasen och använd [COPY-instruktionen](https://docs.docker.com/engine/reference/builder/#copy) för att kopiera filen till katalogen */etc/ssh/.* Mer information om *sshd_config* filer finns i [OpenBSD-dokumentation](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Filen *sshd_config* måste innehålla följande objekt:
    > - `Ciphers`måste innehålla minst ett objekt i listan: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`måste innehålla minst ett objekt i listan: `hmac-sha1,hmac-sha1-96`.

- Använd [EXPOSE-instruktionen](https://docs.docker.com/engine/reference/builder/#expose) för att öppna port 2222 i behållaren. Även om rotlösenordet är känt är port 2222 otillgänglig från internet. Den är endast tillgänglig för behållare inom bryggnätverket i ett privat virtuellt nätverk.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Starta SSH-servern i startskriptet för din behållare.

    ```bash
    /usr/sbin/sshd
    ```

    Ett exempel kan du se hur [standardbehållaren Node.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) startar SSH-servern.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurera appar med flera behållare

- [Använd beständig lagring i Docker Compose](#use-persistent-storage-in-docker-compose)
- [Begränsningar för förhandsversion](#preview-limitations)
- [Alternativ för Docker Komponera](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Använd beständig lagring i Docker Compose

Appar med flera behållare som WordPress behöver beständig lagring för att fungera korrekt. Om du vill aktivera den måste Docker Compose-konfigurationen peka på en lagringsplats *utanför* behållaren. Lagringsplatser i behållaren kvarstår inte efter omstart av appen.

Aktivera beständig lagring `WEBSITES_ENABLE_APP_SERVICE_STORAGE` genom att ange appinställningen med kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Mappa `volumes` alternativet till i filen *docker-compose.yml* till `${WEBAPP_STORAGE_HOME}`. 

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

Multi-container är för närvarande i förhandsversion. Följande apptjänstplattformsfunktioner stöds inte:

- Autentisering/auktorisering
- Hanterade identiteter

### <a name="docker-compose-options"></a>Alternativ för Docker Komponera

I följande listor visas konfigurationsalternativ för Docker Compose som stöds och inte stöds:

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
> Alla andra alternativ som inte uttryckligen anropas ignoreras i offentlig förhandsversion.

## <a name="configure-vnet-integration"></a>Konfigurera VNet-integrering

Om du använder en anpassad behållare med VNet-integrering kan det krävas ytterligare behållarkonfiguration. Se [Integrera din app med ett Virtuellt Azure-nätverk](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Distribuera från privat behållardatabas](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Självstudiekurs: WordPress-appen med flera behållare](tutorial-multi-container-app.md)
