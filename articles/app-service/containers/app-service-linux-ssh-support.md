---
title: SSH-stöd för App Service på Linux – Azure | Microsoft Docs
description: Lär dig mer om att använda SSH med Azure App Service i Linux.
keywords: Azure App Service, webbapp, Linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fef8a17de4539a1427c269cdc512063d07df195c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066867"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-stöd för Azure App Service på Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) används ofta för att fjärrköra administrativa kommandon från en kommando rad Terminal. App Service på Linux ger SSH-stöd till appens behållare med var och en av de inbyggda Docker-avbildningarna som används för körnings stacken för nya webbappar. 

![Körnings stackar](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

För anpassade Docker-avbildningar, genom att konfigurera SSH-servern i den anpassade avbildningen.

Du kan också ansluta till behållaren direkt från den lokala utvecklings datorn med SSH och SFTP.

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsare

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Använda SSH-stöd med anpassade Docker-avbildningar

Se [Konfigurera SSH i en anpassad behållare](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Öppna SSH-session från fjärrgränssnitt

> [!NOTE]
> Den här funktionen är för närvarande en för hands version.
>

Med TCP-tunnlar kan du skapa en nätverks anslutning mellan din utvecklings dator och Web App for Containers över en autentiserad WebSocket-anslutning. Du kan öppna en SSH-session med din behållare som körs i App Service från valfri klient.

För att komma igång måste du installera [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Öppna [Azure Cloud Shell](../../cloud-shell/overview.md)om du vill se hur det fungerar utan att installera Azure CLI. 

Öppna en fjärr anslutning till din app med kommandot [AZ webapp Remote-Connection Create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) . \<Ange \_  _\<prenumerations-ID >_ ,  _\<grupp namn >_ och App-Name > _ för din app.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&`i slutet av kommandot är det bara för bekvämlighet om du använder Cloud Shell. Processen körs i bakgrunden så att du kan köra nästa kommando i samma gränssnitt.

Kommandots utdata ger dig den information du behöver för att öppna en SSH-session.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Öppna en SSH-session med din behållare med den valda klienten med hjälp av den lokala porten. I följande exempel används standard- [SSH](https://ss64.com/bash/ssh.html) -kommandot:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

När du uppmanas till `yes` det skriver du för att fortsätta ansluta. Du uppmanas sedan att ange lösen ordet. Använd `Docker!`, som visades tidigare.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

När du har autentiserats bör du se välkomst skärmen för sessionen.

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

Försök köra det [översta](https://ss64.com/bash/top.html) kommandot. Du bör kunna se appens process i process listan. I exemplet nedan är det en med `PID 263`.

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

Du kan publicera frågor och problem i [Azure-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Mer information om Web App for Containers finns i:

* [Introduktion till fjärrfelsökning av Node. js-appar på Azure App Service från VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Så här använder du en anpassad Docker-avbildning för Web App for Containers](quickstart-docker-go.md)
* [Använda .NET Core i Azure App Service i Linux](quickstart-dotnetcore.md)
* [Använda Ruby i Azure App Service i Linux](quickstart-ruby.md)
* [Azure App Service – Vanliga frågor om Web App for Containers](app-service-linux-faq.md)
