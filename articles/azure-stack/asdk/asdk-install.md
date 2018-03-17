---
title: Installera Azure-stacken Development Kit (ASDK) | Microsoft Docs
description: Beskriver hur du installerar Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e1f4cfb74d83cb23631e5a16a6e6f2dba98027ef
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Installera Azure-stacken Development Kit (ASDK)
Efter [förbereda värddatorn ASDK](asdk-prepare-host.md), ASDK kan distribueras till CloudBuilder.vhdx avbildningen med hjälp av följande steg i den här artikeln.

## <a name="install-the-asdk"></a>Installera ASDK
Stegen i den här artikeln visar hur du distribuerar ASDK med ett grafiskt användargränssnitt (GUI) som angavs genom att hämta och kör den **asdk installer.ps1** PowerShell-skript.

> [!NOTE]
> Användargränssnittet installationsprogram för Azure-stacken Development Kit är en öppen källkod skript baserat på WCF- och PowerShell.


1. När värddatorn startar i CloudBuilder.vhdx avbildningen, logga in med administratörsautentiseringsuppgifter för den angivna när du [förbereda värddatorn development kit](asdk-prepare-host.md) för ASDK installation. Detta bör vara samma som development kit-autentiseringsuppgifter för lokal administratör på värden.
2. Öppna en upphöjd PowerShell-konsolen och kör den  **&lt;enhetsbeteckning > \AzureStack_Installer\asdk-installer.ps1** skript (som kan vara på en annan enhet än C:\ CloudBuilder.vhdx bild). Klicka på **Installera**.

    ![](media/asdk-install/1.PNG) 

3. I den identitetsleverantör **typen** listrutan, Välj **Azure-molnet** eller **AD FS**. Under **lokala administratörslösenordet** ange det lokala administratörslösenordet (som måste matcha det aktuella konfigurerade lokala administratörslösenordet) i den **lösenord** rutan och klicka på  **Nästa**.
    - **Azure-molnet**: konfigurerar Azure Active Directory (AD Azure) som identitetsleverantören. Om du vill använda det här alternativet om du behöver en Internetanslutning, det fullständiga namnet på en Azure AD directory-klient i form av *domainname*. onmicrosoft.com eller en Azure AD verifierat domännamn och autentiseringsuppgifter som global administratör för den angivna katalog. 
    - **AD FS**: standard stämpel katalogtjänsten används som identitetsleverantören. Logga in med standardkontot är azurestackadmin@azurestack.local, och lösenordet är det du angav som en del av installationen.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > För bästa resultat, även om du vill använda en frånkopplad miljö för Azure-stacken med AD FS som identitetsleverantören är det bäst att installera ASDK medan du är ansluten till internet. På så sätt kan kan Windows Server 2016-utvärderingsversion som ingår i development kit installationen aktiveras vid tidpunkten för distribution.
4. Välj ett nätverkskort som ska användas för development kit och klicka sedan på **nästa**.

    ![](media/asdk-install/3.PNG)

5. Välj DHCP eller statiska nätverkskonfigurationen för den virtuella datorn BGPNAT01.
    > [!TIP]
    > Den virtuella datorn BGPNAT01 är gränsroutern som tillhandahåller NAT och VPN-funktioner för Azure-stacken.

    - **DHCP** (standard): den virtuella datorn hämtar den IP-konfigurationen från DHCP-servern.
    - **Statisk**: Använd bara det här alternativet om DHCP inte kan tilldela en giltig IP-adress för Azure stackutrymme för att få åtkomst till Internet. **En statisk IP-adress måste anges med längden nätmask i CIDR-format (till exempel 10.0.0.5/24)**.
    - Ange en giltig **tid servern IP** adress. Detta krävs för fältet anger tid-server som ska användas av development kit. Den här parametern måste anges som en giltig tid serverns IP-adress. Servernamn stöds inte.
      > [!TIP]
      > Du hittar en IP-adress [pool.ntp.org](http:\\pool.ntp.org) eller pinga time.windows.com. 
    - **Du kan också**, anger du följande värden:
        - **VLAN-ID**: Anger VLAN-ID. Använd bara det här alternativet om värden och AzS BGPNAT01 måste konfigurera VLAN-ID för att komma åt den fysiska nätverk (och internet). 
        - **DNS-vidarebefordrare**: en DNS-server har skapats som en del av distributionen av Azure-stacken. Ange din befintliga infrastruktur för DNS-server så att datorer i lösningen för att matcha namn utanför stämpeln. DNS-servern i stämpel vidarebefordras okänt namn på den här servern.

    ![](media/asdk-install/4.PNG)

6. På den **Kontrollera egenskaper för nätverksgränssnitt kort** sidan visas en förloppsindikator. När verifieringen är klar klickar du på **nästa**.

    ![](media/asdk-install/5.PNG)

9. På **sammanfattning** klickar du på **distribuera** ASDK installation startar på värddatorn development kit.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Här kan du också kopiera installationsprogrammet för PowerShell-kommandon som används för att installera development kit. Det här är användbart om du behöver [omdistribuera ASDK på värddatorn med hjälp av PowerShell](asdk-deploy-powershell.md).

10. Om du utför en Azure AD-distribution, uppmanas du att ange dina autentiseringsuppgifter för Azure AD som global administratör konto några minuter efter installationen startar.

    ![](media/asdk-install/7.PNG)

11. Distributionen tar några timmar, under vilken tid värddatorn ska startas om automatiskt en gång. Om du vill övervaka förloppet för distributionen, logga in som azurestack\AzureStackAdmin när development kit värden startas om. När distributionen lyckas PowerShell-konsolen visar: **Slutför: åtgärden 'Distribution'**. 
    > [!IMPORTANT]
    > Om du loggar in som lokal administratör när datorn är ansluten till domänen, visas inte förloppet för distributionen. Inte köra distribution, i stället logga in som azurestack\AzureStackAdmin att verifiera att den körs.

    ![](media/asdk-install/8.PNG)

Grattis, du har installerat ASDK!

Om distributionen misslyckas av någon anledning, kan du [omdistribuera](asdk-redeploy.md) från grunden eller Använd följande PowerShell-kommandon, från samma upphöjd PowerShell-fönstret, starta om distributionen från det senaste lycka steget:

    ```powershell
    cd C:\CloudDeployment\Setup
    .\InstallAzureStackPOC.ps1 -Rerun
    ```

## <a name="next-steps"></a>Nästa steg
[Post distributionskonfiguration](asdk-post-deploy.md)