---
title: SSH-stöd för Azure App Service på Linux | Microsoft Docs
description: Lär dig mer om hur du använder SSH med Azure App Service på Linux.
keywords: Azure apptjänst, webbprogram, linux, oss
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
ms.openlocfilehash: c2beb67a27b667d31402b903f38dbf116e9425d0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34301083"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-stöd för Azure App Service på Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) används ofta för att köra administrativa kommandon från en fjärrdator från en kommandorad terminal. Apptjänst i Linux stöder SSH till appbehållare med var och en av de inbyggda Docker bilder som används för Runtime-Stack för nya webbappar. 

![Runtime stackar](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

För anpassade Docker bilder, genom att konfigurera SSH-server i den anpassade avbildningen.

Du kan också ansluta till behållaren direkt från lokala utvecklingsdatorn med SSH- och SFTP.

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

Om du vill skapa en SSH-klientanslutning med din behållare, bör du köra din app.

Klistra in följande URL i webbläsaren och Ersätt \<programnamn > med appnamn på din:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Om du inte redan har autentiserats, krävs att autentisera med din Azure-prenumeration att ansluta. När autentiseringen är se du ett shell i webbläsaren, där du kan köra kommandon i en behållare.

![SSH-anslutning](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Använda SSH-stöd med anpassade Docker-avbildningar

Utför följande steg för Docker-avbildning för en anpassad Docker-avbildning att stödja SSH-kommunikation mellan behållaren och klienten i Azure-portalen.

Stegen visas i Azure App Service-databasen som [exempel](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Inkludera den `openssh-server` installationen i [ `RUN` instruktion](https://docs.docker.com/engine/reference/builder/#run) i Dockerfile för avbildning och ange lösenordet för rot-kontot till `"Docker!"`.

    > [!NOTE]
    > Den här konfigurationen tillåter inga externa anslutningar till behållaren. SSH kan endast nås via Kudu / SCM plats, vilket är autentiserad publishing autentiseringsuppgifter.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Lägg till en [ `COPY` instruktion](https://docs.docker.com/engine/reference/builder/#copy) till Dockerfile att kopiera en [sshd_config](http://man.openbsd.org/sshd_config) filen till den */etc/ssh/* directory. Konfigurationsfilen ska baseras på filen sshd_config i Azure App Service GitHub-lagret [här](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > Den *sshd_config* filen måste innehålla följande eller om anslutningen misslyckas: 
    > * `Ciphers` måste innehålla minst en av följande: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` måste innehålla minst en av följande: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Inkludera porten 2222 i den [ `EXPOSE` instruktion](https://docs.docker.com/engine/reference/builder/#expose) för Dockerfile. Trots att rotlösenordet är känt går det inte att nå port 2222 från internet. Det är ett internt endast port tillgänglig endast av behållare i bridge nätverk i ett privat virtuellt nätverk.

    ```docker
    EXPOSE 2222 80
    ```

1. Se till att starta tjänsten SSH med hjälp av ett kommandoskript (se exempel på [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Dockerfile använder den [ `ENTRYPOINT` instruktion](https://docs.docker.com/engine/reference/builder/#entrypoint) att köra skriptet.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Öppna SSH-session från fjärrgränssnitt

> [!NOTE]
> Den här funktionen är för närvarande under förhandsgranskning.
>

Använder TCP tunneling du kan skapa en nätverksanslutning mellan din utvecklingsdator och webbprogrammet för behållare över en autentiserad WebSocket-anslutning. På så sätt kan du öppna en SSH-session med den behållare som körs i Apptjänst från klienten önskat.

Om du vill komma igång, måste du installera [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Om du vill se hur det fungerar utan att installera Azure CLI, öppna [Azure Cloud Shell](../../cloud-shell/overview.md). 

Lägga till filnamnstillägget senaste Apptjänst genom att köra [az tillägget lägger till](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add -–name webapp
```

Om du redan har kört `az extension add` innan, kör [az uppdatering av webbprogramtillägg](/cli/azure/extension?view=azure-cli-latest#az-extension-update) i stället:

```azurecli-interactive
az extension update -–name webapp
```

Öppna en anslutning till din app med hjälp av den [az webapp fjärr-anslutning skapa](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) kommando. Ange  _\<grupp\_namn >_ och \_< app\_namn > _ för din app och Ersätt \<port > med ett lokalt portnummer.

```azurecli-interactive
az webapp remote-connection create --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> `&` i slutet av kommandot gäller bara för om du använder molntjänster Shell. Processen körs i bakgrunden så att du kan köra nästa kommando i samma-gränssnittet.

Kommandoutdata ger dig den information du behöver att öppna en SSH-session.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Öppna en SSH-session med din behållare med klienten du själv väljer, som använder den lokala porten. I följande exempel används standardvärdet [ssh](https://ss64.com/bash/ssh.html) kommando:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

När du uppmanas, anger `yes` fortsätta ansluta. Du uppmanas sedan lösenordet. Använd `Docker!`, som tidigare var visas för dig.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Du bör se välkomstskärmen session när du är autentiserad.

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

Du är nu ansluten till din connector. 

Försök med att köra den [upp](https://ss64.com/bash/top.html) kommando. Du bör kunna finns i din app i listan. I de exempel på utdata nedan är det med `PID 263`.

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

För mer information om Web App för behållare, se:

* [Introduktion till fjärrfelsökning av Node.js-appar i Azure App Service från VS-kod](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Så här använder du en anpassad Docker-avbildning för Web App for Containers](quickstart-docker-go.md)
* [Använda .NET Core i Azure App Service i Linux](quickstart-dotnetcore.md)
* [Använda Ruby i Azure App Service i Linux](quickstart-ruby.md)
* [Azure App Service – Vanliga frågor om Web App for Containers](app-service-linux-faq.md)