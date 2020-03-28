---
title: Konfigurera haveriberedskap för Azure VM med Azure Site Recovery
description: Lär dig konfigurera programåterställning för virtuella Azure-datorer till annan Azure-region med Azure Site Recovery-tjänsten.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c5d2bbe920f87421550fadf30a7e7e9d23931bfd
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292474"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Konfigurera haveriberedskap för virtuella Azure-datorer

[Azure Site Recovery-tjänsten](site-recovery-overview.md) bidrar till din katastrofåterställningsstrategi genom att hantera och samordna replikering, redundans och återställning av lokala datorer och virtuella Azure-datorer (VIRTUELLA datorer).

Den här självstudien visar hur du konfigurerar haveriberedskap för virtuella Azure-datorer genom att replikera dem från en Azure-region till en annan. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * skapar ett Recovery Services-valv
> * Verifiera målresursuppsättningar
> * Konfigurera utgående nätverksanslutning för virtuella datorer
> * Aktivera replikering för en virtuell dator

> [!NOTE]
> Den här artikeln innehåller instruktioner för distribution av haveriberedskap med de enklaste inställningarna. Om du vill veta mer om anpassade inställningar läser du artiklarna i [avsnittet Så här gör du](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Granska [arkitekturen och komponenterna för scenariot](concepts-azure-to-azure-architecture.md).
- Granska [supportkraven](site-recovery-support-matrix-azure-to-azure.md) innan du börjar.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**. Välj sedan **IT-& Säkerhetskopiering av hanteringsverktyg** > **och återställning av webbplatser**.
1. Ange ett eget namn för att identifiera valvet i **Namn.** Om du har mer än en prenumeration väljer du den lämpligaste.
1. Skapa en resursgrupp eller välj en befintlig. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Om du vill komma åt valvet från instrumentpanelen väljer du **Fäst på instrumentpanelen** och väljer sedan **Skapa**.

   ![Nytt valv](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="verify-target-resource-settings"></a>Verifiera målresursuppsättningar

Kontrollera din Azure-prenumeration för målregionen.

- Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen. Kontakta supporten och aktivera den kvot som krävs.
- Se till att din prenumeration har tillräckligt med resurser för att hantera storlekar för virtuella datorer som överensstämmer med dina virtuella källdatorer. Site Recovery väljer samma storlek eller närmaste möjliga storlek för den virtuella måldatorn.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Konfigurera utgående nätverksanslutning för virtuella datorer

Site Recovery kräver att vissa ändringar görs i utgående nätverksanslutning från de virtuella datorer som du vill replikera.

> [!NOTE]
> Site Recovery har inte stöd för en autentiseringsproxy för att styra nätverksanslutningar.

### <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en URL-baserad brandväggsproxy för att styra utgående anslutning tillåter du åtkomst till dessa webbadresser:

| **URL** | **Detaljer** |
| ------- | ----------- |
| `*.blob.core.windows.net` | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| `login.microsoftonline.com` | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| `*.hypervrecoverymanager.windowsazure.com` | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| `*.servicebus.windows.net` | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Om du använder en nätverkssäkerhetsgrupp (NSG) skapar du tjänsttaggsbaserade NSG-regler för åtkomst till Azure Storage, Azure Active Directory, Site Recovery Service och Site Recovery övervakning. [Läs mer](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Verifiera certifikat för virtuella Azure-datorer

Kontrollera att de virtuella datorer som du vill replikera har de senaste rotcertifikaten. Om de inte gör det kan den virtuella datorn inte registreras i Site Recovery på grund av säkerhetsbegränsningar.

- I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.
- I virtuella datorer med Linux följer du de riktlinjer du fått från Linux-distributören för att hämta de senaste betrodda rotcertifikaten och listan över återkallade certifikat till den virtuella datorn.

## <a name="set-permissions-on-the-account"></a>Ange behörigheter för kontot

Azure Site Recovery har tre inbyggda roller som styr Site Recovery-hanteringen.

- **Site Recovery-bidragsgivare** – Den här rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i ett Recovery Services-valv. En användare med denna roll kan dock inte skapa eller ta bort ett Recovery Services-valv eller tilldela behörighet till andra användare. Den här rollen lämpar sig bäst för haveriberedskapsadministratörer som kan aktivera och hantera haveriberedskap för program eller hela organisationer.

- **Site Recovery-operatör** – Den här rollen har behörighet att utföra och hantera operationer för redundans och återställning av fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela åtkomstbehörigheter till andra användare. Den här rollen lämpar sig bäst för en haveriberedskapsoperatör som kan redundansväxla virtuella datorer eller program efter instruktioner från programägare och IT-administratörer. Efter lösning av katastrofen kan haveriberedskapsoperatören reprotect och återställa de virtuella datorerna.

- **Site Recovery-läsare** – Den här rollen har behörighet att visa all Site Recovery-hantering. Den här rollen lämpar sig bäst för en IT-chef som kan övervaka aktuell skyddsnivå och skapa supportärenden.

Läs mer om [azure RBAC-inbyggda roller](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Aktivera replikering för en virtuell dator

I följande avsnitt beskrivs hur du aktiverar replikering.

### <a name="select-the-source"></a>Välj källan

Om du vill starta replikeringsuppsättningen väljer du källan där dina virtuella Azure-datorer körs.

1. Gå till **Recovery Services-valv,** välj valvets namn och välj sedan **+Replikera**.
1. För **källan**väljer du **Azure**.
1. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
1. Välj den **källprenumeration** där de virtuella datorerna körs. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
1. Välj **resursgruppen Källa**och välj **OK** för att spara inställningarna.

   ![Konfigurera källan](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Välj virtuella datorer

Site Recovery hämtar en lista med de virtuella datorer som är kopplade till prenumerationen och resursgruppen eller molntjänsten.

1. I **Virtuella datorer** väljer du de virtuella datorer du vill replikera.
1. Välj **OK**.

### <a name="configure-replication-settings"></a>Konfigurera replikeringsinställningar

Site Recovery skapar standardinställningar och replikeringsprinciper för målregionen. Du kan ändra dessa inställningar efter behov.

1. Välj **Inställningar** om du vill visa inställningarna för mål och replikering.

1. Om du vill åsidosätta standardmålinställningarna väljer du **Anpassa bredvid** **resursgrupp, Nätverk, Lagring och Tillgänglighet**.

   ![Konfigurera inställningar](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Anpassa målinställningarna enligt summeringen i tabellen.

   | **Inställning** | **Detaljer** |
   | --- | --- |
   | **Målprenumeration** | Som standard är målprenumerationen densamma som källprenumerationen. Välj **Anpassa** om du vill välja en annan målprenumeration i samma Azure Active Directory-klientorganisation. |
   | **Målplats** | Den målregion som används för haveriberedskap.<br/><br/> Vi rekommenderar att målplatsen överensstämmer med Site Recovery-valvets plats. |
   | **Målgrupp för målresurs** | Den resursgrupp i målregionen som innehåller virtuella Azure-datorer efter redundansväxling.<br/><br/> Som standard skapar Site Recovery en ny resursgrupp `asr` i målregionen med ett suffix. Platsen för målresursgruppen kan vara vilken region som helst utom den region där dina virtuella källdatorer finns. |
   | **Rikta in dig på virtuellt nätverk** | Nätverket i den målregion där virtuella Azure-datorer finns efter redundansväxling.<br/><br/> Som standard skapar Site Recovery ett nytt virtuellt nätverk (och undernät) i målområdet med ett `asr` suffix. |
   | **Cachelagringskonton** | Site Recovery använder ett lagringskonto i källregionen. Ändringar i virtuella källdatorer skickas till det här kontot innan replikering till målplatsen.<br/><br/> Om du använder ett brandväggsaktiverat cachelagringskonto kontrollerar du att du aktiverar **Tillåt betrodda Microsoft-tjänster**. [Läs mer](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions). Se också till att du tillåter åtkomst till minst ett undernät till käll-Vnet. |
   | **Mållagringskonton (käll-VM använder icke-hanterade diskar)** | Som standard skapar Site Recovery ett nytt lagringskonto i målregionen som speglar lagringskontot för den virtuella källdatorn.<br/><br/> Aktivera **Tillåt betrodda Microsoft-tjänster** om du använder ett brandväggsaktiverat cachelagringskonto. |
   | **Replikhanterade diskar (Om käll-VM använder hanterade diskar)** | Som standard skapar Site Recovery replikhanterade diskar i målregionen för att spegla källdatorns hanterade diskar med samma lagringstyp (standard eller premium) som källdatorns hanterade disk. Du kan bara anpassa disktypen. |
   | **Uppsättningar för måltillgänglighet** | Som standard skapar Azure Site Recovery en ny tillgänglighet som `asr` angetts i målregionen med namn med suffix för de virtuella datorerna som är en del av en tillgänglighet som angetts i källregionen. Om tillgänglighetsuppsättning som skapats av Azure Site Recovery redan finns, återanvänds den. |
   | **Måltillgänglighetszoner** | Som standard tilldelar Site Recovery samma zonnummer som källregionen i målregionen om målregionen har stöd för tillgänglighetszoner.<br/><br/> Om målregionen inte stöder tillgänglighetszoner konfigureras måldav:erna som standard som enskilda instanser.<br/><br/> Välj **Anpassa** om du vill konfigurera virtuella datorer som en del av en tillgänglighet som angetts i målområdet.<br/><br/> Du kan inte ändra tillgänglighetstypen (en instans, tillgänglighetsuppsättning eller tillgänglighetszon) när du har aktiverat replikering. Om du vill ändra tillgänglighetstypen inaktiverar och aktiverar du replikering. |

1. Om du vill anpassa replikeringsprincipinställningarna väljer du **Anpassa** **bredvid replikeringsprincipen**och ändrar inställningarna efter behov.

   | **Inställning** | **Detaljer** |
   | --- | --- |
   | **Namn på replikeringsprincip** | Principnamn. |
   | **Kvarhållning av återställningspunkt** | Som standard behåller Site Recovery återställningspunkter i 24 timmar. Du kan ställa in ett värde mellan 1 och 72 timmar. |
   | **Ögonblicksbildfrekvens för appen** | Som standard tar Site Recovery en appkompatibel ögonblicksbild var 4:e timme. Du kan ställa in ett värde mellan 1 och 12 timmar.<br/><br/> En appkonsekvent ögonblicksbild är en ögonblicksbild av programdata i den virtuella datorn. Volume Shadow Copy-tjänsten (VSS) säkerställer att programmen i den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas. |
   | **Replikeringsgrupp** | Om programmet kräver konsekvens för flera virtuella datorer mellan virtuella datorer kan du skapa en replikeringsgrupp för dessa virtuella datorer. Som standard är valda virtuella datorer inte en del av någon replikeringsgrupp. |

1. I **Anpassa** väljer du **Ja** för konsekvens för flera virtuella datorer om du vill lägga till virtuella datorer i en ny eller en befintlig replikeringsgrupp. Välj sedan **OK**.

   > [!NOTE]
   > - Alla datorer i en replikeringsgrupp har delat kraschkonsekventa och appkonsekventa återställningspunkter när det misslyckades.
   > - Om du aktiverar konsekvens med flera virtuella datorer kan arbetsbelastningsprestanda påverkas (den är processorintensiv). Den bör endast användas om datorer kör samma arbetsbelastning och du behöver konsekvens mellan flera datorer.
   > - Du kan ha högst 16 virtuella datorer i en replikeringsgrupp.
   > - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Kontrollera att det inte finns någon brandvägg som blockerar den interna kommunikationen mellan de virtuella datorerna över den här porten.
   > - För virtuella Linux-datorer i en replikeringsgrupp kontrollerar du att den utgående trafiken på port 20004 öppnas manuellt i enlighet med vägledningen för Linux-versionen.

### <a name="configure-encryption-settings"></a>Konfigurera krypteringsinställningar

Om käll-VM har Azure diskkryptering (ADE) aktiverat, granska inställningarna.

1. Verifiera inställningarna:
   1. **Diskkrypteringsnyckelvalv:** Som standard skapar Site Recovery ett nytt nyckelvalv på `asr` käll-VM-diskkrypteringsnycklarna med ett suffix. Om nyckelvalvet redan finns, återanvänds det.
   1. **Nyckelkrypteringsnyckelvalv:** Som standard skapar Site Recovery ett nytt nyckelvalv i målregionen. Namnet har `asr` ett suffix och baseras på krypteringsnycklarna för käll-VM-nyckel. Om nyckelvalvet som skapats av Site Recovery redan finns återanvänds det.
1. Välj **Anpassa** om du vill välja anpassade nyckelvalv.

> [!NOTE]
> Endast virtuella Azure-datorer som kör Windows-operativsystem och är [aktiverade för kryptering med Azure AD-appen](https://aka.ms/ade-aad-app) stöds för närvarande av Azure Site Recovery.

### <a name="track-replication-status"></a>Spåra replikeringsstatus

När replikeringen är aktiverad kan du spåra jobbets status.

1. Välj **Uppdatera** för att få den senaste statusen i **Inställningar.**
1. Spåra förlopp och status på följande sätt:
   1. Spåra förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**.
   1. I **Inställningar** > **replikerade objekt**kan du visa status för virtuella datorer och den första replikeringsförloppet. Välj den virtuella datorn för att öka detaljnivån i dess inställningar.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du haveriberedskap för en virtuell Azure-dator. Nu kan du köra en haveriberedskapsövning för att kontrollera att redundans fungerar som förväntat.

> [!div class="nextstepaction"]
> [Köra ett återställningstest](azure-to-azure-tutorial-dr-drill.md)
