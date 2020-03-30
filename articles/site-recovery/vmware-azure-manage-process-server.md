---
title: Hantera en processserver för virtuella datorer med VMware/fysisk serverkatastrofåterställning i Azure Site Recovery
description: I den här artikeln beskrivs hantera en processserver för haveriberedskap för virtuella datorer/fysiska servrar med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257256"
---
# <a name="manage-process-servers"></a>Hantera processervrar

I den här artikeln beskrivs vanliga uppgifter för att hantera processservern för webbplatsåterställning.

Processservern används för att ta emot, optimera och skicka replikeringsdata till Azure. Den utför också en push-installation av mobilitetstjänsten på virtuella datorer och fysiska servrar som du vill replikera och utför automatisk identifiering av lokala datorer. För att replikera lokala virtuella datorer med VMware eller fysiska servrar till Azure installeras processservern som standard på konfigurationsserverdatorn. 

- För stora distributioner kan du behöva ytterligare lokala processservrar för att skala kapacitet.
- För återställning efter fel från Azure till lokalt måste du konfigurera en tillfällig processserver i Azure. Du kan ta bort den här virtuella datorn när återställning efter fel är klart. 

Läs mer om processservern.


## <a name="upgrade-a-process-server"></a>Uppgradera en processserver

När du distribuerar en processserver lokalt, eller som en Azure VM för återställning efter fel, installeras den senaste versionen av processservern. Site Recovery-teamen släpper korrigeringar och förbättringar regelbundet, och vi rekommenderar att du håller processservrar uppdaterade. Du kan uppgradera en processserver på följande sätt:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Flytta virtuella datorer för att balansera processserverbelastningen

Balansera belastningen genom att flytta virtuella datorer mellan två processservrar enligt följande:

1. Klicka **på** Infrastruktur för **webbplatsåterställning**i valvet. Klicka på **Konfigurationsservrar** **under För & fysiska datorer.**
2. Klicka på den konfigurationsserver som processservrarna är registrerade med.
3. Klicka på den processserver som du vill läsa in balanstrafik för.

    ![LoadBalance (loadbalance)](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Klicka på **Belastningssaldo**, välj den målprocessserver som du vill flytta datorer till. Klicka sedan på **OK**

    ![LoadPS (LoadPS)](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klicka på **Välj datorer**och välj de datorer som du vill flytta från den aktuella till målprocessservern. Information om genomsnittlig dataändring visas mot varje virtuell dator. Klicka sedan på **OK**. 
3. I valvet övervakar du förloppet för jobbet under **Jobb för återställning** > av**webbplats**.

Det tar cirka 15 minuter innan ändringar återspeglas i portalen. Om du vill ha en snabbare effekt [uppdaterar du konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Växla en hel arbetsbelastning till en annan processserver

Flytta hela arbetsbelastningen som hanteras av en processserver till en annan processserver enligt följande:

1. Klicka **på** Infrastruktur för **webbplatsåterställning**i valvet. Klicka på **Konfigurationsservrar** **under För & fysiska datorer.**
2. Klicka på den konfigurationsserver som processservrarna är registrerade med.
3. Klicka på den processserver som du vill byta arbetsbelastning från.
4. Klicka på **Växla**, välj den målprocessserver som du vill flytta arbetsbelastningen till. Klicka sedan på **OK**

    ![Växel](media/vmware-azure-manage-process-server/Switch.PNG)

5. I valvet övervakar du förloppet för jobbet under **Jobb för återställning** > av**webbplats**.

Det tar cirka 15 minuter innan ändringar återspeglas i portalen. Om du vill ha en snabbare effekt [uppdaterar du konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Registrera en huvudmålserver

Huvudmålserver finns på konfigurationsserver- och skalningsprocessservrar. Den måste vara registrerad hos konfigurationsservern. Om det finns ett fel i den här registreringen kan det påverka hälsan för skyddade objekt. Om du vill registrera huvudmålservern med konfigurationsservern loggar du in på den specifika konfigurationsserver/utskalningsprocessserver där registreringen krävs. Navigera till mappen **%PROGRAMDATA%\ASR\Agent**och kör följande på kommando prompten för administratörer.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Registrera om en processserver

Registrera om en processserver som körs lokalt eller på en virtuell Azure-dator med konfigurationsservern enligt följande:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

När du har sparat inställningarna gör du följande:

1. Öppna en kommandotolk för administratörer på processservern.
2. Bläddra till mappen **%PROGRAMDATA%\ASR\Agent**och kör kommandot:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Ändra proxyinställningar för en lokal processserver

Om en lokal processserver använder en proxy för att ansluta till Azure kan du ändra proxyinställningarna på följande sätt:

1. Logga in på processservermaskinen. 
2. Öppna ett kommandofönster för Admin PowerShell och kör följande kommando:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Bläddra till mappen **%PROGRAMDATA%\ASR\Agent**och kör det här kommandot:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Ta bort en processserver

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Exkludera mappar från antivirusprogram

Om antivirusprogram körs på en skalningsprocessserver (eller huvudmålserver) utesluter du följande mappar från antivirusåtgärder:


- C:\Program\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Återställning av Microsoft Azure-webbplatser
- Installationskatalog för processserver. Till exempel: C:\Program-filer (x86)\Microsoft Azure Site Recovery