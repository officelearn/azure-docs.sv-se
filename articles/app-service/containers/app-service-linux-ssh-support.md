---
title: SSH-stöd för App Service i Linux – Azure | Microsoft Docs
description: Lär dig mer om hur du använder SSH med Azure App Service i Linux.
keywords: azure app service, web app, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2d84a4dd0b69ce9ca7fc594dffce3238c620c426
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543981"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-stöd för Azure App Service i Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) ofta används för att köra administrativa kommandon via en fjärranslutning från en kommandorad terminal. App Service i Linux stöder SSH till appbehållare med var och en av de fördefinierade Docker-avbildningar som används för Körningsstack av nya web apps. 

![Körningsstackarna](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

För anpassad Docker-avbildningar, genom att konfigurera SSH-server i en anpassad avbildning.

Du kan också ansluta till behållaren direkt från din lokala utvecklingsdator med hjälp av SSH- och SFTP.

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Använda SSH-stöd med anpassad Docker-avbildningar

Se [konfigurera SSH i en anpassad behållare](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Öppna SSH-session från fjärrgränssnitt

> [!NOTE]
> Den här funktionen är för närvarande i förhandsversion.
>

Använder TCP tunneling du kan skapa en nätverksanslutning mellan din utvecklingsdator och Web App for Containers över en autentiserad WebSocket-anslutning. Det kan du öppna en SSH-session med din behållare som körs i App Service från klient av önskat.

Om du vill komma igång kan du behöva installera [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Om du vill se hur det fungerar utan att installera Azure CLI, öppna [Azure Cloud Shell](../../cloud-shell/overview.md). 

Öppna en anslutning till din app med den [az webapp fjärr-connection skapa](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) kommando. Ange  _\<prenumerations-id >_,  _\<-namn >_ och \_< appnamn > _ för din app.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
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
