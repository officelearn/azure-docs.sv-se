---
title: Konfigurera replikering för Azure-diskkryptering (ADE) aktiverade virtuella datorer i Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar replikering för ADE virtuella datorer från en Azure-region till en annan med Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/14/2018
ms.author: sutalasi
ms.openlocfilehash: 828e75a61b7f5d6d747b13069d25503b43b65222
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46311936"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>Replikera Azure disk encryption (ADE) aktiverade virtuella datorer till en annan Azure-region

Den här artikeln beskriver hur du aktiverar replikering av Azure-diskkryptering (ADE) aktiverade virtuella datorer från en Azure-region till en annan.

>[!NOTE]
>Endast virtuella Azure-datorer som kör Windows OS och [aktiverat för kryptering med Azure AD-app](https://aka.ms/ade-aad-app) stöds för närvarande av Azure Site Recovery.
>

## <a name="required-user-permissions"></a>Nödvändiga användarbehörigheter

Om du vill aktivera replikering av virtuella datorer ADE från portalen måste användaren ha den nedanstående behörigheter.
- Nyckelvalvet behörigheter
    - lista
    - Skapa
    - Hämta

-   Nyckelvalv hemliga behörigheter
    - Visa lista
    - Skapa
    - Hämta

- Nyckelvalv nyckelns behörigheter (krävs endast om de virtuella datorerna använda krypteringsnyckel för att kryptera krypteringsnycklarna på Disk)
    - Visa lista
    - Hämta
    - Skapa
    - Kryptera
    - Avkryptera

Du kan hantera behörigheter genom att gå till key vault-resursen på portalen och lägger till behörigheterna som krävs för användaren.

![keyvaultpermissions](./media/azure-to-azure-how-to-enable-replication-ade-vms/keyvaultpermissions.png)

Om du aktiverar haveriberedskap (DR) inte har behörigheterna som krävs för att kopiera nycklar, i skriptet nedan kan ges till säkerhetsadministratör med rätt behörighet för att kopiera kryptering hemligheter och nycklar till målregionen.

>[!NOTE]
>Om du vill aktivera replikering av ADE VM från portalen, behöver du minst ”lista”-behörigheter på nyckelvalven, hemligheter och nycklar
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>Kopiera ADE nycklar till regionen för Haveriberedskap med hjälp av PowerShell-skript

1. Öppna 'CopyKeys' raw-skriptkoden i ett webbläsarfönster genom att klicka på [den här länken](https://aka.ms/ade-asr-copy-keys-code).
2. Kopiera skriptet till en fil och ge den namnet ”kopiera-keys.ps1”.
2. Öppna Windows PowerShell-programmet och gå till mappen där filen finns.
3. Starta ”kopiera-keys.ps1”
4. Ange autentiseringsuppgifter för Azure-inloggning.
5. Välj den **Azure-prenumeration** för dina virtuella datorer.
6. Vänta tills resursgrupper kan du läsa in och välj sedan den **resursgrupp** för dina virtuella datorer.
7. Välj de virtuella datorerna i listan över virtuella datorer visas. Endast virtuella datorer med Azure-diskkryptering aktiverat visas i listan.
8. Välj den **målplats**.
9. **Disk encryption nyckelvalv**: som standard skapar Azure Site Recovery ett nytt nyckelvalv i målregionen med namn som har suffixet ”asr” baserat på käll-VM för diskkrypteringsnycklarna. Om nyckelvalvet som skapats av Azure Site Recovery redan finns, återanvänds. Du kan välja ett annat nyckelvalv från listan över om det behövs.
10. **Nyckeln nyckelvalv för kryptering**: som standard skapar Azure Site Recovery ett nytt nyckelvalv i målregionen med namn som har suffixet ”asr” baserat på källan VM viktiga krypteringsnycklar. Om nyckelvalvet som skapats av Azure Site Recovery redan finns, återanvänds. Du kan välja ett annat nyckelvalv från listan över om det behövs.

## <a name="enable-replication"></a>Aktivera replikering

Den här proceduren förutsätter att den primära Azure-regionen är Östasien och den sekundära regionen Sydostasien.

1. I valvet, klickar du på **+ replikera**.
2. Observera följande fält:
    - **Källan**: det datum då ursprung för de virtuella datorerna i det här fallet **Azure**.
    - **Källplats**: Azure-regionen där du vill skydda dina virtuella datorer. För den här bilden ser är källplatsen 'Östasien'
    - **Distributionsmodell**: Azure-distributionsmodellen källdatorer.
    - **Käll-prenumeration**: den prenumeration som din virtuella källdatorer tillhör. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
    - **Resursgrupp**: den resursgrupp som din virtuella källdatorer tillhör. Alla virtuella datorer under den valda resursgruppen listas för skydd i nästa steg.

3. I **virtuella datorer > Välj virtuella datorer**och klicka på Välj varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.

4. I **inställningar**, du kan också konfigurera Målinställningar för platsen:

    - **Målplats**: den plats där dina källdata för virtuell dator kommer att replikeras. Beroende på din plats för valda datorer, får Site Recovery du listan med lämplig målregioner. Vi rekommenderar att du behåller målplatsen samma som platsen för Recovery Services-valvet.
    - **Målprenumeration**: Målprenumerationen som används för haveriberedskap. Som standard är målprenumerationen samma som källprenumerationen.
    - **Målresursgrupp**: resursgruppen där alla de replikerade virtuella datorerna tillhör. Som standard skapar Azure Site Recovery en ny resursgrupp i målregionen med namn som har suffixet ”asr”. Om resursgruppen redan skapat av Azure Site Recovery finns, återanvänds. Du kan också välja att anpassa den som visas i avsnittet nedan. Platsen för målresursgruppen kan vara alla Azure-regioner utom den region som är värd för virtuella källdatorer.
    - **Målnätverket har virtuella**: som standard skapar Site Recovery ett nytt virtuellt nätverk i målregionen med namn som har suffixet ”asr”. Detta är mappad till nätverket källa och används för alla framtida skydd. [Läs mer](site-recovery-network-mapping-azure-to-azure.md) om nätverksmappning.
    - **Mållagringskonton (om källan Virtuella måldatorn inte använder hanterade diskar)**: som standard skapar Site Recovery ett nytt mållagringskonto frihandsbilden lagringskonfigurationen för käll-VM. Om lagringskontot finns redan, återanvänds.
    - **Hanterade replikeringsdiskar (om den Virtuella måldatorn använder hanterade diskar)**: Site Recovery skapar nya hanterade replikeringsdiskar i målregionen som speglar den Virtuella källdatorns hanterade diskar med samma lagringstyp (Standard eller premium) som den Virtuella källdatorn hanterade disk.
    - **Cachelagringskonton**: Site Recovery behöver extra lagringskonto med namnet cachelagring i källregionen. Alla ändringar som sker på virtuella källdatorn är spåras och skickas till cachelagringskontot innan du replikerar de till målplatsen.
    - **Tillgänglighetsuppsättning**: som standard skapar Azure Site Recovery en ny tillgänglighetsuppsättning i målregionen med namn som har suffixet ”asr”. Tillgänglighetsuppsättning som skapats av Azure Site Recovery redan finns, återanvänds.
    - **Disk encryption nyckelvalv**: som standard skapar Azure Site Recovery ett nytt nyckelvalv i målregionen med namn som har suffixet ”asr” baserat på käll-VM för diskkrypteringsnycklarna. Om nyckelvalvet som skapats av Azure Site Recovery redan finns, återanvänds.
    - **Nyckeln nyckelvalv för kryptering**: som standard skapar Azure Site Recovery ett nytt nyckelvalv i målregionen med namn som har suffixet ”asr” baserat på källan VM viktiga krypteringsnycklar. Om nyckelvalvet som skapats av Azure Site Recovery redan finns, återanvänds.
    - **Replikeringsprincip**: den definierar inställningarna för recovery point kvarhållning och frekvensen för programkonsekventa ögonblicksbilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningarna för ”24 timmars för kvarhållning av återställningspunkt och” 60 minuters för frekvens för appkonsekvent ögonblicksbild.



## <a name="customize-target-resources"></a>Anpassa målresurser

Du kan ändra standardinställningarna för målet som används av Site Recovery.


1. Klicka på **anpassa:** bredvid mål-prenumeration att ändra målprenumerationen standard. Välj prenumerationen i listan över alla prenumerationer som är tillgängliga i samma Azure Active Directory (AAD)-klient.

2. Klicka på **anpassa:** bredvid ' resursgrupp, lagring, nätverk och tillgänglighet som anger för att ändra den nedan standardinställningar:
    - I **målresursgrupp**, Välj resursgruppen i listan över alla resursgrupper i målplatsen för prenumerationen.
    - I **virtuellt Målnätverk**, väljer nätverket från en lista över det virtuella nätverket på målplatsen.
    - I **tillgänglighetsuppsättning**, du kan lägga till inställningar för tillgänglighetsuppsättningen till den virtuella datorn, om de är en del av en tillgänglighetsuppsättning i källregionen.
    - I **Mållagring konton**, väljer du det konto som du vill använda.


2. Klicka på **anpassa:** bredvid inställningar för datakryptering att ändra den nedan standardinställningar:
    - I **Target disk encryption-nyckelvalv**, Välj mål disk encryption key vault i listan med alla nyckelvalv på målplatsen för prenumerationen.
  - I **Target nyckelkryptering nyckelvalv**, Välj nyckelvalvet för nyckelkryptering som mål i listan med alla nyckelvalv på målplatsen för prenumerationen.

3. Klicka på **skapa målresursen** > **Aktivera replikering**.
4. När de virtuella datorerna har aktiverats för replikering, du kan kontrollera status för VM-hälsa under **replikerade objekt**

>[!NOTE]
>Under den inledande replikeringen kan status ta lite tid att uppdatera utan pågår. Klicka på den **uppdatera** knapp för att hämta senaste status.
>

# <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om hur du kör ett redundanstest.
