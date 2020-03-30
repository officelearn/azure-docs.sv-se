---
title: Flytta virtuell dator till en annan region (Azure Site Recovery)
description: Lär dig hur du kan migrera din virtuella SQL Server-dator från en region till en annan i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022296"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Flytta virtuell SQL Server till en annan region i Azure med Azure Site Recovery-tjänster

I den här artikeln får du lära dig hur du använder Azure Site Recovery för att migrera din virtuella SQL Server-dator (VM) från en region till en annan i Azure. 

För att flytta en virtuell SQL Server-dator till en annan region krävs följande:
1. [**Förbereda:**](#prepare-to-move)Bekräfta att både din källa SQL Server VM och målregion är tillräckligt förberedda för flytten. 
1. [**Konfigurera:**](#configure-azure-site-recovery-vault)Flytta din VIRTUELLA SQL Server-dator kräver att det är ett replikerat objekt i Azure Site Recovery-valvet. Du måste lägga till din virtuella SQL Server-dator i Azure Site Recovery-valvet. 
1. [**Testning:**](#test-move-process)Migrera SQL Server VM kräver att den inte överskrids från källregionen till den replikerade målregionen. För att säkerställa att flyttprocessen lyckas måste du först testa att din VIRTUELLA SQL Server-dator kan växla över till målregionen. Detta kommer att hjälpa till att avslöja eventuella problem och undvika dem när du utför själva flytten. 
1. [**Flytta:**](#move-the-sql-server-vm)När test failover har passerat, och du vet att du är säker att migrera din SQL Server VM, kan du utföra flytten av den virtuella datorn till målregionen. 
1. [**Rensning:**](#clean-up-source-resources)För att undvika faktureringsavgifter tar du bort den virtuella datorn för SQL Server från valvet och onödiga resurser som finns kvar i resursgruppen. 

## <a name="verify-prerequisites"></a>Kontrollera förutsättningar 

- Bekräfta att det [stöds](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)att flytta från källområdet till målområdet .  
- Granska [scenarioarkitekturen och komponenterna](../../../site-recovery/azure-to-azure-architecture.md) samt [supportbegränsningar och supportkrav](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Verifiera kontobehörigheter. Om du har skapat ditt kostnadsfria Azure-konto är du administratör för din prenumeration. Om du inte är prenumerationsadministratör arbetar du med administratören för att tilldela de behörigheter du behöver. Om du vill aktivera replikering för en virtuell dator och kopiera data med Azure Site Recovery måste du ha: 
    - Behörigheter för att skapa en virtuell dator. Den inbyggda rollen för den virtuella *datorns deltagare* har följande behörigheter, bland annat: 
        - Behörigheter för att skapa en virtuell dator i den valda resursgruppen. 
        - Behörigheter för att skapa en virtuell dator i det valda virtuella nätverket. 
        - Behörighet att skriva till det valda lagringskontot. 
      - Behörigheter för att hantera Azure Site Recovery-åtgärder. Rollen Deltagare i *site recovery* har alla behörigheter som krävs för att hantera site recovery-åtgärder i ett recovery services-valv.  

## <a name="prepare-to-move"></a>Förbered att flytta
Förbered både källan SQL Server VM och målområdet för flytten. 

### <a name="prepare-the-source-sql-server-vm"></a>Förbereda källan SQL Server VM

- Kontrollera att alla de senaste rotcertifikaten finns på den virtuella SQL Server-datorn som du vill flytta. Om de senaste rotcertifikaten inte finns där förhindrar säkerhetsbegränsningar datakopiering till målregionen. 
- För virtuella Windows-datorer installerar du alla de senaste Windows-uppdateringarna på den virtuella datorn, så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö följer du standardprocessen för Uppdatering av Windows UPdate och certifikat för din organisation. 
- För virtuella Linux-datorer följer du vägledningen från din Linux-distributör för att få de senaste betrodda rotcertifikaten och listan över återkallade certifikat på den virtuella datorn. 
- Kontrollera att du inte använder en autentiseringsproxy för att styra nätverksanslutningen för de virtuella datorer som du vill flytta. 
- Om den virtuella datorn som du försöker flytta inte har tillgång till internet, eller om den använder en brandväggsproxy för att kontrollera utgående åtkomst, kontrollerar du kraven. 
- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Detta inkluderar men är inte begränsat till belastningsutjämnare, nätverkssäkerhetsgrupper (NSG) och offentliga IPs. 

### <a name="prepare-the-target-region"></a>Förbereda målregionen

- Kontrollera att din Azure-prenumeration låter dig skapa virtuella datorer i målregionen som används för haveriberedskap. Kontakta supporten och aktivera den kvot som krävs. 
- Kontrollera att prenumerationen har tillräckligt med resurser för att stödja virtuella datorer med storlek som matchar dina virtuella källdrarna. Om du använder Site Recovery för att kopiera data till målet väljer Site Recovery samma storlek eller närmaste möjliga storlek för måldatorn. 
- Se till att du skapar en målresurs för varje komponent som identifieras i källnätverkslayouten. Det här steget är viktigt för att säkerställa att dina virtuella datorer har alla funktioner och funktioner i målregionen som du hade i källregionen. 
    - Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverk när du aktiverar replikering för källdatorn. Du kan också förskapa ett nätverk och tilldela det till den virtuella datorn i användarflödet för att aktivera replikering. Du måste skapa andra resurser manuellt i målregionen.
- Information om hur du skapar de vanligaste nätverksresurserna som är relevanta för dig baserat på käll-VM-konfigurationen finns i följande dokumentation: 
    - [Nätverkssäkerhetsgrupper](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Lastbalanserare](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Offentlig IP-adress](../../../virtual-network/virtual-network-public-ip-address.md)
    - Fler nätverkskomponenter finns i [nätverksdokumentationen](../../../virtual-network/virtual-networks-overview.md).
- Skapa manuellt ett icke-produktionsnätverk i målregionen om du vill testa konfigurationen innan du utför det slutliga steget till målregionen. Vi rekommenderar det här steget eftersom det säkerställer minimal störning i produktionsnätverket. 

## <a name="configure-azure-site-recovery-vault"></a>Konfigurera Azure Site Recovery Vault

Följande steg visar hur du använder Azure Site Recovery för att kopiera data till målregionen. Skapa valvet för återställningstjänster i någon annan region än källregionen. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Välj att **skapa en resurs** i det övre vänstra hörnet i navigeringsfönstret. 
1. Välj **IT-& hanteringsverktyg** och välj sedan **Säkerhetskopiering och Platsåterställning**. 
1. Skapa en ny resursgrupp i målområdet under **Projektinformation**på fliken **Grunderna** eller välj en befintlig resursgrupp i målområdet. 
1. Under **Instansinformation**anger du ett namn för valvet och väljer sedan **målregionen** i listrutan. 
1. Välj **Granska + Skapa** om du vill skapa valvet för återställningstjänster. 
1. Välj **Alla tjänster** i det övre vänstra hörnet i navigeringsfönstret och skriv sökrutan `recovery services`. 
1. (Valfritt) Välj stjärnan bredvid **Recovery Services-valv** för att lägga till den i snabbnavigeringsfältet. 
1. Välj **Recovery-tjänstvalv** och välj sedan det Recovery Services-valv som du skapade. 
1. Välj **Replikera**i **fönstret Översikt** . 

   ![Konfigurera replikering](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. Välj **Källa** och välj sedan **Azure** som källa. Välj lämpliga värden för de andra listrutan, till exempel platsen för käll-virtuella datorer. Endast resursgrupper som finns i **källplatsområdet** visas i fältet **Källresursgrupp.** 
1. Välj **Virtuella datorer** och välj sedan de virtuella datorer som du vill migrera. Välj **OK** för att spara valet av virtuell dator. 
1. Välj **Inställningar**och välj sedan **målplatsen** i listrutan. Detta bör vara den resursgrupp som du förberett tidigare. 
1. När du har anpassat replikering väljer du **Skapa målresurser** för att skapa resurserna på den nya platsen. 
1. När resursskapandet är klart väljer du **Aktivera replikering** för att starta replikeringen av din VIRTUELLA SQL Server-dator från källan till målregionen.
1. Du kan kontrollera replikeringsstatusen genom att navigera till återställningsvalvet, välja **replikerade objekt** och visa **status** för din virtuella SQL Server-dator. En status **för Skyddad** anger att replikeringen har slutförts. 

   ![Kontrollera replikeringsstatus](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Testflyttningsprocess
Följande steg visar hur du använder Azure Site Recovery för att testa flyttprocessen. 

1. Navigera till ditt **Recovery Services-valv** i [Azure-portalen](https://portal.azure.com) och välj **Replikerade objekt**. 
1. Välj den VIRTUELLA SQL Server-dator som du vill flytta, kontrollera att **replikeringshälsan** visas som **felfri** och välj sedan **Testa redundans**. 

   ![Testa redundans för din virtuella dator](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. På sidan **Test redundans** väljer du den **senaste appkonsekventa återställningspunkten** som ska användas för redundansen, eftersom det är den enda typen av ögonblicksbild som kan garantera SQL Server-datakonsekvens. 
1. Välj det virtuella nätverket under **Virtuellt Azure-nätverk** och välj sedan **OK** för att testa redundans. 
   
   >[!IMPORTANT]
   > Vi rekommenderar att du använder ett separat Azure VM-nätverk för redundanstestet. Använd inte produktionsnätverket som skapades när du aktiverade replikering och som du vill flytta dina virtuella datorer till så småningom. 

1. Om du vill övervaka förloppet navigerar du till valvet, väljer **Platsåterställningsjobb** under **Övervakning**och väljer sedan det **testväxlingsjobb** som pågår.

   ![Förlopp av säkerhetsväxling för bildskärm](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. När testet är klart navigerar du till **virtuella datorer** i portalen och granskar den nyskapade virtuella datorn. Kontrollera att DEN VIRTUELLA SQL Server-datorn körs, har storleken på rätt sätt och ansluts till rätt nätverk. 
1. Ta bort den virtuella datorn som skapades som en del av testet, eftersom alternativet **Redundans** kommer att vara nedtonat tills redundanstestresurserna rensas. Navigera tillbaka till valvet, välj **Replikerade objekt,** välj VIRTUELL SQL Server och välj sedan **Rensa test redundans**. Spela in och spara alla observationer som är associerade med testet i avsnittet **Anteckningar** och markera kryssrutan **bredvid Testning är klar. Ta bort virtuella test redundansdatorer**. Välj **OK** för att rensa resurser efter testet. 

   ![rensa objekt efter redundanstest](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Flytta den virtuella datorn för SQL Server 
Följande steg visar hur du flyttar DEN VIRTUELLA SQL Server-datorn från källregionen till målregionen. 

1. Navigera till Recovery Services-valvet, välj **Replikerade objekt,** välj den virtuella datorn och välj sedan **Recovery Services** **Redundans**. 

   ![Initiera redundans](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. Välj den **senaste appkonsekventa** återställningspunkten under **Återställningspunkt**. 
1. Markera kryssrutan **bredvid Stäng av datorn innan du påbörjar redundansen**. Site Recovery kommer att försöka stänga av källan VM innan du utlöser redundans. Redundans fortsätter även om avstängningen misslyckas. 
1. Välj **OK** för att starta redundansen.
1. Du kan övervaka redundansprocessen från samma jobbsida **för platsåterställning** som du visade när du övervakade redundanstestet i föregående avsnitt. 
1. När jobbet är klart kontrollerar du att SQL Server-virtuella datorer visas i målområdet som förväntat. 
1. Navigera tillbaka till valvet, välj **Replikerade objekt,** välj VIRTUELL SQL Server och välj **Commit** för att slutföra flyttprocessen till målområdet. Vänta tills commit-jobbet är klart. 
1. Registrera din VIRTUELLA SQL Server-dator med SQL VM-resursprovidern för att aktivera **SQL-virtuella** datorhanterbarhet i Azure-portalen och funktioner som är associerade med resursprovidern. Mer information finns i [Registrera SQL Server VM med SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-rp.md). 

  > [!WARNING]
  > SQL Server-datakonsekvens garanteras endast med appkonsekventa ögonblicksbilder. Den **senaste bearbetade** ögonblicksbilden kan inte användas för SQL Server-redundans eftersom en ögonblicksbild av kraschåterställning inte kan garantera SQL Server-datakonsekvens. 

## <a name="clean-up-source-resources"></a>Rensa källresurser
Om du vill undvika faktureringsavgifter tar du bort den virtuella datorn för SQL Server från valvet och tar bort onödiga associerade resurser. 

1. Navigera tillbaka till **valvet för platsåterställning,** välj **Replikerade objekt**och välj virtuell SQL Server. 
1. Välj **Inaktivera replikering**. Välj en orsak till att skydda inaktivering och välj sedan **OK** för att inaktivera replikering. 

   >[!IMPORTANT]
   > Det är viktigt att utföra det här steget för att undvika att debiteras för Azure Site Recovery replication. 

1. Om du inte har några planer på att återanvända någon av resurserna i källregionen tar du bort alla relevanta nätverksresurser och motsvarande lagringskonton. 


## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server om en prisvägledning för Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server på en windows VM-viktig anteckning](virtual-machines-windows-sql-server-iaas-release-notes.md)


