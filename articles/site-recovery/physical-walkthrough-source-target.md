---
title: "Konfigurera käll- och för fysisk serverreplikering till Azure med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattar hur du gör inställningar för källa och mål för replikering av fysiska servrar till Azure-lagring med Azure Site Recovery-tjänsten"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>Steg 7: Konfigurera käll- och för fysisk serverreplikering till Azure

Den här artikeln beskriver hur du konfigurerar inställningar för källan och målet vid replikering av lokala fysiska servrar till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.

Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Ställ in konfigurationsservern, registrera i valvet och identifiera datorer.

1. Klicka på **Site Recovery** > **steg 1: förbereda infrastrukturen** > **källa**.
2. Om du inte har en konfigurationsserver, klickar du på **+ konfigurationsservern**.
3. I **Lägg till Server**, kontrollera att **konfigurationsservern** visas i **servertyp**.
4. Hämta installationsfilen för enhetlig installationsprogram för Site Recovery.
5. Ladda ned valvregistreringsnyckeln. Du behöver detta när du kör installationsprogrammet för enhetlig. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Konfigurera källan](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Registrera konfigurationsservern i valvet

Gör följande innan du startar och sedan installera enhetlig för att installera konfigurationsservern, processervern och huvudmålservern. Videon beskriver hur du konfigurerar komponenterna för VMware VM till Azure-replikering. Dock gäller samma process för fysisk server till Azure-replikering.

- Kontrollera att systemklockan är synkroniserad med i konfigurationsservern VM och en [tidsserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Den måste matcha. Installationen misslyckas om det är 15 minuter framför eller bakom.
- Kör installationsprogrammet som en lokal administratör på configuration server-datorn.
- Kontrollera att TLS 1.0 är aktiverat på den virtuella datorn.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurationsservern kan också installeras [från kommandoraden](http://aka.ms/installconfigsrv).




## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Innan du konfigurerar målmiljön, kontrollera att du har ett Azure storage-konto och virtuella nätverk som har upprättats.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den Azure-prenumeration som du vill använda.
2. Ange om ditt mål distributionsmodell är Resource Manager-baserade eller klassiska.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![mål](./media/physical-walkthrough-source-target/gs-target.png)

4. Om du inte har skapat ett lagringskonto eller nätverket, klickar du på **+ lagringskonto** eller **+ nätverk**, för att skapa ett konto för hanteraren för filserverresurser eller nätverk infogad.

## <a name="next-steps"></a>Nästa steg

Gå till [steg 8: skapa en replikeringsprincip](physical-walkthrough-replication.md)
