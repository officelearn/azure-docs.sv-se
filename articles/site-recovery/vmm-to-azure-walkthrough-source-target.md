---
title: "Konfigurera källan och målet för Hyper-V-replikering till Azure (med System Center VMM) med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattar hur du gör inställningar för källa och mål för replikering av Hyper-V virtuella datorer i VMM-moln till Azure-lagring med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5edb6d87-25a5-40fe-b6f1-ddf7b55a6b31
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: c72f839d0a1288dccb7deb3e44fc2b20d64818f0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-with-vmm-replication-to-azure"></a>Steg 8: Ställ in på källan och målet för Hyper-V (med VMM) replikering till Azure

Efter [skapar ett valv](vmm-to-azure-walkthrough-create-vault.md) och att ange vad du vill replikera, Använd den här artikeln för att konfigurera inställningar för källan och målet vid replikering av lokala Hyper-V virtuella datorer i System Center Virtual Machine Manager (VMM)-moln till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.

Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

S1. Klicka på **Förbereda infrastrukturen** > **Källa**.

    ![Set up source](./media/vmm-to-azure-walkthrough-source-target/set-source1.png)

2. I **Förbered källa** klickar du på **+ VMM** för att lägga till en VMM-server.

    ![Konfigurera källan](./media/vmm-to-azure-walkthrough-source-target/set-source2.png)

3. På **Lägg till server** kontrollerar du att **System Center VMM-server** visas i **Servertyp** och att VMM-servern uppfyller [de allmänna kraven och URL-kraven](#prerequisites).
4. Ladda ned installationsfilen för Azure Site Recovery-providern.
5. Ladda ned registreringsnyckeln. Du behöver den när du kör installationsprogrammet. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/vmm-to-azure-walkthrough-source-target/set-source3.png)

## <a name="install-the-provider-on-the-vmm-server"></a>Installera providern på VMM-servern

1. Kör installationsfilen på VMM-servern för providern.
2. I **Microsoft Update** kan du välja uppdateringar så att provideruppdateringarna installeras i enlighet med din Microsoft Update-princip.
3. I **Installation** accepterar du eller ändrar standardinstallationsplatsen för providern och klickar på **Installera**.

    ![Installationsplats](./media/vmm-to-azure-walkthrough-source-target/provider2.png)
4. När installationen är klar klickar du på **Registrera** för att registrera VMM-servern i valvet.
5. På sidan **Valvinställningar** klickar du på **Bläddra** och väljer valvnyckelfilen. Ange Azure Site Recovery-prenumerationen och valvnamnet.

    ![Serverregistrering](./media/vmm-to-azure-walkthrough-source-target/provider10.png)
6. I **Internetanslutning** anger du hur providern som körs på VMM-servern ska ansluta till Site Recovery via internet.

   * Om du vill att providern ska ansluta direkt väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
   * Om din befintliga proxyserver kräver autentisering, eller om du vill använda en anpassad proxyserver, väljer du **Anslut till Azure Site Recovery med proxyserver**.
   * Om du använder en anpassad proxyserver anger du adressen, porten och autentiseringsuppgifterna.
   * Om du använder en proxyserver bör du redan ha tillåtit URL:erna som beskrivs i [krav](#on-premises-prerequisites).
   * Om du använder en anpassad proxyserver skapas ett RunAs-konto (DRAProxyAccount) i VMM automatiskt med de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Du kan ändra inställningarna för RunAs-kontot i VMM i VMM-konsolen. I **Inställningar** expanderar du **Säkerhet** > **Kör som-konton** och ändrar sedan lösenordet för DRAProxyAccount. Du måste starta om VMM-tjänsten så att den här inställningen börjar gälla.

     ![Internet](./media/vmm-to-azure-walkthrough-source-target/provider13.png)
7. Acceptera eller ändra platsen för ett SSL-certifikat som genereras automatiskt för datakryptering. Det här certifikatet används om du aktiverar datakryptering för ett moln som skyddas av Azure på Azure Site Recovery-portalen. Skydda det här certifikatet. När du kör en redundansväxling till Azure måste den dekrypteras om datakryptering är aktiverat.
8. I **Servernamn** anger du ett eget namn som identifierar VMM-servern i valvet. I en klusterkonfiguration anger du namnet på VMM-klusterrollen.
9. Aktivera **Synkronisera molnmetadata** om du vill synkronisera metadata för alla moln på VMM-servern med valvet. Den här åtgärden behöver bara göras en gång på varje server. Om du inte vill synkronisera alla moln kan du lämna den här inställningen avmarkerad och synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen. Slutför processen genom att klicka på **Registrera**.

    ![Serverregistrering](./media/vmm-to-azure-walkthrough-source-target/provider16.png)
10. Registreringen startar. När registreringen är klar visas servern på bladet **Site Recovery-infrastruktur** > **VMM-servrar**.


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Installera Azure Recovery Services-agenten på Hyper-V-värdar

1. När du har konfigurerat providern måste du hämta installationsfilen för Azure Recovery Services-agenten. Kör installationsprogrammet på varje Hyper-V-server i VMM-molnet.

    ![Hyper-V-platser](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent1.png)
2. I **Kravkontroll**, klicka på **Nästa**. Alla nödvändiga komponenter som saknas installeras automatiskt.

    ![Krav för Recovery Services-agenten](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent2.png)
3. Godkänn eller ändra installationsplatsen och cachelagringsplatsen på **Installationsinställningar**. Du kan konfigurera cachen på en enhet som har minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme. Klicka på **Installera**.
4. När installationen är klar klickar du på **Stäng** för att slutföra.

    ![Registrera MARS-agenten](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent3.png)

### <a name="command-line-installation"></a>Kommandoradsinstallation
Du kan installera Microsoft Azure Recovery Services-agenten från kommandoraden med följande kommando:

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Konfigurera Internetåtkomst via en proxyserver till Site Recovery från Hyper-V-värdar

Recovery Services-agenten som körs på Hyper-V-värdar behöver Internetåtkomst till Azure för VM-replikering. Om du ansluter till Internet via en proxyserver konfigurerar du den så här:

1. Öppna snapin-modulen Microsoft Azure Backup MMC på Hyper-V-värden. Som standard finns det en genväg till Microsoft Azure Backup på skrivbordet eller i C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Klicka på **Ändra egenskaper** i snapin-modulen.
3. Ange information om proxyservern på fliken **Proxykonfiguration**.

    ![Registrera MARS-agenten](./media/vmm-to-azure-walkthrough-source-target/mars-proxy.png)
4. Kontrollera att agenten kan nå URL:erna som beskrivs i [kravavsnittet](#on-premises-prerequisites).

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön
Ange Azure-lagringskontot som ska användas för replikering och det Azure-nätverk som virtuella Azure-datorer ska ansluta till efter en redundansväxling.

1. Klicka på **Förbered infrastruktur** > **Mål** och välj den prenumeration och resursgrupp där du vill skapa de redundansväxlade virtuella datorerna. Välj den distributionsmodell som du vill använda i Azure (klassisk eller Resource Manager) för de redundansväxlade virtuella datorerna.

    ![Lagring](./media/vmm-to-azure-walkthrough-source-target/enablerep3.png)

2. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

    ![Lagring](./media/vmm-to-azure-walkthrough-source-target/compatible-storage.png)

3. Om du inte har skapat ett lagringskonto och vill skapa ett med hjälp av Resource Manager klickar du på **+Lagringskonto** för att göra det direkt.  På bladet **Skapa lagringskonto** anger du kontonamn, typ, prenumeration och plats. Kontot måste finnas på samma plats som Recovery Services-valvet.

   ![Lagring](./media/vmm-to-azure-walkthrough-source-target/gs-createstorage.png)


   * Om du vill skapa ett lagringskonto med hjälp av den klassiska modellen gör du det på Azure-portalen. [Läs mer](../storage/common/storage-create-storage-account.md)
   * Om du använder ett Premium Storage-konto för replikerade data konfigurerar du ytterligare ett standardlagringskonto för att lagra replikeringsloggar som samlar in löpande ändringar i lokala data.
5. Om du inte har skapat ett Azure-nätverk och vill skapa ett med hjälp av Resource Manager klickar du på **+Nätverk** för att göra det direkt. På bladet **Skapa virtuellt nätverk** anger du nätverksnamn, adressintervall, information om undernät, prenumeration och plats. Nätverket måste finnas på samma plats som Recovery Services-valvet.

   ![Nätverk](./media/vmm-to-azure-walkthrough-source-target/gs-createnetwork.png)

   Om du vill skapa ett nätverk med den klassiska modellen gör du det på Azure-portalen. [Läs mer](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).





## <a name="next-steps"></a>Nästa steg

Gå till [steg 9: Konfigurera nätverksmappning](vmm-to-azure-walkthrough-network-mapping.md)
