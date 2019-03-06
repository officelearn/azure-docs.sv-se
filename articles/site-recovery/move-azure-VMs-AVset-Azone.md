---
title: Flytta virtuella Azure IaaS-datorer till en annan Azure-region som zonlåsta virtuella datorer med hjälp av Azure Site Recovery-tjänsten | Microsoft Docs
description: Använd Azure Site Recovery till att flytta virtuella Azure IaaS-datorer till en annan Azure-region som zonlåsta virtuella datorer.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 77c38089a4f9c6518d9736df7f3020c66ad17b3b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877052"
---
# <a name="move-azure-vms-into-availability-zones"></a>Flytta virtuella Azure-datorer till tillgänglighetszoner
Tillgänglighetszonerna i Azure skyddar dina program och data mot datacenterfel. Varje tillgänglighetszon utgörs av ett eller flera datacenter som är utrustade med fristående kraft, nedkylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Med tillgänglighetszonerna kan Azure erbjuda branschens bästa serviceavtal med en drifttid på 99,99 % för virtuella datorer. Tillgänglighetszoner stöds i utvalda regioner, vilket beskrivs [här](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). 

I ett scenario där du har distribuerat dina virtuella datorer som ”enskilda instanser” till en viss region, och du vill förbättra din tillgänglighet genom att flytta dessa till tillgänglighetszonen, kan du göra det med hjälp av Azure Site Recovery. Detta kan kategoriseras ytterligare i:

- Flytta virtuella datorer som enskilda instanser till tillgänglighetszoner i en målregion
- Flytta virtuella datorer i en tillgänglighetsuppsättning till tillgänglighetszoner i en målregion

> [!IMPORTANT]
> För närvarande stöder Azure Site Recovery flytt av virtuella datorer från en region till en annan, men inte flytt inom en region. 

## <a name="verify-prerequisites"></a>Kontrollera förutsättningar

- Kontrollera om målregionen har [stöd för tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) – Kontrollera om ditt val av [kombination för källregion och målregion stöds](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) och fatta ett välgrundat beslut om målregionen.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Verifiera kontobehörigheter: Om du nyligen skapade ditt kostnadsfria Azure-konto är du administratör för prenumerationen. Om du inte är administratör för prenumerationen kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en virtuell dator och så småningom kopiera data till målet med Azure Site Recovery måste du ha:

    1. Behörighet att skapa en virtuell dator i Azure-resurser. Den inbyggda rollen ”Virtuell datordeltagare” har dessa behörigheter, som omfattar:
        - Behörighet att skapa en virtuell dator i den valda resursgruppen
        - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
        - Behörighet att skriva till det valda lagringskontot

    2. Du behöver också behörighet för att hantera åtgärder i Azure Site Recovery. Rollen Site Recovery-bidragsgivare har alla behörigheter som krävs för att hantera Site Recovery-åtgärder i ett Recovery Services-valv.

## <a name="prepare-the-source-vms"></a>Förbereda källans virtuella datorer

1. Dina virtuella datorer måste använda hanterade diskar om du vill flytta dem till en tillgänglighetszon med hjälp av Site Recovery. Du kan konvertera befintliga virtuella Windows-datorer som använder ohanterade diskar och du kan konvertera de virtuella datorerna till att använda hanterade diskar, genom att följa anvisningarna [här](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Om du vill göra detta kontrollerar du att tillgänglighetsuppsättningen är konfigurerad som ”Hanterad”. 
2. Kontrollera att alla de senaste rotcertifikaten finns på de virtuella Azure-datorer som du vill flytta. Om de senaste rotcertifikaten inte finns kan datakopieringen till målregionen inte aktiveras på grund av säkerhetsbegränsningar.

3. I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.

4. I virtuella datorer med Linux följer du de riktlinjer du fått från Linux-distributören för att hämta de senaste betrodda rotcertifikaten och listan över återkallade certifikat till den virtuella datorn.
5. Använd inte en autentiseringsproxy för att kontrollera nätverksanslutning för virtuella datorer som du vill flytta.

6. Om den virtuella dator som du försöker flytta saknar åtkomst till Internet och använder en brandväggsproxy till att styra utgående åtkomst, kontrollerar du kraven [här](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Identifiera källans nätverkslayout och alla resurser som du använder – inklusive belastningsutjämnare, NSG:er, offentlig IP etc. för verifieringen.

## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen för haveriberedskap. Kontakta supporten och aktivera den eventuella kvot som krävs.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Om du använder Site Recovery till att kopiera data till målet väljer det samma storlek eller närmast möjliga storlek för den virtuella måldatorn.

3. Se till att du skapar en målresurs för varje komponent som identifieras i källans nätverkslayout. Det är viktigt att se till att dina virtuella datorer innehåller alla funktioner som fanns i källan efter att de har skickats till målregionen.

    > [!NOTE]
    > Azure Site Recovery identifierar och skapar ett virtuellt nätverk och lagringskonto automatiskt när du aktiverar replikering för den virtuella källdatorn. Du kan även skapa dessa resurser i förväg och tilldela dem till den virtuella datorn som en del av det steg där du aktiverar replikering. Du behöver dock skapa andra resurser manuellt i målregionen, enligt beskrivningen nedan.

     I följande dokument beskrivs hur du skapar de vanligaste nätverksresurserna, baserat på den konfigurationen för virtuella källdator.

    - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials
        
     - [Offentlig IP-adress](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Alla andra nätverkskomponenter beskrivs i nätverkets [dokumentation](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

> [!IMPORTANT]
> Du bör använda en zonredundant lastbalanserare i målet. Läs mer [här](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Du kan [skapa ett icke-produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manuellt i målregionen, om du vill testa konfigurationen innan du skickar den slutliga versionen till målregionen. Detta ger minimala störningar i produktionsmiljön och rekommenderas därför.


> [!NOTE]
> Stegen nedan avser en enskild virtuell dator. Du kan utöka dem till flera virtuella datorer genom att gå till Recovery Services-valvet, klicka på + Replikera och markera de relevanta virtuella datorerna.

## <a name="enable-replication"></a>Aktivera replikering
I stegen nedan visas hur du använder Azure Site Recovery till att aktivera replikering av data till målregionen, innan du så småningom flyttar dem till tillgänglighetszoner.

1. I Azure-portalen klickar du på **Virtuella datorer** och väljer den virtuella dator som du vill flytta till tillgänglighetszonerna.
2. I **Åtgärder** klickar du på **Haveriberedskap**.
3. I **Konfigurera haveriberedskap** > **Målregion** väljer du den målregion som du ska replikera till. Se till att den här regionen [stöder](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) tillgänglighetszoner.
![enable-rep-1.PNG](media/azure-vms-to-zones/enable-rep-1.PNG)

1. Välj **Nästa: Avancerade inställningar**
2. Välj lämpliga värden för målprenumeration, virtuell måldator, resursgrupp och virtuellt nätverk.
3. I avsnittet **Tillgänglighet** väljer du den tillgänglighetszon dit du vill flytta den virtuella datorn. 
> [!NOTE]
> Om du inte ser alternativet för tillgänglighetsuppsättningar eller tillgänglighetszoner bör du kontrollera att [förutsättningar](#prepare-the-source-vms) är uppfyllda och att [förberedelsen](#prepare-the-source-vms) av de virtuella källdatorerna är klar.

   ![enable-rep-2.PNG](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Klicka för att aktivera replikering. Ett jobb startas som aktiverar replikering för den virtuella datorn.

## <a name="verify-settings"></a>Kontrollera inställningarna

När replikeringen har slutförts kan du kontrollera replikeringsstatus, ändra inställningar för replikering och testa distributionen.

1. I den virtuella datormenyn, klickar du på **Haveriberedskap**.
2. Du kan kontrollera replikeringshälsan, återställningspunkter som har skapats samt käll- och målregioner på kartan.

   ![Replikeringsstatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testa konfigurationen

1. I den virtuella datormenyn klickar du på **Haveriberedskap**.
2. Klicka på ikonen **Testa redundans**.
3. I **Testa redundans** väljer du en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstid (RTO)
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassat**: Välj en annan återställningspunkt.

3. Välj målets virtuella Azure-testnätverk som du vill flytta de virtuella Azure-datorerna till för att testa konfigurationen. 

> [!IMPORTANT]
> Vi rekommenderar att du använder ett separat Azure VM-nätverk för testet och inte det produktionsnätverk i målregionen som du vill flytta dina virtuella datorer till så småningom.

4. När du vill börja testa flytten klickar du på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** > **Site Recovery-jobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella dator som skapades i samband med flyttestet klickar du på **Rensa redundanstestning** i det replikerade objektet. I **Kommentarer** skriver du ned och sparar eventuella observationer från testet.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Utför flytten till målregionen och bekräfta.

1.  I den virtuella datormenyn klickar du på **Haveriberedskap**.
2. Klicka på ikonen **Redundans**.
3. I **Redundans** väljer du **Senaste**. 
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**. 
5. När jobbet har slutförts kan du kontrollera att den virtuella datorn visas i Azure-målregionen som förväntat.
6. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Genomför**. Detta avslutar flytten till målregionen. Vänta tills incheckningsjobbet har slutförts.

## <a name="discard-the-resource-in-the-source-region"></a>Ta bort resursen från källregionen 

1. Gå till den virtuella datorn.  Klicka på **Inaktivera replikering**.  Detta stoppar processen med att kopiera data för den virtuella datorn.  

> [!IMPORTANT]
> Det är viktigt att utföra steget ovan för att undvika att debiteras för Site Recovery-replikering efter flytten. Källans replikeringsinställningar rensas automatiskt. Observera att Site Recovery-tillägget som installeras som en del av replikeringen inte tas bort automatiskt utan måste tas bort manuellt. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien ökade du tillgängligheten för en virtuell Azure-dator genom att flytta till en tillgänglighetsuppsättning eller tillgänglighetszon. Nu kan du konfigurera haveriberedskap för de flyttade virtuella datorerna.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)


