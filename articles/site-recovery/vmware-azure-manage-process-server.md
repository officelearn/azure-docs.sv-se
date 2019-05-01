---
title: Hantera en processerver som används för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hantera en processerver som ställts in för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925641"
---
# <a name="manage-process-servers"></a>Hantera processervrar

Den här artikeln beskrivs vanliga uppgifter för hantering av processervern för Site Recovery.

Processervern används för att ta emot, optimera och skicka replikeringsdata till Azure. Den genomför också en push-installation av mobilitetstjänsten på virtuella VMware-datorer och fysiska servrar som du vill replikera, samt utför automatisk identifiering av lokala datorer. För att replikera lokala virtuella VMware-datorer eller fysiska servrar till Azure, installeras processerver som standard på configuration server-datorn. 

- Du kanske behöver ytterligare lokala processervrar som skala kapaciteten för stora distributioner.
- För återställning efter fel från Azure till lokalt, måste du konfigurera en tillfällig processerver i Azure. Du kan ta bort den här virtuella datorn när återställningen är klar. 

Läs mer om processervern.


## <a name="upgrade-a-process-server"></a>Uppgradera en processerver

När du distribuerar en processerver lokalt, eller som en Azure virtuell dator för återställning efter fel, den senaste versionen av processervern är installerad. Site Recovery utvecklingsteam versionen korrigeringar och förbättringar med jämna mellanrum och vi rekommenderar att du behåller processervrar uppdaterad. Du kan uppgradera en processerver på följande sätt:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Flytta virtuella datorer för belastningsutjämning process server

Balansera belastningen genom att flytta virtuella datorer mellan två processervrar på följande sätt:

1. I valvet under **hantera** klickar du på **Site Recovery-infrastruktur**. Under **för VMware och fysiska datorer**, klickar du på **Konfigurationsservrar**.
2. Klicka på konfigurationsservern med vilken processen-servrar är registrerade.
3. Klicka på processervern som du vill belastningsutjämna trafik.

    ![DSM](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Klicka på **belastningsutjämna**, Välj målprocesserver som du vill flytta datorer. Klicka sedan på **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klicka på **Välj datorer**, och välj de datorer du vill flytta från aktuellt till målprocesserver. Information om genomsnittlig dataändring visas mot varje virtuell dator. Klicka sedan på **OK**. 
3. I valvet, övervaka förloppet för jobbet under **övervakning** > **Site Recovery-jobb**.

Det tar ungefär 15 minuter för ändringar återspeglas i portalen. För en snabbare effekt [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Växla en hela arbetsbelastning till en annan processerver

Flytta hela arbetsbelastningen som hanteras av en processerver till en annan processerver enligt följande:

1. I valvet under **hantera** klickar du på **Site Recovery-infrastruktur**. Under **för VMware och fysiska datorer**, klickar du på **Konfigurationsservrar**.
2. Klicka på konfigurationsservern med vilken processen-servrar är registrerade.
3. Klicka på processervern som du vill växla arbetsbelastningen.
4. Klicka på **växel**, Välj målprocesserver som du vill flytta arbetsbelastningen. Klicka sedan på **OK**

    ![Växel](media/vmware-azure-manage-process-server/Switch.PNG)

5. I valvet, övervaka förloppet för jobbet under **övervakning** > **Site Recovery-jobb**.

Det tar ungefär 15 minuter för ändringar återspeglas i portalen. För en snabbare effekt [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server).



## <a name="reregister-a-process-server"></a>Registrera en processerver

Registrera en processerver som körs lokalt eller på en virtuell Azure-dator med konfigurationsservern på följande sätt:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

När du har sparat inställningarna gör du följande:

1. Öppna en administratörskommandotolk på processervern.
2. Bläddra till mappen **%PROGRAMDATA%\ASR\Agent**, och kör kommandot:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Ändra inställningarna för proxyservern för en lokal processerver

Om en lokal processerver använder en proxyserver för att ansluta till Azure, kan du ändra proxyinställningarna enligt följande:

1. Logga in på den process server-datorn. 
2. Öppna en Admin PowerShell-kommandofönster och kör följande kommando:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Bläddra till mappen **%PROGRAMDATA%\ASR\Agent**, och kör kommandot:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Ta bort en processerver

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Undanta mappar från ett antivirusprogram

Om ett antivirusprogram körs på en skalbar processerver (eller huvudmålservern), kan du undanta följande mappar från ett virusskyddsprogram åtgärder:


- C:\Program Files\Microsoft Azure Recovery Services-agenten
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Processen installationskatalog. Exempel: C:\Program Files (x86) \Microsoft Azure Site Recovery