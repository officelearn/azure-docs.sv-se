---
title: "Ställ in haveriberedskap för lokala Hyper-V virtuella datorer (utan VMM) till Azure med Azure Site Recovery | Microsoft Docs"
description: "Lär dig hur du ställer in haveriberedskap för lokala Hyper-V virtuella datorer (utan VMM) till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31`/2017
ms.author: raynew
ms.openlocfilehash: 633c14bd25bc8a1419196b2c76ca94c26db68991
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Ställ in haveriberedskap för lokala Hyper-V virtuella datorer till Azure

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten bidrar till din strategi för katastrofåterställning genom att hantera och samordna replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Den här kursen visar hur du ställer in haveriberedskap för lokala Hyper-V virtuella datorer till Azure. Kursen gäller för Hyper-V virtuella datorer som inte hanteras av System Center Virtual Machine Manager (VMM). I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Välj källa för replikering och målet.
> * Konfigurera källmiljön för replikering, inklusive lokal Site Recovery-komponenter och replikering målmiljön.
> * Skapa en replikeringsprincip.
> * Aktivera replikering för en virtuell dator.

Detta är den tredje vägledningen i en serie. Den här kursen förutsätter att du redan har slutfört uppgifterna i de föregående självstudiekurser:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

Innan du börjar är det bra att [granska arkitekturen](concepts-hyper-v-to-azure-architecture.md) för den här katastrofåterställning.

## <a name="select-a-replication-goal"></a>Välj ett mål för replikering


1. I **alla tjänster** > **Recovery Services-valv**, klicka på namnet på valvet vi som skapades i föregående självstudierna **ContosoVMVault**.
2. I **komma igång**, klickar du på **Site Recovery**. Klicka på **Förbered infrastrukturen**
3. I **skyddsmål** > **där är dina datorer finns**väljer **lokalt**.
4. I **där du vill replikera dina datorer**väljer **till Azure**.
5. I **är dina datorer virtualiserade**väljer **Ja, med Hyper-V**.
6. I **använder du System Center VMM**väljer **nr**. Klicka sedan på **OK**.

    ![Målet för replikering](./media/tutorial-hyper-v-to-azure/replication-goal.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Om du vill konfigurera källmiljön du till Hyper-V-värdar till en Hyper-V-plats, hämta och installera Azure Site Recovery-providern och Azure Recovery Services-agenten och registrera Hyper-V-platsen i valvet. 

1. I **Förbered infrastrukturen**, klickar du på **källa**.
2. Klicka på **+ Hyper-V-platsen**, och ange namnet på den plats som vi skapade i föregående självstudierna **ContosoHyperVSite**.
3. Klicka på **+ Hyper-V Server**.
4. Hämta installationsfilen för providern.
5. Ladda ned valvregistreringsnyckeln. Du behöver detta när du kör installationsprogrammet för providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Ladda ned providern](./media/tutorial-hyper-v-to-azure/download.png)
    

### <a name="install-the-provider"></a>Installera providern

Kör installationsfilen för providern (AzureSiteRecoveryProvider.exe) på varje Hyper-V värden som du lade till att den **ContosoHyperVSite** plats. Azure Site Recovery Provider och Recovery Services-agenten installeras på varje Hyper-V-värd.

1. I guiden installationsprogram för Azure Site Recovery > **Microsoft Update**, välja för att använda Microsoft Update för att söka efter uppdateringar för providern.
2. I **Installation**, Acceptera standardinstallationsplatsen för providern och agenten och på **installera**.
3. Efter installationen i Registreringsguiden för Microsoft Azure Site Recovery > **Valvinställningar**, klickar du på **Bläddra**, och i **nyckelfilen**väljer valvnyckeln fil som du laddas ned. 
4. Ange Azure Site Recovery-prenumeration, valvnamnet (**ContosoVMVault**), och Hyper-V-platsen (**ContosoHyperVSite**) som Hyper-V-servern tillhör.
5. I **proxyinställningar**väljer **Anslut direkt till Azure Site Recovery utan en proxy**.
6. I **registrering**efter att servern är registrerad i valvet, klickar du på **Slutför**.

Hämtas metadata från Hyper-V-servern av Azure Site Recovery och servern visas i **Site Recovery-infrastruktur** > **Hyper-V-värdar**. Det kan ta upp till 30 minuter.


## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera target-resurser. 

1. Klicka på **Förbered infrastruktur** > **mål**.
2. Välj prenumerationen och resursgruppen **ContosoRG**, i vilket virtuella Azure-datorer skapas efter växling vid fel.
3. Välj den **Resource Manager ”** distributionsmodell.

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.


## <a name="set-up-a-replication-policy"></a>Konfigurera en princip för lösenordsreplikering

1. Klicka på **Förbered infrastruktur** > **replikeringsinställningarna** > **+ skapa och koppla**.
2. I **skapa och koppla princip**, ange ett principnamn **ContosoReplicationPolicy**.
3. Lämna standardinställningarna och klickar på **OK**.
    - **Kopieringsfrekvens** anger att delta data (efter den inledande replikeringen) replikeras var femte minut.
    - **Kvarhållningstid för återställningspunkten** innebär det att kvarhållning windows för varje återställningspunkt två två timmar.
    - **Frekvens av programkonsekventa ögonblicksbilder** anger att återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas varje timme.
    - **Starttid för inledande replikering**, anger den första replikeringen startar omedelbart.
4. När principen skapas, klickar du på **OK**. När du skapar en ny princip associeras den automatiskt med den angivna Hyper-V-platsen (**ContosoHyperVSite**)

    ![Replikeringsprincip](./media/tutorial-hyper-v-to-azure/replication-policy.png)


## <a name="enable-replication"></a>Aktivera replikering


1. I **replikera program**, klickar du på **källa**. 
2. I **källa**, Välj den **ContosoHyperVSite** plats. Klicka sedan på **OK**.
3. I **mål**, kontrollera Azure som mål, valvet-prenumeration och **Resource Manager** distributionsmodell.
4. Välj den **contosovmsacct1910171607** lagringskonto som vi skapade i föregående självstudierna för replikerade data och **ContosoASRnet** nätverk i vilka Azure virtuella datorer kommer att finnas efter växling vid fel.
5. I **virtuella datorer** > **Välj**, Välj den virtuella datorn som du vill replikera. Klicka sedan på **OK**.

 Du kan följa förloppet för den **Aktivera skydd** åtgärden i **jobb** > **Site Recovery-jobb**. Efter den **Slutför skydd** jobbet är slutfört den inledande replikeringen är klar och den virtuella datorn är redo för redundans.

## <a name="next-steps"></a>Nästa steg
[Köra ett återställningstest](tutorial-dr-drill-azure.md)
