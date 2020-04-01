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
ms.openlocfilehash: dc37cb6fa05a2be56de7bf5536d7274190257d85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303927"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Flytta virtuella Azure-datorer till en annan Azure-region

Du kanske vill flytta Azure-infrastruktur som en tjänst (IaaS) virtuella datorer från en region till en annan för att förbättra tillförlitlighet, tillgänglighet, hantering eller styrning. Den här självstudien visar hur du flyttar virtuella datorer till en annan region med hjälp av Azure Site Recovery. Du lär dig följande:

> [!div class="checklist"]
> * Kontrollera förutsättningar
> * Förbereda källregionens virtuella datorer
> * Förbereda målregionen
> * Kopiera data till målregionen
> * Testa konfigurationen
> * Utföra flytten
> * Ta bort resurserna från källregionen


> [!IMPORTANT]
> I den här artikeln beskrivs hur du flyttar virtuella Azure-datorer från en region till en annan *enligt .* Om ditt mål är att förbättra tillgängligheten för din infrastruktur genom att flytta virtuella datorer till tillgänglighetszoner läser du [Flytta virtuella Azure-datorer till tillgänglighetszoner](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Krav

- Se till att du har virtuella Azure-datorer i regionen Azure som du vill flytta *från*.
- Kontrollera att ditt val av [källregion-mål region kombination stöds](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)och noggrant välja målområdet.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Verifiera kontobehörigheter. Om du just har skapat ditt kostnadsfria Azure-konto är *du* administratör för din prenumeration. Om du inte är administratör kan du arbeta med administratören för att få de behörigheter du behöver:
  -  Om du vill aktivera replikering för en virtuell dator och kopiera data till målet med hjälp av Site Recovery måste du ha behörighet att skapa en virtuell dator i dina Azure-resurser. Den inbyggda rollen Virtuell datordeltagare har dessa behörigheter. Med behörigheterna kan du:
        - Skapa en virtuell dator i den valda resursgruppen.
        - Skapa en virtuell dator i det valda virtuella nätverket.
        - Skriva till det valda lagringskontot.

  - Du behöver också behörigheter för att hantera åtgärder för webbplatsåterställning. Rollen Site Recovery Contributor har alla behörigheter som krävs för att hantera site recovery-åtgärder i ett Azure Recovery Services-valv.

## <a name="prepare-the-source-vms"></a>Förbereda källregionens virtuella datorer

1. Kontrollera att de virtuella Azure-datorerna som du planerar att flytta har de senaste rotcertifikaten. Om de inte gör det kan du inte aktivera datakopiering till målområdet på grund av säkerhetsbegränsningar.

    - För virtuella Windows-datorer installerar du de senaste Windows-uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö följer du standardprocesserna för Windows Update och certifikatuppdatering för din organisation.
    - För virtuella Linux-datorer följer du vägledning från din Linux-distributör för att få de senaste betrodda rotcertifikaten och listan över återkallade certifikat.
2. Kontrollera att du inte använder en autentiseringsproxy för att styra nätverksanslutningen för virtuella datorer som du planerar att flytta.
3. Om en virtuell dator som du vill flytta inte har tillgång till internet och använder en brandväggsproxy för att kontrollera utgående åtkomst kontrollerar du [kraven](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
4. Dokumentera källnätverkslayouten och alla resurser som du använder för närvarande, inklusive (men inte begränsat till) belastningsutjämnare, nätverkssäkerhetsgrupper och offentliga IP-adresser för verifiering.

## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. I din Azure-prenumeration kontrollerar du att du kan skapa virtuella datorer i målregionen som används för haveriberedskap. Kontakta supporten för att aktivera den kvot som krävs om det behövs.

2. Kontrollera att prenumerationen har tillräckligt med resurser för att stödja virtuella källdr-datorer. Om du använder Site Recovery för att kopiera data till målet väljer den samma storlek eller närmaste tillgängliga storlek för mål-virtuella datorer.

3. Se till att du skapar en målresurs för varje komponent som du identifierade i källnätverkslayouten. Detta säkerställer att dina virtuella datorer har alla funktioner och funktioner i målregionen som de hade i källregionen.

   Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverks- och lagringskonto när du aktiverar replikering för källdatorn. Du kan också förskapa dessa resurser och tilldela dem till den virtuella datorn som en del av steget aktivera-replikering. Men du måste manuellt skapa andra resurser i målregionen. Se följande dokument för att skapa de vanligaste nätverksresurserna baserat på konfigurationen av den virtuella källan:

   - [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Lastbalanserare](https://docs.microsoft.com/azure/load-balancer)
   - [Offentlig IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Information om andra nätverkskomponenter finns i Dokumentationen för [Azure-nätverk](https://docs.microsoft.com/azure/?pivot=products&panel=network). 

4. Om du vill testa konfigurationen innan du utför flytten skapar du manuellt [ett icke-produktionsnätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) i målregionen. Testa installationen skapar minimal störning med produktionsmiljön, och vi rekommenderar det.
    
## <a name="copy-data-to-the-target-region"></a>Kopiera data till målregionen
I följande steg används Azure Site Recovery för att kopiera data till målregionen.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Skapa valvet i alla regioner utom källan

1. Logga in på [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Välj **Skapa en** > **säkerhetskopiering****av** > resurser och återställning av webbplatser .
3. För **Namn**anger du det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp med namnet **ContosoRG**.
5. Ange en Azure-region. Information om hur du kontrollerar regioner som stöds finns i [Azure Site Recovery Pricing Details](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För Recovery Services-valv väljer du **Översikt** > **ConsotoVMVault** > **+Replicate**.
7. För **Källa**väljer du **Azure**.
8. För **källplats**väljer du den käll-Azure-region där dina virtuella datorer körs för närvarande.
9. Välj distributionsmodellen för Azure Resource Manager. Välj sedan **resursgruppen** **Källa** och Käll .
10. Välj **OK** för att spara inställningarna.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivera replikering för virtuella Azure-datorer och börja kopiera data

Site Recovery hämtar en lista över de virtuella datorer som är associerade med prenumerations- och resursgruppen.

1. Välj den virtuella dator som du vill flytta och välj sedan **OK**.
2. För **Inställningar**väljer du **Haveriberedskap**.
3. För**Konfigurera region**för **mål för katastrofåterställning** > väljer du den målregion som du replikerar till.
4. Välj att använda standardmålresurserna eller de som du har skapat i föra.
5. Välj **Aktivera replikering** för att starta jobbet.

   ![Aktivera replikering](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Testa konfigurationen


1. Gå till valvet. I **Inställningar** > **replikerade objekt**väljer du den virtuella dator som du vill flytta till målområdet. Välj sedan **Testa redundans**.
2. I **Test Redundans**väljer du en återställningspunkt som ska användas för redundansen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Ingen tid går åt till att bearbeta data, så det här alternativet ger ett mål för låg återställningstid (RTO).
   - **Senaste appkonsekvent:** Misslyckas med alla virtuella datorer till den senaste appkonsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassad**: Välj annan återställningspunkt.

3. Välj målets virtuella Azure-nätverk, dit du vill flytta de virtuella Azure-datorerna för att testa konfigurationen.

   > [!IMPORTANT]
   > Vi rekommenderar att du använder ett separat Azure VM-nätverk för test redundans, inte produktionsnätverket i målregionen.

4. Om du vill börja testa flytten väljer du **OK**. Om du vill spåra förloppet väljer du den virtuella datorn för att öppna dess **egenskaper.** Du kan också välja jobbet **Testa redundans** i valvet. Välj sedan >  > **Jobbplatsåterställningsjobb**för **jobb**.**Jobs**
5. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella datorn som du skapade för testning väljer du **Rensa test redundans** på det replikerade objektet. Från **Anteckningar**registrerar och sparar du alla observationer som är relaterade till testet.

## <a name="perform-the-move-and-confirm"></a>Utför flytten och bekräfta

1. Gå till valvet i**Replikerade objekt** **inställningar,** > välj den virtuella datorn och välj sedan **Redundans**.
1. För **Redundans**väljer du **Senaste**. 
2. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av källdatorns virtuella dator innan du utlöser redundansen. Men redundansen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundans på sidan **Jobb**.
3. När jobbet är klart kontrollerar du att den virtuella datorn visas i azure-regionen för målet som förväntat.
4. Högerklicka på den virtuella datorn i **Replikerade objekt**och välj **Commit**. Detta avslutar flytten. Vänta tills commit-jobbet är klart.

## <a name="discard-the-resources-from-the-source-region"></a>Ta bort resurserna från källregionen

- Gå till den virtuella datorn och välj **Inaktivera replikering**. Detta stoppar processen med att kopiera data för den virtuella datorn.

  > [!IMPORTANT]
  > Slutför det här steget för att undvika att debiteras för site recovery-replikering efter flytten.

Om du inte planerar att återanvända någon av källresurserna gör du så här:

1. Ta bort alla relevanta nätverksresurser i källområdet som du listade i steg 4 i [Förbereda källdatorn .](#prepare-the-source-vms)
2. Ta bort motsvarande lagringskonto från källregionen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du flyttar virtuella Azure-datorer till en annan Azure-region. Nu kan du konfigurera haveriberedskap för dessa virtuella datorer.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)

