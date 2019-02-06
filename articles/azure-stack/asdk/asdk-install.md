---
title: Installera Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Beskriver hur du installerar Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 09/10/2018
ms.openlocfilehash: 20c96d1e25e0aef86d09c37ed919e61ec6058c6d
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747449"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Installera Azure Stack Development Kit (ASDK)
Efter [förbereda värddatorn ASDK](asdk-prepare-host.md), ASDK kan distribueras till CloudBuilder.vhdx avbildningen med hjälp av följande steg i den här artikeln.

## <a name="install-the-asdk"></a>Installera ASDK
Stegen i den här artikeln visar hur du distribuerar ASDK med ett grafiskt användargränssnitt (GUI) som tillhandahålls av ladda ned och köra den **asdk installer.ps1** PowerShell-skript.

> [!NOTE]
> Installationsprogrammet användargränssnittet för Azure Stack Development Kit är en öppen källkod-skript som baseras på WCF- och PowerShell.


1. När värddatorn startar i CloudBuilder.vhdx-avbildning, logga in med administratörsautentiseringsuppgifter för anges när du [förberett värddatorn development kit](asdk-prepare-host.md) för ASDK installation. Detta bör vara samma som development kit-autentiseringsuppgifter för lokal administratör på värden.
2. Öppna en upphöjd PowerShell-konsol och kör den  **&lt;enhetsbeteckning > \AzureStack_Installer\asdk-installer.ps1** skript (som kan vara på en annan enhet än C:\ i CloudBuilder.vhdx bild). Klicka på **Installera**.

    ![](media/asdk-install/1.PNG) 

3. I identitetsleverantörens **typ** listrutan, väljer **Azure-molnet** eller **AD FS**. Under **lokala administratörslösenordet** skriver du det lokala administratörslösenordet (som måste matcha det aktuella konfigurera lokala administratörslösenordet) i den **lösenord** rutan och klicka sedan på  **Nästa**.
    - **Azure Cloud**: Konfigurerar Azure Active Directory (Azure AD) som identitetsleverantör. Om du vill använda det här alternativet måste du ha en Internetanslutning, det fullständiga namnet på en Azure AD directory-klient i form av *domainname*. onmicrosoft.com eller en Azure AD verifierat domännamn och autentiseringsuppgifter som global administratör för den angivna katalogen. Efter distributionen krävs inte behörighet för Azure Active Directory global administratör. Vissa åtgärder kan dock kräva autentiseringsuppgifter för global administratör. Till exempel ett resource provider installer skript eller en ny funktion som kräver en behörighet som ska beviljas. Du kan tillfälligt återställa kontots behörigheter som global administratör eller använda ett separat globalt administratörskonto som äger den *standard providerprenumeration*.
    - **AD FS**: Katalogtjänsten för standard-stämpel används som identitetsleverantör. Logga in med standardkontot är azurestackadmin@azurestack.local, och lösenordet är det som du angav som en del av installationen.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > För bästa resultat även om du vill använda en frånkopplad Azure Stack-miljö med hjälp av AD FS som identitetsleverantör, är det bäst att installera ASDK medan du är ansluten till internet. På så sätt kan kan Windows Server 2016 utvärderingsversionen ingår development kit installationen aktiveras vid tidpunkten för distribution.
4. Välj ett nätverkskort som ska användas för i development kit och klicka sedan på **nästa**.

    ![](media/asdk-install/3.PNG)

5. Välj DHCP eller statiska nätverkskonfigurationen för den virtuella datorn BGPNAT01.
    > [!TIP]
    > BGPNAT01 VM är edge-routern med NAT- och VPN-funktioner för Azure Stack.

    - **DHCP** (standard): Den virtuella datorn hämtar IP-nätverkskonfiguration från DHCP-servern.
    - **Statisk**: Använd bara det här alternativet om DHCP inte kan tilldela en giltig IP-adress för Azure Stack för åtkomst till Internet. **Statisk IP-adress måste anges med längden nätmask i CIDR-format (till exempel 10.0.0.5/24)**.
    - Ange en giltig **tid IP-adress** adress. Detta krävs för fältet anger tid-server som ska användas av i development kit. Den här parametern måste anges som en giltig tid serverns IP-adress. Servernamn stöds inte.

      > [!TIP]
      > En IP-adress finns [ntppool.org](https://www.ntppool.org/) eller pinga time.windows.com. 

    - **Du kan också**, anger du följande värden:
        - **VLAN ID**: Anger VLAN-ID. Använd bara det här alternativet om värden och AzS-BGPNAT01 måste konfigurera VLAN-ID för att komma åt den fysiska nätverk (och internet). 
        - **DNS-vidarebefordrare**: En DNS-server skapas som en del av Azure Stack-distribution. Ange din befintliga infrastruktur för DNS-server så att datorer i lösningen att matcha namn utanför stämpeln. DNS-server i stämpel vidarebefordras okänt namn på den här servern.

    ![](media/asdk-install/4.PNG)

6. På den **verifierar egenskaper för nätverksgränssnitt kort** sidan visas en förloppsindikator. När verifieringen är klar klickar du på **nästa**.

    ![](media/asdk-install/5.PNG)

9. På **sammanfattning** klickar du på **distribuera** att starta installationen av ASDK på värddatorn för development kit.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Här kan du också kopiera installationsprogrammet PowerShell-kommandon som används för att installera i development kit. Det här är användbart om du någon gång behöver [omdistribuera ASDK på värddatorn med hjälp av PowerShell](asdk-deploy-powershell.md).

10. Om du utför en Azure AD-distribution, uppmanas du att ange dina autentiseringsuppgifter för Azure AD som global administratör-konto ett par minuter efter installationen startar.

    ![](media/asdk-install/7.PNG)

11. Distributionsprocessen tar några timmar, under tiden värddatorn ska startas om automatiskt en gång. Om du vill övervaka förloppet för distributionen, logga in som azurestack\AzureStackAdmin när development kit värden startas om. När distributionen är klar visas PowerShell-konsolen: **SLUTFÖR: Åtgärden ”distribution”**. 
    > [!IMPORTANT]
    > Om du loggar in som lokal administratör när datorn är ansluten till domänen, visas inte förloppet för distributionen. Inte köra distributionen, i stället logga in som azurestack\AzureStackAdmin att verifiera att den körs.

    ![](media/asdk-install/8.PNG)

Grattis, du installerat har ASDK!

Om distributionen misslyckas av någon anledning, kan du [omdistribuera](asdk-redeploy.md) från grunden eller Använd följande PowerShell-kommandon, från samma upphöjd PowerShell-fönstret, starta om distributionen från det senaste lyckade steget:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Nästa steg
[Konfiguration efter distribution](asdk-post-deploy.md)
