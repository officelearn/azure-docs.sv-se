---
title: Skicka jobb till ett HPC Pack-kluster i Azure | Microsoft Docs
description: Lär dig hur du konfigurerar den lokala datorn för att skicka jobb till ett HPC Pack-kluster i Azure
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
ms.openlocfilehash: c4fd48e40eb4f03daf4bcb7e3b7d6794880799cf
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036497"
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Registrera HPC-jobb från lokala datorer till ett HPC Pack-kluster som distribuerats i Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Konfigurera en lokal klientdator för att skicka jobb till en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) kluster i Azure. Den här artikeln visar hur du ställer in en lokal dator med klientverktyg för att skicka jobbet via HTTPS till klustret i Azure. På så sätt kan kan flera kluster-användare skicka jobb till en molnbaserad HPC Pack-kluster, men utan att ansluta direkt till huvudnoden VM eller åtkomst till en Azure-prenumeration.

![Skicka ett jobb till ett kluster i Azure][jobsubmit]

## <a name="prerequisites"></a>Förutsättningar
* **HPC Pack huvudnoden distribueras i en Azure VM** -rekommenderar vi att du använder automatiserade verktyg som en [Azure-snabbstartsmall](https://azure.microsoft.com/documentation/templates/) huvudnoden och kluster. Behöver du DNS-namnet på klustrets huvudnod och autentiseringsuppgifterna för en Klusteradministratör för att slutföra stegen i den här artikeln.
* **Klientdatorn** -du behöver en Windows- eller Windows Server-klientdator som kan köra HPC Pack-klientverktyg (se [systemkrav](https://technet.microsoft.com/library/dn535781.aspx)). Du kan använda alla klientdatorer valfritt om du bara vill använda HPC Pack webbportalen eller REST API för att skicka jobb.
* **HPC Pack installationsmediet** – om du vill installera HPC Pack klientverktyg, kostnadsfri installationspaketet för den senaste versionen av HPC Pack är tillgängligt från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Kontrollera att du hämtar samma version av HPC Pack som är installerad på huvudnoden VM.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Steg 1: Installera och konfigurera webbkomponenterna på huvudnoden
Se till att HPC Pack webbkomponenterna är konfigurerade på klustrets huvudnod i HPC Pack för att aktivera ett REST-gränssnitt att skicka jobb till klustret via HTTPS. Om de inte redan har installerats måste du först installera webbkomponenterna genom att köra installationsfilen HpcWebComponents.msi. Konfigurera sedan komponenterna genom att köra HPC PowerShell-skriptet **Set-HPCWebComponents.ps1**.

Detaljerade anvisningar finns [installerar Microsoft HPC Pack Webbkomponenter](http://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Vissa Azure-snabbstartsmallar för HPC Pack-kluster installerar och konfigurerar webbkomponenterna automatiskt.
> 
> 

**Att installera webbkomponenter**

1. Ansluta till huvudnoden VM med hjälp av autentiseringsuppgifterna för en Klusteradministratör.
2. Kör HpcWebComponents.msi från installationsmappen för HPC Pack på huvudnoden.
3. Följ stegen i guiden för att installera webbkomponenter

**Konfigurera webbkomponenterna**

1. Starta HPC PowerShell som administratör på klustrets huvudnod.
2. Om du vill ändra katalogen till platsen för skript för konfiguration, skriver du följande kommando:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Om du vill konfigurera REST-gränssnittet och starta HPC-webbtjänsten, skriver du följande kommando:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. När du uppmanas att välja ett certifikat, väljer du det certifikat som motsvarar det offentliga DNS-namnet för huvudnoden. Till exempel om du distribuerar huvudnoden virtuell dator med den klassiska distributionsmodellen certifikatnamnet ser ut som CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Om du använder Resource Manager-distributionsmodellen certifikatnamnet ser ut som CN =&lt;*HeadNodeDnsName*&gt;.&lt; *region*&gt;. cloudapp.azure.com.
   
   > [!NOTE]
   > Du väljer det här certifikatet senare när du skickar in jobb till huvudnoden från en lokal dator. Inte välja eller konfigurera ett certifikat som motsvarar namnet på huvudnod i Active Directory-domänen (till exempel CN =*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
5. Om du vill konfigurera webbportalen för jobbinlämningar, skriver du följande kommando:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. När skriptet har slutförts kan du stoppa och starta om Schemaläggaren för HPC-jobb genom att skriva följande kommandon:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Steg 2: Installera verktyg för HPC Pack-klienten på en lokal dator
Om du vill installera verktyg för HPC Pack-klienten på datorn kan ladda ned installationsfilerna för HPC Pack (fullständig installation) från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). När du påbörjar installationen, väljer du alternativet för installationsprogrammet för den **HPC Pack-klientverktyg**.

Om du vill använda HPC Pack-klientverktyg för att skicka jobb till huvudnoden VM, måste du också att exportera ett certifikat från klustrets huvudnod och installera den på klientdatorn. Certifikatet måste finnas i. CER-format.

**Exportera certifikatet från klustrets huvudnod**

1. Lägg till snapin-modulen certifikat till en Microsoft Management Console för det lokala datorkontot på klustrets huvudnod. Anvisningar om att lägga till snapin-modulen finns i [lägga till snapin-modulen certifikat i en MMC](https://technet.microsoft.com/library/cc754431.aspx).
2. I konsolträdet expanderar **certifikat – lokal dator** > **personliga**, och klicka sedan på **certifikat**.
3. Leta upp det certifikat som du har konfigurerat för HPC Pack webbkomponenterna i [steg 1: Installera och konfigurera webbkomponenterna på huvudnoden](#step-1-install-and-configure-the-web-components-on-the-head-node) (till exempel CN =&lt;*HeadNodeDnsName* &gt;. cloudapp.net).
4. Högerklicka på certifikatet och klicka på **alla uppgifter** > **exportera**.
5. I guiden Exportera certifikat klickar du på **nästa**, och se till att **Nej, exportera inte den privata nyckeln** har valts.
6. Följ stegen i guiden för att exportera certifikatet i DER-kodad binärfil X.509 (. CER)-format.

**Importera certifikatet på klientdatorn**

1. Kopiera certifikatet som du exporterade från huvudnoden till en mapp på klientdatorn.
2. Kör certmgr.msc på klientdatorn.
3. I Certifikathanteraren Expandera **certifikat – aktuell användare** > **betrodda rotcertifikatutfärdare**, högerklicka på **certifikat**, och sedan Klicka på **alla uppgifter** > **Import**.
4. I guiden Importera certifikat klickar du på **nästa** och följ stegen för att importera certifikatet som du exporterade från huvudnoden till arkivet Betrodda rotcertifikatutfärdare.

> [!TIP]
> Du kanske ser en säkerhetsvarning, eftersom certifikatutfärdare på huvudnoden inte känns igen av klientdatorn. I testsyfte kan du ignorera den här varningen och slutför Importera certifikat.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Steg 3: Kör testjobb på klustret
Du kan kontrollera konfigurationen, försök att köra jobb på klustret i Azure från den lokala datorn. Du kan till exempel använda HPC Pack GUI-verktyg och kommandon för kommandoraden skicka jobb till klustret. Du kan också använda en webbaserad portal för att skicka jobb.

**Att köra jobbet skicka kommandon på klientdatorn**

1. Starta Kommandotolken på en klientdator där HPC Pack-klientverktyg installeras.
2. Ange en Exempelkommando. Om du vill visa alla jobb i klustret, exempelvis ett kommando som liknar en av följande, beroende på det fullständiga DNS-namnet för huvudnoden:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    eller
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > Använd det fullständiga DNS-namnet på huvudnoden, inte IP-adress, i scheduler-URL. Om du anger IP-adress, ser ett fel ut som ”servercertifikatet måste antingen ha en giltig certifikatkedja eller placeras i arkivet Betrodda rotcertifikatutfärdare”.
   > 
   > 
3. När du uppmanas att ange användarnamnet (i formatet &lt;DomainName&gt;\\&lt;användarnamn&gt;) och lösenordet för administratören för HPC-kluster eller en annan användare i klustret som du har konfigurerat. Du kan välja att spara autentiseringsuppgifterna lokalt för mer jobbåtgärder.
   
    En lista över jobb visas.

**Du använder HPC Job Manager på klientdatorn**

1. Om du inte tidigare sparar autentiseringsuppgifter för domänen för en användare i klustret när du skickar ett jobb, du kan lägga till autentiseringsuppgifter i Autentiseringshanteraren.
   
    a. Starta Autentiseringshanteraren i Kontrollpanelen på klientdatorn.
   
    b. Klicka på **Windows-autentiseringsuppgifter** > **Lägg till autentiseringsuppgift för allmän**.
   
    c. Ange Internetadressen (till exempel https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler eller https://&lt;HeadNodeDnsName&gt;.&lt; region&gt;.cloudapp.azure.com/HpcScheduler), och användarnamn (&lt;DomainName&gt;\\&lt;användarnamn&gt;) och lösenordet för Klusteradministratören eller någon annan kluster-användare som du har konfigurerat.
2. Starta HPC Job Manager på klientdatorn.
3. I den **Välj huvudnod** dialogrutan, ange Webbadressen till klustrets huvudnod i Azure (till exempel https://&lt;HeadNodeDnsName&gt;. cloudapp.net och https://&lt;HeadNodeDnsName&gt;.&lt; region&gt;. cloudapp.azure.com).
   
    HPC Job Manager öppnas och visar en lista över jobb på huvudnoden.

**Att använda webbportalen som körs på klustrets huvudnod**

1. Starta en webbläsare på klientdatorn och ange något av följande adresser, beroende på det fullständiga DNS-namnet för huvudnoden:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    eller
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. I dialogrutan säkerhet skriver du autentiseringsuppgifter för domänen till administratören för HPC-kluster. (Du kan också lägga till andra användare i klustret i olika roller. Se [hantera klustret användare](https://technet.microsoft.com/library/ff919335.aspx).)
   
    Webbportalen öppnas jobblistevyn.
3. För att skicka ett exempeljobb som returnerar strängen ”Hello World” från klustret, klickar du på **nytt jobb** i det vänstra navigeringsfältet.
4. På den **nytt jobb** sidan under **från bidrag sidor**, klickar du på **HelloWorld**. Skicka jobbsidan visas.
5. Klicka på **skicka**. Om du uppmanas ange domänautentiseringsuppgifter för administratör för HPC-kluster. Jobbet skickas och jobb-ID som visas på den **Mina jobb** sidan.
6. Om du vill visa resultatet av jobbet som du skickade, klickar du på jobb-ID och klicka sedan på **uppgiftsvyn** att visa kommandoutdata (under **utdata**).

## <a name="next-steps"></a>Nästa steg
* Du kan också skicka jobb till Azure-kluster med den [HPC Pack REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Om du vill skicka kluster jobb från en klient för Linux finns i Python-exemplet i den [HPC Pack 2012 R2 SDK och exempelkod](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
