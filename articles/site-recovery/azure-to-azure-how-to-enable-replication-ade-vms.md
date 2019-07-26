---
title: Konfigurera replikering för Azure Disk Encryption-aktiverade virtuella datorer i Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar replikering för Azure Disk Encryption-aktiverade virtuella datorer från en Azure-region till en annan med hjälp av Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: b000610b5cba6f768a629ad797500a57597f2569
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335690"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replikera Azure Disk Encryption-aktiverade virtuella datorer till en annan Azure-region

Den här artikeln beskriver hur du replikerar Azure Disk Encryption-aktiverade virtuella datorer från en Azure-region till en annan.

>[!NOTE]
>Azure Site Recovery stöder för närvarande endast virtuella Azure-datorer som kör ett Windows-operativsystem och som är [aktiverat för kryptering med Azure Active Directory (Azure AD)](https://aka.ms/ade-aad-app).

## <a id="required-user-permissions"></a>Nödvändiga användar behörigheter
Site Recovery kräver att användaren har behörighet att skapa nyckel valvet i mål regionen och kopiera nycklar till regionen.

För att aktivera replikering av disk kryptering – aktiverade virtuella datorer från den Azure Portal behöver användaren följande behörigheter:

- Nyckel valvs behörigheter
    - Lista, skapa och hämta
    
- Hemliga behörigheter för Key Vault
    - Hemlighetshanteringsåtgärder
        - Hämta, lista och ange
    
- Nyckel valv nyckel behörigheter (krävs endast om de virtuella datorerna använder nyckel krypterings nyckel för att kryptera disk krypterings nycklar)
    - Nyckelhanteringsåtgärder
        - Hämta, lista och skapa
    - Kryptografiåtgärder
        - Dekryptera och kryptera

Om du vill hantera behörigheter går du till Key Vault-resursen i portalen. Lägg till de behörigheter som krävs för användaren. I följande exempel visas hur du aktiverar behörigheter till Key Vault- *ContosoWeb2Keyvault*, som finns i käll regionen.

1. Gåtill **Start** > - > **ContosoWeb2KeyVault > åtkomst principer**.

   ![Fönstret behörigheter för nyckel valv](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Du kan se att det inte finns några användar behörigheter. Välj **Lägg till ny**. Ange användar-och behörighets information.

   ![Nyckel valvs behörigheter](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Om användaren som aktiverar haveri beredskap (DR) inte har behörighet att kopiera nycklarna, kan en säkerhets administratör som har rätt behörighet använda följande skript för att kopiera krypterings hemligheter och nycklar till mål regionen.

Information om hur du felsöker behörigheter finns i [behörighets problem i Key Vault](#trusted-root-certificates-error-code-151066) senare i den här artikeln.

>[!NOTE]
>Om du vill aktivera replikering av disk kryptering – aktiverade virtuella datorer från portalen behöver du minst "lista" behörigheter för nyckel valv, hemligheter och nycklar.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Kopiera disk krypterings nycklar till DR-regionen med hjälp av PowerShell-skriptet

1. [Öppna den råa skript koden "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Kopiera skriptet till en fil och ge det namnet **copy-Keys. ps1**.
3. Öppna Windows PowerShell-programmet och gå till mappen där du sparade filen.
4. Kör Copy-Keys. ps1.
5. Ange Azure-autentiseringsuppgifter för att logga in.
6. Välj **Azure** -prenumerationen för dina virtuella datorer.
7. Vänta tills resurs grupperna har lästs in och välj sedan **resurs gruppen** för dina virtuella datorer.
8. Välj de virtuella datorerna i listan som visas. Endast virtuella datorer som har Aktiver ATS för disk kryptering finns i listan.
9. Välj **mål platsen**.

    - **Nyckel valv för disk kryptering**
    - **Nyckel valv för nyckel kryptering**

   Som standard skapar Site Recovery ett nytt nyckel valv i mål regionen. Valvets namn har ett "ASR"-suffix som baseras på den virtuella käll diskens disk krypterings nycklar. Om det redan finns ett nyckel valv som har skapats av Site Recovery används det igen. Välj ett annat nyckel valv i listan om det behövs.

## <a name="enable-replication"></a>Aktivera replikering

I det här exemplet är den primära Azure-regionen Asien, östra och den sekundära regionen Asien, sydöstra.

1. I valvet väljer du **+ Replikera**.
2. Observera följande fält.
    - **Källa**: Ursprungs platsen för de virtuella datorerna, som i det här fallet är **Azure**.
    - **Käll plats**: Den Azure-region där du vill skydda dina virtuella datorer. I det här exemplet är käll platsen "Asien, östra".
    - **Distributions modell**: Käll datorernas distributions modell för Azure.
    - **Käll prenumeration**: Den prenumeration som dina virtuella käll datorer tillhör. Det kan vara vilken prenumeration som helst i samma Azure Active Directory klient organisation som Recovery Services-valvet.
    - **Resursgrupp**: Resurs gruppen som de virtuella käll datorerna tillhör. Alla virtuella datorer i den valda resurs gruppen visas som skydd i nästa steg.

3. I **Virtual Machines** > **Välj virtuella datorer**väljer du varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Välj **OK**.

4. I **Inställningar**kan du konfigurera följande inställningar för mål platsen.

    - **Målplats**: Den plats där data för den virtuella käll datorn kommer att replikeras. Site Recovery innehåller en lista över lämpliga mål regioner baserade på den valda datorns plats. Vi rekommenderar att du använder samma plats som Recovery Services valvets plats.
    - **Målprenumeration**: Mål prenumerationen som används för haveri beredskap. Som standard är mål prenumerationen densamma som käll prenumerationen.
    - **Målresursgrupp**: Resurs gruppen som alla dina replikerade virtuella datorer tillhör. Som standard skapar Site Recovery en ny resurs grupp i mål regionen. Namnet får suffixet "ASR". Om det redan finns en resurs grupp som har skapats av Azure Site Recovery används den igen. Du kan också välja att anpassa den, som du ser i följande avsnitt. Platsen för mål resurs gruppen kan vara valfri Azure-region förutom den region där de virtuella käll datorerna finns.
    - **Virtuellt målnätverk**: Som standard skapar Site Recovery ett nytt virtuellt nätverk i mål regionen. Namnet får suffixet "ASR". Den är mappad till ditt käll nätverk och används för framtida skydd. [Läs mer](site-recovery-network-mapping-azure-to-azure.md) om nätverks mappning.
    - **Mål lagrings konton (om den virtuella käll datorn inte använder hanterade diskar)** : Som standard skapar Site Recovery ett nytt mål lagrings konto genom att mimicking för din käll-VM-lagring. Om det redan finns ett lagrings konto används det igen.
    - **Hanterade replik diskar (om den virtuella käll datorn använder hanterade diskar)** : Site Recovery skapar nya replik hanterade diskar i mål regionen för att spegla den virtuella käll datorns hanterade diskar av samma lagrings typ (standard eller Premium) som den virtuella käll datorns hanterade diskar.
    - **Cachelagringskonton**: Site Recovery behöver ett extra lagrings konto som heter *cache Storage* i käll regionen. Alla ändringar på de virtuella käll datorerna spåras och skickas till cache Storage-kontot. De replikeras sedan till mål platsen.
    - **Tillgänglighetsuppsättning**: Som standard skapar Site Recovery en ny tillgänglighets uppsättning i mål regionen. Namnet har suffixet "ASR". Om en tillgänglighets uppsättning som skapats av Site Recovery redan finns återanvänds den.
    - **Nyckelvalv för diskkryptering**: Som standard skapar Site Recovery ett nytt nyckel valv i mål regionen. Den har ett "ASR"-suffix som baseras på den virtuella käll diskens disk krypterings nycklar. Om det redan finns ett nyckel valv som skapats av Azure Site Recovery, används det igen.
    - **Nyckelvalv för nyckelkryptering**: Som standard skapar Site Recovery ett nytt nyckel valv i mål regionen. Namnet har ett "ASR"-suffix som baseras på de virtuella käll dator nyckel krypterings nycklarna. Om ett nyckel valv som skapats av Azure Site Recovery redan finns återanvänds det.
    - **Replikeringsprincip**: Definierar inställningarna för kvarhållning av återställnings punkts historik och frekvens för programkonsekventa ögonblicks bilder. Som standard skapar Site Recovery en ny replikeringsprincip med standardinställningar *24 timmar* för kvarhållning av återställnings punkter och *60 minuter* för programkonsekventa ögonblicks bild frekvens.

## <a name="customize-target-resources"></a>Anpassa mål resurser

Följ dessa steg om du vill ändra Site Recovery inställningarna för standard mål.

1. Välj **Anpassa** bredvid "mål prenumeration" om du vill ändra standard mål prenumerationen. Välj prenumerationen i listan över prenumerationer som är tillgängliga i Azure AD-klienten.

2. Välj **Anpassa** bredvid "resurs grupp, nätverk, lagring och tillgänglighets uppsättningar" för att ändra följande standardinställningar:
    - För **mål resurs grupp**väljer du resurs gruppen i listan över resurs grupper på mål platsen för prenumerationen.
    - För **virtuellt mål nätverk**väljer du nätverket från en lista över virtuella nätverk på mål platsen.
    - För **tillgänglighets uppsättning**kan du lägga till inställningar för tillgänglighets uppsättningar på den virtuella datorn, om de är en del av en tillgänglighets uppsättning i käll regionen.
    - För **mål lagrings konton**väljer du det konto som ska användas.

2. Ändra följande standardinställningar genom att välja **Anpassa** bredvid "krypterings inställningar":
   - För **mål diskens krypterings nyckel valv**väljer du mål diskens krypterings nyckel valv i listan över nyckel valv i prenumerationens mål plats.
   - Välj nyckel valv för nyckel valv i listan över nyckel valv på mål platsen för prenumerationen för nyckel **valv för mål nyckel**.

3. Välj **skapa mål resurs** > **Aktivera replikering**.
4. När de virtuella datorerna har Aktiver ATS för replikering kan du kontrol lera de virtuella datorernas hälso status under **replikerade objekt**.

>[!NOTE]
>Under den inledande replikeringen kan statusen ta lite tid att uppdatera, utan att det är uppenbart förloppet. Klicka på **Uppdatera** för att hämta den senaste statusen.

## <a name="update-target-vm-encryption-settings"></a>Uppdatera mål VM-krypterings inställningar
I följande fall måste du uppdatera den virtuella mål datorns krypterings inställningar:
  - Du har aktiverat Site Recovery replikering på den virtuella datorn. Senare har du aktiverat disk kryptering på den virtuella käll datorn.
  - Du har aktiverat Site Recovery replikering på den virtuella datorn. Senare ändrades disk krypterings nyckeln eller nyckel krypterings nyckeln på den virtuella käll datorn.

Du kan använda [ett skript](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) för att kopiera krypterings nycklarna till mål regionen och sedan uppdatera inställningarna för mål kryptering i **Recovery Services-valv** > *replikerade objekt* > **Egenskaper**  >  **Beräkning och nätverk**.

![Dialog rutan Uppdatera inställningar för ADE-fönstret](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Felsök problem med Key Vault-behörighet under Azure-till-Azure VM-replikering

Azure Site Recovery kräver minst Läs behörighet för käll områdets nyckel valv och Skriv behörighet på mål region nyckel valvet för att läsa hemligheten och kopiera den till mål region nyckel valvet. 

**Orsak 1:** Du har inte behörigheten "GET" för **käll region nyckel valvet** för att läsa nycklarna. </br>
**Så här åtgärdar du:** Oavsett om du är prenumerations administratör eller inte, är det viktigt att du har behörighet för nyckel valvet.

1. Gå till nyckel valvet för käll regionen som i det här exemplet är "ContososourceKeyvault" > **åtkomst principer** 
2. Under **Välj huvud** namn lägger du till ditt användar namn tilldradmin@contoso.comexempel: ""
3. Under **nyckel behörigheter** väljer du hämta 
4. Under **hemlig behörighet** väljer du hämta 
5. Spara åtkomst principen

**Orsak 2:** Du har inte den behörighet som krävs för **mål region nyckel valvet** för att skriva nycklarna. </br>

*Till exempel*: Du försöker replikera en virtuell dator som har Key Vault- *ContososourceKeyvault* i en käll region.
Du har alla behörigheter för käll region nyckel valvet. Men under skyddet väljer du det redan skapade Key Vault-ContosotargetKeyvault, som inte har behörighet. Ett fel uppstår.

Behörighet krävs för [mål nyckel valvet](#required-user-permissions)

**Så här åtgärdar du:** Gå till **Start** > -ContosotargetKeyvault > **åtkomst principer** och Lägg till lämpliga behörigheter. > 

## <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om att köra ett redundanstest.
