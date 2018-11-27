---
title: Konfigurera haveriberedskap för virtuella Azure-datorer till en sekundär Azure-region med Azure Site Recovery
description: Lär dig konfigurera programåterställning för virtuella Azure-datorer till annan Azure-region med Azure Site Recovery-tjänsten.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/18/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cff6d38867ef8ecaf1435fd4c4cc22fe63d70575
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283254"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region"></a>Konfigurera haveriberedskap för virtuella Azure-datorer till en sekundär Azure-region

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudien visar hur du konfigurerar haveriberedskap till en sekundär Azure-region för virtuella Azure-datorer. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapar ett Recovery Services-valv
> * Verifiera målresursuppsättningar
> * Konfigurera utgående åtkomst för virtuella datorer
> * Aktivera replikering för en virtuell dator

> [!NOTE]
> Den här artikeln innehåller instruktioner för distribution av haveriberedskap med de enklaste inställningarna. Om du vill veta mer om anpassade inställningar läser du artiklarna i [avsnittet med anvisningar](azure-to-azure-how-to-enable-replication.md). o

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](concepts-azure-to-azure-architecture.md).
- Granska [kraven för stöd](site-recovery-support-matrix-azure-to-azure.md) för alla komponenter.

## <a name="create-a-vault"></a>Skapa ett valv

Skapa valvet i valfri region, utom i källregionen.

1. Logga in på [Azure-portalen](https://portal.azure.com) > **Recovery Services**.
2. Klicka på **Skapa en resurs** > **Övervakning och hantering** > **Backup och Site Recovery**.
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

  - [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [Windows Azure Datacenter IP-intervall i Tyskland](https://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [Windows Azure Datacenter IP-intervall i Kina](https://www.microsoft.com/en-us/download/details.aspx?id=42064)
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

Läs mer om [inbyggda roller för Azure RBAC](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication"></a>Aktivera replikering

### <a name="select-the-source"></a>Välj källan

1. I Recovery Services-valven klickar du på valvnamnet > **+Replikera**.
2. I **Källa** väljer du **Azure**.
3. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
4. Välj den **källprenumeration** där de virtuella datorerna körs. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
5. Välj **Källresursgrupp** för Resource Manager-baserade virtuella datorer eller **Molntjänst** för klassiska virtuella datorer.
6. Spara inställningarna genom att klicka på **OK**.

### <a name="select-the-vms"></a>Välj virtuella datorer

Site Recovery hämtar en lista med de virtuella datorer som är kopplade till prenumerationen och resursgruppen eller molntjänsten.

1. I **Virtuella datorer** väljer du de virtuella datorer du vill replikera.
2. Klicka på **OK**.

### <a name="configure-replication-settings"></a>Konfigurera replikeringsinställningar

Site Recovery skapar standardinställningar och replikeringsprinciper för målregionen. Du kan ändra inställningarna efter behov.

1. Klicka på **Inställningar** och visa inställningar för mål och replikering.
2. Om du vill åsidosätta standardinställningarna för målet klickar du på **Anpassa** intill **resursgrupp, nätverk, lagring och tillgänglighetsuppsättningar**.

  ![Konfigurera inställningar](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Anpassa målinställningar enligt följande:

    - **Målprenumeration**: Målprenumerationen som används för haveriberedskap. Som standard är målprenumerationen samma som källprenumerationen. Klicka på Anpassa om du vill välja en annan målprenumeration inom samma Azure Active Directory-klientorganisation.
    - **Målplats**: den målregion som används för haveriberedskap. Vi rekommenderar att målplatsen överensstämmer med Site Recovery-valvets plats.
    - **Målresursgrupp**: den resursgrupp i målregionen som innehåller virtuella Azure-datorer efter redundansväxling. Som standard skapar Site Recovery en ny resursgrupp i målregionen med suffixet ”asr”. resursgruppens plats i målresursgruppen kan vara valfri region förutom den region där dina virtuella källdatorer finns.
    - **Virtuellt målnätverk**: nätverket i målregionen som innehåller virtuella Azure-datorer efter redundansväxling.
      Som standard skapar Site Recovery ett nytt virtuellt nätverk (och undernät) i målregionen med suffixet ”asr”.
    - **Cachelagringskonton**: Site Recovery använder ett lagringskonto i källregionen. Ändringar i virtuella källdatorer skickas till det här kontot innan replikering till målplatsen.
      >[!NOTE]
      >Om du använder cachelagringskonto med brandvägg aktiverat kontrollerar du att ”Tillåt betrodda Microsoft-tjänster” är på. [Läs mer.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **Mållagringskonton (om den virtuella måldatorn inte använder hanterade diskar)**: Som standard skapar Site Recovery ett nytt lagringskonto i målregionen som speglar lagringskontot för den virtuella källdatorn.
      >[!NOTE]
      >Om du använder käll- eller mållagringskonto med brandvägg aktiverat kontrollerar du att ”Tillåt betrodda Microsoft-tjänster” är på. [Läs mer.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **Hanterade replikeringsdiskar (om den virtuella måldatorn använder hanterade diskar)**: Som standard skapar Site Recovery hanterade replikeringsdiskar i målregionen, som speglar den virtuella källdatorns hanterade diskar med samma lagringstyp (standard eller premium) som den virtuella källdatorns hanterade disk.
    - **Tillgänglighetsuppsättningar för mål**: Som standard skapar Site Recovery en ny tillgänglighetsuppsättning i målregionen med suffixet ”asr”. Det går bara att lägga till tillgänglighetsuppsättningar om virtuella datorer ingår i en tillgänglighetsuppsättning i källregionen.

4. Om du vill anpassa inställningarna för replikeringsprincip klickar du på **Anpassa** intill **Replikeringsprincip** och ändrar följande inställningar efter behov:

    - **Namn på replikeringsprincip**: Principnamn.
    - **Kvarhållning av återställningspunkt**: Som standard behåller Site Recovery återställningspunkter under 24 timmar. Du kan ställa in ett värde mellan 1 och 72 timmar.
    - **Appkompatibel ögonblicksbildsfrekvens**: Som standard tar Site Recovery en appkompatibel ögonblicksbild var fjärde timme. Du kan ställa in ett värde mellan 1 och 12 timmar. En appkompatibel ögonblicksbild är en ögonblicksbild av programdata i den virtuella datorn. Volume Shadow Copy-tjänsten (VSS) säkerställer att programmen i den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.
    - **Replikeringsgrupp**: Om programmet kräver konsekvens för flera virtuella datorer mellan VM kan du skapa en replikeringsgrupp för dessa virtuella datorer. Som standard är valda virtuella datorer inte en del av någon replikeringsgrupp.

5. I **Anpassa** väljer du **Ja** för konsekvens för flera virtuella datorer om du vill lägga till virtuella datorer i en ny eller en befintlig replikeringsgrupp. för att göra virtuella datorer till en del av en replikeringsgrupp. Klicka sedan på **OK**.

    - Alla maskiner i en replikeringsgrupp har delade kraschkonsekventa och appkonsekventa återställningspunkter när de redundansväxlas. Aktivering av konsekvens för flera virtuella datorer kan påverka prestandan och ska bara användas om datorer kör samma arbetsbelastning och konsekvens mellan flera datorer krävs.
    - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004. Se till att det inte finns någon brandvägg som blockerar den interna kommunikationen mellan de virtuella datorerna på port 20004. Om du vill att virtuella datorer med Linux ska vara med i en replikeringsgrupp måste du se till att du manuellt öppnar för utgående trafik på port 20004 enligt riktlinjerna för den specifika Linux-versionen.

### <a name="configure-encryption-settings"></a>Konfigurera krypteringsinställningar

Om den virtuella källdatorn har Azure-diskkryptering (ADE) aktiverad visas krypteringsinställningar:

1. Granska krypteringsinställningarna.
    - **Diskkrypteringsnyckelvalv**: som standard skapar Azure Site Recovery ett nytt nyckelvalv i målregionen med namn som har suffixet ”asr” baserat på den virtuella källdatorns diskkrypteringsnycklar. Om det nyckelvalv som skapades av Azure Site Recovery redan finns återanvänds det.
    - **Nyckelkrypteringsnyckelvalv**: som standard skapar Azure Site Recovery ett nytt nyckelvalv i målregionen med namn som har suffixet ”asr” baserat på den virtuella källdatorns nyckelkrypteringsnycklar. Om det nyckelvalv som skapades av Azure Site Recovery redan finns återanvänds det.

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
