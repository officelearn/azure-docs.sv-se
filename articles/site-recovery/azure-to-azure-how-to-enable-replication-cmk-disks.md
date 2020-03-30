---
title: Aktivera replikering för krypterade virtuella Azure-datorer i Azure Site Recovery
description: I den här artikeln beskrivs hur du konfigurerar replikering för virtuella datorer med customer-managed key (CMK) aktiverade diskar från en Azure-region till en annan med hjälp av Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897967"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replikera datorer med CMK-aktiverade diskar (Customer-Managed Keys)

I den här artikeln beskrivs hur du replikerar virtuella Azure-datorer med CUSTOMER-Managed Keys (CMK) aktiverade hanterade diskar, från en Azure-region till en annan.

## <a name="prerequisite"></a>Krav
Du måste skapa diskkrypteringsuppsättningarna i målregionen för målprenumerationen innan du aktiverar replikering för dina virtuella datorer som har CMK-aktiverade hanterade diskar.

## <a name="enable-replication"></a>Aktivera replikering

I det här exemplet är den primära Azure-regionen Östasien och den sekundära regionen är Sydostasien.

1. Välj **+Replikera**i valvet .
2. Observera följande fält.
    - **Källa**: Utgångspunkten för de virtuella datorerna, som i det här fallet är **Azure**.
    - **Källplats**: Den Azure-region där du vill skydda dina virtuella datorer. I det här exemplet är källplatsen "Östasien".
    - **Distributionsmodell**: Azure-distributionsmodellen för källdatorerna.
    - **Källa prenumeration:** Prenumeration som din källa virtuella datorer tillhör. Det kan vara alla prenumerationer som finns i samma Azure Active Directory-klient som ditt återställningstjänster-valv.
    - **Resursgrupp**: Den resursgrupp som käll virtuella datorer tillhör. Alla virtuella datorer i den valda resursgruppen visas för skydd i nästa steg.

3. I **Virtuella datorer** > **Välj virtuella datorer**väljer du varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Välj **OK**.

4. I **Inställningar**kan du konfigurera följande inställningar för målplats.

    - **Målplats**: Den plats där källdata för virtuella datorer replikeras. Site Recovery innehåller en lista över lämpliga målområden baserat på den valda datorns plats. Vi rekommenderar att du använder samma plats som återställningstjänstvalvets plats.
    - **Målprenumeration**: Målprenumerationen som används för haveriberedskap. Som standard är målprenumerationen densamma som källprenumerationen.
    - **Målgrupp:** Den resursgrupp som alla replikerade virtuella datorer tillhör. Som standard skapar Site Recovery en ny resursgrupp i målregionen. Namnet får `asr` suffixet. Om det redan finns en resursgrupp som har skapats av Azure Site Recovery återanvänds den. Du kan också välja att anpassa den, som visas i följande avsnitt. Platsen för målresursgruppen kan vara vilken Azure-region som helst utom den region där käll virtuella datorer är värd.
    - **Mål virtuellt nätverk**: Som standard skapar Site Recovery ett nytt virtuellt nätverk i målregionen. Namnet får `asr` suffixet. Den är mappad till ditt källnätverk och används för framtida skydd. [Läs mer](site-recovery-network-mapping-azure-to-azure.md) om nätverksmappning.
    - **Mållagringskonton (om källdatorns virtuella datorer inte använder hanterade diskar):** Som standard skapar Site Recovery ett nytt mållagringskonto genom att härma lagringskonfigurationen för käll-VM. Om det redan finns ett lagringskonto återanvänds det.
    - **Replikhanterade diskar (om källdatorn använder hanterade diskar):** Site Recovery skapar nya replikhanterade diskar i målområdet för att spegla källdatorns hanterade diskar av samma lagringstyp (standard eller premium) som källdatorns hanterade diskar.
    - **Cachelagringskonton**: Site Recovery behöver ett extra lagringskonto som kallas *cachelagring* i källregionen. Alla ändringar på käll-virtuella datorer spåras och skickas till cachelagringskontot. De replikeras sedan till målplatsen.
    - **Tillgänglighetsuppsättning**: Som standard skapar Site Recovery en ny tillgänglighetsuppsättning i målregionen. Namnet har `asr` suffixet. Om det redan finns en tillgänglighetsuppsättning som har skapats av Site Recovery återanvänds den.
    - **Diskkrypteringsuppsättningar (DES):** Site Recovery behöver diskkrypteringsuppsättningar som ska användas för replik- och målhanterade diskar. Du måste skapa DES i förväg i målprenumerationen och målregionen innan du aktiverar replikeringen. Som standard är en DES inte markerad. Du måste klicka på "Anpassa" för att välja en DES per källdisk.
    - **Replikeringsprincipen**: Definierar inställningarna för återställningspunktkvarhållningshistorik och appkonsekvent ögonblicksbildfrekvens. Som standard skapar Site Recovery en ny replikeringsprincip med standardinställningar på *24 timmar* för återställningspunktkvarhållning och *60 minuter* för appkonsekvent ögonblicksbildfrekvens.

    ![Aktivera replikering för datorer med CMK-aktiverade diskar](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Anpassa målresurser

Följ dessa steg för att ändra standardmålinställningarna för webbplatsåterställning.

1. Välj **Anpassa** bredvid Målprenumeration om du vill ändra standardmålprenumerationen. Välj prenumerationen i listan över prenumerationer som är tillgängliga i Azure AD-klienten.

2. Välj **Anpassa bredvid** "Resursgrupp, Nätverk, Lagring och Tillgänglighetsuppsättningar" om du vill ändra följande standardinställningar:
    - För **målgruppen Målresurs**väljer du resursgruppen i listan över resursgrupper på prenumerationens målplats.
    - För **virtuellt målnätverk**väljer du nätverket från en lista över virtuella nätverk på målplatsen.
    - För **tillgänglighetsuppsättning**kan du lägga till inställningar för tillgänglighetsuppsättning till den virtuella datorn, om de ingår i en tillgänglighetsuppsättning i källregionen.
    - För **mållagringskonton**väljer du det konto som ska användas.

3. Välj **Anpassa** bredvid "Lagringskrypteringsinställningar" för att välja mål-DES för varje kundhanterad nyckel (CMK) aktiverad källhanterad disk. Vid tidpunkten för valet kommer du också att kunna se vilket målnyckelvalv DES är associerat med.

4. Välj **Skapa målresurs** > **Aktivera replikering**.
5. När de virtuella datorerna har aktiverats för replikering kan du kontrollera de virtuella datorernas hälsostatus under **Replikerade objekt**.

![Aktivera replikering för datorer med CMK-aktiverade diskar](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Under den första replikeringen kan statusen ta lite tid att uppdatera, utan uppenbara framsteg. Klicka på **Uppdatera** för att få den senaste statusen.

## <a name="faqs"></a>Vanliga frågor och svar

* Jag har aktiverat CMK på en befintlig replikerad artikel, hur kan jag se till att CMK tillämpas på målområdet också?

    Du kan ta reda på namnet på den replikhanterade disken (skapad av Azure Site Recovery i målregionen) och bifoga DES till den här replikdisken. Du kommer dock inte att kunna se DES-detaljerna i diskbladet när du har fäst den. Du kan också välja att inaktivera replikeringen av den virtuella datorn och aktivera den igen. Det kommer att se till att du ser DES och nyckel valv detaljer i diskar bladet för replikerade objektet.

* Jag har lagt till en ny CMK-aktiverad disk till det replikerade objektet. Hur replikerar jag den här disken med Azure Site Recovery?

    Det går inte att lägga till en ny CMK-aktiverad disk till ett befintligt replikerat objekt. Inaktivera replikeringen och aktivera replikeringen igen för den virtuella datorn.

