---
title: Konfigurera replikering för Azure Disk Encryption-aktiverade virtuella datorer i Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar replikering för Azure Disk Encryption-aktiverade virtuella datorer från en Azure-region till en annan med hjälp av Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791019"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replikera Azure Disk Encryption-aktiverade virtuella datorer till en annan Azure-region

Den här artikeln beskriver hur du replikerar Azure Disk Encryption-aktiverade virtuella datorer från en Azure-region till en annan.

>[!NOTE]
>Azure Site Recovery stöder för närvarande endast virtuella Azure-datorer som kör ett Windows-operativsystem och som är [aktiverat för kryptering med Azure Active Directory (Azure AD)](https://aka.ms/ade-aad-app).

## <a name="required-user-permissions"></a>Nödvändiga användarbehörigheter
Site Recovery kräver att användaren har behörighet att skapa nyckelvalvet i mål-region och kopiera nycklarna till regionen.

Om du vill aktivera replikering av Disk Encryption-aktiverade virtuella datorer från Azure-portalen, användaren måste du ha följande behörigheter:

- Nyckelvalvet behörigheter
    - Visa lista
    - Skapa
    - Hämta

-   Nyckelvalv hemliga behörigheter
    - Visa lista
    - Skapa
    - Hämta

- Nyckelvalv nyckelns behörigheter (krävs endast om de virtuella datorerna använder viktiga krypteringsnyckel för att kryptera diskkrypteringsnycklarna)
    - Visa lista
    - Hämta
    - Skapa
    - Kryptera
    - Avkryptera

För att hantera behörigheter, går du till key vault-resursen i portalen. Lägg till behörigheterna som krävs för användaren. I följande exempel visas hur du aktiverar behörigheter till nyckelvalvet *ContosoWeb2Keyvault*, vilket är i källregionen.

1. Gå till **Start** > **Keyvaults** > **ContosoWeb2KeyVault > principerna för dataåtkomst**.

   ![Fönstret för nyckelvalvet behörigheter](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Du kan se att det finns inga användarbehörigheter. Välj **Lägg till ny**. Ange information om användare och behörigheter.

   ![keyvault-behörigheter](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Om den användare som är att aktivera haveriberedskap (DR) inte har behörighet att kopiera nycklarna, kan en administratör som har rätt behörighet använda följande skript och kopiera kryptering hemligheter och nycklar till målregion.

Om du vill felsöka behörigheter, som avser [nyckeln vault behörighetsproblem](#trusted-root-certificates-error-code-151066) senare i den här artikeln.

>[!NOTE]
>Om du vill aktivera replikering av Disk Encryption-aktiverade virtuella datorer från portalen, behöver du minst ”List” behörigheter i nyckelvalven, hemligheter och nycklar.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Kopiera Disk krypteringsnycklar till regionen för Haveriberedskap med hjälp av PowerShell-skript

1. [Öppna ”CopyKeys” raw-skriptkoden](https://aka.ms/ade-asr-copy-keys-code).
2. Kopiera skriptet till en fil och ge den namnet **kopia keys.ps1**.
3. Öppna Windows PowerShell-programmet och gå till mappen där du sparade filen.
4. Execute Copy-keys.ps1.
5. Ange autentiseringsuppgifter för inloggning för Azure.
6. Välj den **Azure-prenumeration** för dina virtuella datorer.
7. Vänta tills resursgrupper att läsa in och välj sedan den **resursgrupp** för dina virtuella datorer.
8. Välj de virtuella datorerna i listan som visas. Endast virtuella datorer som är aktiverade för diskkryptering finns med i listan.
9. Välj den **målplats**.

    - **Nyckelvalv för kryptering av disk**
    - **Nyckelkrypterad nyckelvalv**

   Som standard skapar Site Recovery ett nytt nyckelvalv i målregionen. Namnet på valvet har suffixet ”asr” som baseras på käll-VM för diskkrypteringsnycklarna. Om ett nyckelvalv redan finns som skapades av Site Recovery återanvänds. Välj ett annat nyckelvalv i listan om det behövs.

## <a name="enable-replication"></a>Aktivera replikering

I det här exemplet primära Azure-regionen är Östasien och den sekundära regionen är Sydostasien.

1. I valvet, väljer **+ replikera**.
2. Observera följande fält.
    - **Källa**: Referenspunkt för de virtuella datorerna i det här fallet **Azure**.
    - **Källplats**: Azure-regionen där du vill skydda dina virtuella datorer. Det här exemplet är källplatsen ”Östasien”.
    - **Distributionsmodellen**: Azure-distributionsmodellen källdatorer.
    - **Käll-prenumeration**: Den prenumeration som din virtuella källdatorer tillhör. Det kan vara någon prenumeration som tillhör samma Azure Active Directory-klient som recovery services-valv.
    - **Resursgrupp**: Den resursgrupp som din virtuella källdatorer tillhör. Alla virtuella datorer i den valda resursgruppen listas för skydd i nästa steg.

3. I **virtuella datorer** > **Välj virtuella datorer**, Välj varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Välj **OK**.

4. I **inställningar**, du kan konfigurera följande inställningar för mål-plats.

    - **Målplats**: Den plats där dina källdata för virtuell dator kommer att replikeras. Site Recovery tillhandahåller en lista över passande regioner baserat på den valda datorns plats. Vi rekommenderar att du använder samma plats som platsen för Recovery Services-valvet.
    - **Målprenumeration**: Målprenumerationen som används för katastrofåterställning. Som standard är samma som källprenumerationen i målprenumerationen.
    - **Målresursgrupp**: Den resursgrupp där alla de replikerade virtuella datorerna tillhör. Som standard skapar Site Recovery en ny resursgrupp i målregionen. Namnet får suffixet ”asr”. Om en resursgrupp redan finns som har skapats med Azure Site Recovery återanvänds. Du kan också välja att anpassa den, som visas i följande avsnitt. Platsen för målresursgruppen kan vara alla Azure-regioner utom den region där virtuella källdatorer finns.
    - **Virtuellt målnätverk**: Som standard skapar Site Recovery ett nytt virtuellt nätverk i målregionen. Namnet får suffixet ”asr”. Den har mappats till nätverket källa och används för alla framtida skydd. [Läs mer](site-recovery-network-mapping-azure-to-azure.md) om nätverksmappning.
    - **Mållagringskonton (om källan virtuella datorn inte använder hanterade diskar)**: Som standard skapar Site Recovery ett nytt mållagringskonto genom frihandsbilden lagringskonfigurationen för käll-VM. Om det finns redan ett storage-konto, återanvänds.
    - **Hanterade replikeringsdiskar (om den Virtuella måldatorn använder hanterade diskar)**: Site Recovery skapar nya hanterade replikeringsdiskar i målregionen som speglar den Virtuella källdatorns hanterade diskar med samma lagringstyp (standard eller premium) som den Virtuella källdatorns hanterade diskar.
    - **Cachelagringskonton**: Site Recovery behöver ett konto för extra lagringsutrymme som heter *cachelagra storage* i källregionen. Alla ändringar på virtuella källdatorn spåras och skickas till cachelagringskontot. De är sedan replikeras till målplatsen.
    - **Tillgänglighetsuppsättning**: Som standard skapar Site Recovery en ny tillgänglighetsuppsättning i målregionen. Namnet har suffixet ”asr”. Om en tillgänglighetsuppsättning som har skapats av Site Recovery redan finns, återanvänds.
    - **Nyckelvalv för diskkryptering**: Som standard skapar Site Recovery ett nytt nyckelvalv i målregionen. Den har suffixet ”asr” som baseras på käll-VM för diskkrypteringsnycklarna. Om det finns ett nyckelvalv som redan har skapats med Azure Site Recovery, återanvänds.
    - **Nyckelvalv för nyckelkryptering**: Som standard skapar Site Recovery ett nytt nyckelvalv i målregionen. Namnet har suffixet ”asr” som baseras på källan VM viktiga krypteringsnycklar. Om ett nyckelvalv som skapats av Azure Site Recovery redan finns, återanvänds.
    - **Replikeringsprincip**: Definierar inställningarna för historik för återställningspunkt kvarhållning och ögonblicksbildsfrekvens för appkonsekvent. Som standard skapar Site Recovery en ny replikeringsprincip med standardinställningarna för *24 timmar* peka kvarhållning för återställning och *60 minuter* för frekvensen för programkonsekventa ögonblicksbilder.

## <a name="customize-target-resources"></a>Anpassa målresurser

Följ dessa steg om du vill ändra Site Recovery-standardinställningarna för målet.

1. Välj **anpassa** bredvid ”målprenumerationen” ändra målprenumerationen standard. Välj prenumerationen i listan över prenumerationer som är tillgängliga i Azure AD-klient.

2. Välj **anpassa** bredvid ”resursgrupp, nätverk, lagring och tillgänglighetsuppsättningar” att ändra följande standardinställningar:
    - För **målresursgrupp**, Välj resursgruppen i listan över resursgrupper på målplatsen för prenumerationen.
    - För **virtuellt Målnätverk**, väljer nätverket från en lista över virtuella nätverk på målplatsen.
    - För **tillgänglighetsuppsättning**, du kan lägga till inställningar för tillgänglighetsuppsättningen till den virtuella datorn, om de är en del av en tillgänglighetsuppsättning i källregionen.
    - För **Mållagring konton**, Välj kontot som ska användas.

2. Välj **anpassa** bredvid ”krypteringsinställningar” ändra följande standardinställningar:
   - För **Target disk encryption-nyckelvalv**, Välj mål disk encryption key vault i listan med nyckelvalv på målplatsen för prenumerationen.
   - För **Target nyckelkryptering nyckelvalv**, Välj nyckelvalvet för nyckelkryptering som mål i listan med nyckelvalv på målplatsen för prenumerationen.

3. Välj **skapa målresursen** > **Aktivera replikering**.
4. När de virtuella datorerna har aktiverats för replikering, kan du kontrollera hälsostatus för de virtuella datorer under **replikerade objekt**.

>[!NOTE]
>Under den inledande replikeringen kan status ta lite tid att uppdatera utan att tydligt pågår. Klicka på **uppdatera** att hämta senaste status.

## <a name="update-target-vm-encryption-settings"></a>Uppdatera krypteringsinställningar för mål VM
I följande scenarier, kommer du att behöva uppdatera krypteringsinställningar för mål VM:
  - Du har aktiverat Site Recovery-replikering på den virtuella datorn. Senare kan aktivera du diskkryptering på den Virtuella källdatorn.
  - Du har aktiverat Site Recovery-replikering på den virtuella datorn. Senare kan ändrat du disk krypteringsnyckeln eller nyckelkrypteringsnyckel på den Virtuella källdatorn.

Du kan använda [ett skript](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) att kopiera dem till målregion och sedan uppdatera krypteringsinställningar mål i **Recovery services-valv** > *replikerat objekt*  >  **Egenskaper** > **beräkning och nätverk**.

![Uppdatera ADE inställningar dialogruta](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Felsöka nyckelvalv behörighetsproblem vid replikering av virtuella datorer i Azure till Azure

**Orsak 1:** Du kan ha valt från målregionen ett redan skapat nyckelvalv som inte har behörigheterna som krävs i stället för att låta Site Recovery skapa ett. Kontrollera att nyckelvalvet har den kräver behörigheter, enligt beskrivningen ovan.

*Till exempel*: Försök att replikera en virtuell dator som har nyckelvalvet *ContososourceKeyvault* på en källregionen.
Du har alla behörigheter för källnyckelvalvet-region. Men vid skydd, väljer du redan skapat nyckelvalvet ContosotargetKeyvault som inte har behörighet. Ett fel inträffar.

**Så här åtgärdar du:** Gå till **Start** > **Keyvaults** > **ContososourceKeyvault** > **åtkomstprinciper** och Lägg till de behörigheter som krävs.

**Orsak 2:** Du kan ha valt från målregionen ett redan skapat nyckelvalv som inte har kryptera behörigheter i stället för att låta Site Recovery skapa en dekryptering. Se till att du har Kryptera dekryptera behörigheter om du krypterar även nyckeln i källregionen.</br>

*Till exempel*: Försök att replikera en virtuell dator som har ett nyckelvalv *ContososourceKeyvault* i källregionen. Du har nödvändig behörighet på källnyckelvalvet-region. Men vid skydd, väljer du redan skapat nyckelvalvet ContosotargetKeyvault som inte har behörighet att dekryptera och kryptera. Ett fel inträffar.</br>

**Så här åtgärdar du:** Gå till **Start** > **Keyvaults** > **ContososourceKeyvault** > **åtkomstprinciper**. Lägg till behörigheter under **Nyckelbehörigheter** > **kryptografiska åtgärder**.

## <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om hur du kör ett redundanstest.
