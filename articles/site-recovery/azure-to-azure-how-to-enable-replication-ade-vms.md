---
title: Aktivera replikering för krypterade virtuella Azure-datorer i Azure Site Recovery
description: I den här artikeln beskrivs hur du konfigurerar replikering för Azure Disk Encryption-aktiverade virtuella datorer från en Azure-region till en annan med hjälp av Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 2bbb02df782439d934e96e7c16f28b9c11cc01fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408624"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replikera Virtuella virtuella datorer med Azure Disk-kryptering till en annan Azure-region

I den här artikeln beskrivs hur du replikerar virtuella Azure-datorer med Azure Disk Encryption (ADE) aktiverat, från en Azure-region till en annan.

>[!NOTE]
> Site Recovery stöder för närvarande ADE, med och utan Azure Active Directory (AAD) för virtuella datorer som kör Windows-operativsystem. För Linux-operativsystem stöder vi endast ADE utan AAD. Dessutom måste de virtuella datorerna använda hanterade diskar för datorer som kör ADE 1.1 (utan AAD). Virtuella datorer med ohanterade diskar stöds inte. Om du växlar från ADE 0.1 (med AAD) till 1.1 måste du inaktivera replikering och aktivera replikering för en virtuell dator efter att 1.1 har aktiverats.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>Nödvändiga användarbehörigheter
Site Recovery kräver att användaren har behörighet att skapa nyckelvalvet i målregionen och kopiera nycklar från källområdesnyckelvalvet till nyckelvalvet för målregionen.

För att aktivera replikering av diskkrypteringsaktiverade virtuella datorer från Azure-portalen behöver användaren följande behörigheter för både **källregionen och målområdesnyckelvalven.**

- Behörigheter för nyckelvalv
    - Lista, skapa och hämta
    
- Hemliga behörigheter för nyckelvalv
    - Hemliga hanteringsåtgärder
        - Hämta, lista och ange
    
- Nyckelvalvnyckelbehörigheter (krävs endast om de virtuella datorerna använder nyckelkrypteringsnyckel för att kryptera diskkrypteringsnycklar)
    - Nyckelhanteringsoperationer
        - Hämta, lista och skapa
    - Kryptografiska operationer
        - Dekryptera och kryptera

Om du vill hantera behörigheter går du till nyckelvalvresursen i portalen. Lägg till de behörigheter som krävs för användaren. I följande exempel visas hur du aktiverar behörigheter till nyckelvalvet *ContosoWeb2Keyvault*, som finns i källregionen.

1. Gå till **Hem** > **Keyvaults** > **ContosoWeb2KeyVault > åtkomstprinciper**.

   ![Fönstret Behörighetsfönster för nyckelvalv](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Du kan se att det inte finns några användarbehörigheter. Välj **Lägg till ny**. Ange information om användare och behörigheter.

   ![Behörigheter för Keyvault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Om användaren som aktiverar haveriberedskap (DR) inte har behörighet att kopiera nycklarna kan en säkerhetsadministratör som har rätt behörighet använda följande skript för att kopiera krypteringshemligheter och nycklar till målregionen.

Om du vill felsöka behörigheter läser du behörighetsproblem med [nyckelvalv](#trusted-root-certificates-error-code-151066) senare i den här artikeln.

>[!NOTE]
>Om du vill aktivera replikering av diskkrypteringsaktiverade virtuella datorer från portalen behöver du minst behörigheter för lista på nyckelvalv, hemligheter och nycklar.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Kopiera diskkrypteringsnycklar till DR-regionen med hjälp av PowerShell-skriptet

1. [Öppna råskriptkoden "CopyKeys".](https://aka.ms/ade-asr-copy-keys-code)
2. Kopiera skriptet till en fil och namnge det **Copy-keys.ps1**.
3. Öppna Windows PowerShell-programmet och gå till mappen där du sparade filen.
4. Kör Copy-keys.ps1.
5. Ange Azure-autentiseringsuppgifter för att logga in.
6. Välj **Azure-prenumeration** för dina virtuella datorer.
7. Vänta tills resursgrupperna har läses in och välj sedan **resursgruppen** för dina virtuella datorer.
8. Välj de virtuella datorerna i listan som visas. Endast virtuella datorer som är aktiverade för diskkryptering finns i listan.
9. Välj **målplats**.

    - **Diskkrypteringsnyckelvalv**
    - **Nyckelnyckelvalv**

   Som standard skapar Site Recovery ett nytt nyckelvalv i målregionen. Valvets namn har ett suffix "asr" som baseras på käll-VM-diskkrypteringsnycklarna. Om det redan finns ett nyckelvalv som har skapats av Site Recovery återanvänds det. Välj ett annat nyckelvalv i listan om det behövs.

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
    - **Målgrupp:** Den resursgrupp som alla replikerade virtuella datorer tillhör. Som standard skapar Site Recovery en ny resursgrupp i målregionen. Namnet får suffixet "asr". Om det redan finns en resursgrupp som har skapats av Azure Site Recovery återanvänds den. Du kan också välja att anpassa den, som visas i följande avsnitt. Platsen för målresursgruppen kan vara vilken Azure-region som helst utom den region där käll virtuella datorer är värd.
    - **Mål virtuellt nätverk**: Som standard skapar Site Recovery ett nytt virtuellt nätverk i målregionen. Namnet får suffixet "asr". Den är mappad till ditt källnätverk och används för framtida skydd. [Läs mer](site-recovery-network-mapping-azure-to-azure.md) om nätverksmappning.
    - **Mållagringskonton (om källdatorns virtuella datorer inte använder hanterade diskar):** Som standard skapar Site Recovery ett nytt mållagringskonto genom att härma lagringskonfigurationen för käll-VM. Om det redan finns ett lagringskonto återanvänds det.
    - **Replikhanterade diskar (om källdatorn använder hanterade diskar):** Site Recovery skapar nya replikhanterade diskar i målområdet för att spegla källdatorns hanterade diskar av samma lagringstyp (standard eller premium) som källdatorns hanterade diskar.
    - **Cachelagringskonton**: Site Recovery behöver ett extra lagringskonto som kallas *cachelagring* i källregionen. Alla ändringar på käll-virtuella datorer spåras och skickas till cachelagringskontot. De replikeras sedan till målplatsen.
    - **Tillgänglighetsuppsättning**: Som standard skapar Site Recovery en ny tillgänglighetsuppsättning i målregionen. Namnet har suffixet "asr". Om det redan finns en tillgänglighetsuppsättning som har skapats av Site Recovery återanvänds den.
    - **Diskkrypteringsnyckelvalv:** Som standard skapar Site Recovery ett nytt nyckelvalv i målregionen. Den har ett "asr" suffix som baseras på källan VM diskkrypteringsnycklar. Om det redan finns ett nyckelvalv som har skapats av Azure Site Recovery återanvänds det.
    - **Nyckelkrypteringsnyckelvalv:** Som standard skapar Site Recovery ett nytt nyckelvalv i målregionen. Namnet har ett suffix för "asr" som baseras på krypteringsnycklarna för käll-VM-nycklar. Om det redan finns ett nyckelvalv som skapats av Azure Site Recovery återanvänds det.
    - **Replikeringsprincipen**: Definierar inställningarna för återställningspunktkvarhållningshistorik och appkonsekvent ögonblicksbildfrekvens. Som standard skapar Site Recovery en ny replikeringsprincip med standardinställningar på *24 timmar* för återställningspunktkvarhållning och *60 minuter* för appkonsekvent ögonblicksbildfrekvens.

## <a name="customize-target-resources"></a>Anpassa målresurser

Följ dessa steg för att ändra standardmålinställningarna för webbplatsåterställning.

1. Välj **Anpassa** bredvid Målprenumeration om du vill ändra standardmålprenumerationen. Välj prenumerationen i listan över prenumerationer som är tillgängliga i Azure AD-klienten.

2. Välj **Anpassa bredvid** "Resursgrupp, Nätverk, Lagring och Tillgänglighetsuppsättningar" om du vill ändra följande standardinställningar:
    - För **målgruppen Målresurs**väljer du resursgruppen i listan över resursgrupper på prenumerationens målplats.
    - För **virtuellt målnätverk**väljer du nätverket från en lista över virtuella nätverk på målplatsen.
    - För **tillgänglighetsuppsättning**kan du lägga till inställningar för tillgänglighetsuppsättning till den virtuella datorn, om de ingår i en tillgänglighetsuppsättning i källregionen.
    - För **mållagringskonton**väljer du det konto som ska användas.

2. Välj **Anpassa** bredvid "Krypteringsinställningar" om du vill ändra följande standardinställningar:
   - För Nyckelvalv för **måldiskkryptering**väljer du nyckelvalvet för måldisken i listan över nyckelvalv på prenumerationens målplats.
   - För Nyckelvalv för **målnyckel**väljer du nyckelvalvet för målnyckelnyckeln i listan över nyckelvalv på prenumerationens målplats.

3. Välj **Skapa målresurs** > **Aktivera replikering**.
4. När de virtuella datorerna har aktiverats för replikering kan du kontrollera de virtuella datorernas hälsostatus under **Replikerade objekt**.

>[!NOTE]
>Under den första replikeringen kan statusen ta lite tid att uppdatera, utan uppenbara framsteg. Klicka på **Uppdatera** för att få den senaste statusen.

## <a name="update-target-vm-encryption-settings"></a>Uppdatera krypteringsinställningar för mål-VM
I följande scenarier måste du uppdatera krypteringsinställningarna för mål-VM:
  - Du har aktiverat site recovery-replikering på den virtuella datorn. Senare aktiverade du diskkryptering på källdatorn.
  - Du har aktiverat site recovery-replikering på den virtuella datorn. Senare ändrade du diskkrypteringsnyckeln eller nyckelkrypteringsnyckeln på källdatorn.

Du kan använda [ett skript](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) för att kopiera krypteringsnycklarna till målregionen och sedan uppdatera inställningarna för målkryptering i **Återställningstjänster valv** > *replikerade objekt* > **Egenskaper** > **Beräkning och Nätverk**.

![Dialogrutan Uppdatera ADE-inställningar](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Felsöka behörighetsproblem för nyckelvalv under Azure-to-Azure VM-replikering

Azure Site Recovery kräver minst läsbehörighet för källregionen Nyckelvalv och skrivbehörighet för nyckelvalvet för målregionen för att läsa hemligheten och kopiera det till nyckelvalvet för målregionen. 

**Orsak 1:** Du har inte behörigheten GET på **källregionen Nyckelvalv för** att läsa nycklarna. </br>
**Så här fixar du:** Oavsett om du är prenumerationsadministratör eller inte är det viktigt att du har behörighet för nyckelvalvet.

1. Gå till källregion Nyckelvalv som i det här exemplet är "ContososourceKeyvault" > **Access-principer** 
2. Under **Välj huvudansvarig** lägg tilldradmin@contoso.comditt användarnamn till exempel: " "
3. Under **Nyckelbehörigheter** väljer du GET 
4. Under **Hemlig behörighet** väljer du GET 
5. Spara åtkomstprincipen

**Orsak 2:** Du har inte behörighet för **key-valvet** i målregionen för att skriva nycklarna. </br>

*Till exempel:* Du försöker replikera en virtuell dator som har nyckelvalv *contososourceKeyvault* på en källregion.
Du har alla behörigheter för källområdesnyckelvalvet. Men under skydd väljer du det redan skapade nyckelvalvet ContosotargetKeyvault, som inte har behörighet. Ett fel uppstår.

Behörighet krävs för [målnyckelvalvet](#required-user-permissions)

**Så här fixar du:** Gå till **Hem** > **Keyvaults** > **ContosotargetKeyvault** > **Access-principer** och lägg till lämpliga behörigheter.

## <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om att köra en testundanställning.
