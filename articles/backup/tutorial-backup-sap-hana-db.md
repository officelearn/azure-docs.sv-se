---
title: Självstudie – säkerhetskopiera SAP HANA databaser i virtuella Azure-datorer
description: I den här självstudien lär du dig att säkerhetskopiera SAP HANA databaser som körs på virtuella Azure-datorer till ett Azure Backup Recovery Services-valv.
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: bb84f6b362adf7c190f3300e6e3f1bc572153151
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75753986"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Självstudie: säkerhetskopiera SAP HANA databaser på en virtuell Azure-dator

Den här självstudien visar hur du säkerhetskopierar SAP HANA databaser som körs på virtuella Azure-datorer till ett Azure Backup Recovery Services-valv. I den här artikeln får du lära dig att:

> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv
> * Identifiera databaser
> * Konfigurera säkerhets kopior

[Här](sap-hana-backup-support-matrix.md#scenario-support) följer alla scenarier som vi för närvarande stöder.

## <a name="onboard-to-the-public-preview"></a>Publicera till den offentliga för hands versionen

Publicera till den offentliga för hands versionen enligt följande:

* I portalen registrerar du ditt prenumerations-ID till Recovery Services tjänst leverantören genom att [följa den här artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).

* För PowerShell kör du denna cmdlet. Den bör slutföras som "registrerad".

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="prerequisites"></a>Krav

Kontrol lera att du gör följande innan du konfigurerar säkerhets kopieringar:

1. Installera och aktivera ODBC-drivrutinshanteraren från det officiella SLES-paketet/mediet med zypper på den virtuella datorn som kör SAP HANA-databasen, enligt följande:

```bash
sudo zypper update
sudo zypper install unixODBC
```

>[!NOTE]
> Om du inte uppdaterar databaserna kontrollerar du att versionen av unixODBC är minst 2.3.4. Om du vill veta vilken version av uniXODBC kör du `odbcinst -j` som rot
>

2. Tillåt anslutning från den virtuella datorn till Internet, så att den kan komma åt Azure, enligt beskrivningen i [proceduren nedan](#set-up-network-connectivity).

3. Kör skriptet för för registrering på den virtuella datorn där HANA är installerat som en rot användare. [Det här skriptet](https://aka.ms/scriptforpermsonhana) kommer att ange [rätt behörigheter](#setting-up-permissions).

## <a name="set-up-network-connectivity"></a>Konfigurera nätverks anslutning

För alla åtgärder kräver den virtuella datorn för SAP HANA anslutning till offentliga Azure-IP-adresser. VM-åtgärder (databas identifiering, konfigurera säkerhets kopiering, schemalägga säkerhets kopiering, återställning av återställnings punkter osv.) kan inte köras utan anslutning till offentliga Azure-IP-adresser.

Upprätta anslutningar genom att använda något av följande alternativ:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Tillåt IP-intervall för Azure-datacenter

Med det här alternativet tillåts [IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) i den nedladdade filen. Använd cmdleten Set-AzureNetworkSecurityRule för att få åtkomst till en nätverks säkerhets grupp (NSG). Om listan med betrodda mottagare bara innehåller landsspecifika IP-adresser, måste du också uppdatera de säkra mottagarna visar tjänst tag gen Azure Active Directory (Azure AD) för att aktivera autentisering.

### <a name="allow-access-using-nsg-tags"></a>Tillåt åtkomst med NSG-Taggar

Om du använder NSG för att begränsa anslutningen bör du använda AzureBackup service tag för att tillåta utgående åtkomst till Azure Backup. Dessutom bör du även tillåta anslutning för autentisering och data överföring genom att använda [regler](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) för Azure AD och Azure Storage. Detta kan göras från Azure Portal eller via PowerShell.

Så här skapar du en regel med hjälp av portalen:

  1. I **alla tjänster**går du till **nätverks säkerhets grupper** och väljer Nätverks säkerhets gruppen.
  2. Välj **utgående säkerhets regler** under **Inställningar**.
  3. Välj **Lägg till**. Ange all information som krävs för att skapa en ny regel enligt beskrivningen i [säkerhets regel inställningar](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Se till att alternativet **destination** har angetts till **service tag** och **mål tjänst tag gen** är inställt på **AzureBackup**.
  4. Klicka på **Lägg till**för att spara den nyligen skapade utgående säkerhets regeln.

Så här skapar du en regel med hjälp av PowerShell:

 1. Lägg till autentiseringsuppgifter för Azure-kontot och uppdatera de nationella molnen<br/>
      `Add-AzureRmAccount`<br/>

 2. Välj prenumerationen NSG<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Välj NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Lägg till Tillåt utgående regel för Azure Backup service tag<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Lägg till Tillåt utgående regel för lagrings tjänst tag gen<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Lägg till Tillåt utgående regel för AzureActiveDirectory service tag<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Spara NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Tillåt åtkomst med hjälp av Azure Firewall-Taggar**. Om du använder Azure-brandväggen kan du skapa en program regel med hjälp av AzureBackup [FQDN-taggen](https://docs.microsoft.com/azure/firewall/fqdn-tags). Detta tillåter utgående åtkomst till Azure Backup.

**Distribuera en HTTP-proxyserver för att dirigera trafik**. När du säkerhetskopierar en SAP HANA-databas på en virtuell Azure-dator använder säkerhets kopierings tillägget på den virtuella datorn HTTPS-API: er för att skicka hanterings kommandon till Azure Backup och data till Azure Storage. Säkerhets kopierings tillägget använder också Azure AD för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Tilläggen är den enda komponenten som är konfigurerad för åtkomst till det offentliga Internet.

Anslutnings alternativen omfattar följande fördelar och nack delar:

**Alternativ** | **Fördelar** | **Nackdelar**
--- | --- | ---
Tillåta IP-intervall | Inga ytterligare kostnader | Komplext att hantera eftersom IP-adressintervall ändras med tiden <br/><br/> Ger åtkomst till hela Azure, inte bara Azure Storage
Använda NSG service-Taggar | Enklare att hantera när intervall ändringar slås samman automatiskt <br/><br/> Inga ytterligare kostnader <br/><br/> | Kan endast användas med NSG: er <br/><br/> Ger åtkomst till hela tjänsten
Använd Azure Firewall FQDN-Taggar | Enklare att hantera eftersom nödvändiga FQDN-namn hanteras automatiskt | Kan endast användas med Azure brand vägg
Använda en HTTP-proxy | Detaljerad kontroll i proxyn över lagrings-URL: er tillåts <br/><br/> En enda punkt i Internet åtkomst till virtuella datorer <br/><br/> Ändringar i Azure IP-adress ingår inte | Ytterligare kostnader för att köra en virtuell dator med proxy-programvaran

## <a name="setting-up-permissions"></a>Konfigurera behörigheter

Skriptet för för registrering utför följande åtgärder:

1. Skapar AZUREWLBACKUPHANAUSER i HANA-systemet och lägger till de nödvändiga rollerna och behörigheterna:
   * DATABAS administratör: för att skapa nya databaser under återställningen.
   * Katalog läsning: för att läsa säkerhets kopierings katalogen.
   * SAP_INTERNAL_HANA_SUPPORT: för att få åtkomst till några privata tabeller.
2. Lägger till en nyckel till Hdbuserstore för HANA-plugin-programmet för att hantera alla åtgärder (databas frågor, återställnings åtgärder, konfigurera och köra säkerhets kopiering).

Bekräfta att nyckeln skapas genom att köra kommandot HDBSQL på datorn HANA med SIDADM-autentiseringsuppgifter:

```hdbsql
hdbuserstore list
```

Kommandots utdata ska Visa nyckeln {SID} {DBNAME}, där användaren visas som AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Se till att du har en unik uppsättning SSFS-filer under/usr/sap/{SID}/home/.hdb/. Det får bara finnas en mapp i den här sökvägen.
>

## <a name="create-a-recovery-service-vault"></a>Skapa ett Recovery Service-valv

Ett Recovery Services-valv är en entitet som lagrar säkerhets kopior och återställnings punkter som skapats med tiden. Recovery Services valvet innehåller också de säkerhets kopierings principer som är associerade med de skyddade virtuella datorerna.

Så här skapar du ett Recovery Services-valv:

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

2. Välj **alla tjänster** på den vänstra menyn

![Välj Alla tjänster](./media/tutorial-backup-sap-hana-db/all-services.png)

3. I dialogrutan **Alla tjänster** anger du **Recovery Services**. Listan över resurser filtreras enligt dina inaktuella inaktuella. Välj **Recovery Services valv**i listan över resurser.

![Välj Recovery Services valv](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. På instrument panelen för **Recovery Services** valv väljer du **Lägg till**.

![Lägg till Recovery Services valv](./media/tutorial-backup-sap-hana-db/add-vault.png)

Dialog rutan **Recovery Services valv** öppnas. Ange värden för **namn, prenumeration, resurs grupp** och **plats**

![Skapa Recovery Services-valv](./media/tutorial-backup-sap-hana-db/create-vault.png)

* **Namn**: namnet används för att identifiera Recovery Services-valvet och måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller minst två, men högst 50 tecken. Namnet måste börja med en bokstav och får bara bestå av bokstäver, siffror och bindestreck. I den här självstudien har vi använt namnet **SAPHanaVault**.
* **Prenumeration**: Välj den prenumeration som ska användas. Om du är medlem i endast en prenumeration ser du det namnet. Om du inte är säker på vilken prenumeration du ska använda använder du standard prenumerationen (rekommenderas). Det finns flera alternativ bara om ditt arbets-eller skol konto är associerat med fler än en Azure-prenumeration. Här har vi använt prenumerations prenumerationen för **SAP HANA Solution Lab** .
* **Resurs grupp**: Använd en befintlig resurs grupp eller skapa en ny. Här har vi använt **SAPHANADemo**.<br>
Om du vill se en lista över tillgängliga resurs grupper i din prenumeration väljer du **Använd befintlig**och väljer sedan en resurs i list rutan. Om du vill skapa en ny resurs grupp väljer du **Skapa ny** och anger namnet. Fullständig information om resurs grupper finns i [Azure Resource Manager översikt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
* **Plats**: Välj det geografiska området för valvet. Valvet måste finnas i samma region som den virtuella datorn som kör SAP HANA. Vi har använt **USA, östra 2**.

5. Välj **Granska + Skapa**.

   ![Välj granska & skapa](./media/tutorial-backup-sap-hana-db/review-create.png)

Recovery Services-valvet har nu skapats.

## <a name="discover-the-databases"></a>Identifiera databaserna

1. Klicka på **säkerhetskopiera**i **komma igång**i valvet. I **var kör din arbets belastning?** väljer du **SAP HANA i virtuell Azure-dator**.
2. Klicka på **Starta identifiering**. Detta initierar identifiering av oskyddade virtuella Linux-datorer i valv regionen. Du kommer att se den virtuella Azure-dator som du vill skydda.
3. I **välj Virtual Machines**klickar du på länken för att hämta skriptet som ger behörighet till tjänsten Azure Backup för att få åtkomst till de SAP HANA virtuella datorerna för databas identifiering.
4. Kör skriptet på den virtuella dator som är värd för SAP HANA databas (er) som du vill säkerhetskopiera.
5. När du har kört skriptet på den virtuella datorn väljer du den virtuella datorn i **välj Virtual Machines**. Klicka sedan på **identifiera databaser**.
6. Azure Backup identifierar alla SAP HANA-databaser på den virtuella datorn. Under identifieringen registrerar Azure Backup den virtuella datorn med valvet och installerar ett tillägg på den virtuella datorn. Ingen agent är installerad på databasen.

   ![Identifiera databaserna](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Nu när de databaser som vi vill säkerhetskopiera identifieras är det dags att aktivera säkerhets kopiering.

1. Klicka på **Konfigurera säkerhets kopiering**.

![Konfigurera säkerhetskopiering](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. I **Välj objekt att säkerhetskopiera**väljer du en eller flera databaser som du vill skydda och klickar sedan på **OK**.

![Välj objekt som ska säkerhets kopie ras](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. I **säkerhets kopierings policy > väljer du säkerhets kopierings princip**, skapar en ny säkerhets kopierings princip för databaserna, i enlighet med anvisningarna i nästa avsnitt.

![Välj säkerhetskopieringsprincip](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. När du har skapat principen klickar du på **Aktivera säkerhets kopiering**på **menyn säkerhets kopiering**.

   ![Klicka på Aktivera säkerhets kopiering](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Spåra förloppet för säkerhets kopierings konfigurationen i området **meddelanden** i portalen.

## <a name="creating-a-backup-policy"></a>Skapa en säkerhets kopierings princip

En säkerhets kopierings princip definierar när säkerhets kopieringar görs och hur länge de ska behållas.

* En policy skapas på valvnivå.
* Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.

Ange princip inställningarna enligt följande:

1. I **Policynamn** anger du ett namn för den nya policyn. I det här fallet anger du **SAPHANA**.

![Ange namn för ny princip](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Välj en **säkerhets kopierings frekvens**i **fullständig säkerhets kopierings princip**. Du kan välja **varje dag** eller **varje vecka**. I den här självstudien valde vi den **dagliga** säkerhets kopieringen.

![Välj en säkerhets kopierings frekvens](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Konfigurera inställningar för kvarhållning för fullständig säkerhets kopiering i **kvarhållningsintervall**.
   * Som standard är alla alternativ markerade. Rensa eventuella gränser för kvarhållningsintervall som du inte vill använda och ange de som du gör.
   * Den minsta Retentions perioden för någon typ av säkerhets kopiering (fullständig/differentiell/log) är sju dagar.
   * Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
   * Säkerhets kopian för en angiven dag märks och bevaras baserat på vecko kvarhållningsintervallet och-inställningen.
   * De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.
4. I menyn **fullständig säkerhets kopierings policy** klickar du på **OK** för att godkänna inställningarna.
5. Välj sedan **differentiell säkerhets kopiering** för att lägga till en differentiell princip.
6. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning. Vi har aktiverat en differentiell säkerhets kopia varje **söndag** kl. **2:00**, som sparas i **30 dagar**.

   ![Princip för differentiell säkerhets kopiering](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

>[!NOTE]
>Stegvisa säkerhets kopieringar stöds inte för närvarande.
>

7. Klicka på **OK** för att spara principen och återgå till huvud menyn för **säkerhets kopierings policyn** .
8. Välj **logg säkerhets kopiering** för att lägga till en transaktions logg princip för säkerhets kopiering
   * **Logg säkerhets kopiering** är som standard inställt på **aktive**rad. Detta kan inte inaktive ras eftersom SAP HANA hanterar alla logg säkerhets kopior.
   * Vi har angett **2 timmar** som säkerhets kopierings schema och **15 dagars** kvarhållningsperiod.

    ![Logg säkerhets kopierings princip](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

>[!NOTE]
> Logg säkerhets kopior börjar bara att flyta efter en lyckad fullständig säkerhets kopiering har slutförts.
>

9. Klicka på **OK** för att spara principen och återgå till huvud menyn för **säkerhets kopierings policyn** .
10. När du är klar med att definiera säkerhets kopierings principen klickar du på **OK**.

Du har nu konfigurerat säkerhets kopiering (er) för SAP HANA databas (er).

## <a name="next-steps"></a>Efterföljande moment

* Lär dig hur du [Kör säkerhets kopiering på begäran på SAP HANA databaser som körs på virtuella Azure-datorer](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Lär dig hur du [återställer SAP HANA databaser som körs på virtuella Azure-datorer](sap-hana-db-restore.md)
* Lär dig hur du [hanterar SAP HANA databaser som säkerhets kopie ras med Azure Backup](sap-hana-db-manage.md)
* Lär dig hur du [felsöker vanliga problem när du säkerhetskopierar SAP HANA databaser](backup-azure-sap-hana-database-troubleshoot.md)
