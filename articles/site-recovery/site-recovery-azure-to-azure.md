---
title: "Replikera virtuella Azure-datorer mellan Azure-regioner för katastrofåterställningskrav: Azure till Azure | Microsoft Docs"
description: "Sammanfattas de steg som du behöver för att replikera virtuella Azure-datorer mellan Azure-regioner (Azure till Azure) med Azure Site Recovery-tjänsten för disaster recovery behov."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Replikera virtuella Azure-datorer mellan regioner med Azure Site Recovery

>[!NOTE]
>
> Azure Site Recovery replikering för virtuella Azure-datorer (VM) är för närvarande under förhandsgranskning.

Den här artikeln beskriver hur du replikerar virtuella Azure-datorer mellan Azure-regioner med hjälp av den [Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.

Publicera kommentarer och frågor längst ned i den här artikeln eller på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="disaster-recovery-in-azure"></a>Katastrofåterställning i Azure

Inbyggda Azure-infrastrukturen-funktioner bidrar till ett stabilt och flexibel tillgängligheten för arbetsbelastningar som körs på virtuella Azure-datorer. Det finns många orsaker till varför du behöver planera för katastrofåterställning mellan Azure-regioner själv:

- Du måste uppfylla efterlevnadsriktlinjer för specifika appar och arbetsbelastningar som kräver en affärskontinuitet och haveriberedskap (BCDR).
- Vill du kunna skydda och återställa virtuella Azure-datorer baserat på din affärsbeslut och inte bara baserat på inbyggda funktioner för Azure.
- Du behöver testa redundans och återställning i enlighet med ditt företag och efterlevnad behov utan inverkan på produktion.
- Du måste växla över till regionen som recovery vid en katastrof och växla tillbaka till den ursprungliga källa regionen sömlöst.

Använda Site Recovery för Azure-Azure VM-replikering som hjälper dig att utföra dessa uppgifter.


## <a name="why-use-site-recovery"></a>Varför ska jag använda Site Recovery?      

Site Recovery tillhandahåller ett enkelt sätt att replikera virtuella Azure-datorer mellan regioner:

- **Automatisk distribution**. Till skillnad från en aktiv-aktiv replikeringsmodell behövs det ingen för en kostsam och komplexa infrastruktur på den sekundära regionen. När du aktiverar replikering skapar Site Recovery automatiskt resurserna som krävs i målregionen, baserat på inställningarna för datakälla region.
- **Kontrollera regioner**. Med Site Recovery kan du replikera från en region för varje region inom en kontinent. Jämför detta med läsbehörighet geo-redundant lagring, som replikerar asynkront mellan standard [länkas regioner](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) endast. Geo-redundant lagring med läsbehörighet tillhandahåller skrivskyddad åtkomst till data i mål-region.
- **Automatisk replikering**. Site Recovery tillhandahåller automatiserad kontinuerlig replikering. Redundans och återställning kan aktiveras med en enda klickning.
- **Återställningstidsmålet och Återställningspunktmål**. Site Recovery utnyttjar Azure nätverksinfrastrukturen som ansluter regioner för att hålla RTO och Återställningspunktmål mycket låg.
- **Testa**. Du kan köra katastrofåterställning flyttar med på begäran redundanstestningar, vid behov utan att påverka dina produktionsarbetsbelastningar eller pågående replikering.
- **Återställningsplaner**. Du kan använda återställningsplaner för att samordna redundans och återställning efter fel för hela programmet körs på flera virtuella datorer. Funktionen återställning plan har omfattande förstklassigt integrering med Azure automation-runbooks.


## <a name="deployment-summary"></a>Översikt över distribution

Här följer en sammanfattning av vad du behöver göra för att konfigurera replikering av virtuella datorer mellan Azure-regioner:

1. Skapa ett Recovery Services-valv. Valvet innehåller konfigurationsinställningar och styr replikeringen.
2. Aktivera replikering för virtuella Azure-datorer.
3. Köra ett redundanstest för att se till att allt fungerar som förväntat.

>[!IMPORTANT]
>
> Du kan kontrollera den [stöd matrix för replikering av Azure Virtuella](./site-recovery-support-matrix-azure-to-azure.md).

>[!IMPORTANT]
>
> Mer information om hur du konfigurerar nödvändiga utgående nätverksanslutningen för virtuella datorer i Azure för Site Recovery replikering finns i [nätverk riktlinjerna](./site-recovery-azure-to-azure-networking-guidance.md).

### <a name="before-you-start"></a>Innan du börjar

* Ditt Azure-konto måste ha vissa [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) att aktivera replikering för en virtuell Azure-dator.
* Din Azure-prenumeration måste vara aktiverat för att skapa virtuella datorer i den målplats som du vill använda som disaster recovery-region. Kontakta supporten om du vill aktivera kvoten som krävs.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> Vi rekommenderar att du skapar Recovery Services-valvet på den plats där du vill att dina virtuella datorer för replikering. Om exempelvis din målplatsen är centrala USA, skapa valv i **centrala USA**.

## <a name="enable-replication"></a>Aktivera replikering

I **Recovery Services-valv**, klicka på valvnamnet. I valvet, klickar du på den **+ replikera** knappen.

### <a name="step-1-configure-the-source"></a>Steg 1. Konfigurera källan
1. I **källa**väljer **Azure - PREVIEW**.
2. I **datakällplats**, väljer du den Azure-region där din virtuella dator körs för närvarande.
3. Välj distributionsmodell för din virtuella datorer: **Resource Manager** eller **klassiska**.
4. Välj den **källresursgruppen** för hanteraren för virtuella datorer eller **Molntjänsten** för klassiska virtuella datorer.

    ![Konfigurera källan](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>Steg 2. Välj virtuella datorer

* Välj de virtuella datorerna som du vill replikera och klicka sedan på **OK**.

    ![Välj virtuella datorer](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>Steg 3. Konfigurera inställningar

1. Åsidosätter standardinställningarna för målet och ange inställningarna för ditt val genom att klicka på **anpassa**. Mer information finns i [anpassa målresurser](site-recovery-replicate-azure-to-azure.md##customize-target-resources).

    ![Konfigurera inställningar](./media/site-recovery-azure-to-azure/settings.png)


2. Som standard skapar Site Recovery en replikeringsprincip som tar programkonsekventa ögonblicksbilder var fjärde timme och behåller återställningspunkter under 24 timmar. Klicka för att skapa en princip med olika inställningar **anpassa** bredvid **Replikeringsprincipen**.

    ![Anpassa principen](./media/site-recovery-azure-to-azure/customize-policy.png)

3. Starta etablering target-resurser, klicka på **skapa målresurser**. Etablering tar en minut. Stäng inte bladet under etableringen eller måste du börja om från början.

4. Om du vill starta replikeringen av den valda virtuella datorn, klickar du på **Aktivera replikering**.

5. Du kan följa förloppet för den **Aktivera skydd** jobb **inställningar** > **jobb** > **Site Recovery-jobb**.

6. I **inställningar** > **replikerade objekt**, du kan visa status för virtuella datorer och den inledande replikeringen pågår. Klicka på den virtuella datorn och öka detaljnivån till dess inställningar.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

När du ställer in allt köra ett redundanstest för att kontrollera att allt fungerar som förväntat:

1. Att växla över en enskild dator i **inställningar** > **replikerade objekt**, klicka på den virtuella datorn **+ testa redundans** ikon.

2. Att växla över en återställningsplan i **inställningar** > **Återställningsplaner**, högerklickar på planen **Redundanstest**. Om du vill skapa en återställningsplan [följer du dessa instruktioner](site-recovery-create-recovery-plans.md). 

3. I **Redundanstest**, Välj det virtuella Azure-nätverket som virtuella Azure-datorer är ansluten till efter att växlingen sker.

4. Starta växlingen vid fel, klicka på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på den **Redundanstest** jobb i valvnamnet > **inställningar** > **jobb** > **Site Recovery-jobb**.

5. När redundansväxlingen är klar repliken Azure datorn visas i Azure-portalen > **virtuella datorer**. Kontrollera att den virtuella datorn är rätt storlek som den är ansluten till rätt nätverk och att den körs.

6. Ta bort de virtuella datorerna som skapades under redundanstestningen, klicka på **Rensa redundanstestet** på det replikerade objektet eller återställningsplanen. I **anteckningar**, registrera och spara observationer från redundanstestningen. 

[Lär dig mer](site-recovery-test-failover-to-azure.md) om redundanstestning.


## <a name="next-steps"></a>Nästa steg

Testa distributionen när du har:

- [Lär dig mer](site-recovery-failover.md) om olika typer av redundansväxlingar och hur du kör dem.
- Lär dig mer om [med återställningsplaner](site-recovery-create-recovery-plans.md) att minska Återställningstidsmål.
