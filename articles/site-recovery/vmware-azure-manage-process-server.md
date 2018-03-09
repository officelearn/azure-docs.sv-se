---
title: Hantera en processerver i Azure Site Recovery | Microsoft Docs
description: "Den här artikeln beskriver hantera en processerver har ställts in för VMware VM och fysisk server-replikering i Azure Site Recovery."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
editor: 
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 096b2890d41402448809ae87759fcd6b67bee2fe
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="manage-process-servers"></a>Hantera servrar

Som standard installeras processervern som används när du replikerar virtuella VMware-datorer eller fysiska servrar till Azure på den lokala configuration server-datorn. Det finns ett antal instanser som du måste ställa in en separat process-server:

- Du kan behöva ytterligare lokala servrar att skala kapacitet för stora distributioner.
- För återställning efter fel, behöver du en tillfällig processerver i Azure. Du kan ta bort den här virtuella datorn när återställningen är klar. 

Den här artikeln sammanfattar vanliga hanteringsuppgifter för dessa ytterligare servrar.

## <a name="upgrade-a-process-server"></a>Uppgradera en processerver

Uppgradera en processerver som kör lokalt eller i Azure (för återställning efter fel), enligt följande:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Vanligtvis när du använder bild för Azure-galleriet för att skapa en processerver i Azure för återställning efter fel, kör den den senaste versionen. Site Recovery team versionen korrigeringar och förbättringar regelbundet och vi rekommenderar att du behåller servrar uppdaterade.



## <a name="reregister-a-process-server"></a>Registrera en processerver

Om du behöver registrera en processerver körs lokalt eller i Azure, med konfigurationsservern och göra följande:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

När du har sparat inställningarna gör du följande:

1. Öppna en administratörskommandotolk på processervern.
2. Bläddra till mappen **%PROGRAMDATA%\ASR\Agent**, och kör kommandot:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Ändra proxyinställningar för en lokal process-server

Om processervern använder en proxyserver för att ansluta till Site Recovery i Azure, kan du använda den här proceduren om du behöver ändra befintliga proxyinställningar.

1. Logga in på serverdatorn för processen. 
2. Öppna en Admin PowerShell-Kommandotolken och kör följande kommando:
  ```
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


