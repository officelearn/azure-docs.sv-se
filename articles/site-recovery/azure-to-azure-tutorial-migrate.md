---
title: Flytta virtuella Azure-datorer till en annan region med Azure Site Recovery
description: Använd Azure Site Recovery för att flytta virtuella IaaS-datorer i Azure från en Azure-region till en annan.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 4882206692c334d6ab6af28feb5d2cba5277eea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303944"
---
# <a name="move-azure-vms-to-another-region"></a>Migrera virtuella Azure-datorer till en annan region

Det finns olika scenarier där du vill flytta dina befintliga Virtuella Azure IaaS-datorer (VMs) från en region till en annan. Du vill till exempel förbättra tillförlitligheten och tillgängligheten för dina befintliga virtuella datorer, förbättra hanterbarheten eller flytta av styrningsskäl. Mer information finns i [översikten över flytta azure vm](azure-to-azure-move-overview.md). 

Du kan använda [Azure Site Recovery-tjänsten](site-recovery-overview.md) för att hantera och dirigera haveriberedskap av lokala datorer och virtuella Azure-datorer för affärskontinuitet och haveriberedskap (BCDR). Du kan också använda Site Recovery för att hantera flytten av virtuella Azure-datorer till en sekundär region.

I den här kursen ska du:

> [!div class="checklist"]
> 
> * Verifiera förutsättningar för flytten
> * Förbereda käll-virtuella datorer och målregionen
> * Kopiera data och aktivera replikering
> * Testa konfigurationen och utför flytten
> * Ta bort resurserna i källregionen
> 
> [!NOTE]
> Den här självstudien visar hur du flyttar virtuella Azure-datorer från en region till en annan som den är. Om du behöver förbättra tillgängligheten genom att flytta virtuella datorer i en tillgänglighetsuppsättning till zonsatta virtuella datorer i en annan region läser du [självstudien Flytta Azure virtuella datorer till tillgänglighetszoner](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Krav

- Kontrollera att virtuella Azure-datorer finns i Azure-regionen som du vill flytta från.
- Kontrollera att ditt val av [källregion - kombinationen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)av målregionen stöds och fatta ett välgrundat beslut om målregionen.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Verifiera kontobehörigheter. Om du har skapat ditt kostnadsfria Azure-konto är du administratör för din prenumeration. Om du inte är prenumerationsadministratör arbetar du med administratören för att tilldela de behörigheter du behöver. Om du vill aktivera replikering för en virtuell dator och i huvudsak kopiera data med hjälp av Azure Site Recovery måste du ha:

    - Behörighet att skapa en virtuell dator i Azure-resurser. Den inbyggda rollen för den virtuella datorns deltagare har följande behörigheter, bland annat:
    - Behörighet att skapa en virtuell dator i den valda resursgruppen
    - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
    - Behörighet att skriva till det valda lagringskontot
    
    - Behörigheter för att hantera Azure Site Recovery-åtgärder. Rollen Deltagare i site recovery har alla behörigheter som krävs för att hantera site recovery-åtgärder i ett recovery services-valv.

- Kontrollera att alla de senaste rotcertifikaten finns på de virtuella Azure-datorer som du vill flytta. Om de senaste rotcertifikaten inte finns på den virtuella datorn förhindrar säkerhetsbegränsningar datakopian till målregionen.

- I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.
    
- För virtuella Linux-datorer följer du vägledningen från din Linux-distributör för att få de senaste betrodda rotcertifikaten och listan över återkallade certifikat på den virtuella datorn.
- Kontrollera att du inte använder en autentiseringsproxy för att styra nätverksanslutningen för virtuella datorer som du vill flytta.

- Om den virtuella datorn som du försöker flytta inte har tillgång till internet, eller om den använder en brandväggsproxy för att kontrollera utgående åtkomst, [kontrollerar du kraven](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Detta inkluderar men är inte begränsat till belastningsutjämnare, nätverkssäkerhetsgrupper (NSG) och offentliga IPs.

- Kontrollera att din Azure-prenumeration låter dig skapa virtuella datorer i målregionen som används för haveriberedskap. Kontakta supporten och aktivera den kvot som krävs.

- Se till att din prenumeration har tillräckligt med resurser för att stödja virtuella datorer med storlekar som matchar dina virtuella käll-datorer. Om du använder Site Recovery för att kopiera data till målet väljer Site Recovery samma storlek eller närmaste möjliga storlek för måldatorn.

- Se till att du skapar en målresurs för varje komponent som identifieras i källnätverkslayouten. Det här steget är viktigt för att säkerställa att dina virtuella datorer har alla funktioner och funktioner i målregionen som du hade i källregionen.

     > [!NOTE] 
     > Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverk när du aktiverar replikering för källdatorn. Du kan också förskapa ett nätverk och tilldela det till den virtuella datorn i användarflödet för aktivera replikering. Som nämnts senare måste du manuellt skapa andra resurser i målregionen.

    Information om hur du skapar de vanligaste nätverksresurserna som är relevanta för dig baserat på käll-VM-konfigurationen finns i följande dokumentation:
    - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer)
    -  [Offentlig IP](../virtual-network/virtual-network-public-ip-address.md)
    - Information om andra nätverkskomponenter finns i [nätverksdokumentationen](https://docs.microsoft.com/azure/?pivot=products&panel=network).



## <a name="prepare"></a>Förbereda
Följande steg visar hur du förbereder den virtuella datorn för flytten med Azure Site Recovery som en lösning. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Skapa valvet i alla regioner, utom källregionen

1. Logga in på [Azure Portal](https://portal.azure.com) > **Recovery Services**.
1. Välj **Skapa en** > **säkerhetskopiering****av** > resurser och återställning av webbplatser .
1. I **Namn** anger du det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du den lämpligaste.
1. Skapa resursgruppen **ContosoRG**.
1. Ange en Azure-region. Information om hur du kontrollerar regioner som stöds finns i geografisk tillgänglighet i [prisinformation för Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. I **Recovery Services-valv**väljer du **Översikt** > **ContosoVMVault** > **+Replicate**.
1. I **Källa** väljer du **Azure**.
1. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
1. Välj Resource Manager-distributionsmodellen. Välj sedan **källprenumerationen** och **källresursgruppen**.
1. Välj **OK** för att spara inställningarna.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivera replikering för virtuella Azure-datorer och börja kopiera data

Site Recovery hämtar en lista över de virtuella datorer som är associerade med prenumerations- och resursgruppen.

1. I nästa steg väljer du den virtuella dator som du vill flytta och väljer sedan **OK**.
1. Välj **Haveriberedskap**i **Inställningar**.
1. I Området Konfigurera**mål för** **haveriberedskap** > väljer du den målregion som du ska replikera till.
1. I den här självstudiekursen accepterar du de andra standardinställningarna.
1. Välj **Aktivera replikering**. Det här steget startar ett jobb för att aktivera replikering för den virtuella datorn.

    ![Aktivera replikering](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Flytta

Följande steg visar hur du utför flytten till målregionen.

1. Gå till valvet. I **Inställningar** > **replikerade objekt**väljer du den virtuella datorn och väljer sedan **Redundans**.
2. I **Redundans** väljer du **Senaste**.
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**.
4. När jobbet är klart kontrollerar du att den virtuella datorn visas i mål Azure-regionen som förväntat.


## <a name="discard"></a>Ignorera 

Om du har markerat den flyttade virtuella datorn och behöver ändras till en felningspunkt eller vill gå tillbaka till en föregående punkt, högerklickar du den virtuella datorn > **ändringsåterställningspunkt**i de **replikerade objekten**. Det här steget ger dig möjlighet att ange en annan återställningspunkt och redundans till den. 


## <a name="commit"></a>Checka in 

När du har kontrollerat den flyttade virtuella datorn och är redo att genomföra ändringen, högerklickar du den virtuella datorn > **commit**i de **replikerade objekten.** Det här steget avslutar flyttprocessen till målregionen. Vänta tills commit-jobbet är klart.

## <a name="clean-up"></a>Rensa

Följande steg hjälper dig att rensa källregionen samt relaterade resurser som användes för flytten.

För alla resurser som har använts för flytten:

- Gå till den virtuella datorn. Välj **Inaktivera replikering**. Det här steget hindrar processen från att kopiera data för den virtuella datorn.

   > [!IMPORTANT]
   > Det är viktigt att utföra det här steget för att undvika att debiteras för Azure Site Recovery replication.

Om du inte har några planer på att återanvända någon av källresurserna gör du följande ytterligare steg:

1. Ta bort alla relevanta nätverksresurser i källområdet som du identifierade i [förutsättningar](#prerequisites).
1. Ta bort motsvarande lagringskonto från källregionen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en Azure-virtuell dator till en annan Azure-region. Nu kan du konfigurera haveriberedskap för den virtuella datorn som du har flyttat.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)

