---
title: Installera IBM zD&T dev/test-miljö på Azure | Microsoft-dokument
description: Distribuera IBM Z Development and Test Environment (zD&T) på Azure Virtual Machine (VM) infrastruktur som en tjänst (IaaS).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025936"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Installera IBM zD&T dev/test-miljö på Azure

Om du vill skapa en utvecklings-/testmiljö för stordatorarbetsbelastningar på IBM Z Systems kan du distribuera IBM Z Development and Test Environment (zD&T) på Azure Virtual Machine (VM) infrastruktur som en tjänst (IaaS).

Med zD&T kan du dra nytta av kostnadsbesparingarna för x86-plattformen för dina mindre kritiska utvecklings- och testmiljöer och sedan skicka tillbaka uppdateringarna till en Z-systemproduktionsmiljö. Mer information finns i [installationsinstruktionerna för IBM ZD&T](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure och Azure Stack stöder följande versioner:

- zD&T Personlig utgåva
- zD&T Parallell Sysplex
- zD&T Enterprise Edition

Alla utgåvor av zD&T körs endast på x86 Linux-system, inte Windows Server. Enterprise Edition stöds på antingen Red Hat Enterprise Linux (RHEL) eller Ubuntu/Debian. Både RHEL- och Debian VM-avbildningar är tillgängliga för Azure.

Den här artikeln visar hur du konfigurerar zD&T Enterprise Edition på Azure så att du kan använda zD&T Enterprise Edition-webbservern för att skapa och hantera miljöer. Installera zD&T installerar inga miljöer. Du måste skapa dessa separat som installationspaket. Till exempel är ADCD (Application Developers Controlled Distributions) volymavbildningar av testmiljöer. De finns i zip-bilder på mediedistributionen som finns tillgänglig från IBM. Se hur [du konfigurerar en ADCD-miljö på Azure](demo.md).

Mer information finns i [zD&T Översikt](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) i IBM Knowledge Center.

Den här artikeln visar hur du konfigurerar Z Development and Test Environment (zD&T) Enterprise Edition på Azure. Sedan kan du använda zD&T Enterprise Edition-webbservern för att skapa och hantera Z-baserade miljöer på Azure.

## <a name="prerequisites"></a>Krav

> [!NOTE]
> IBM gör att zD&T Enterprise Edition kan installeras endast i utvecklings-/testmiljöer–*inte* produktionsmiljöer.

- En Azure-prenumeration. Om du inte har ett, skapa ett [gratis konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Du behöver åtkomst till media, som endast är tillgängligt för IBM-kunder och partner. För mer information, kontakta din IBM-representant eller se kontaktinformationen på [zD&](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) T-webbplatsen.

- En [licensieringsserver](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Detta krävs för åtkomst till miljöerna. Hur du skapar den beror på hur du licensierar programvaran från IBM:

     - **Maskinvarubaserad licensieringsserver** kräver en USB-maskinvaruenhet som innehåller de rationella token som krävs för att komma åt alla delar av programvaran. Du måste få detta från IBM.

     - **Programvarubaserad licensieringsserver** kräver att du konfigurerar en centraliserad server för hantering av licensnycklarna. Den här metoden är att föredra och kräver att du ställer in nycklarna du får från IBM i hanteringsservern.

## <a name="create-the-base-image-and-connect"></a>Skapa basavbildningen och anslut

1. Skapa en [virtuell dator](/azure/virtual-machines/linux/quick-create-portal) med den operativsystemkonfiguration du vill ha i Azure-portalen. Den här artikeln förutsätter en B4ms VM (med 4 vCPUs och 16 GB minne) som kör Ubuntu 16,04.

2. När den virtuella datorn har skapats öppnar du inkommande portar 22 för SSH, 21 för FTP och 9443 för webbservern.

3. Få SSH-autentiseringsuppgifterna som visas på den virtuella datorns **översiktsblad** via **knappen Anslut.** Markera fliken **SSH** och kopiera kommandot SSH-inloggning till Urklipp.

4. Logga in på ett [Bash-skal](/azure/cloud-shell/quickstart) från den lokala datorn och klistra in kommandot. Det kommer att vara i formuläret **ssh\<\>\@\<användar-ID IP-adress\>**. När du uppmanas att ange dina autentiseringsuppgifter anger du dem för att upprätta en anslutning till din arbetskatalog.

## <a name="copy-the-installation-file-to-the-server"></a>Kopiera installationsfilen till servern

Installationsfilen för webbservern är **ZDT\_Installera\_EE\_V12.0.0.1.tgz**. Det ingår i media som tillhandahålls av IBM. Du måste ladda upp filen till din Ubuntu VM.

1. Från kommandoraden anger du följande kommando för att se till att allt är uppdaterat i den nyskapade bilden:

    ```
    sudo apt-get update
    ```

2. Skapa katalogen som ska installeras för att:

    ```
    mkdir ZDT
    ```

3. Kopiera filen från den lokala datorn till den virtuella datorn:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Det här kommandot kopierar installationsfilen till ZDT-katalogen i din hemkatalog, vilket varierar beroende på om klienten kör Windows eller Linux.

## <a name="install-the-enterprise-edition"></a>Installera Enterprise Edition

1. Gå till ZDT-katalogen och expandera\_filen\_ZDT Install EE\_V12.0.0.1.tgz med följande kommandon:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Kör installationsprogrammet:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Välj **1** om du vill installera Enterprise Server.

4. Tryck **på Retur** och läs licensavtalen noggrant. I slutet av licensen anger du **Ja** för att fortsätta.

5. När du uppmanas att ändra lösenordet för den nyskapade användaren, **ibmsys1**, använd kommandot **sudo passwd ibmsys1** och ange det nya lösenordet.

6. För att kontrollera om installationen lyckades

    ```
    dpkg -l | grep zdtapp
    ```

7. Kontrollera att utdata innehåller strängen **zdtapp 12.0.0.0**, vilket indikerar att paketgasen har installerats

### <a name="starting-enterprise-edition"></a>Starta Enterprise Edition

Tänk på att när webbservern startar körs den under zD-&T-användar-ID som skapades under installationsprocessen.

1. Starta webbservern genom att använda roten Användar-ID för att köra följande kommando:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Kopiera URL-utdata med skriptet, som ser ut som:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Klistra in URL:en i en webbläsare för att öppna hanteringskomponenten för zD-&T-installationen.

## <a name="next-steps"></a>Nästa steg

[Konfigurera en ADCD (Application Developers Controlled Distribution) i IBM zD&T v1](./demo.md)
