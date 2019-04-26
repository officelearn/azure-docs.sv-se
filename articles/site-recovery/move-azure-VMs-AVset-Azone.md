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
ms.openlocfilehash: b6107211f49978bbacd1a827a9adc37ccef60a5b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196206"
---
# <a name="move-azure-vms-into-availability-zones"></a>Flytta virtuella Azure-datorer till tillgänglighetszoner
Tillgänglighetszoner i Azure att skydda dina program och data från datacenter havererar. Varje tillgänglighetszon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Fysisk avgränsning av Tillgänglighetszoner inom en region skyddar program och data från datacenter havererar. Azure erbjuder ett servicenivåavtal (SLA) med 99,99% tillgänglighet för virtuella datorer (VM) med Tillgänglighetszoner. Tillgänglighetszoner stöds i utvalda regioner som anges i [vad är Tillgänglighetszoner i Azure?](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones).

I ett scenario där dina virtuella datorer distribueras som *instans* i en viss region, och du vill förbättra tillgängligheten till din genom att flytta de virtuella datorerna till en Tillgänglighetszon, du kan göra det med hjälp av Azure Site Recovery. Den här åtgärden kan ytterligare kategoriseras till:

- Flytta en instans virtuella datorer i Tillgänglighetszoner i en målregion
- Flytta virtuella datorer i en tillgänglighetsuppsättning i Tillgänglighetszoner i en målregion

> [!IMPORTANT]
> För närvarande kan Azure Site Recovery stöder flytta virtuella datorer från en region till en annan men har stöd inte för att flytta inom en region.

## <a name="check-prerequisites"></a>Kontrollera krav

- Kontrollera om målregionen har [stöd för Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). Kontrollera att ditt val av [källa region/mål-region kombinationen stöds](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Fatta ett välgrundat beslut i målregionen.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Kontrollera behörigheterna. Om du nyligen skapade ditt kostnadsfria Azure-konto är du administratör för din prenumeration. Om du inte är prenumerationsadministratör, tillsammans med administratören tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en virtuell dator och slutligen kopierar data till målet med hjälp av Azure Site Recovery, måste du ha:

    1. Behörighet att skapa en virtuell dator i Azure-resurser. Den *virtuell Datordeltagare* inbyggd roll har dessa behörigheter, bland annat:
        - Behörighet att skapa en virtuell dator i den valda resursgruppen
        - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
        - Behörighet att skriva till det valda lagringskontot

    2. Behörighet för att hantera Azure Site Recovery-uppgifter. Den *Site Recovery-bidragsgivare* rollen har alla behörigheter som krävs för att hantera Site Recovery-åtgärder i ett Recovery Services-valv.

## <a name="prepare-the-source-vms"></a>Förbereda virtuella källdatorer

1. Dina virtuella datorer bör använda hanterade diskar om du vill flytta dem till en Tillgänglighetszon med hjälp av Site Recovery. Du kan konvertera befintliga Windows-datorer med ohanterade diskar som använder hanterade diskar. Följ stegen i [konvertera en Windows-dator från ohanterade diskar till managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Se till att tillgänglighetsuppsättningen är konfigurerad som *hanteras*.
2. Kontrollera att alla de senaste rotcertifikaten finns på de virtuella Azure-datorer som du vill flytta. Om de senaste rotcertifikaten inte finns, kan Datakopieringen till målregion inte aktiveras på grund av säkerhetsbegränsningar.

3. I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö, följer du de standard Windows update och certifikatet processerna för din organisation.

4. För virtuella Linux-datorer följer du de riktlinjer som tillhandahålls av Linux-distributören för att få de senaste betrodda rotcertifikaten och listan över återkallade certifikat på den virtuella datorn.
5. Kontrollera att du inte använder en autentiseringsproxy för att kontrollera nätverksanslutningen för virtuella datorer som du vill flytta.

6. Om den virtuella datorn som du försöker flytta har inte åtkomst till internet och använder en brandväggsproxy för att styra utgående åtkomst kontrollerar kraven på [ Konfigurera utgående nätverksanslutning](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Identifiera källan nätverk layout och de resurser du för närvarande använder för verifiering, inklusive belastningsutjämnare, NSG: er och offentlig IP-adress.

## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Kontrollera att din Azure-prenumeration kan du skapa virtuella datorer i målregionen för haveriberedskap. Om det behövs, kontakta support om du vill aktivera kvoten som krävs.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Om du använder Site Recovery för att kopiera data till målet, väljer samma storlek eller närmaste möjliga storlek för den Virtuella måldatorn.

3. Skapa en målresurs för varje komponent som anges i källan nätverk layout. Den här åtgärden säkerställer att när du utför snabba lösningar för till målregion, dina virtuella datorer har alla funktioner och funktioner som fanns i källan.

    > [!NOTE]
    > Azure Site Recovery automatiskt identifierar och skapar ett virtuellt nätverk och lagring-konto när du aktiverar replikering för den Virtuella källdatorn. Du kan också skapa dessa resurser och tilldela till den virtuella datorn som en del av Aktivera replikering steg. Men för vissa resurser, som tidigare nämnts senare, måste du skapa dem manuellt i målregionen.

     I följande dokument hur du skapar de mest använda nätverksresurser som är relevanta för dig, baserat på den Virtuella källdatorkonfigurationen.

    - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Offentlig IP-adress](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Alla andra nätverkskomponenter, finns i nätverket [dokumentation](https://docs.microsoft.com/azure/#pivot=products&panel=network).

    > [!IMPORTANT]
    > Kontrollera att du använder en zonredundant belastningsutjämnare i målet. Du kan läsa mer på [Standard Load Balancer och Tillgänglighetszoner](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Manuellt [skapa ett produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) i målregionen om du vill testa konfigurationen innan du utför snabba lösningar för till målregion. Vi rekommenderar den här metoden eftersom den gör minimala störningar med produktionsmiljön.

## <a name="enable-replication"></a>Aktivera replikering
Följande steg vägleder dig när du använder Azure Site Recovery för att aktivera replikering av data till målregion innan du så småningom att flytta dem i Tillgänglighetszoner.

> [!NOTE]
> De här stegen är för en enskild virtuell dator. Du kan utöka samma till flera virtuella datorer. Gå till Recovery Services-valv väljer **+ replikera**, och välj de relevanta virtuella datorerna tillsammans.

1. I Azure-portalen väljer du **virtuella datorer**, och välj den virtuella datorn som du vill flytta i Tillgänglighetszoner.
2. I **Operations**väljer **haveriberedskap**.
3. I **konfigurera haveriberedskap** > **målregionen**, Välj den målregion som du ska replikera. Se till att den här regionen [stöder](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) Tillgänglighetszoner.

    ![Välja målregion](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Välj **Nästa: Avancerade inställningar**.
5. Välj lämpliga värden för målprenumerationen, virtuella datorns målresursgrupp och virtuellt nätverk.
6. I den **tillgänglighet** väljer Tillgänglighetszon dit du vill flytta den virtuella datorn. 
   > [!NOTE]
   > Om du inte ser alternativet för tillgänglighetsuppsättning eller tillgänglighet zon, kontrollerar du att den [krav](#prepare-the-source-vms) är uppfyllda och [förberedelse](#prepare-the-source-vms) av virtuella datorer är klar.
  
    ![Val för att välja en Tillgänglighetszon](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Välj **Aktivera replikering**. Den här åtgärden startar ett jobb för att aktivera replikering för den virtuella datorn.

## <a name="check-settings"></a>Kontrollera inställningarna

När replikeringen har slutförts kan du kontrollera replikeringsstatus, ändra inställningar för replikering och testa distributionen.

1. I VM-menyn väljer du **haveriberedskap**.
2. Du kan kontrollera replikeringshälsan, återställningspunkter som har skapats och källa och målregioner på kartan.

   ![Replikeringsstatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testa konfigurationen

1. I virtuella datorns meny väljer **haveriberedskap**.
2. Välj den **Redundanstest** ikon.
3. I **Redundanstest**, Välj en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data så den ger ett lågt mål för återställningstid (RTO).
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassat**: Välj en annan återställningspunkt.

3. Välj målets virtuella Azure-testnätverk som du vill flytta de virtuella Azure-datorerna till för att testa konfigurationen. 

    > [!IMPORTANT]
    > Vi rekommenderar att du använder en separat Azure VM-nätverk för testfel och inte produktionsnätverket i målregionen som du vill flytta dina virtuella datorer.

4. Om du vill börja testa flytten, Välj **OK**. Om du vill spåra förloppet, väljer du den virtuella datorn att öppna dess egenskaper. Du kan också välja den **Redundanstest** jobbet i valvnamnet > **inställningar** > **jobb** > **Site Recovery-jobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella datorn skapats som en del av testning flytten, Välj **Rensa redundanstestning** på det replikerade objektet. I **Kommentarer** skriver du ned och sparar eventuella observationer från testet.

## <a name="move-to-the-target-region-and-confirm"></a>Flytta till målregion och bekräfta

1.  I virtuella datorns meny väljer **haveriberedskap**.
2. Välj den **redundans** ikon.
3. I **Redundans** väljer du **Senaste**. 
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**. 
5. När jobbet har slutförts kan du kontrollera att den virtuella datorn visas i mål-Azure-region som förväntat.
6. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Genomför**. Detta avslutar flytten till målregionen. Vänta tills commit-jobbet har slutförts.

## <a name="discard-the-resource-in-the-source-region"></a>Ta bort resursen från källregionen

Gå till den virtuella datorn. Välj **inaktivera replikering**. Den här åtgärden stoppar processen att kopiera data för den virtuella datorn.  

> [!IMPORTANT]
> Göra det föregående steget för att undvika att debiteras för Site Recovery-replikering efter flytten. Källans replikeringsinställningar rensas automatiskt. Observera att Site Recovery-tillägget som installeras som en del av replikeringen tas inte bort och måste tas bort manuellt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien får ökar du tillgängligheten för en Azure-dator genom att flytta till en tillgänglighetsuppsättning eller Tillgänglighetszon. Du kan nu ange katastrofåterställning för flyttade VM.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)


