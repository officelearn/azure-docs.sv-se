---
title: Skicka jobb till ett HPC Pack kluster i Azure | Microsoft Docs
description: Lär dig hur du ställer in en lokal dator att skicka jobb till ett HPC Pack kluster i Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 025ff3dea365ab75af55f107da1fb7331861eb06
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166377"
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Registrera HPC-jobb från lokala datorer till ett HPC Pack-kluster som distribuerats i Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Konfigurera en lokal klientdator för att skicka jobb till en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) kluster i Azure. Den här artikeln visar hur du ställer in en lokal dator med klientverktyg att skicka jobbet via HTTPS till klustret i Azure. På så sätt kan kan flera kluster-användare skicka jobb till en molnbaserad HPC Pack kluster, utan att ansluta direkt till huvudnoden VM eller få åtkomst till en Azure-prenumeration.

![Skicka ett jobb till ett kluster i Azure][jobsubmit]

## <a name="prerequisites"></a>Förutsättningar
* **HPC Pack huvudnod distribueras i en Azure VM** -rekommenderar vi att du använder automatiserade verktyg som en [Azure quickstart mallen](https://azure.microsoft.com/documentation/templates/) att distribuera huvudnod och kluster. Du behöver DNS-namnet på huvudnoden och autentiseringsuppgifterna för en Klusteradministratör för att slutföra stegen i den här artikeln.
* **Klientdatorn** -du behöver en Windows- eller Windows Server-klientdator som kan köra HPC Pack klientverktyg (se [systemkrav](https://technet.microsoft.com/library/dn535781.aspx)). Om du endast vill använda HPC Pack webbportal eller REST API för att skicka jobb kan du använda alla klientdatorer som du väljer.
* **HPC Pack installationsmediet** - om du vill installera HPC Pack klientverktyg, ledigt installationspaketet för den senaste versionen av HPC Pack är tillgänglig från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Kontrollera att du hämtar samma version av HPC Pack som är installerad på huvudnoden VM.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Steg 1: Installera och konfigurera webbkomponenterna på huvudnoden
Se till att webbkomponenterna HPC Pack konfigureras på huvudnoden HPC Pack om du vill aktivera ett REST-gränssnitt att skicka jobb till klustret via HTTPS. Om de inte redan har installerats måste du först installera komponenterna genom att köra HpcWebComponents.msi installationsfilen. Konfigurera sedan komponenterna genom att köra HPC PowerShell-skriptet **Set HPCWebComponents.ps1**.

Detaljerade anvisningar finns [installera Microsoft HPC Pack Webbkomponenter](http://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Vissa mallar för Azure quickstart för HPC Pack kluster installera och konfigurera webbkomponenterna automatiskt.
> 
> 

**Att installera webbkomponenter**

1. Ansluta till huvudnod VM med hjälp av autentiseringsuppgifterna för en Klusteradministratör.
2. Kör HpcWebComponents.msi på huvudnoden från HPC Pack installationsmapp.
3. Följ stegen i guiden för att installera webbkomponenter

**Så här konfigurerar du webbkomponenterna**

1. Starta HPC PowerShell som administratör på huvudnoden.
2. Om du vill ändra katalogen till platsen för konfigurationsskript, skriver du följande kommando:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Om du vill konfigurera REST-gränssnittet och starta HPC-webbtjänsten, Skriv följande kommando:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. Välj det certifikat som motsvarar det offentliga DNS-namnet på huvudnoden när du uppmanas att välja ett certifikat. Till exempel om du distribuerar huvudnod VM som använder den klassiska distributionsmodellen, certifikatnamnet ser ut som CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Om du använder Resource Manager-distributionsmodellen, certifikatnamnet ser ut som CN =&lt;*HeadNodeDnsName*&gt;.&lt; *region*&gt;. cloudapp.azure.com.
   
   > [!NOTE]
   > Väljer du det här certifikatet senare när du skicka jobb till huvudnoden från en lokal dator. Inte markera eller konfigurera ett certifikat som motsvarar namnet på huvudnod i Active Directory-domän (till exempel CN =*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
5. Om du vill konfigurera webbportalen för jobbet, skriver du följande kommando:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. När skriptet har slutförts, stoppa och starta om tjänsten för Rapportjobbschemaläggning i HPC-kluster genom att skriva följande kommandon:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Steg 2: Installera klientverktyg HPC Pack på en lokal dator
Om du vill installera klientverktyg HPC Pack på datorn hämta HPC Pack-installationsfiler (fullständig installation) från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). När du påbörjar installationen, välja installationsprogrammet för den **HPC Pack klientverktyg**.

Om du vill använda klientverktygen HPC Pack för att skicka jobb till huvudnoden VM, måste du också exportera ett certifikat från huvudnod och installera den på klientdatorn. Certifikatet måste vara i. CER-format.

**Exportera certifikatet från huvudnod**

1. Lägg till snapin-modulen certifikat till en Microsoft Management Console för det lokala datorkontot på huvudnoden. Anvisningar för hur du lägger till snapin-modulen finns [lägga till snapin-modulen certifikat i en MMC](https://technet.microsoft.com/library/cc754431.aspx).
2. I konsolträdet expanderar **certifikat – lokal dator** > **personliga**, och klicka sedan på **certifikat**.
3. Leta upp det certifikat som du har konfigurerat för HPC Pack webbkomponenterna i [steg 1: Installera och konfigurera webbkomponenterna på huvudnoden](#step-1:-install-and-configure-the-web-components-on-the-head-node) (exempelvis, CN =&lt;*HeadNodeDnsName* &gt;. cloudapp.net).
4. Högerklicka på certifikatet och klickar på **alla aktiviteter** > **exportera**.
5. I guiden Exportera certifikat klickar du på **nästa**, och kontrollera att **Nej, exportera inte den privata nyckeln** är markerad.
6. Följ stegen i guiden för att exportera certifikatet i DER-kodad binärfil X.509 (. CER)-format.

**Importera certifikatet på klientdatorn**

1. Kopiera det certifikat som du exporterade från huvudnod till en mapp på klientdatorn.
2. Kör certmgr.msc på klientdatorn.
3. Expandera i Certifikathanteraren **certifikat – aktuell användare** > **betrodda rotcertifikatutfärdare**, högerklicka på **certifikat**, och sedan Klicka på **alla aktiviteter** > **importera**.
4. I guiden Importera certifikat klickar du på **nästa** och följ stegen för att importera certifikatet som du exporterade från huvudnod i arkivet för betrodda rotcertifikatutfärdare.

> [!TIP]
> Du kan se en säkerhetsvarning, eftersom certifikatutfärdare på huvudnoden känner inte igen av klienten. I testsyfte kan du ignorera den här varningen och slutföra Importera certifikat.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Steg 3: Kör testjobb på klustret
Om du vill verifiera konfigurationen, försök att köra jobb på klustret i Azure från den lokala datorn. Du kan till exempel använda HPC Pack GUI-verktyg och kommandon på kommandoraden för att skicka jobb till klustret. Du kan också använda en webbaserad portal för att skicka jobb.

**Att köra jobbet skicka kommandon på klientdatorn**

1. Starta Kommandotolken på en klientdator där klientverktyg HPC Pack installeras.
2. Ange en Exempelkommando. Om du vill visa en lista över alla jobb i klustret, exempelvis ett kommando som liknar följande, beroende på det fullständiga DNS-namnet på huvudnoden:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    eller
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Använd det fullständiga DNS-namnet på huvudnod inte IP-adress, i scheduler-URL. Om du anger IP-adressen, visas ett felmeddelande liknande ”servercertifikatet måste antingen ha en giltig certifikatkedja eller ska placeras i arkivet Betrodda rotcertifikatutfärdare”.
   > 
   > 
3. När du uppmanas, anger användarnamnet (i formatet &lt;DomainName&gt;\\&lt;användarnamn&gt;) och lösenord för administratören för HPC-kluster eller en annan användare i klustret som du har konfigurerat. Du kan välja att lagra autentiseringsuppgifter lokalt för flera jobbåtgärder.
   
    En lista över jobb visas.

**Du använder HPC Job Manager på klientdatorn**

1. Om du inte tidigare sparar autentiseringsuppgifter för domänen för en användare i klustret när du skickar in ett jobb, du kan lägga till autentiseringsuppgifter i Autentiseringshanteraren.
   
    a. Starta Autentiseringshanteraren i Kontrollpanelen på klientdatorn.
   
    b. Klicka på **Windows-autentiseringsuppgifter** > **lägga till en allmän autentiseringsuppgift**.
   
    c. Ange Internet-adress (till exempel https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler eller https://&lt;HeadNodeDnsName&gt;.&lt; region&gt;.cloudapp.azure.com/HpcScheduler), och användarnamn (&lt;DomainName&gt;\\&lt;användarnamn&gt;) och lösenord för Klusteradministratören eller någon annan kluster-användare som du har konfigurerat.
2. Starta HPC Job Manager på klientdatorn.
3. I den **Välj huvudnod** dialogrutan, ange Webbadressen till huvudnod i Azure (till exempel https://&lt;HeadNodeDnsName&gt;. cloudapp.net eller https://&lt;HeadNodeDnsName&gt;.&lt; region&gt;. cloudapp.azure.com).
   
    HPC Job Manager öppnas och visar en lista över jobb på huvudnoden.

**Att använda webbportalen körs på huvudnoden**

1. Starta en webbläsare på klientdatorn och ange ett av följande adresser, beroende på det fullständiga DNS-namnet på huvudnoden:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    eller
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Ange autentiseringsuppgifter för domänen av administratör för HPC-kluster i dialogrutan säkerhet. (Du kan också lägga till andra användare i klustret i olika roller. Se [hantera klustret användare](https://technet.microsoft.com/library/ff919335.aspx).)
   
    Webbportalen öppnar listvyn jobb.
3. För att skicka ett prov jobb som returnerar strängen ”Hello World” från klustret, klickar du på **nytt jobb** i det vänstra navigeringsfönstret.
4. På den **nytt jobb** sidan under **från skicka sidor**, klickar du på **HelloWorld**. Sidan skicka jobbet visas.
5. Klicka på **skicka**. Om du uppmanas ange autentiseringsuppgifter för domänen av administratör för HPC-kluster. Jobbet har skickats och jobb-ID som visas på den **Mina jobb** sidan.
6. Om du vill visa resultatet av jobbet som du har skickat klickar du på jobb-ID och klicka sedan på **uppgiftsvyn** att visa kommandoutdata (under **utdata**).

## <a name="next-steps"></a>Nästa steg
* Du kan också skicka jobb till Azure klustret med det [HPC Pack REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Om du vill skicka kluster-jobb från en Linux-klient finns i Python-exempel i den [HPC Pack 2012 R2 SDK och exempelkod](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
