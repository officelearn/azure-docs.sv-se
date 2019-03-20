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
ms.openlocfilehash: 0f73e68fd0c01d4323e8675d3fa12f7ca1051cdb
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192930"
---
# <a name="move-azure-vms-to-another-region"></a>Migrera virtuella Azure-datorer till en annan region

Du kanske vill flytta Azure-infrastruktur som en tjänst (IaaS) virtuella datorer från en region till en annan för att förbättra tillförlitlighet, tillgänglighet, hantering eller styrning. Den här självstudien Lär dig att flytta virtuella datorer till en annan region med hjälp av Azure Site Recovery. Du lär dig följande:

> [!div class="checklist"]
> * Kontrollera förutsättningar
> * Förbereda källregionens virtuella datorer
> * Förbereda målregionen
> * Kopiera data till målregionen
> * Testa konfigurationen
> * Utföra flytten
> * Ta bort resurser från källregionen


> [!IMPORTANT]
> Den här artikeln beskriver hur du flyttar virtuella Azure-datorer från en region till en annan *skick*. Om målet är att förbättra tillgängligheten för din infrastruktur genom att flytta virtuella datorer till tillgänglighetszoner finns i [flytta virtuella Azure-datorer till Tillgänglighetszoner](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Förutsättningar

- Se till att du har virtuella Azure-datorer i Azure-region som du vill flytta källan *från*.
- Kontrollera att ditt val av [källa / mål-region region kombinationen stöds](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), och välj noggrant målregionen.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Kontrollera behörigheterna. Om du nyligen skapade ditt kostnadsfria Azure-konto *du* är administratör för din prenumeration. Om du inte är administratör kan arbeta med administratören för att få de behörigheter som du behöver:
  -  Om du vill aktivera replikering för en virtuell dator och kopiera data till målet med hjälp av Site Recovery, måste du ha behörighet att skapa en virtuell dator i Azure-resurser. Den inbyggda rollen Virtuell datordeltagare har dessa behörigheter. Med behörighet kan du:
        - Skapa en virtuell dator i den valda resursgruppen.
        - Skapa en virtuell dator i det valda virtuella nätverket.
        - Skriva till det valda lagringskontot.

  - Du måste också behörigheter för att hantera Site Recovery-åtgärder. Rollen Site Recovery-bidragsgivare har alla behörigheter som krävs för att hantera Site Recovery-åtgärder i ett Azure Recovery Services-valv.

## <a name="prepare-the-source-vms"></a>Förbereda virtuella källdatorer

1. Kontrollera att den virtuella Azure-datorer som du planerar att flytta har de senaste rotcertifikaten. Om inte, kan inte du Aktivera kopiering av data till målregion på grund av säkerhetsbegränsningar.

    - Installera de senaste Windows-uppdateringarna för Windows-datorer så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö, följer du standardprocedurerna för Windows Update och certifikat-update för din organisation.
    - Virtuella datorer med Linux följer du anvisningarna från Linux-distributören för att få de senaste betrodda rotcertifikaten och listan över återkallade certifikat.
2. Se till att du inte använder en autentiseringsproxy för att styra nätverksanslutningar för virtuella datorer som du planerar att flytta.
3. När en virtuell dator som du vill flytta har inte åtkomst till internet och använder en brandväggsproxy för att styra utgående åtkomst, kontrollera den [krav](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Dokumentera källa nätverk layouten och alla resurser som du använder, inklusive (men inte begränsat till) belastningsutjämnare, nätverkssäkerhetsgrupper och offentliga IP-adresser för verifiering.

## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Kontrollera att du kan skapa virtuella datorer i målregionen som används för haveriberedskap i din Azure-prenumeration. Kontakta supporten om du vill aktivera kvoten som krävs om det behövs.

2. Se till att din prenumeration har tillräckligt med resurser för att stödja dina virtuella källdatorer. Om du använder Site Recovery för att kopiera data till målet väljer samma storlek eller närmaste tillgängliga storleken för de virtuella måldatorerna.

3. Se till att du skapar en målresurs för varje komponent som du identifierade i källan nätverk layout. Detta säkerställer att dina virtuella datorer har alla funktioner och funktioner i målregionen som de hade i källregionen.

   Azure Site Recovery automatiskt identifierar och skapar ett virtuellt nätverk och lagring-konto när du aktiverar replikering för den Virtuella källdatorn. Du kan också skapa dessa resurser och tilldela dem till den virtuella datorn som en del av steget Aktivera replikering. Men du måste manuellt skapa andra resurser i målregionen. Se följande dokument för att skapa de mest använda nätverksresurser baserat på den din Virtuella källdatorkonfigurationen:

   - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [Offentlig IP-adress](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Alla andra nätverkskomponenter, finns det [dokumentation om Azure networking](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. Att testa konfigurationen innan du utför överflyttningen manuellt [skapa ett produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) i målregionen. Testa installationen skapar minimala störningar med produktionsmiljön och rekommenderas.
    
## <a name="copy-data-to-the-target-region"></a>Kopiera data till målregionen
Följande steg använder Azure Site Recovery för att kopiera data till målregionen.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Skapa valvet i alla regioner utom källan

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Välj **skapa en resurs** > **hanteringsverktyg** > **Backup och Site Recovery**.
3. För **namn**, ange det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp med namnet **ContosoRG**.
5. Ange en Azure-region. Du kan kontrollera vilka regioner som stöds [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För Recovery Services-valv väljer **översikt** > **ConsotoVMVault** > **+ replikera**.
7. För **källa**väljer **Azure**.
8. För **källplats**, Välj den Azure källregion där de virtuella datorerna körs just nu.
9. Välj den Azure Resource Manager-distributionsmodellen. Välj den **datakälla prenumerationen** och **källresursgruppen**.
10. Välj **OK** att spara inställningarna.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivera replikering för virtuella datorer i Azure och börja kopiera data

Site Recovery hämtar en lista över de virtuella datorerna som är associerade med prenumerationen och resursgruppen.

1. Välj den virtuella datorn som du vill flytta och välj sedan **OK**.
2. För **inställningar**väljer **haveriberedskap**.
3. För **konfigurera haveriberedskap** > **målregionen**, väljer du den målregion som du replikerar till.
4. Välja att använda standard-målresurser eller de som du redan har skapat.
5. Välj **Aktivera replikering** att starta jobbet.

   ![Aktivera replikering](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testa konfigurationen


1. Gå till valvet. I **inställningar** > **replikerade objekt**, Välj den virtuella dator som du vill flytta till målregionen. Välj **Redundanstest**.
2. I **Redundanstest**, Välj en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Ingen tid är på bearbetning av data, så det här alternativet ger en låg återställningstid (RTO).
   - **Senaste appkonsekventa**: Växlar över alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassat**: Välj en annan återställningspunkt.

3. Välj det virtuella Azure-målnätverk dit du vill flytta de virtuella Azure-datorerna för att testa konfigurationen.

   > [!IMPORTANT]
   > Vi rekommenderar att du använder ett separat nätverk för virtuella Azure-datorn för att testa redundans, inte produktionsnätverket i målregionen.

4. Om du vill börja testa flytten, Välj **OK**. Om du vill spåra förloppet, väljer du den virtuella datorn att öppna dess **egenskaper.** Eller Välj den **Redundanstest** jobbet i valvet. Välj **inställningar** > **jobb** > **Site Recovery-jobb**.
5. När redundansen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella datorn som du skapade för testning, Välj **Rensa redundanstestning** på det replikerade objektet. Från **anteckningar**och sparar eventuella observationer som rör testet.

## <a name="perform-the-move-and-confirm"></a>Utföra förflyttningen och bekräfta

1. Gå till valvet i **inställningar** > **replikerade objekt**, Välj den virtuella datorn och välj sedan **redundans**.
1. För **redundans**väljer **senaste**. 
2. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den Virtuella källdatorn innan redundansen. Men redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa redundansförloppet på sidan **Jobb**.
3. När jobbet har slutförts kontrollerar du att den virtuella datorn visas i mål-Azure-region som förväntat.
4. I **replikerade objekt**, högerklicka på den virtuella datorn och välj **genomför**. Detta avslutar flytten. Vänta tills commit-jobbet har slutförts.

## <a name="discard-the-resources-from-the-source-region"></a>Ta bort resurser från källregionen

- Gå till den virtuella datorn och välj **inaktivera replikering**. Detta stoppar processen med att kopiera data för den virtuella datorn.

  > [!IMPORTANT]
  > Slutför det här steget för att undvika att debiteras för Site Recovery-replikering efter flytten.

Om du inte planerar att använda någon av resurserna som källa, gör du följande:

1. Ta bort alla relevanta nätverksresurser i källregionen som du angett i steg 4 i [förbereda virtuella datorer](#prepare-the-source-vms).
2. Ta bort motsvarande lagringskonto från källregionen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att flytta virtuella Azure-datorer till en annan Azure-region. Nu kan du konfigurera haveriberedskap för dessa virtuella datorer.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)

