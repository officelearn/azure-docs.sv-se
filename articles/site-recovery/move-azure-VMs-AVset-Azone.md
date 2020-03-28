---
title: Flytta virtuella datorer till en Azure-region med tillgänglighetszoner med Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 5728ce7125695b191de4f91d5bd9003384f428a7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298316"
---
# <a name="move-azure-vms-into-availability-zones"></a>Flytta virtuella Azure-datorer till tillgänglighetszoner
Tillgänglighetszoner i Azure hjälper till att skydda dina program och data från datacenterfel. Varje tillgänglighetszon består av ett eller flera datacenter som är utrustade med oberoende ström, kylning och nätverkstjänster. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska separationen av tillgänglighetszoner inom en region hjälper till att skydda program och data från datacenterfel. Med tillgänglighetszoner erbjuder Azure ett servicenivåavtal (SLA) på 99,99 % för drifttid för virtuella datorer . Tillgänglighetszoner stöds i vissa regioner, som nämns i [Vad är tillgänglighetszoner i Azure?](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region).

I ett scenario där dina virtuella datorer distribueras som *en instans* till en viss region och du vill förbättra din tillgänglighet genom att flytta dessa virtuella datorer till en tillgänglighetszon, kan du göra det med hjälp av Azure Site Recovery. Denna åtgärd kan ytterligare kategoriseras i:

- Flytta virtuella datorer med en instans till tillgänglighetszoner i en målregion
- Flytta virtuella datorer i en tillgänglighetsuppsättning till tillgänglighetszoner i en målregion

> [!IMPORTANT]
> Azure Site Recovery stöder för närvarande att flytta virtuella datorer från en region till en annan men stöder inte att flytta inom en region.

## <a name="check-prerequisites"></a>Kontrollera krav

- Kontrollera om målregionen har [stöd för tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region). Kontrollera att ditt val av [källregion/målregionkombination stöds](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Fatta ett välgrundat beslut om målregionen.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Kontrollera kontobehörigheter. Om du just har skapat ditt kostnadsfria Azure-konto är du administratör för din prenumeration. Om du inte är prenumerationsadministratör arbetar du med administratören för att tilldela de behörigheter du behöver. Om du vill aktivera replikering för en virtuell dator och så småningom kopiera data till målet med hjälp av Azure Site Recovery måste du ha:

    1. Behörighet att skapa en virtuell dator i Azure-resurser. Den inbyggda rollen för den virtuella *datorns deltagare* har följande behörigheter, bland annat:
        - Behörighet att skapa en virtuell dator i den valda resursgruppen
        - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
        - Behörighet att skriva till det valda lagringskontot

    2. Behörighet att hantera Azure Site Recovery-uppgifter. Rollen *Deltagare i site recovery* har alla behörigheter som krävs för att hantera åtgärder för webbplatsåterställning i ett recovery services-valv.

## <a name="prepare-the-source-vms"></a>Förbereda källregionens virtuella datorer

1. Dina virtuella datorer bör använda hanterade diskar om du vill flytta dem till en tillgänglighetszon med hjälp av Site Recovery. Du kan konvertera befintliga virtuella Windows-datorer som använder ohanterade diskar för att använda hanterade diskar. Följ stegen på [Konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Kontrollera att tillgänglighetsuppsättningen är konfigurerad som *hanterad*.
2. Kontrollera att alla de senaste rotcertifikaten finns på de virtuella Azure-datorer som du vill flytta. Om de senaste rotcertifikaten inte finns kan datakopian till målregionen inte aktiveras på grund av säkerhetsbegränsningar.

3. I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö följer du standardprocesserna för Windows-uppdatering och certifikatuppdatering för din organisation.

4. För virtuella Linux-datorer följer du vägledningen från din Linux-distributör för att få de senaste betrodda rotcertifikaten och listan över återkallade certifikat på den virtuella datorn.
5. Kontrollera att du inte använder en autentiseringsproxy för att styra nätverksanslutningen för virtuella datorer som du vill flytta.

6. Om den virtuella datorn som du försöker flytta inte har tillgång till internet och använder en brandväggsproxy för att kontrollera utgående åtkomst kontrollerar du kraven vid [Konfigurera utgående nätverksanslutning](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

7. Identifiera källnätverkslayouten och de resurser som du för närvarande använder för verifiering, inklusive belastningsutjämnare, NSG:er och offentlig IP.

## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Kontrollera att din Azure-prenumeration kan du skapa virtuella datorer i målregionen som används för haveriberedskap. Kontakta supporten om det behövs för att aktivera den kvot som krävs.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Om du använder Site Recovery för att kopiera data till målet väljer den samma storlek eller närmaste möjliga storlek för måldatorn.

3. Skapa en målresurs för varje komponent som identifieras i källnätverkslayouten. Den här åtgärden säkerställer att när du har klippt över till målregionen har dina virtuella datorer alla funktioner och funktioner som du hade i källan.

    > [!NOTE]
    > Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverks- och lagringskonto när du aktiverar replikering för källdatorn. Du kan också förskapa dessa resurser och tilldela den virtuella datorn som en del av aktivera replikeringssteget. Men för andra resurser, som nämnts senare, måste du manuellt skapa dem i målregionen.

     Följande dokument visar hur du skapar de vanligaste nätverksresurserna som är relevanta för dig, baserat på käll-VM-konfigurationen.

    - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer)
    - [Offentlig IP](../virtual-network/virtual-network-public-ip-address.md)
    
   För andra nätverkskomponenter finns i [nätverksdokumentationen](https://docs.microsoft.com/azure/?pivot=products&panel=network).

    > [!IMPORTANT]
    > Se till att du använder en zonundant belastningsutjämnare i målet. Du kan läsa mer på [standardbelastningsutjämnings- och tillgänglighetszoner](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Skapa [manuellt ett icke-produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) i målregionen om du vill testa konfigurationen innan du klipper över till målregionen. Vi rekommenderar den här metoden eftersom det orsakar minimal störning i produktionsmiljön.

## <a name="enable-replication"></a>Aktivera replikering
Följande steg hjälper dig när du använder Azure Site Recovery för att aktivera replikering av data till målregionen, innan du så småningom flyttar dem till tillgänglighetszoner.

> [!NOTE]
> Dessa steg är för en enda virtuell dator. Du kan utöka samma till flera virtuella datorer. Gå till valvet för återställningstjänster, välj **+ Replikera**och välj relevanta virtuella datorer tillsammans.

1. I Azure-portalen väljer du **Virtuella datorer**och väljer den virtuella dator som du vill flytta till tillgänglighetszoner.
2. I **Åtgärder** väljer du **Haveriberedskap**.
3. I Området Konfigurera**mål för** **haveriberedskap** > väljer du den målregion som du ska replikera till. Se till att den här regionen [stöder](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region) tillgänglighetszoner.

    ![Urval av målregion](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Välj **Nästa: Avancerade inställningar**.
5. Välj lämpliga värden för målprenumerationen, mål-VM-resursgruppen och det virtuella nätverket.
6. I avsnittet **Tillgänglighet** väljer du den tillgänglighetszon där du vill flytta den virtuella datorn. 
   > [!NOTE]
   > Om du inte ser alternativet för tillgänglighetsuppsättning eller Availabilty-zon kontrollerar du att [förutsättningarna](#prepare-the-source-vms) uppfylls och [att förberedelsen](#prepare-the-source-vms) av käll-virtuella datorer är klar.
  
    ![Val för att välja en tillgänglighetszon](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Välj **Aktivera replikering**. Den här åtgärden startar ett jobb för att aktivera replikering för den virtuella datorn.

## <a name="check-settings"></a>Kontrollera inställningar

När replikeringen har slutförts kan du kontrollera replikeringsstatus, ändra inställningar för replikering och testa distributionen.

1. I den virtuella datormenyn väljer du **Haveriberedskap**.
2. Du kan kontrollera replikeringshälsa, de återställningspunkter som har skapats och källan och målområdena på kartan.

   ![Replikeringsstatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testa konfigurationen

1. Välj **Haveriberedskap**på menyn för den virtuella datorn .
2. Välj ikonen **Testa redundans.**
3. I **Test Redundans**väljer du en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data så den ger ett lågt mål för återställningstid (RTO).
   - **Senaste app-konsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste app-konsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassad**: Välj annan återställningspunkt.

3. Välj målets virtuella Azure-testnätverk som du vill flytta de virtuella Azure-datorerna till för att testa konfigurationen. 

    > [!IMPORTANT]
    > Vi rekommenderar att du använder ett separat Azure VM-nätverk för testfelet och inte produktionsnätverket i målregionen där du vill flytta dina virtuella datorer.

4. Om du vill börja testa flytten väljer du **OK**. Om du vill spåra förloppet väljer du den virtuella datorn för att öppna dess egenskaper. Du kan också välja jobbet **Testa redundans** i valvnamnet >**Jobs** > **Jobbplatsåterställningsjobb**för **inställningar** > .
5. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella datorn som skapats som en del av att testa flytten väljer du **Rensa test redundans** på det replikerade objektet. I **Kommentarer** skriver du ned och sparar eventuella observationer från testet.

## <a name="move-to-the-target-region-and-confirm"></a>Flytta till målområdet och bekräfta

1.  Välj **Haveriberedskap**på menyn för den virtuella datorn .
2. Välj ikonen **Redundans.**
3. I **Redundans** väljer du **Senaste**. 
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**. 
5. När jobbet är klart kontrollerar du att den virtuella datorn visas i mål Azure-regionen som förväntat.
6. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Genomför**. Detta avslutar flytten till målregionen. Vänta tills commit-jobbet är klart.

## <a name="discard-the-resource-in-the-source-region"></a>Ta bort resursen från källregionen

Gå till den virtuella datorn. Välj **Inaktivera replikering**. Den här åtgärden stoppar processen att kopiera data för den virtuella datorn.  

> [!IMPORTANT]
> Gör föregående steg för att undvika att debiteras för site recovery-replikering efter flytten. Källans replikeringsinställningar rensas automatiskt. Observera att tillägget Site Recovery som installeras som en del av replikeringen inte tas bort och måste tas bort manuellt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien ökade du tillgängligheten för en Virtuell Azure-dator genom att flytta till en tillgänglighetsuppsättning eller tillgänglighetszon. Nu kan du ställa in haveriberedskap för den flyttade virtuella datorn.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)


