---
title: Konfigurera kund - behållare i Azure App Service | Microsoft Docs
description: Lär dig att konfigurera Node.js-appar att fungera i Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 1e5faa8d356b891d825586414c0a1a1b9fa47090
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853328"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Konfigurera en anpassad Linux-behållare för Azure App Service

Den här artikeln visar hur du konfigurerar en anpassad Linux-behållare för att köra på Azure App Service.

Den här guiden innehåller huvudbegrepp och instruktioner för skapande av Linux-appar i App Service. Om du aldrig har använt Azure App Service, följer du de [anpassad behållare Snabbstart](quickstart-docker-go.md) och [självstudien](tutorial-custom-docker-image.md) första. Det finns också en [Snabbstart för flera behållare](quickstart-multi-container.md) och [självstudien](tutorial-multi-container-app.md).

## <a name="configure-port-number"></a>Konfigurera portnummer

Webbservern i den anpassade avbildningen kan använda en annan port än 80. Du kan informera Azure om den port som används av din anpassade med hjälp av den `WEBSITES_PORT` appinställningen. GitHub-sidan för [Python-exemplet i den här självstudien](https://github.com/Azure-Samples/docker-django-webapp-linux) visar att du behöver ställa in `WEBSITES_PORT` på _8000_. Du kan ställa in den genom att köra [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell. Exempel:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Den anpassade behållaren kan använda miljövariabler som måste anges externt. Du kan skicka dem i genom att köra [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell. Exempel:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Den här metoden fungerar både för enskild behållare appar eller appar för flera behållare, där miljövariablerna som anges i den *docker-compose.yml* fil.

## <a name="use-persistent-shared-storage"></a>Använda beständiga delad lagring

Du kan använda den */home* katalogen i filsystemet för din app kan spara filer mellan omstarter och dela dem mellan instanser. Den `/home` i din app har angetts för att konfigurera appen behållare för att få åtkomst till beständig lagring.

När beständig lagring är inaktiverad och skriver sedan till den `/home` directory är inte beständiga mellan omstarter av appen eller över flera instanser. Det enda undantaget är den `/home/LogFiles` directory som används för att lagra loggarna för Docker och behållare. När beständig lagring är aktiverat, alla skrivningar till den `/home` directory sparas och kan nås av alla instanser av en utskalad-app.

Som standard är beständig lagring *inaktiverat*. Om du vill aktivera eller inaktivera den, ange den `WEBSITES_ENABLE_APP_SERVICE_STORAGE` appinställningen genom att köra [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell. Exempel:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

> [!NOTE]
> Du kan också [konfigurera egna beständig lagring](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Aktivera SSH

SSH möjliggör säker kommunikation mellan en container och en klient. För en anpassad behållare ska stödja SSH måste du lägga till den i Dockerfile som själva.

> [!TIP]
> Alla inbyggda Linux-behållare har lagt till SSH-instruktionerna i deras bild-databaser. Du kan gå igenom följande instruktioner med den [Node.js 10.14 databasen](https://github.com/Azure-App-Service/node/blob/master/10.14) att se hur det är det aktiverat.

- Använd den [kör](https://docs.docker.com/engine/reference/builder/#run) anvisningarna för att installera SSH-servern och ange lösenordet för rotkontot på `"Docker!"`. Till exempel för en avbildning baserat på [Alpine Linux](https://hub.docker.com/_/alpine), behöver du följande kommandon:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Den här konfigurationen tillåter inte att externa anslutningar till behållaren. SSH är endast tillgängligt via `https://<app-name>.scm.azurewebsites.net` och autentiseras med autentiseringsuppgifterna för publicering.

- Lägg till [sshd_config-filen](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) till lagringsplatsen för avbildningen och Använd den [kopia](https://docs.docker.com/engine/reference/builder/#copy) anvisningarna för att kopiera filen till den */etc/ssh/* directory. Mer information om *sshd_config* filer, se [OpenBSD dokumentation](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Filen *sshd_config* måste innehålla följande objekt:
    > - `Ciphers`måste innehålla minst ett objekt i listan: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`måste innehålla minst ett objekt i listan: `hmac-sha1,hmac-sha1-96`.

- Använd den [EXPONERA](https://docs.docker.com/engine/reference/builder/#expose) anvisningarna för att öppna port 2222 i behållaren. Även om rotlösenordet är känt är port 2222 inte tillgänglig från internet. Det är tillgängligt endast via behållare inom ett privat virtuellt nätverks nätverksbrygga.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- I Start-skriptet för din behållare börjar du SSH-servern.

    ```bash
    /usr/sbin/sshd
    ```

    Ett exempel finns i hur standard [Node.js 10.14 behållare](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) startar SSH-servern.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurera appar för flera behållare

- [Använd beständig lagring i Docker Compose](#use-persistent-storage-in-docker-compose)
- [Begränsningar i förhandsversionen](#preview-limitations)
- [Alternativ för docker Compose](#docker-compose-options)
- [Konfigurationsalternativ för Kubernetes](#kubernetes-configuration-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Använd beständig lagring i Docker Compose

Appar för flera behållare som WordPress behöver beständig lagring ska fungera korrekt. Om du vill aktivera den Docker Compose-konfiguration måste peka på en lagringsplats *utanför* din behållare. Lagringsplatser i di behållare Spara inte ändringar efter omstart av app.

Aktivera beständig lagring genom att ange den `WEBSITES_ENABLE_APP_SERVICE_STORAGE` app inställningen, med den [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

I din *docker-compose.yml* filen, mappa den `volumes` alternativet att `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` är en miljövariabel i App Service som är mappad till beständig lagring för din app. Exempel:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="use-custom-storage-in-docker-compose"></a>Använd anpassad lagring i Docker Compose

Azure Storage (Azure Files eller Azure Blob) kan monteras med flera behållare appar med hjälp av anpassade-id. Om du vill visa namnet custom-id, kör [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

I din *docker-compose.yml* filen, mappa den `volumes` alternativet att `custom-id`. Exempel:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

### <a name="preview-limitations"></a>Begränsningar för förhandsversion

Flera behållare förhandsvisas just nu. Följande funktioner i App Service-plattformen stöds inte:

- Autentisering/auktorisering
- Hanterade identiteter

### <a name="docker-compose-options"></a>Alternativ för docker Compose

I listan nedan visas som stöds respektive Docker Compose konfigurationsalternativ:

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
> Andra alternativ som inte uttryckligen påpekas ignoreras i offentlig förhandsversion.

### <a name="kubernetes-configuration-options"></a>Konfigurationsalternativ för Kubernetes

Följande konfigurationsalternativ har stöd för Kubernetes:

- args
- command
- containrar
- image
- namn
- ports
- spec

> [!NOTE]
> Andra alternativ som inte uttryckligen påpekas stöds inte i offentlig förhandsversion.
>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Distribuera från privata lagringsplats](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Självstudie: Flera behållare WordPress-appen](tutorial-multi-container-app.md)
