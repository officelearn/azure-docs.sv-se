---
title: SSH-stöd för Azure App Service i Linux | Microsoft Docs
description: Lär dig mer om hur du använder SSH med Azure App Service i Linux.
keywords: Azure apptjänst, webbapp, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 631933647e27428349fc1efeb17f62f4614f7f64
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423314"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-stöd för Azure App Service i Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) ofta används för att köra administrativa kommandon via en fjärranslutning från en kommandorad terminal. App Service i Linux stöder SSH till appbehållare med var och en av de fördefinierade Docker-avbildningar som används för Körningsstack av nya web apps. 

![Körningsstackarna](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

För anpassad Docker-avbildningar, genom att konfigurera SSH-server i en anpassad avbildning.

Du kan också ansluta till behållaren direkt från din lokala utvecklingsdator med hjälp av SSH- och SFTP.

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

Om du vill göra en SSH-anslutning för klienten med din behållare, bör du köra din app.

Klistra in följande URL i webbläsaren och Ersätt \<app_name > med appnamnet på din:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Om du inte redan har autentiserats, krävs att autentisera med Azure-prenumerationen för att ansluta. När autentiseringen är klar visas ett gränssnitt i webbläsaren, där du kan köra kommandon i di behållare.

![SSH-anslutning](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Använda SSH-stöd med anpassad Docker-avbildningar

Utför följande steg för en Docker-avbildning för en anpassad Docker-avbildning att stödja SSH-kommunikation mellan behållaren och klienten i Azure-portalen.

De här stegen visas i Azure App Service-databasen som [ett exempel](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Inkludera den `openssh-server` installationen i [ `RUN` instruktionen](https://docs.docker.com/engine/reference/builder/#run) i Dockerfile för din avbildning och ange lösenordet för rot-konto till `"Docker!"`.

    > [!NOTE]
    > Den här konfigurationen tillåter inga externa anslutningar till containern. SSH kan bara kommas åt via Kudu / SCM-webbplatsen som autentiseras med hjälp av autentiseringsuppgifterna för publicering.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Lägg till en [ `COPY` instruktionen](https://docs.docker.com/engine/reference/builder/#copy) till Dockerfile att kopiera en [sshd_config](http://man.openbsd.org/sshd_config) filen till den */etc/ssh/* directory. Konfigurationsfilen ska baseras på sshd_config-filen i Azure Apptjänst GitHub-lagringsplatsen [här](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > Den *sshd_config* filen måste innehålla följande eller om anslutningen misslyckas: 
    > * `Ciphers` måste innehålla minst en av följande: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` måste innehålla minst en av följande: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Inkludera port 2222 i den [ `EXPOSE` instruktionen](https://docs.docker.com/engine/reference/builder/#expose) för Dockerfile. Trots att rotlösenordet är känt går det inte att nå port 2222 från internet. Det är en intern endast port tillgänglig endast via behållare inom ett privat virtuellt nätverks nätverksbrygga.

    ```docker
    EXPOSE 2222 80
    ```

1. Se till att starta SSH-tjänsten med ett kommandoskript (se exempel på [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Dockerfile som använder den [ `ENTRYPOINT` instruktionen](https://docs.docker.com/engine/reference/builder/#entrypoint) att köra skriptet.

    ```docker
    COPY init_container.sh /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Öppna SSH-session från fjärrgränssnitt

> [!NOTE]
> Den här funktionen är för närvarande i förhandsversion.
>

Använder TCP tunneling du kan skapa en nätverksanslutning mellan din utvecklingsdator och Web App for Containers över en autentiserad WebSocket-anslutning. Det kan du öppna en SSH-session med din behållare som körs i App Service från klient av önskat.

Om du vill komma igång kan du behöva installera [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Om du vill se hur det fungerar utan att installera Azure CLI, öppna [Azure Cloud Shell](../../cloud-shell/overview.md). 

Lägg till det senaste App Service-tillägget genom att köra [az-tillägget lägger du till](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add --name webapp
```

Om du redan har kört `az extension add` innan, kör [az tilläggsuppdateringen](/cli/azure/extension?view=azure-cli-latest#az-extension-update) i stället:

```azurecli-interactive
az extension update --name webapp
```

Öppna en anslutning till din app med den [az webapp fjärr-connection skapa](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) kommando. Ange  _\<prenumeration\_id >_,  _\<grupp\_namn >_ och \_< app\_namn > _ för din app.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription_id> --resource-group <group_name> -n <app_name> &
```

> [!TIP]
> `&` i slutet av kommandot är du bara för att underlätta om du använder Cloud Shell. Processen körs i bakgrunden så att du kan köra nästa kommando i samma shell.

Utdata från kommandot ger dig den information du behöver att öppna en SSH-session.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Öppna en SSH-session med din behållare med klienten föredrar, med hjälp av den lokala porten. I följande exempel används standardvärdet [ssh](https://ss64.com/bash/ssh.html) kommando:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

När kommer du att tillfrågas, skriver `yes` att fortsätta att ansluta. Du uppmanas sedan lösenordet. Använd `Docker!`, som visades för dig tidigare.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

När du är autentiserad visas på välkomstskärmen för sessionen.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Du är nu ansluten till din anslutning. 

Prova att köra den [upp](https://ss64.com/bash/top.html) kommando. Du bör kunna se hur din app i listan. I de exempel på utdata nedan är det med `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Nästa steg

Du kan publicera frågor och funderingar på den [Azure-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Mer information om Web App for Containers finns:

* [Introduktion till fjärrfelsökning av Node.js-appar på Azure App Service från VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Så här använder du en anpassad Docker-avbildning för Web App for Containers](quickstart-docker-go.md)
* [Använda .NET Core i Azure App Service i Linux](quickstart-dotnetcore.md)
* [Använda Ruby i Azure App Service i Linux](quickstart-ruby.md)
* [Azure App Service – Vanliga frågor om Web App for Containers](app-service-linux-faq.md)
