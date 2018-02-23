---
title: "Konfigurera katastrofåterställning för virtuella Azure-datorer till en sekundär region som Azure med Azure Site Recovery (förhandsgranskning)"
description: "Lär dig hur du ställer in katastrofåterställning för virtuella Azure-datorer till en annan Azure region med hjälp av Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 276b05a2148382ba4d1c8b4a17a5ca9f409990a8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Konfigurera katastrofåterställning för virtuella Azure-datorer till en sekundär Azure region (förhandsgranskning)

Den [Azure Site Recovery](../site-recovery-overview.md) tjänsten bidrar till din strategi för katastrofåterställning genom att hantera och samordna replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Den här kursen visar hur du ställer in återställning till en sekundär Azure region för Azure virtuella datorer. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett Recovery Services-valv
> * Kontrollera inställningarna för mål-resurs
> * Ställ in utgående åtkomst för virtuella datorer
> * Aktivera replikering för en virtuell dator

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Se till att du förstår de [scenariots arkitektur och komponenter](concepts-azure-to-azure-architecture.md).
- Granska de [supportkrav](site-recovery-support-matrix-azure-to-azure.md) för alla komponenter.

## <a name="create-a-vault"></a>Skapa ett valv

Skapa valvet i varje region, utom området källa.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **skapar du en resurs** > **hantering och övervakning** > **säkerhetskopiering och återställning av**.
3. I **Namn** anger du ett eget namn som identifierar valvet. Om du har mer än en prenumeration väljer du en.
4. Skapa en resursgrupp eller välj en befintlig. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
5. För att snabbt komma åt valvet från instrumentpanelen, klickar du på **fäst på instrumentpanelen** och klicka sedan på **skapa**.

   ![Nytt valv](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Det nya valvet har lagts till i den **instrumentpanelen** under **alla resurser**, och på primära **Recovery Services-valv** sidan.

## <a name="verify-target-resources"></a>Kontrollera target-resurser

1. Kontrollera att din Azure-prenumeration kan du skapa virtuella datorer i målregionen som används för katastrofåterställning. Kontakta supporten om du vill aktivera kvoten som krävs.

2. Kontrollera att din prenumeration har tillräckligt med resurser för att stödja virtuella datorer med storlekar som matchar dina virtuella källdatorer. Site Recovery hämtar samma storlek eller den närmaste möjliga storleken för mål VM.

## <a name="configure-outbound-network-connectivity"></a>Konfigurera utgående nätverksanslutning

Site Recovery fungerar som förväntat, måste du göra några ändringar i utgående nätverksanslutningen, från virtuella datorer som du vill replikera.

- Site Recovery stöder inte användning av en autentiseringsproxy till kontrollen nätverksanslutning.
- Om du har en autentiseringsproxy går inte att aktivera replikering.

### <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för URL: er

Om du använder en URL-baserade brandväggen proxy för att styra utgående anslutning Tillåt åtkomst till följande URL-adresser som används av Site Recovery.

| **URL** | **Detaljer** |
| ------- | ----------- |
| *.blob.core.windows.net | Gör att data skrivs från den virtuella datorn till cache lagringskonto i regionen källa. |
| login.microsoftonline.com | Tillhandahåller autentiseringen och auktoriseringen till Site Recovery-webbadresser. |
| *.hypervrecoverymanager.windowsazure.com | Gör att den virtuella datorn kan kommunicera med Site Recovery-tjänsten. |
| *.servicebus.windows.net | Tillåter VM att skriva Site Recovery övervakning och diagnostikdata. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

När du använder IP-baserade brandväggen, proxy- eller NSG-regler för att styra utgående anslutning måste måste följande IP-adressintervall vara godkända. Hämta en lista över intervall från följande länkar:

  - [Microsoft Azure Datacenter-IP-adressintervall](http://www.microsoft.com/download/details.aspx?id=41653)
  - [Windows Azure Datacenter-IP-intervall i Tyskland](http://www.microsoft.com/download/details.aspx?id=54770)
  - [Windows Azure Datacenter-IP-intervall i Kina](http://www.microsoft.com/download/details.aspx?id=42064)
  - [URL:er och IP-adressintervall för Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery tjänstslutpunkten IP-adresser](https://aka.ms/site-recovery-public-ips)

Använd dessa listor för att konfigurera nätverket åtkomstkontroller i nätverket. Du kan använda den här [skriptet](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) att skapa nödvändiga NSG-regler.

## <a name="verify-azure-vm-certificates"></a>Verifiera Azure VM-certifikat

Kontrollera att de senaste rotcertifikat finns på Windows- eller Linux virtuella datorer du vill replikera. Om de senaste rotcertifikat inte kan inte den virtuella datorn registrerade till Site Recovery förfallodatum säkerhetsbegränsningar.

- Installera de senaste uppdateringarna på den virtuella datorn för virtuella Windows-datorer, så att alla betrodda rotcertifikat som finns på datorn. I en frånkopplad miljö Följ standard Windows Update och certifikatet update processer för din organisation.

- För Linux virtuella datorer, följer du de riktlinjer som tillhandahålls av din Linux-distributören att hämta den senaste betrodda rotcertifikat och listan över återkallade certifikat på den virtuella datorn.

## <a name="set-permissions-on-the-account"></a>Ange behörigheter för kontot

Azure Site Recovery tillhandahåller tre inbyggda roller för att styra Site Recovery-hanteringsåtgärder.

- **Site Recovery-deltagare** -rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i Recovery Services-valvet. Skapa en användare med den här rollen, men kan inte eller ta bort ett Recovery Services-valv eller tilldela behörigheter till andra användare. Den här rollen passar bäst för disaster recovery-administratörer som kan aktivera och hantera katastrofåterställning för program eller hela organisationer.

- **Site Recovery-operatorn** -rollen har behörighet att köra och åtgärder för redundans och återställning efter fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela behörigheter till andra användare. Den här rollen är bäst för en disaster recovery-operatör som kan växlas över virtuella datorer eller program när du uppmanas av applikationsägare och IT-administratörer. Bokför upplösning över DR-operatorn kan skydda igen och återställning av virtuella datorer.

- **Site Recovery Reader** -rollen har behörighet att visa alla Site Recovery-hanteringsåtgärder. Den här rollen passar bäst för en övervakning IT-chef som kan övervaka det aktuella tillståndet för skydd och höja supportärenden.

Läs mer på [inbyggda Azure RBAC-roller](../../active-directory/role-based-access-built-in-roles.md)

## <a name="enable-replication"></a>Aktivera replikering

### <a name="select-the-source"></a>Välj källa

1. Klicka på valvnamnet i Recovery Services-valv > **+ replikera**.
2. I **källa**väljer **Azure - PREVIEW**.
3. I **datakällplats**, väljer du den Azure-region där din virtuella dator körs för närvarande.
4. Välj den **virtuella Azure-datorn distributionsmodell** för virtuella datorer: **Resource Manager** eller **klassiska**.
5. Välj den **källresursgruppen** för hanteraren för virtuella datorer, eller **Molntjänsten** för klassiska virtuella datorer.
6. Spara inställningarna genom att klicka på **OK**.

### <a name="select-the-vms"></a>Välj de virtuella datorerna

Site Recovery hämtar en lista över de virtuella datorerna som är associerad med tjänsten prenumeration och resurs grupp eller ett moln.

1. I **virtuella datorer**, Välj de virtuella datorerna som du vill replikera.
2. Klicka på **OK**.

### <a name="configure-replication-settings"></a>Konfigurera replikeringsinställningar

Skapar site Recovery standardinställningar och replikeringsprincip för mål-region. Du kan ändra inställningarna efter behov.

1. Klicka på **inställningar** att visa inställningar för målet.
2. Om du vill åsidosätta standardinställningarna för mål klickar du på **anpassa**. 

![Konfigurera inställningar](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Målplatsen**: målregionen som används för katastrofåterställning. Vi rekommenderar att målplatsen matchar platsen för Site Recovery-valvet.

- **Målresursgruppen**: resursgrupp i målregionen som innehåller virtuella Azure-datorer efter redundans. Som standard skapar Site Recovery en ny resursgrupp i målregionen med ett ”asr” suffix.

- **Virtuella målnätverket**: nätverket i målregionen att virtuella datorer finns efter växling vid fel.
  Som standard skapar Site Recovery ett nytt virtuellt nätverk (och undernät) i området mål med en ”asr” suffix.

- **Cachelagra lagringskonton**: Site Recovery använder ett lagringskonto i regionen källa. Ändringar av virtuella källdatorer skickas till det här kontot innan replikeringen till målplatsen.

- **Rikta lagringskonton**: som standard skapar Site Recovery ett nytt lagringskonto i målregionen för spegling av källan VM storage-konto.

- **Rikta tillgänglighetsuppsättningar**: som standard skapar en ny tillgänglighetsuppsättning i målregionen med suffixet ”asr” Site Recovery. Du kan bara lägga till tillgänglighetsuppsättningar om virtuella datorer som är en del av en uppsättning i käll-region.

- **Namn på**: principnamn.

- **Kvarhållningstid för återställningspunkten**: som standard sparas Site Recovery återställningspunkter under 24 timmar. Du kan konfigurera ett värde mellan 1 och 72 timmar.

- **Frekvens av programkonsekventa ögonblicksbilder**: som standard Site Recovery tar en ögonblicksbild av programkonsekventa var fjärde timme. Du kan konfigurera ett värde mellan 1 och 12 timmar. Programkonsekventa ögonblicksbilder är en tidpunkt i ögonblicksbild av programdata inuti den virtuella datorn. Volume Shadow Copy Service (VSS) säkerställer appen på den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.

### <a name="track-replication-status"></a>Spåra replikeringsstatus

1. I **inställningar**, klickar du på **uppdatera** att hämta senaste status.

2. Du kan följa förloppet för den **Aktivera skydd** jobb **inställningar** > **jobb** > **Site Recovery-jobb**.

3. I **inställningar** > **replikerade objekt**, du kan visa status för virtuella datorer och den inledande replikeringen pågår. Klicka på den virtuella datorn och öka detaljnivån till dess inställningar.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen konfigurerats katastrofåterställning för en Azure VM. Nästa steg är att testa din konfiguration.

> [!div class="nextstepaction"]
> [Köra ett återställningstest](azure-to-azure-tutorial-dr-drill.md)
