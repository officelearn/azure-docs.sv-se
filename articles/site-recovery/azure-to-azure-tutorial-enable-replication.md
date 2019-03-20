---
title: Konfigurera haveriberedskap för virtuella Azure-datorer till en sekundär Azure-region med Azure Site Recovery
description: Lär dig konfigurera programåterställning för virtuella Azure-datorer till annan Azure-region med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: ff18a14b314b5757629205f4bf0eb134411688ec
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57853138"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Konfigurera haveriberedskap för virtuella Azure-datorer

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudien visar hur du konfigurerar haveriberedskap för virtuella Azure-datorer genom att replikera dem från en Azure-region till en annan. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapar ett Recovery Services-valv
> * Verifiera målresursuppsättningar
> * Konfigurera utgående åtkomst för virtuella datorer
> * Aktivera replikering för en virtuell dator

> [!NOTE]
> Den här artikeln innehåller instruktioner för distribution av haveriberedskap med de enklaste inställningarna. Om du vill veta mer om anpassade inställningar läser du artiklarna i [avsnittet med anvisningar](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du:

- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](concepts-azure-to-azure-architecture.md).
- Granska den [supportkrav](site-recovery-support-matrix-azure-to-azure.md) innan du börjar.

## <a name="create-a-vault"></a>Skapa ett valv

Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Hanteringsverktyg** > **Backup och Site Recovery**.
3. I **Namn** anger du ett eget namn som identifierar valvet. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp eller välj en befintlig. Ange en Azure-region. Information om vilka regioner som stöds finns under Geografisk tillgänglighet i avsnittet med [Azure Site Recovery-prisinformation](https://azure.microsoft.com/pricing/details/site-recovery/).
5. För att snabbt komma åt valvet från instrumentpanelen klickar du på **Fäst på instrumentpanelen** och sedan på **Skapa**.

   ![Nytt valv](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Det nya valvet läggs till på **Instrumentpanelen** under **Alla resurser** och på huvudsidan för **Recovery Services-valv**.

## <a name="verify-target-resources"></a>Verifiera målresurser

1. Verifiera att Azure-prenumerationen låter dig skapa virtuella datorer i målregionen. Kontakta supporten och aktivera den kvot som krävs.
2. Se till att din prenumeration har tillräckligt med resurser för att hantera storlekar för virtuella datorer som överensstämmer med dina virtuella källdatorer. Site Recovery väljer samma storlek eller närmaste möjliga storlek för den virtuella måldatorn.

## <a name="configure-outbound-network-connectivity"></a>Konfigurera utgående nätverksanslutning

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

Om du vill styra utgående anslutningar med hjälp av IP-adresser i stället för URL:er tillåter du dessa adresser för IP-baserade brandväggar, proxy eller NSG-regler.

  - [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Windows Azure Datacenter IP-intervall i Tyskland](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Windows Azure Datacenter IP-intervall i Kina](https://www.microsoft.com/download/details.aspx?id=42064)
  - [URL:er och IP-adressintervall för Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [IP-adresser för Site Recovery-tjänstens slutpunkter](https://aka.ms/site-recovery-public-ips)

Du kan använda det här [skriptet](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) om du vill skapa de NSG-regler som krävs.

## <a name="verify-azure-vm-certificates"></a>Verifiera certifikat för virtuella Azure-datorer

Kontrollera att de virtuella datorer som du vill replikera har de senaste rotcertifikaten. Om de inte har det kan de virtuella datorerna inte registreras i Site Recovery på grund av säkerhetsbegränsningar.

- I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.
- I virtuella datorer med Linux följer du de riktlinjer du fått från Linux-distributören för att hämta de senaste betrodda rotcertifikaten och listan över återkallade certifikat till den virtuella datorn.

## <a name="set-permissions-on-the-account"></a>Ange behörigheter för kontot

Azure Site Recovery har tre inbyggda roller som styr Site Recovery-hanteringen.

- **Site Recovery-bidragsgivare** – Den här rollen har alla behörigheter som krävs för att hantera Azure Site Recovery-åtgärder i ett Recovery Services-valv. En användare med denna roll kan dock inte skapa eller ta bort ett Recovery Services-valv eller tilldela behörighet till andra användare. Den här rollen lämpar sig bäst för haveriberedskapsadministratörer som kan aktivera och hantera haveriberedskap för program eller hela organisationer.

- **Site Recovery-operatör** – Den här rollen har behörighet att utföra och hantera operationer för redundans och återställning av fel. En användare med den här rollen kan inte aktivera eller inaktivera replikering, skapa eller ta bort valv, registrera ny infrastruktur eller tilldela åtkomstbehörigheter till andra användare. Den här rollen lämpar sig bäst för en haveriberedskapsoperatör som kan redundansväxla virtuella datorer eller program efter instruktioner från programägare och IT-administratörer. När problemet lösts kan haveriberedskapsoperatören återaktivera skydd och återställa de virtuella datorerna efter fel.

- **Site Recovery-läsare** – Den här rollen har behörighet att visa all Site Recovery-hantering. Den här rollen lämpar sig bäst för en IT-chef som kan övervaka aktuell skyddsnivå och skapa supportärenden.

Läs mer om [Azure RBAC inbyggda roller](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication"></a>Aktivera replikering

### <a name="select-the-source"></a>Välj källan

1. I Recovery Services-valven klickar du på valvnamnet > **+Replikera**.
2. I **Källa** väljer du **Azure**.
3. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
4. Välj den **källprenumeration** där de virtuella datorerna körs. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
5. Välj den **källresursgruppen**, och klicka på **OK** att spara inställningarna.

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


3. Anpassa Målinställningar som sammanfattas i tabellen.

    **Inställning** | **Detaljer**
    --- | ---
    **Målprenumerationen** | Som standard är samma som källprenumerationen i målprenumerationen. Klicka på Anpassa om du vill välja en annan målprenumeration inom samma Azure Active Directory-klientorganisation.
    **Målplats** | Den målregion som används för haveriberedskap.<br/><br/> Vi rekommenderar att målplatsen överensstämmer med Site Recovery-valvets plats.
    **Målresursgrupp** | Den resursgrupp i målregionen som innehåller virtuella Azure-datorer efter redundansväxling.<br/><br/> Som standard skapar Site Recovery en ny resursgrupp i målregionen med suffixet ”asr”. Platsen för målresursgruppen kan vara vilken region som helst utom den region som är värd för dina virtuella källdatorer.
    **Virtuellt Målnätverk** | Nätverket i den målregion där virtuella Azure-datorer finns efter redundansväxling.<br/><br/> Som standard skapar Site Recovery ett nytt virtuellt nätverk (och undernät) i målregionen med suffixet ”asr”.
    **Cachelagringskonton** | Site Recovery använder ett lagringskonto i källregionen. Ändringar i virtuella källdatorer skickas till det här kontot innan replikering till målplatsen.<br/><br/> Om du använder en brandvägg aktiverad cachelagringskontot, se till att du aktiverar **Tillåt att betrodda Microsoft-tjänster**. [Läs mer.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Mållagringskonton (Virtuella måldatorn använder icke-hanterade diskar)** | Som standard skapar Site Recovery ett nytt lagringskonto i målregionen som speglar lagringskontot för den virtuella källdatorn.<br/><br/> Aktivera **Tillåt att betrodda Microsoft-tjänster** om du använder en brandvägg aktiverad cachelagringskontot.
    **Hanterade replikeringsdiskar (om källan som virtuell dator använder hanterade diskar)** | Som standard skapar Site Recovery hanterade replikeringsdiskar i målregionen, som speglar den virtuella källdatorns hanterade diskar med samma lagringstyp (standard eller premium) som den virtuella källdatorns hanterade disk.
    **Tillgänglighetsuppsättningar för mål** | Som standard skapar Azure Site Recovery en ny tillgänglighetsuppsättning i målregionen med namn som har suffixet ”asr” för de virtuella datorer som ingår i en tillgänglighetsuppsättning i källregionen. Om den tillgänglighetsuppsättning som skapades av Azure Site Recovery redan finns återanvänds den.
    **Tillgänglighetszoner för mål** | Som standard tilldelar Site Recovery samma zonnummer som källregionen i målregionen om målregionen har stöd för tillgänglighetszoner.<br/><br/> Om målregion inte stöder tillgänglighetszoner kan konfigureras de virtuella datorerna som mål som enda instanser som standard.<br/><br/> Klicka på **anpassa** att konfigurera virtuella datorer som en del av en tillgänglighetsuppsättning i målregionen.<br/><br/> Du kan inte ändra typen tillgänglighet (instans, tillgänglighetszon för set eller tillgänglighet) när du har aktiverat replikering. Du måste inaktivera och aktivera replikering för att ändra tillgänglighetstypen.

4. Om du vill anpassa inställningarna för replikeringsprincipen klickar du på **anpassa** bredvid **replikeringsprincip**, och ändra inställningarna efter behov.

    **Inställning** | **Detaljer**
    --- | ---
    **Namn på replikeringsprincip** | Principnamn.
    **Kvarhållning av återställningspunkt** | Som standard behåller Site Recovery återställningspunkter i 24 timmar. Du kan ställa in ett värde mellan 1 och 72 timmar.
    **Appkompatibel ögonblicksbildsfrekvens** | Som standard tar Site Recovery en appkompatibel ögonblicksbild var 4:e timme. Du kan ställa in ett värde mellan 1 och 12 timmar.<br/><br/> En appkompatibel ögonblicksbild är en point-in-time-ögonblicksbild av programdata på den virtuella datorn. Volume Shadow Copy-tjänsten (VSS) säkerställer att programmen i den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.
    **Replikeringsgrupp** | Om programmet kräver konsekvens för flera virtuella datorer mellan virtuella datorer kan du skapa en replikeringsgrupp för dessa virtuella datorer. Som standard är valda virtuella datorer inte en del av någon replikeringsgrupp.

5. I **Anpassa** väljer du **Ja** för konsekvens för flera virtuella datorer om du vill lägga till virtuella datorer i en ny eller en befintlig replikeringsgrupp. Klicka sedan på **OK**. 

    >[!NOTE]
    >- Alla datorer i en replikeringsgrupp har delade krascher konsekvent och programkonsekventa återställningspunkter vid redundansväxling.
    >- Aktivering av konsekvens för flera datorer kan påverka prestandan (det är Processorintensiva). Den bör endast användas om datorer kör samma arbetsbelastning och konsekvens mellan flera datorer.
    >- Du kan ha högst 16 virtuella datorer i en replikeringsgrupp.
    >- Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Kontrollera att det finns ingen brandvägg blockerar den interna kommunikationen mellan de virtuella datorerna på den här porten.
    >- Kontrollera att utgående trafik på port 20004 manuellt öppnas i enlighet med vägledning för Linux-versionen för virtuella Linux-datorer i en replikeringsgrupp.



### <a name="configure-encryption-settings"></a>Konfigurera krypteringsinställningar

Om den Virtuella källdatorn har Azure-diskkryptering (ADE) aktiverad, granskar du inställningarna.

1. Kontrollera inställningarna:
    - **Nyckelvalv för diskkryptering**: Som standard skapar Site Recovery ett nytt nyckelvalv på källan VM diskkrypteringsnycklarna, med suffixet ”asr”. Om det finns redan i key vault, återanvänds.
    - **Nyckelvalv för nyckelkryptering**: Som standard skapar Site Recovery ett nytt nyckelvalv i målregionen. Namnet har suffixet ”asr” och baseras på källan VM viktiga krypteringsnycklar. Om nyckelvalvet som skapats av Site Recovery redan finns, återanvänds.

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
