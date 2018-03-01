---
title: "SSH-stöd för Azure App Service på Linux | Microsoft Docs"
description: "Lär dig mer om hur du använder SSH med Azure App Service på Linux."
keywords: "Azure apptjänst, webbprogram, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 905c257ab40057f05081e54e8680bd818023d886
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-stöd för Azure App Service på Linux

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) är kryptografiska nätverksprotokoll för att använda nätverkstjänster på ett säkert sätt. Den används oftast för att logga in på en dator via fjärranslutning på ett säkert sätt från en kommandorad och utföra administrativa kommandon från en fjärrdator.

Apptjänst i Linux stöder SSH till appbehållare med var och en av de inbyggda Docker bilder som används för Runtime-Stack för nya webbappar.

![Runtime stackar](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Du kan också använda SSH med din anpassade Docker-avbildningar genom att inkludera SSH-server som en del av avbildningen och konfigurera den som beskrivs i den här artikeln.

## <a name="making-a-client-connection"></a>Gör en klientanslutning

Om du vill skapa en SSH-klientanslutning, startas huvudwebbplatsen.

Klistra in källa Control (SCM) slutpunkt för ditt webbprogram i webbläsaren med hjälp av följande format:

```bash
https://<your sitename>.scm.azurewebsites.net/webssh/host
```

Om du inte redan har autentiserats, krävs att autentisera med din Azure-prenumeration att ansluta.

![SSH-anslutning](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="ssh-support-with-custom-docker-images"></a>SSH-support med anpassade Docker-avbildningar

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

## <a name="next-steps"></a>Nästa steg

Du kan publicera frågor och funderingar på den [Azure-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

För mer information om Web App för behållare, se:

* [Så här använder du en anpassad Docker-avbildning för Web App for Containers](quickstart-docker-go.md)
* [Använda .NET Core i Azure App Service i Linux](quickstart-dotnetcore.md)
* [Använda Ruby i Azure App Service i Linux](quickstart-ruby.md)
* [Azure App Service – Vanliga frågor om Web App for Containers](app-service-linux-faq.md)