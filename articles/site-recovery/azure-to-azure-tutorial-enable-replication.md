---
title: Konfigurera haveri beredskap för Azure VM med Azure Site Recovery
description: Lär dig konfigurera programåterställning för virtuella Azure-datorer till annan Azure-region med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 1/8/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a7d25dfad20d8eff25020070d0bb32d5777fdb62
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754593"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Konfigurera katastrof återställning för virtuella Azure-datorer

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudien visar hur du konfigurerar haveri beredskap för virtuella Azure-datorer genom att replikera dem från en Azure-region till en annan. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * skapar ett Recovery Services-valv
> * Verifiera målresursuppsättningar
> * Konfigurera utgående nätverks anslutning för virtuella datorer
> * Aktivera replikering för en virtuell dator

> [!NOTE]
> Den här artikeln innehåller instruktioner för distribution av haveriberedskap med de enklaste inställningarna. Om du vill veta mer om anpassade inställningar läser du artiklarna i [avsnittet med anvisningar](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Granska [arkitekturen och komponenterna för scenariot](concepts-azure-to-azure-architecture.md).
- Granska [support kraven](site-recovery-support-matrix-azure-to-azure.md) innan du börjar.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**. Välj sedan **hanterings verktyg** > **säkerhets kopiering och Site Recovery**.
3. I **Namn** anger du ett eget namn som identifierar valvet. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp eller välj en befintlig. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
5. För att snabbt komma åt valvet från instrumentpanelen klickar du på **Fäst på instrumentpanelen** och sedan på **Skapa**.

   ![Nytt valv](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="verify-target-resource-settings"></a>Verifiera målresursuppsättningar

1. Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen. Kontakta supporten och aktivera den kvot som krävs.
2. Se till att din prenumeration har tillräckligt med resurser för att hantera storlekar för virtuella datorer som överensstämmer med dina virtuella källdatorer. Site Recovery väljer samma storlek eller närmaste möjliga storlek för den virtuella måldatorn.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Konfigurera utgående nätverks anslutning för virtuella datorer

Site Recovery kräver att vissa ändringar görs i utgående nätverksanslutning från de virtuella datorer som du vill replikera.

> [!NOTE]
> Site Recovery har inte stöd för en autentiseringsproxy för att styra nätverksanslutningar.


### <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en webbadressbaserad brandväggsproxy för att styra utgående nätverksanslutningar ger du tillgång till dessa URL:er.

| **URL** | **Detaljer** |
| ------- | ----------- |
| *.blob.core.windows.net | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| login.microsoftonline.com | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| *.hypervrecoverymanager.windowsazure.com | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| *.servicebus.windows.net | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Om du använder NSG skapar du service tag-baserade NSG-regler för åtkomst till Azure Storage Azure Active Directory Site Recovery tjänst och Site Recovery övervakning. [Läs mer](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

Om du vill kontrol lera utgående anslutningar med hjälp av IP-adresser i stället för NSG-regler, tillåter du dessa adresser för IP-baserade brand väggar, proxy eller NSG-regler.

>[!NOTE]
>Vi rekommenderar att du alltid konfigurerar NSG-regler med service märken för utgående åtkomst.

  - [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Windows Azure Datacenter IP-intervall i Tyskland](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Windows Azure Datacenter IP-intervall i Kina](https://www.microsoft.com/download/details.aspx?id=42064)
  - [URL:er och IP-adressintervall för Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [IP-adresser för Site Recovery-tjänstens slutpunkter](https://aka.ms/site-recovery-public-ips)

## <a name="verify-azure-vm-certificates"></a>Verifiera certifikat för virtuella Azure-datorer

Kontrollera att de virtuella datorer som du vill replikera har de senaste rotcertifikaten. Om de inte har det kan de virtuella datorerna inte registreras i Site Recovery på grund av säkerhetsbegränsningar.

- I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.
- I virtuella datorer med Linux följer du de riktlinjer du fått från Linux-distributören för att hämta de senaste betrodda rotcertifikaten och listan över återkallade certifikat till den virtuella datorn.

## <a name="set-permissions-on-the-account"></a>Ange behörigheter för kontot

Azure Site Recovery har tre inbyggda roller som styr Site Recovery-hanteringen.

- **Site Recovery-bidragsgivare** – Den här rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i ett Recovery Services-valv. En användare med denna roll kan dock inte skapa eller ta bort ett Recovery Services-valv eller tilldela behörighet till andra användare. Den här rollen lämpar sig bäst för haveriberedskapsadministratörer som kan aktivera och hantera haveriberedskap för program eller hela organisationer.

- **Site Recovery-operatör** – Den här rollen har behörighet att utföra och hantera operationer för redundans och återställning av fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela åtkomstbehörigheter till andra användare. Den här rollen lämpar sig bäst för en haveriberedskapsoperatör som kan redundansväxla virtuella datorer eller program efter instruktioner från programägare och IT-administratörer. När problemet lösts kan haveriberedskapsoperatören återaktivera skydd och återställa de virtuella datorerna efter fel.

- **Site Recovery-läsare** – Den här rollen har behörighet att visa all Site Recovery-hantering. Den här rollen lämpar sig bäst för en IT-chef som kan övervaka aktuell skyddsnivå och skapa supportärenden.

Lär dig mer om [inbyggda Azure RBAC-roller](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Aktivera replikering för en virtuell dator

### <a name="select-the-source"></a>Välj källan

1. I Recovery Services-valven klickar du på valvnamnet > **+Replikera**.
2. I **Källa** väljer du **Azure**.
3. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
4. Välj den **källprenumeration** där de virtuella datorerna körs. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
5. Välj **käll resurs gruppen**och spara inställningarna genom att klicka på **OK** .

    ![Konfigurera källan](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Välj virtuella datorer

Site Recovery hämtar en lista med de virtuella datorer som är kopplade till prenumerationen och resursgruppen eller molntjänsten.

1. I **Virtuella datorer** väljer du de virtuella datorer du vill replikera.
2. Klicka på **OK**.

### <a name="configure-replication-settings"></a>Konfigurera replikeringsinställningar

Site Recovery skapar standardinställningar och replikeringsprinciper för målregionen. Du kan ändra inställningarna efter behov.

1. Klicka på **Inställningar** och visa inställningar för mål och replikering.
2. Om du vill åsidosätta standardmålinställningarna för målet klickar du på **Anpassa** intill **Resursgrupp, nätverk, lagring och tillgänglighet**.

   ![Konfigurera inställningar](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Anpassa mål inställningarna som sammanfattas i tabellen.

    **Inställning** | **Detaljer**
    --- | ---
    **Mål prenumeration** | Som standard är mål prenumerationen densamma som käll prenumerationen. Klicka på Anpassa om du vill välja en annan målprenumeration inom samma Azure Active Directory-klientorganisation.
    **Målplats** | Den målregion som används för haveriberedskap.<br/><br/> Vi rekommenderar att målplatsen överensstämmer med Site Recovery-valvets plats.
    **Mål resurs grupp** | Den resursgrupp i målregionen som innehåller virtuella Azure-datorer efter redundansväxling.<br/><br/> Som standard skapar Site Recovery en ny resursgrupp i målregionen med suffixet ”asr”. Platsen för mål resurs gruppen kan vara vilken region som helst, förutom den region där dina virtuella käll datorer finns.
    **Virtuellt mål nätverk** | Nätverket i den målregion där virtuella Azure-datorer finns efter redundansväxling.<br/><br/> Som standard skapar Site Recovery ett nytt virtuellt nätverk (och undernät) i målregionen med suffixet ”asr”.
    **Cachelagra lagrings konton** | Site Recovery använder ett lagringskonto i källregionen. Ändringar i virtuella källdatorer skickas till det här kontot innan replikering till målplatsen.<br/><br/> Om du använder ett brand Väggs-aktiverat lagrings konto för cache kontrollerar du att du aktiverar **Tillåt betrodda Microsoft-tjänster**. [Läs mer.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).. Se också till att du tillåter åtkomst till minst ett undernät för det virtuella käll nätverket.
    **Mål lagrings konton (den virtuella käll datorn använder icke-hanterade diskar)** | Som standard skapar Site Recovery ett nytt lagringskonto i målregionen som speglar lagringskontot för den virtuella källdatorn.<br/><br/> Aktivera **Tillåt betrodda Microsoft-tjänster** om du använder ett brand Väggs-aktiverat cache Storage-konto.
    **Hanterade replik diskar (om den virtuella käll datorn använder Managed Disks)** | Som standard skapar Site Recovery hanterade replikeringsdiskar i målregionen, som speglar den virtuella källdatorns hanterade diskar med samma lagringstyp (standard eller premium) som den virtuella källdatorns hanterade disk. Du kan bara anpassa disk typ 
    **Tillgänglighets uppsättningar för mål** | Som standard skapar Azure Site Recovery en ny tillgänglighetsuppsättning i målregionen med namn som har suffixet ”asr” för de virtuella datorer som ingår i en tillgänglighetsuppsättning i källregionen. Om den tillgänglighetsuppsättning som skapades av Azure Site Recovery redan finns återanvänds den.
    **Mål tillgänglighets zoner** | Som standard tilldelar Site Recovery samma zonnummer som källregionen i målregionen om målregionen har stöd för tillgänglighetszoner.<br/><br/> Om mål regionen inte stöder tillgänglighets zoner konfigureras de virtuella mål datorerna som enskilda instanser som standard.<br/><br/> Klicka på **Anpassa** för att konfigurera virtuella datorer som en del av en tillgänglighets uppsättning i mål regionen.<br/><br/> Du kan inte ändra tillgänglighets typ (enskild instans, tillgänglighets uppsättning eller tillgänglighets zon) när du har aktiverat replikering. Du måste inaktivera och aktivera replikering för att ändra tillgänglighetstypen.

4. Om du vill anpassa inställningarna för replikeringsprincipen klickar du på **Anpassa** bredvid **replikeringsprincip**och ändrar inställningarna efter behov.

    **Inställning** | **Detaljer**
    --- | ---
    **Namn på replikeringsprincip** | Principnamn.
    **Kvarhållning av återställnings punkt** | Som standard behåller Site Recovery återställningspunkter i 24 timmar. Du kan ställa in ett värde mellan 1 och 72 timmar.
    **Frekvens för programkonsekventa ögonblicks bilder** | Som standard tar Site Recovery en appkompatibel ögonblicksbild var 4:e timme. Du kan ställa in ett värde mellan 1 och 12 timmar.<br/><br/> En programkonsekvent ögonblicks bild är en tidpunkts ögonblicks bild av program data i den virtuella datorn. Volume Shadow Copy-tjänsten (VSS) säkerställer att programmen i den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.
    **Replikeringsgrupp** | Om programmet kräver konsekvens för flera virtuella datorer mellan virtuella datorer kan du skapa en replikeringsgrupp för dessa virtuella datorer. Som standard är valda virtuella datorer inte en del av någon replikeringsgrupp.

5. I **Anpassa** väljer du **Ja** för konsekvens för flera virtuella datorer om du vill lägga till virtuella datorer i en ny eller en befintlig replikeringsgrupp. Klicka på **OK**. 

    >[!NOTE]
    >- Alla datorer i en replikeringsgrupp har delade krascher konsekventa och programkonsekventa återställnings punkter vid växling vid fel.
    >- Att aktivera konsekvens för flera virtuella datorer kan påverka arbets belastnings prestandan (den är processor intensiv). Den bör endast användas om datorer kör samma arbets belastning och du behöver konsekvens på flera datorer.
    >- Du kan ha högst 16 virtuella datorer i en replikeringsgrupp.
    >- Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Se till att det inte finns någon brand vägg som blockerar den interna kommunikationen mellan de virtuella datorerna via den här porten.
    >- För virtuella Linux-datorer i en replikeringsgrupp ser du till att utgående trafik på port 20004 öppnas manuellt i enlighet med vägledningen för Linux-versionen.



### <a name="configure-encryption-settings"></a>Konfigurera krypteringsinställningar

Om den virtuella käll datorn har Azure Disk Encryption (ADE) aktiverat, granskar du inställningarna.

1. Verifiera inställningarna:
    - **Nyckel valv för disk kryptering**: som standard skapar Site Recovery ett nytt nyckel valv på den virtuella käll diskens disk krypterings nycklar med ett "ASR"-suffix. Om nyckel valvet redan finns återanvänds det.
    - **Nyckel valv för nyckel kryptering**: som standard skapar Site Recovery ett nytt nyckel valv i mål regionen. Namnet har "ASR"-suffix och baseras på den virtuella käll nyckelns krypterings nycklar. Om nyckel valvet som skapats av Site Recovery redan finns återanvänds det.

2. Klicka på **Anpassa** för att välja anpassade nyckelvalv.

>[!NOTE]
>Endast virtuella Azure-datorer som kör Windows-operativsystem och är [aktiverade för kryptering med Azure AD-appen](https://aka.ms/ade-aad-app) stöds för närvarande av Azure Site Recovery.
>

### <a name="track-replication-status"></a>Spåra replikeringsstatus

1. I **Inställningar** klickar du på **Uppdatera** för att hämta senaste status.
2. Spåra förlopp och status på följande sätt:
    - Spåra förloppet för jobbet **Aktivera skydd** i **Inställningar** > **Jobb** > **Site Recovery-jobb**.
    - I **inställningar** > **Replikerade objekt** visas status för virtuella datorer och den inledande replikeringsprocessen. Klicka på en virtuell dator för att visa dess inställningar.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du haveriberedskap för en virtuell Azure-dator. Nu kan du initiera ett programåterställningstest för att kontrollera att redundans fungerar som förväntat.

> [!div class="nextstepaction"]
> [Köra ett återställningstest](azure-to-azure-tutorial-dr-drill.md)
