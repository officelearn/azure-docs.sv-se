---
title: Hantera en processerver för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hantera en processerver som ställts in för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: d99b5d1fdca39466d5e09ca077329b7ffa8622bc
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568860"
---
# <a name="manage-process-servers"></a>Hantera processervrar

Som standard installeras processervern som används när du replikerar virtuella VMware-datorer eller fysiska servrar till Azure på den lokala configuration server-datorn. Det finns några instanser där du behöver ställa in en separat processerver:

- Du kanske behöver ytterligare lokala processervrar som skala kapaciteten för stora distributioner.
- För återställning efter fel, behöver du en tillfällig processerver i Azure. Du kan ta bort den här virtuella datorn när återställningen är klar. 

Den här artikeln sammanfattas vanliga hanteringsuppgifter för dessa ytterligare processervrar.

## <a name="upgrade-a-process-server"></a>Uppgradera en processerver

Uppgradera en processerver som körs lokalt eller i Azure (för återställning efter fel), enligt följande:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  När du använder Azure Gallery Image för att skapa en processerver i Azure för återställning efter fel, körs den normalt den senast tillgängliga versionen. Site Recovery utvecklingsteam versionen korrigeringar och förbättringar med jämna mellanrum och vi rekommenderar att du behåller processervrar uppdaterad.



## <a name="reregister-a-process-server"></a>Registrera en processerver

Om du vill registrera en processerver som körs lokalt eller i Azure, med konfigurationsservern, gör du följande:

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

Om processervern använder en proxyserver för att ansluta till Site Recovery i Azure, kan du använda den här proceduren om du behöver ändra befintliga proxyinställningar.

1. Logga in på den process server-datorn. 
2. Öppna en Admin PowerShell-kommandofönster och kör följande kommando:
  ```powershell
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. Bläddra till mappen **%PROGRAMDATA%\ASR\Agent**, och kör följande kommando:
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>Ta bort en processerver

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>Hantera ett antivirusprogram på processervrar

Om ett antivirusprogram är aktiv på en fristående processerver eller huvudmålserver, undanta följande mappar från ett virusskyddsprogram åtgärder:


- C:\Program Files\Microsoft Azure Recovery Services-agenten
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Installationskatalog processen, exempel: C:\Program Files (x86) \Microsoft Azure Site Recovery

