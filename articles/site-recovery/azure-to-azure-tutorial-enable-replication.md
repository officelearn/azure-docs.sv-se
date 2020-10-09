---
title: Konfigurera haveri beredskap för Azure VM med Azure Site Recovery
description: Lär dig konfigurera programåterställning för virtuella Azure-datorer till annan Azure-region med Azure Site Recovery-tjänsten.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 50bf1ec7f21ccbc3a3fa8feaea02e45bd08a158a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421424"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Konfigurera katastrof återställning för virtuella Azure-datorer

[Azure Site Recoverys](site-recovery-overview.md) tjänsten bidrar till din strategi för haveri beredskap genom att hantera och dirigera replikering, redundans och återställning efter fel för lokala datorer och virtuella datorer i Azure.

Den här självstudien visar hur du konfigurerar haveri beredskap för virtuella Azure-datorer genom att replikera dem från en Azure-region till en annan. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapar ett Recovery Services-valv
> * Verifiera målresursuppsättningar
> * Konfigurera utgående nätverks anslutning för virtuella datorer
> * Aktivera replikering för en virtuell dator

> [!NOTE]
> Den här artikeln innehåller instruktioner för distribution av haveriberedskap med de enklaste inställningarna. Om du vill lära dig mer om anpassade inställningar kan du läsa artiklarna i [avsnittet How to](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Granska [arkitekturen och komponenterna för scenariot](./azure-to-azure-architecture.md).
- Granska [support kraven](./azure-to-azure-support-matrix.md) innan du börjar.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**. Välj sedan **den & hanterings verktyg**  >  **säkerhets kopiering och Site Recovery**.
1. I **namn**anger du ett eget namn som identifierar valvet. Om du har mer än en prenumeration väljer du den lämpligaste.
1. Skapa en resursgrupp eller välj en befintlig. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Om du vill komma åt valvet från instrument panelen väljer du **Fäst på instrument panelen** och väljer sedan **skapa**.

   ![Nytt valv](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="verify-target-resource-settings"></a>Verifiera målresursuppsättningar

Kontrol lera din Azure-prenumeration för mål regionen.

- Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen. Kontakta supporten och aktivera den kvot som krävs.
- Se till att din prenumeration har tillräckligt med resurser för att hantera storlekar för virtuella datorer som överensstämmer med dina virtuella källdatorer. Site Recovery väljer samma storlek eller närmaste möjliga storlek för den virtuella måldatorn.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Konfigurera utgående nätverks anslutning för virtuella datorer

Site Recovery kräver att vissa ändringar görs i utgående nätverksanslutning från de virtuella datorer som du vill replikera.

> [!NOTE]
> Site Recovery har inte stöd för en autentiseringsproxy för att styra nätverksanslutningar.

### <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutning ger du åtkomst till följande URL: er:

| **Namn**                  | **Kommersiellt**                               | **Myndigheter**                                 | **Beskrivning** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| Replikering               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Om du använder en nätverks säkerhets grupp (NSG) skapar du service-tag-baserade NSG-regler för åtkomst till Azure Storage, Azure Active Directory, Site Recovery tjänst och Site Recovery övervakning. [Läs mer](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Verifiera certifikat för virtuella Azure-datorer

Kontrollera att de virtuella datorer som du vill replikera har de senaste rotcertifikaten. Om de inte gör det går det inte att registrera den virtuella datorn på Site Recovery på grund av säkerhets begränsningar.

- I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.
- I virtuella datorer med Linux följer du de riktlinjer du fått från Linux-distributören för att hämta de senaste betrodda rotcertifikaten och listan över återkallade certifikat till den virtuella datorn.

## <a name="set-permissions-on-the-account"></a>Ange behörigheter för kontot

Azure Site Recovery har tre inbyggda roller som styr Site Recovery-hanteringen.

- **Site Recovery-bidragsgivare** – Den här rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i ett Recovery Services-valv. En användare med denna roll kan dock inte skapa eller ta bort ett Recovery Services-valv eller tilldela behörighet till andra användare. Den här rollen lämpar sig bäst för haveriberedskapsadministratörer som kan aktivera och hantera haveriberedskap för program eller hela organisationer.

- **Site Recovery-operatör** – Den här rollen har behörighet att utföra och hantera operationer för redundans och återställning av fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela åtkomstbehörigheter till andra användare. Den här rollen lämpar sig bäst för en haveriberedskapsoperatör som kan redundansväxla virtuella datorer eller program efter instruktioner från programägare och IT-administratörer. Efter haveri stängningen kan haveri återställnings operatören återaktivera och återställa de virtuella datorerna.

- **Site Recovery-läsare** – Den här rollen har behörighet att visa all Site Recovery-hantering. Den här rollen lämpar sig bäst för en IT-chef som kan övervaka aktuell skyddsnivå och skapa supportärenden.

Lär dig mer om [inbyggda Azure-roller](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Aktivera replikering för en virtuell dator

I följande avsnitt beskrivs hur du aktiverar replikering.

### <a name="select-the-source"></a>Välj källan

Påbörja replikeringen genom att välja den källa där dina virtuella Azure-datorer körs.

1. Gå till **Recovery Services valv**, Välj valv namnet och välj sedan **+ Replikera**.
1. För **källan**väljer du **Azure**.
1. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
1. Välj den **källprenumeration** där de virtuella datorerna körs. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
1. Välj **käll resurs gruppen**och spara inställningarna genom att välja **OK** .

   ![Konfigurera källan](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Välj virtuella datorer

Site Recovery hämtar en lista med de virtuella datorer som är kopplade till prenumerationen och resursgruppen eller molntjänsten.

1. I **Virtuella datorer** väljer du de virtuella datorer du vill replikera.
1. Välj **OK**.

### <a name="configure-replication-settings"></a>Konfigurera replikeringsinställningar

Site Recovery skapar standardinställningar och replikeringsprinciper för målregionen. Du kan ändra inställningarna efter behov.

1. Välj **Inställningar** för att visa inställningarna för mål och replikering.

1. Om du vill åsidosätta standard mål inställningarna väljer du **Anpassa** bredvid **resurs grupp, nätverk, lagring och tillgänglighet**.

   ![Konfigurera inställningar](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Anpassa mål inställningarna som sammanfattas i tabellen.

   | **Inställning** | **Information** |
   | --- | --- |
   | **Mål prenumeration** | Som standard är mål prenumerationen densamma som käll prenumerationen. Välj **Anpassa** om du vill välja en annan mål prenumeration inom samma Azure Active Directory klient. |
   | **Målplats** | Den målregion som används för haveriberedskap.<br/><br/> Vi rekommenderar att målplatsen överensstämmer med Site Recovery-valvets plats. |
   | **Mål resurs grupp** | Den resursgrupp i målregionen som innehåller virtuella Azure-datorer efter redundansväxling.<br/><br/> Som standard skapar Site Recovery en ny resurs grupp i mål regionen med ett `asr` suffix. Platsen för mål resurs gruppen kan vara vilken region som helst, förutom den region där dina virtuella käll datorer finns. |
   | **Virtuellt mål nätverk** | Nätverket i den målregion där virtuella Azure-datorer finns efter redundansväxling.<br/><br/> Som standard skapar Site Recovery ett nytt virtuellt nätverk (och undernät) i mål regionen med ett `asr` suffix. |
   | **Cachelagra lagrings konton** | Site Recovery använder ett lagringskonto i källregionen. Ändringar i virtuella källdatorer skickas till det här kontot innan replikering till målplatsen.<br/><br/> Om du använder ett brand Väggs-aktiverat lagrings konto för cache kontrollerar du att du aktiverar **Tillåt betrodda Microsoft-tjänster**. [Läs mer](../storage/common/storage-network-security.md#exceptions). Se också till att du tillåter åtkomst till minst ett undernät för det virtuella käll-VNet. |
   | **Mål lagrings konton (den virtuella käll datorn använder icke-hanterade diskar)** | Som standard skapar Site Recovery ett nytt lagringskonto i målregionen som speglar lagringskontot för den virtuella källdatorn.<br/><br/> Aktivera **Tillåt betrodda Microsoft-tjänster** om du använder ett brand Väggs-aktiverat cache Storage-konto. |
   | **Hanterade replik diskar (om den virtuella käll datorn använder Managed Disks)** | Som standard skapar Site Recovery replik Managed disks i mål regionen för att spegla den virtuella käll datorns hanterade diskar med samma lagrings typ (standard eller Premium) som den virtuella käll datorns hanterade disk. Du kan bara anpassa disk typen. |
   | **Tillgänglighets uppsättningar för mål** | Som standard skapar Azure Site Recovery en ny tillgänglighets uppsättning i mål regionen med namnet med `asr` suffix för den virtuella dator delen av en tillgänglighets uppsättning i käll regionen. Om tillgänglighets uppsättningen som skapats av Azure Site Recovery redan finns används den igen. |
   | **Mål tillgänglighets zoner** | Som standard tilldelar Site Recovery samma zonnummer som källregionen i målregionen om målregionen har stöd för tillgänglighetszoner.<br/><br/> Om mål regionen inte stöder tillgänglighets zoner konfigureras de virtuella mål datorerna som enskilda instanser som standard.<br/><br/> Välj **Anpassa** för att konfigurera virtuella datorer som en del av en tillgänglighets uppsättning i mål regionen.<br/><br/> Du kan inte ändra tillgänglighets typ (enskild instans, tillgänglighets uppsättning eller tillgänglighets zon) när du har aktiverat replikering. Om du vill ändra tillgänglighets typ inaktiverar du och aktiverar replikering. |

1. Om du vill anpassa inställningarna för replikeringsprincipen väljer du **Anpassa** bredvid **replikeringsprincip**och ändrar inställningarna efter behov.

   | **Inställning** | **Information** |
   | --- | --- |
   | **Namn på replikeringsprincip** | Principnamn. |
   | **Kvarhållning av återställnings punkt** | Som standard behåller Site Recovery återställningspunkter i 24 timmar. Du kan ställa in ett värde mellan 1 och 72 timmar. |
   | **Frekvens för programkonsekventa ögonblicks bilder** | Som standard tar Site Recovery en appkompatibel ögonblicksbild var 4:e timme. Du kan ställa in ett värde mellan 1 och 12 timmar.<br/><br/> En programkonsekvent ögonblicks bild är en tidpunkts ögonblicks bild av program data i den virtuella datorn. Volume Shadow Copy-tjänsten (VSS) säkerställer att programmen i den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas. |
   | **Replikeringsgrupp** | Om programmet kräver konsekvens för flera virtuella datorer mellan virtuella datorer kan du skapa en replikeringsgrupp för dessa virtuella datorer. Som standard är valda virtuella datorer inte en del av någon replikeringsgrupp. |

1. I **Anpassa** väljer du **Ja** för konsekvens för flera virtuella datorer om du vill lägga till virtuella datorer i en ny eller en befintlig replikeringsgrupp. Välj sedan **OK**.

   > [!NOTE]
   > - Alla datorer i en replikeringsgrupp har delade krascher konsekventa och programkonsekventa återställnings punkter vid växling vid fel.
   > - Att aktivera konsekvens för flera virtuella datorer kan påverka arbets belastnings prestandan (den är processor intensiv). Den bör endast användas om datorer kör samma arbets belastning och du behöver konsekvens på flera datorer.
   > - Du kan ha högst 16 virtuella datorer i en replikeringsgrupp.
   > - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Se till att det inte finns någon brand vägg som blockerar den interna kommunikationen mellan de virtuella datorerna via den här porten.
   > - För virtuella Linux-datorer i en replikeringsgrupp ser du till att utgående trafik på port 20004 öppnas manuellt i enlighet med vägledningen för Linux-versionen.

### <a name="configure-encryption-settings"></a>Konfigurera krypteringsinställningar

Om den virtuella käll datorn har Azure Disk Encryption (ADE) aktiverat, granskar du inställningarna.

1. Verifiera inställningarna:
   1. **Nyckel valv för disk kryptering**: som standard skapar Site Recovery ett nytt nyckel valv på den virtuella käll diskens disk krypterings nycklar med ett `asr` suffix. Om nyckel valvet redan finns återanvänds det.
   1. **Nyckel valv för nyckel kryptering**: som standard skapar Site Recovery ett nytt nyckel valv i mål regionen. Namnet har ett `asr` suffix och baseras på den virtuella käll nyckelns krypterings nycklar. Om nyckel valvet som skapats av Site Recovery redan finns återanvänds det.
1. Välj **Anpassa** för att välja anpassade nyckel valv.

>[!NOTE]
> Site Recovery stöder för närvarande ADE, med och utan Azure Active Directory (AAD) för virtuella datorer som kör Windows operativ system. För Linux-operativsystem stöder vi bara ADE utan AAD. För datorer som kör ADE 1,1 (utan AAD) måste dessutom de virtuella datorerna använda hanterade diskar. Virtuella datorer med ohanterade diskar stöds inte. Om du växlar från ADE 0,1 (med AAD) till 1,1 måste du inaktivera replikering och aktivera replikering för en virtuell dator efter att ha aktiverat 1,1.

### <a name="track-replication-status"></a>Spåra replikeringsstatus

När replikering har Aktiver ATS kan du spåra jobbets status.

1. I **Inställningar**väljer du **Uppdatera** för att hämta den senaste statusen.
1. Spåra förlopp och status på följande sätt:
   1. Spåra förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**.
   1. I **Inställningar**  >  **replikerade objekt**kan du Visa status för virtuella datorer och den inledande replikeringen. Välj den virtuella datorn för att öka detalj nivån i inställningarna.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du haveriberedskap för en virtuell Azure-dator. Nu kan du köra en granskning av haveri beredskap för att kontrol lera att redundansväxlingen fungerar som förväntat.

> [!div class="nextstepaction"]
> [Köra ett programåterställningstest](azure-to-azure-tutorial-dr-drill.md)
