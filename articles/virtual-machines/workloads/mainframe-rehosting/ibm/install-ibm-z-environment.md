---
title: Installera IBM zD & T dev/test-miljö på Azure | Microsoft Docs
description: Distribuera IBM Z Development och Test-miljö (zD & T) på Azure Virtual Machine (VM)-infrastruktur som en tjänst (IaaS).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 2f5520213e7d8792c89f5445d470987323173dc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859349"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Installera IBM zD & T dev/test-miljö på Azure

Om du vill skapa en miljö för utveckling/testning för stordatorprogram arbetsbelastningar på IBM Z Systems, kan du distribuera IBM Z Development och testa miljö (zD & T) på Azure Virtual Machine (VM)-infrastruktur som en tjänst (IaaS).

Med zD & T, kan du dra nytta av kostnadsbesparingar med x86 plattform för mindre kritiska miljöer för utveckling och testning och sedan push uppdateringarna tillbaka till en produktionsmiljö för Z-System. Mer information finns i den [IBM ZD & T Installationsinstruktioner](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure och Azure Stack stöd för följande versioner:

- zD & T Personal Edition
- zD & T parallella Sysplex
- zD&T Enterprise Edition

Alla utgåvor av zD & T köras bara på x86 Linux-system använder inte Windows Server. Enterprise-utgåvan stöds i Red Hat Enterprise Linux (RHEL) eller Ubuntu/Debian. RHEL- och Debian VM-avbildningar är tillgängliga för Azure.

Den här artikeln visar hur du ställer in zD & T Enterprise Edition på Azure så att du kan använda zD & T Enterprise Edition-webbservern för skapande och hantering av miljöer. Installera zD & T installerar inte några miljöer. Du måste skapa dessa separat som installationspaket. Till exempel är program utvecklare kontrolleras distributioner (ADCD) volymavbildningar för testmiljöer. De ingår i zip-avbildningar på Mediedistribution som är tillgängliga från IBM. Se hur du [en ADCD miljö på Azure](demo.md).

Mer information finns i den [zD & T översikt](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) i IBM Knowledge Center.

Den här artikeln visar hur du ställer in Z utveckling och Test-miljö (zD & T) Enterprise Edition på Azure. Du kan sedan använda zD & T Enterprise Edition-webbserver för att skapa och hantera Z-baserade miljöer i Azure.

## <a name="prerequisites"></a>Nödvändiga komponenter

> [!NOTE]
> IBM tillåter zD & T Enterprise Edition installeras i miljöer för utveckling/testning endast –*inte* produktionsmiljöer.

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Du behöver åtkomst till media, som är endast tillgängligt för IBM-kunder och partner. Mer information kontaktar du din IBM-representant eller se kontaktinformationen på den [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) webbplats.

- En [licensieringsservern](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Detta krävs för åtkomst till miljöer. Hur du skapar den beror på hur du licensierar programvara från IBM:

     - **Maskinvarubaserad licensieringsservern** kräver en USB-maskinvaruenhet som innehåller de rationella token som behövs för att få åtkomst till alla delar av programvaran. Du måste skaffa från IBM.

     - **Programvarubaserade licensieringsservern** kräver att du installerar en central server för hantering av licensiering nycklarna. Den här metoden är att föredra och kräver att du ställer in de nycklar som du får från IBM i management-servern.

## <a name="create-the-base-image-and-connect"></a>Skapa basavbildningen och Anslut

1. Azure-portalen [skapa en virtuell dator](/azure/virtual-machines/linux/quick-create-portal) med den konfiguration du vill. Den här artikeln förutsätter en B4ms virtuell dator (med 4 virtuella processorer och 16 GB minne) som kör Ubuntu 16.04.

2. När den virtuella datorn har skapats kan du öppna ingående portar 22 för SSH, 21 för FTP och 9443 på webbservern.

3. Hämta SSH-autentiseringsuppgifterna som visas på den **översikt** bladet för den virtuella datorn via den **Connect** knappen. Välj den **SSH** fliken och kopiera kommandot SSH-inloggning till Urklipp.

4. Logga in på en [Bash-gränssnittet](/azure/cloud-shell/quickstart) från din lokala dator och klistra in kommandot. Det är i formatet **ssh\<användar-id\>\@\<IP-adress\>**. När du tillfrågas om dina autentiseringsuppgifter, anger du dem för att upprätta en anslutning till din arbetskatalog.

## <a name="copy-the-installation-file-to-the-server"></a>Kopiera installationsfilen till servern

Installationsfilen för webbservern är **ZDT\_installera\_EE\_V12.0.0.1.tgz**. Den ingår i media från IBM. Du måste överföra filen till din Ubuntu-VM.

1. Från kommandoraden, anger du följande kommando för att se till att allt är uppdaterad i den nyligen skapade avbildningen:

    ```
    sudo apt-get update
    ```

2. Skapa en katalog att installera på:

    ```
    mkdir ZDT
    ```

3. Kopiera filen från den lokala datorn till den virtuella datorn:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Det här kommandot kopierar installationsfilen till den ZDT katalogen i din arbetskatalog, vilket varierar beroende på om din klient kör Windows eller Linux.

## <a name="install-the-enterprise-edition"></a>Installera Enterprise-utgåvan

1. Gå till katalogen ZDT och expandera ZDT\_installera\_EE\_V12.0.0.1.tgz-fil med hjälp av följande kommandon:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Kör installationsprogrammet:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Välj **1** installera Enterprise Server.

4. Tryck på **RETUR** och läsa licensavtalet noggrant. I slutet av licensen ange **Ja** att fortsätta.

5. När du uppmanas att ändra lösenordet för den nyligen skapade användaren **ibmsys1**, Använd kommandot **sudo passwd ibmsys1** och ange det nya lösenordet.

6. Kontrollera om installationen lyckades ange

    ```
    dpkg -l | grep zdtapp
    ```

7. Kontrollera att utdata innehåller strängen **zdtapp 12.0.0.0**, vilket visar som paketet gas installerats

### <a name="starting-enterprise-edition"></a>Starta Enterprise Edition

Tänk på att när webbservern startar det körs under zD & T användar-ID som skapades under installationen.

1. Använda roten användar-ID för att köra följande kommando för att starta webbservern:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Kopiera URL: en utdata av skriptet, som ser ut som:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Klistra in URL: en i en webbläsare för att öppna komponenten management för din zD & T installation.

## <a name="next-steps"></a>Nästa steg

[Ange in ett program utvecklare kontrollerad Distribution (ADCD) i IBM zD & T v1](./demo.md)
