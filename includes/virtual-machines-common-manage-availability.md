## <a name="understand-planned-vs-unplanned-maintenance"></a>Om planerat och oplanerat underhåll
Det finns två typer av händelser i Microsoft Azure-plattformen som kan påverka tillgängligheten för dina virtuella datorer: planerat och oplanerat underhåll.

* **Planerat underhåll** är periodiska uppdateringar som Microsoft utför i syfte att förbättra tillförlitligheten, prestandan och säkerheten för den plattformsinfrastruktur som dina virtuella datorer körs i. De flesta av uppdateringarna görs utan att det påverkar dina virtuella datorer eller molntjänster. Men i vissa fall måste de virtuella datorerna startas om för att nödvändiga uppdateringar av plattformsinfrastrukturen ska börja gälla.
* **Oplanerat underhåll** utförs när det uppstått något fel på den underliggande maskinvaran eller fysiska infrastrukturen. Det kan vara lokala nätverksfel, lokala diskfel eller andra fel på racknivå. När ett sådant fel upptäcks migrerar Azure automatiskt din virtuella dator från den felaktiga fysiska datorn till en felfri fysisk dator. Den här typen av händelser kan också göra att den virtuella datorn startas om, men det är ovanligt.

För att undvika påverkan av den här typen av avbrott rekommenderar vi att du gör följande för att säkerställa hög tillgänglighet för dina virtuella datorer:

* [Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans]
* [Konfigurera varje programnivå i separata tillgänglighetsuppsättningar]
* [Kombinera en belastningsutjämnare med tillgänglighetsuppsättningar]
* [Använd flera lagringskonton för varje tillgänglighetsuppsättning]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans
För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Den här konfigurationen garanterar att minst en virtuell dator är tillgänglig under planerat eller oplanerat underhåll och uppfyller garantin på 99,95 procents driftstid i Azures serviceavtal. Mer information finns i [Serviceavtal för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Undvik att endast ha en enda virtuell dator i en tillgänglighetsuppsättning. Virtuella datorer med en sådan konfiguration är inte kvalificerade för serviceavtalets drifttidsgaranti vid planerat Azure-underhåll, med undantag för om den virtuella datorn använder [Azure Premium Storage](../articles/storage/storage-premium-storage.md). Azures serviceavtal gäller för enskilda virtuella datorer som använder Premium Storage.

Varje virtuell dator i tillgänglighetsuppsättningen tilldelas en **uppdateringsdomän** och en **feldomän** av den underliggande Azure-plattformen. För en viss tillgänglighetsuppsättning tilldelas som standard fem uppdateringsdomäner, som inte kan konfigureras av användare, (Resource Manager-distributioner kan utökas till 20 uppdateringsdomäner) för att ange grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas om samtidigt. Om fler än fem virtuella datorer har konfigurerats i en enskild tillgänglighetsuppsättning placeras den sjätte virtuella datorn i samma uppdateringsdomän som den första virtuella datorn. Den sjunde placeras i samma uppdateringsdomän som den andra virtuella datorn och så vidare. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om.

Feldomäner definierar den grupp av virtuella datorer som delar samma strömkälla och nätverksswitch. Som standard är de virtuella datorer som konfigureras i din tillgänglighetsuppsättning indelade i tre feldomäner för Resource Manager-distributioner (två feldomäner för klassisk distribution). Att placera de virtuella datorerna i en tillgänglighetsuppsättning skyddar inte ditt program mot operativsystemfel eller programspecifika fel, men det begränsar påverkan av potentiella fel på fysisk maskinvara, problem med nätverket och strömavbrott.

<!--Image reference-->
   ![Skiss på en konfiguration med uppdateringsdomäner och feldomäner](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains-and-availability-sets"></a>Feldomäner och tillgänglighetsuppsättningar för hanterad disk
För virtuella datorer som använder [Azure Managed Disks](../articles/storage/storage-faq-for-disks.md) justeras de virtuella datorerna efter feldomänerna för hanterade diskar när en hanterad tillgänglighetsuppsättning används. På så sätt säkerställs att alla hanterade diskar som är kopplade till en virtuell dator finns i samma feldomän. Endast virtuella datorer med hanterade diskar kan skapas i en hanterad tillgänglighetsuppsättning. Antalet feldomäner kan vara två eller tre, beroende på region.


## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Konfigurera varje programnivå i separata tillgänglighetsuppsättningar
Om de virtuella datorerna är nästan identiska och tjänar samma syfte för ditt program rekommenderar vi att du konfigurerar en tillgänglighetsuppsättning för varje nivå av ditt program.  Om du placerar två olika nivåer i samma tillgänglighetsuppsättning kan alla virtuella datorer på samma programnivå startas om samtidigt. Genom att konfigurera minst två virtuella datorer i en tillgänglighetsuppsättning för varje nivå garanterar du att minst en virtuell dator är tillgänglig på varje nivå.

Du kan till exempel placera alla virtuella datorer i klientdelen av ditt program som kör IIS, Apache eller Nginx i en enskild tillgänglighetsuppsättning. Se till att endast virtuella klientdatorer placeras i samma tillgänglighetsuppsättning. Se också till att endast virtuella datanivådatorer placeras i en egen tillgänglighetsuppsättning, till exempel replikerade virtuella datorer som kör SQL Server eller MySQL.

<!--Image reference-->
   ![Programnivåer](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Kombinera en belastningsutjämnare med tillgänglighetsuppsättningar
Kombinera [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) med en tillgänglighetsuppsättning för att få bästa programåterhämtning. Azure Load Balancer distribuerar trafiken mellan flera virtuella datorer. Azure Load Balancer ingår i standardnivån för Virtual Machines. Azure Load Balancer ingår inte i alla nivåer för Virtual Machines. Mer information om belastningsutjämning för virtuella datorer finns i [Belastningsutjämna virtuella datorer](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Om belastningsutjämnaren inte konfigureras för att jämna ut trafiken mellan flera virtuella datorer kommer varje planerat underhåll att påverka endast den virtuella dator som hanterar trafik och orsaka ett avbrott på programnivån. Om du placerar flera virtuella datorer av samma nivå under samma belastningsutjämnare och tillgänglighetsuppsättning kommer trafiken att hanteras kontinuerligt av minst en instans.

## <a name="use-multiple-storage-accounts-for-each-availability-set"></a>Använd flera lagringskonton för varje tillgänglighetsuppsättning
Om du använder Azure Managed Disks kan du hoppa över följande vägledning. Azure Managed Disks har inbyggd hög tillgänglighet och redundans eftersom diskarna lagras i feldomäner som justeras efter dina VM-tillgänglighetsuppsättningar. Mer information finns i [Översikt över Azure Managed Disks](../articles/storage/storage-managed-disks-overview.md).

Om du använder ohanterade diskar finns metodtips för de lagringskonton som används av virtuella hårddiskar (VHD) i den virtuella datorn. Varje virtuell hårddisk är en sidblob i ett Azure Storage-konto. Det är viktigt att se till att det finns redundans och isolering i alla lagringskonton så att hög tillgänglighet garanteras för de virtuella datorerna i tillgänglighetsuppsättningen.

1. **Förvara alla diskar (operativsystem och data) som är associerade med en virtuell dator i samma lagringskonto**
2. **[Gränserna](../articles/storage/storage-scalability-targets.md) för lagringskontot ska tas i beaktande** när flera virtuella hårddiskar läggs till i ett lagringskonto
3. **Använd ett separat lagringskonto för varje virtuell dator i en tillgänglighetsuppsättning.** Flera virtuella datorer i samma tillgänglighetsuppsättning får INTE dela samma lagringskonto. Virtuella datorer i olika tillgänglighetsuppsättningar får dela samma lagringskonto så länge föregående metodtips följs

<!-- Link references -->
[Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurera varje programnivå i separata tillgänglighetsuppsättningar]: #configure-each-application-tier-into-separate-availability-sets
[Kombinera en belastningsutjämnare med tillgänglighetsuppsättningar]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Använd flera lagringskonton för varje tillgänglighetsuppsättning]: #use-multiple-storage-accounts-for-each-availability-set



<!--HONumber=Feb17_HO2-->


