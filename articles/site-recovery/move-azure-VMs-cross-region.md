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
ms.openlocfilehash: bbede01844f20c0240b154fd319b818a43463131
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824683"
---
# <a name="move-azure-vms-to-another-region"></a>Migrera virtuella Azure-datorer till en annan region

Det finns många scenarier där du vill flytta dina befintliga virtuella Azure IaaS-datorer från en region till en annan – för att förbättra befintliga virtuella datorers tillförlitlighet och tillgänglighet eller om det krävs enligt gällande regler osv., enligt beskrivning. 

I den här självstudien får du lära dig att flytta virtuella Azure-datorer till en annan region med hjälp av Azure Site Recovery. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * [Kontrollera förutsättningar](#verify-prerequisites)
> * [Förbereda virtuella källdatorer](#prepare-the-source-vms)
> * [Förbereda målregionen](#prepare-the-target-region)
> * [Kopiera data till målregionen](#copy-data-to-the-target-region)
> * [Testa konfigurationen](#test-the-configuration)
> * [Utföra flytten](#perform-the-move-to-the-target-region-and-confirm)
> * [Ta bort resursen från källregionen](#discard-the-resource-in-the-source-region)

> [!IMPORTANT]
> I det här dokumentet får du hjälp att flytta virtuella Azure-datorer från en region till en annan i befintligt skick. Om du behöver förbättra tillgängligheten för din infrastruktur genom att flytta virtuella datorer till tillgänglighetszoner läser du självstudien här.

## <a name="verify-prerequisites"></a>Kontrollera förutsättningar

- Kontrollera att du har virtuella Azure-datorer i den Azure-källregion som du vill flytta från.
- Kontrollera om kombinationen av [källregion/målregion är giltig](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) och fatta ett välgrundat beslut om målregionen.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Verifiera kontobehörigheter: Om du nyligen skapade ditt kostnadsfria Azure-konto är du administratör för prenumerationen. Om du inte är administratör för prenumerationen kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en virtuell dator och så småningom kopiera data till målet med Azure Site Recovery måste du ha:

    1. Behörighet att skapa en virtuell dator i Azure-resurser. Den inbyggda rollen ”Virtuell datordeltagare” har dessa behörigheter, som omfattar:
        - Behörighet att skapa en virtuell dator i den valda resursgruppen
        - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
        - Behörighet att skriva till det valda lagringskontot

    2. Du behöver också behörighet för att hantera åtgärder i Azure Site Recovery. Rollen Site Recovery-bidragsgivare har alla behörigheter som krävs för att hantera Site Recovery-åtgärder i ett Recovery Services-valv.

## <a name="prepare-the-source-vms"></a>Förbereda virtuella källdatorer

1. Kontrollera att alla de senaste rotcertifikaten finns på de virtuella Azure-datorer som du vill flytta. Om de senaste rotcertifikaten inte finns kan datakopieringen till målregionen inte aktiveras på grund av säkerhetsbegränsningar.

    - I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.
    - I virtuella datorer med Linux följer du de riktlinjer du fått från Linux-distributören för att hämta de senaste betrodda rotcertifikaten och listan över återkallade certifikat till den virtuella datorn.
2. Använd inte en autentiseringsproxy för att kontrollera nätverksanslutning för virtuella datorer som du vill flytta.
3. Om den virtuella dator som du försöker flytta saknar åtkomst till Internet och använder en brandväggsproxy till att styra utgående åtkomst läser du kraven [här](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Identifiera och anteckna källans nätverkslayout och alla resurser som du för närvarande använder. Det kan till exempel handla om lastbalanserare, NSG:er offentliga IP-adresser osv. för din verifiering.

## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen för haveriberedskap. Kontakta supporten och aktivera den eventuella kvot som krävs.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Om du använder Site Recovery till att kopiera data till målet väljer det samma storlek eller närmast möjliga storlek för den virtuella måldatorn.

3. Se till att du skapar en målresurs för varje komponent som identifieras i källans nätverkslayout. Det är viktigt att se till att dina virtuella datorer innehåller alla funktioner som fanns i källan efter att de har skickats till målregionen.

    > [!NOTE]
    > Azure Site Recovery identifierar och skapar ett virtuellt nätverk och lagringskonto automatiskt när du aktiverar replikering för den virtuella källdatorn. Du kan även skapa dessa resurser i förväg och tilldela dem till den virtuella datorn som en del av det steg där du aktiverar replikering. Du behöver dock skapa andra resurser manuellt i målregionen, enligt beskrivningen nedan.

     I följande dokument beskrivs hur du skapar de vanligaste nätverksresurserna, baserat på den konfigurationen för virtuella källdator.

    - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Offentlig IP-adress](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Alla andra nätverkskomponenter beskrivs i nätverkets [dokumentation](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. Du kan [skapa ett icke-produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manuellt i målregionen om du vill testa konfigurationen innan du slutgiltigt växlar över till målregionen. Detta ger minimala störningar i produktionsmiljön och rekommenderas därför.
    
## <a name="copy-data-to-the-target-region"></a>Kopiera data till målregionen
I stegen nedan får du hjälp att kopiera data till målregionen med hjälp av Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Hanteringsverktyg** > **Backup och Site Recovery**.
3. I **Namn** anger du det egna namnet **ContosoVMVault**. Om du har fler än en prenumeration väljer du den rätta.
4. Skapa en resursgrupp med namnet **ContosoRG**.
5. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. I Recovery Services-valven klickar du på **Översikt** > **ConsotoVMVault** > **+Replikera**.
7. I **Källa** väljer du **Azure**.
8. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
9. Välj Resource Manager-distributionsmodellen. Välj sedan **källprenumerationen** och **källresursgruppen**.
10. Spara inställningarna genom att klicka på **OK**.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivera replikering för virtuella Azure-datorer och börja kopiera data.

Site Recovery hämtar en lista med de virtuella datorer som är kopplade till prenumerationen och resursgruppen.

1. I nästa steg: Välj den virtuella dator som du vill flytta. Klicka sedan på **OK**.
3. I **Inställningar** klickar du på **Haveriberedskap**.
4. I **Konfigurera haveriberedskap** > **Målregion** väljer du den målregion som du ska replikera till.
5. Du kan välja att använda antingen standardmålresurser eller dem som du har skapat i förväg.
6. Klicka på **Aktivera replikering**. Ett jobb startas som aktiverar replikering för den virtuella datorn.

    ![aktivera replikering](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testa konfigurationen


1. Navigera till valvet. I **Inställningar** > **Replikerade objekt** klickar du på den virtuella dator som du vill flytta till målregionen och klickar sedan på ikonen **+ Testa redundans**.
2. I **Testa redundans** väljer du en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstid (RTO)
   - **Senaste appkonsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassat**: Välj en annan återställningspunkt.

3. Välj det virtuella Azure-målnätverk dit du vill flytta de virtuella Azure-datorerna för att testa konfigurationen. 

> [!IMPORTANT]
> Vi rekommenderar att du använder ett separat virtuellt Azure-nätverk för feltestet och inte det produktionsnätverk i den målregion dit du vill flytta dina virtuella datorer så småningom.

4. När du vill börja testa flytten klickar du på **OK**. Om du vill spåra förloppet klickar du på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** > **Site Recovery-jobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella dator som skapades i samband med flyttestet klickar du på **Rensa redundanstestning** i det replikerade objektet. I **Kommentarer** skriver du ned och sparar eventuella observationer från testet.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Utför flytten till målregionen och bekräfta.

1.  Gå till valvet. I **Inställningar** > **Replikerade objekt** klickar du på den virtuella datorn och sedan på **Redundans**.
2. I **Redundans** väljer du **Senaste**. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**. 
4. När jobbet har slutförts kan du kontrollera att den virtuella datorn visas i Azure-målregionen som förväntat.
5. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Genomför**. Detta avslutar flytten till målregionen. Vänta tills incheckningsjobbet har slutförts.

## <a name="discard-the-resource-in-the-source-region"></a>Ta bort resursen från källregionen 

1. Gå till den virtuella datorn.  Klicka på **Inaktivera replikering**.  Detta stoppar processen med att kopiera data för den virtuella datorn.  

> [!IMPORTANT]
> Det är viktigt att utföra steget ovan för att undvika att debiteras för Site Recovery-replikering efter flytten.

Fortsätt med nästa uppsättning steg om du inte har för avsikt att återanvända några av källresurserna.

1. Ta bort alla relevanta nätverksresurser från den källregion som ingick i steg 4 i [Förbereda de virtuella källdatorerna](#prepare-the-source-vms) 
2. Ta bort motsvarande lagringskonto från källregionen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en virtuell Azure-dator till en annan Azure-region. Nu kan du konfigurera haveriberedskap för de flyttade virtuella datorerna.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)

