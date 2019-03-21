---
title: Flytta virtuella Azure IaaS-datorer till en annan Azure-region med hjälp av Azure Site Recovery-tjänsten | Microsoft Docs
description: Använd Azure Site Recovery för att flytta virtuella IaaS-datorer i Azure från en Azure-region till en annan.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 7619b8831d75ce639c6f6c773c7c7d491abc93e7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58122037"
---
# <a name="move-azure-vms-to-another-region"></a>Migrera virtuella Azure-datorer till en annan region

Det finns olika scenarier där du vill flytta dina befintliga Azure IaaS-datorer (VM) från en region till en annan. Exempelvis kan du förbättra tillförlitlighet och tillgänglighet för dina befintliga virtuella datorer, att förbättra hanterbarhet eller flytta styrning skäl. Mer information finns i den [Azure VM flytta översikt](azure-to-azure-move-overview.md). 

Du kan använda den [Azure Site Recovery](site-recovery-overview.md) att hantera och samordna haveriberedskap på lokala datorer och virtuella Azure-datorer för företag affärskontinuitet och haveriberedskap (BCDR). Du kan också använda Site Recovery för att hantera virtuella Azure-datorer flyttas till en sekundär region.

I den här kursen ska du:

> [!div class="checklist"]
> 
> * Kontrollera krav för flytten
> * Förbereda virtuella källdatorn och målregion
> * Kopiera data och aktivera replikering
> * Testa konfigurationen och utföra förflyttningen
> * Ta bort alla resurser i källregionen
> 
> [!NOTE]
> Den här självstudien visar hur du flyttar virtuella Azure-datorer från en region till en annan skick. Om du behöver att förbättra tillgängligheten genom att flytta virtuella datorer i en tillgänglighetsuppsättning zonen Fäst virtuella datorer i en annan region, finns i den [flytta virtuella Azure-datorer i Tillgänglighetszoner självstudien](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Förutsättningar

- Se till att virtuella Azure-datorer är i Azure-region från vilken du vill flytta.
- Kontrollera att ditt val av [källregionen - mål-region kombinationen stöds](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), och fatta ett välgrundat beslut om målregion.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Kontrollera behörigheterna. Om du har skapat ditt kostnadsfria Azure-konto är du administratör för din prenumeration. Om du inte är administratör för prenumerationen, tillsammans med administratören tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en virtuell dator och kopiera i stort sett data med hjälp av Azure Site Recovery, måste du ha:

    * Behörighet att skapa en virtuell dator i Azure-resurser. Den inbyggda rollen virtuell Datordeltagare har dessa behörigheter, som omfattar:
        - Behörighet att skapa en virtuell dator i den valda resursgruppen
        - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
        - Behörighet att skriva till det valda lagringskontot

    * Behörigheter för att hantera Azure Site Recovery-åtgärder. Rollen Site Recovery-bidragsgivare har alla behörigheter som krävs för att hantera Site Recovery-åtgärder i ett Recovery Services-valv.

## <a name="prepare-the-source-vms"></a>Förbereda virtuella källdatorer

1. Se till att de senaste rotcertifikaten finns på virtuella Azure-datorer som du vill flytta. Om de senaste rotcertifikaten inte finns på den virtuella datorn, förhindrar säkerhetsbegränsningar Datakopieringen till målregionen.

    - I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.
    - För virtuella Linux-datorer följer du de riktlinjer som tillhandahålls av Linux-distributören för att få de senaste betrodda rotcertifikaten och listan över återkallade certifikat på den virtuella datorn.
1. Se till att du inte använder en autentiseringsproxy för att styra nätverksanslutningar för virtuella datorer som du vill flytta.
1. Om den virtuella datorn som du försöker flytta har inte åtkomst till internet, eller om den använder en brandväggsproxy för att styra utgående åtkomst [Kontrollera kraven](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
1. Identifiera källan nätverk layouten och alla resurser som du använder. Detta omfattar men är inte begränsat till belastningsutjämnare, nätverkssäkerhetsgrupper (NSG) och offentliga IP-adresser.

## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Kontrollera att din Azure-prenumeration kan du skapa virtuella datorer i målregionen som används för katastrofåterställning. Kontakta supporten och aktivera den kvot som krävs.

1. Se till att din prenumeration har tillräckligt med resurser som stöder virtuella datorer med storlekarna som matchar dina virtuella källdatorer. Om du använder Site Recovery för att kopiera data till målet väljer Site Recovery samma storlek eller närmaste möjliga storlek för den Virtuella måldatorn.

1. Se till att du skapar en målresurs för varje komponent som har identifierats i källan nätverk layout. Det här steget är viktigt att säkerställa att dina virtuella datorer har alla funktioner och funktioner i målregionen som fanns i källregionen.

    > [!NOTE]
    > Azure Site Recovery automatiskt identifierar och skapar ett virtuellt nätverk när du aktiverar replikering för den Virtuella källdatorn. Du kan också skapa ett nätverk och tilldela den till den virtuella datorn i användarflödet för Aktivera replikering. Som tidigare nämnts senare måste du manuellt skapa andra resurser i målregionen.

     Om du vill skapa de ofta används för nätverksresurser som är relevanta för dig baserat på den Virtuella källdatorkonfigurationen, finns i följande dokumentation:

   - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [Offentlig IP-adress](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
     Alla andra nätverkskomponenter, finns det [nätverk dokumentation](https://docs.microsoft.com/azure/#pivot=products&panel=network).

1. Manuellt [skapa ett produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) i målregionen om du vill testa konfigurationen innan du utför slutliga övergången till målregionen. Vi rekommenderar det här steget eftersom det garanterar minimala störningar med företagets nätverk.

## <a name="copy-data-to-the-target-region"></a>Kopiera data till målregionen
Följande steg visar hur du använder Azure Site Recovery för att kopiera data till målregionen.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Skapa valvet i valfri region, utom i källregionen

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
1. Välj **skapa en resurs** > **hanteringsverktyg** > **Backup och Site Recovery**.
1. I **Namn** anger du det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du den lämpligaste.
1. Skapa resursgruppen **ContosoRG**.
1. Ange en Azure-region. Regioner som stöds finns under geografisk tillgänglighet i [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
1. I **Recovery Services-valv**väljer **översikt** > **ContosoVMVault** > **+ replikera**.
1. I **Källa** väljer du **Azure**.
1. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
1. Välj Resource Manager-distributionsmodellen. Välj sedan **källprenumerationen** och **källresursgruppen**.
1. Välj **OK** att spara inställningarna.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivera replikering för virtuella datorer i Azure och börja kopiera data

Site Recovery hämtar en lista över de virtuella datorerna som är associerade med prenumerationen och resursgruppen.

1. I nästa steg, väljer du den virtuella datorn som du vill flytta och välj sedan **OK**.
1. I **inställningar**väljer **haveriberedskap**.
1. I **konfigurera haveriberedskap** > **målregionen**, Välj den målregion som du ska replikera.
1. I den här självstudiekursen accepterar du de andra standardinställningarna.
1. Välj **Aktivera replikering**. Det här steget startar ett jobb för att aktivera replikering för den virtuella datorn.

    ![Aktivera replikering](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="test-the-configuration"></a>Testa konfigurationen

1. Gå till valvet. I **inställningar** > **replikerade objekt**, Välj den virtuella datorn som du vill flytta till målregion och välj sedan **+ testa redundans**.
1. I **Redundanstest**, Välj en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett låga Recovery mål för återställningstid (RTO).
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassat**: Välj en annan återställningspunkt.

1. Välj det virtuella Azure-målnätverk dit du vill flytta de virtuella Azure-datorerna för att testa konfigurationen.
    > [!IMPORTANT]
    > Vi rekommenderar att du använder en separat Azure VM-nätverk för redundanstestning. Använd inte produktionsnätverk som skapades när du har aktiverat replikering och som du vill flytta dina virtuella datorer till så småningom.
1. När du vill börja testa flytten klickar du på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** > **Site Recovery-jobb**.
1. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs, är lämplig storlek och är ansluten till rätt nätverk.
1. Om du vill ta bort VM-tha skapades som en del av testning flytten, klickar på **Rensa redundanstestning** på det replikerade objektet. I **anteckningar**och sparar eventuella observationer som är associerade med testet.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Utför övergången till målregion och bekräfta

1. Gå till valvet. I **inställningar** > **replikerade objekt**, Välj den virtuella datorn och välj sedan **redundans**.
1. I **Redundans** väljer du **Senaste**.
1. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
1. När jobbet har slutförts kan du kontrollera att den virtuella datorn visas i mål-Azure-region som förväntat.
1. I **replikerade objekt**, höger-Välj den virtuella datorn > **genomför**. Det här steget har slutförts flyttningsprocessen till målregion. Vänta tills commit-jobbet har slutförts.

## <a name="delete-the-resource-in-the-source-region"></a>Ta bort resursen i källregionen

Gå till den virtuella datorn. Välj **inaktivera replikering**. Det här steget stoppar processen från att kopiera data för den virtuella datorn.

> [!IMPORTANT]
> Det är viktigt att utföra det här steget för att undvika att debiteras för Azure Site Recovery-replikering.

Följ dessa steg om du har inga planer på att återanvända någon av resurserna som källa:

1. Ta bort alla relevanta nätverksresurser i källregionen listat som en del av steg 4 i [förbereda virtuella datorer](#prepare-the-source-vms).
1. Ta bort motsvarande lagringskonto från källregionen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien får flyttat du en virtuell Azure-dator till en annan Azure-region. Nu kan du konfigurera haveriberedskap för den virtuella datorn som du har flyttats.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)

