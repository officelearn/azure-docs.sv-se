---
title: Självstudiekurs - Säkerhetskopiera SAP HANA-databaser i virtuella Azure-datorer
description: I den här självstudien kan du läsa om hur du säkerhetskopierar SAP HANA-databaser som körs på Azure VM till ett Azure Backup Recovery Services-valv.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: f64dd74ad0e038c5cad152e20ae2255de03114e3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79501451"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Självstudiekurs: Säkerhetskopiera SAP HANA-databaser i en virtuell Azure-dator

Den här självstudien visar hur du säkerhetskopierar SAP HANA-databaser som körs på virtuella Azure-datorer till ett Azure Backup Recovery Services-valv. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv
> * Upptäck databaser
> * Konfigurera säkerhetskopior

[Här](sap-hana-backup-support-matrix.md#scenario-support) är alla scenarier som vi för närvarande stöder.

## <a name="prerequisites"></a>Krav

Se till att du gör följande innan du konfigurerar säkerhetskopior:

* Tillåt anslutning från den virtuella datorn till internet, så att den kan nå Azure, enligt beskrivningen i proceduren [för nätverksanslutning](#set-up-network-connectivity) nedan.
* En nyckel bör finnas i **hdbuserstore** som uppfyller följande kriterier:
  * Det bör finnas i standard **hdbuserstore**
  * För MDC bör nyckeln peka på SQL-porten för **NAMESERVER**. När det gäller SDC bör det peka på SQL-porten **för INDEXSERVER**
  * Den bör ha autentiseringsuppgifter för att lägga till och ta bort användare
* Kör SAP HANA-konfigurationsskriptet för säkerhetskopiering (skript för förhandsregistrering) på den virtuella datorn där HANA är installerat, som rotanvändare. [Det här skriptet](https://aka.ms/scriptforpermsonhana) gör HANA-systemet redo för säkerhetskopiering. Se avsnittet [Vad förregistreringsskriptet gör](#what-the-pre-registration-script-does) för att förstå mer om skriptet för förhandsregistrering.

## <a name="set-up-network-connectivity"></a>Konfigurera nätverksanslutning

För alla åtgärder kräver SAP HANA-vm-anslutningen till offentliga Azure-IP-adresser. VM-åtgärder (databasidentifiering, konfigurera säkerhetskopior, schemalägga säkerhetskopior, återställa återställningspunkter och så vidare) misslyckas utan anslutning till offentliga Azure-IP-adresser.

Upprätta anslutning med något av följande alternativ:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Tillåt IP-intervall för Azure-datacenter

Med det här alternativet kan [IP-intervallen](https://www.microsoft.com/download/details.aspx?id=41653) i den hämtade filen. Om du vill komma åt en nätverkssäkerhetsgrupp (NSG) använder du cmdleten Set-AzureNetworkSecurityRule. Om listan med betrodda mottagare endast innehåller regionspecifika IPs måste du också uppdatera listan över betrodda mottagare i Azure Active Directory (Azure AD) för att aktivera autentisering.

### <a name="allow-access-using-nsg-tags"></a>Tillåt åtkomst med NSG-taggar

Om du använder NSG för att begränsa anslutningen bör du använda AzureBackup-tjänsttaggen för att ge utgående åtkomst till Azure Backup. Dessutom bör du också tillåta anslutning för autentisering och dataöverföring med hjälp av [regler](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) för Azure AD och Azure Storage. Detta kan göras från Azure-portalen eller via PowerShell.

Så här skapar du en regel med hjälp av portalen:

  1. Gå till **Nätverkssäkerhetsgrupper** i **Alla tjänster**och välj nätverkssäkerhetsgruppen.
  2. Välj **Utgående säkerhetsregler** under **Inställningar**.
  3. Välj **Lägg till**. Ange alla nödvändiga detaljer för att skapa en ny regel enligt beskrivningen i [säkerhetsregelinställningar .](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings) Kontrollera att alternativet **Mål** är inställt på **Service Tag** och **måltjänsttaggen** är inställt på **AzureBackup**.
  4. Klicka på **Lägg till**om du vill spara den nyligen skapade utgående säkerhetsregeln.

Så här skapar du en regel med PowerShell:

 1. Lägga till Azure-kontoautentiseringsuppgifter och uppdatera de nationella molnen<br/>
      `Add-AzureRmAccount`<br/>

 2. Välj NSG-prenumerationen<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Välj NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Lägg till tillåt utgående regel för Azure Backup-tjänsttagg<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Lägg till tillåt utgående regel för lagringstjänsttagg<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Lägg till tillåt utgående regel för AzureActiveDirectory-tjänsttagg<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Spara NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Tillåt åtkomst med hjälp av Azure Firewall-taggar**. Om du använder Azure-brandväggen skapar du en programregel med hjälp av [AzureBackup FQDN-taggen](https://docs.microsoft.com/azure/firewall/fqdn-tags). Detta möjliggör utgående åtkomst till Azure Backup.

**Distribuera en HTTP-proxyserver för att dirigera trafik**. När du säkerhetskopierar en SAP HANA-databas på en Virtuell Azure-dator använder säkerhetskopieringstillägget på den virtuella datorn HTTPS-API:erna för att skicka hanteringskommandon till Azure Backup och data till Azure Storage. Tillägget för säkerhetskopiering använder också Azure AD för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Tilläggen är den enda komponent som är konfigurerad för åtkomst till det offentliga internet.

Anslutningsalternativen omfattar följande fördelar och nackdelar:

**Alternativet** | **Fördelar** | **Nackdelar**
--- | --- | ---
Tillåta IP-intervall | Inga extra kostnader | Komplex att hantera eftersom IP-adressintervallen ändras med tiden <br/><br/> Ger åtkomst till hela Azure, inte bara Azure Storage
Använda NSG-tjänsttaggar | Enklare att hantera när intervalländringar slås samman automatiskt <br/><br/> Inga extra kostnader <br/><br/> | Kan endast användas med NSG <br/><br/> Ger tillgång till hela tjänsten
Använda FQDN-taggar för Azure Firewall | Enklare att hantera eftersom de nödvändiga FQDN:erna hanteras automatiskt | Kan endast användas med Azure-brandväggen
Använda en HTTP-proxy | Detaljerad kontroll i proxyn över lagringsadresserna är tillåten <br/><br/> En enda punkt för internetåtkomst till virtuella datorer <br/><br/> Inte föremål för Azure IP-adressändringar | Ytterligare kostnader för att köra en virtuell dator med proxyprogramvaran

## <a name="what-the-pre-registration-script-does"></a>Vad förregistrering skriptet gör

Om du kör skriptet för förhandsregistrering utförs följande funktioner:

* Den installerar eller uppdaterar alla nödvändiga paket som krävs av Azure Backup-agenten på din distribution.
* Den utför utgående nätverksanslutningskontroller med Azure Backup-servrar och beroende tjänster som Azure Active Directory och Azure Storage.
* Den loggar in i ditt HANA-system med hjälp av användarnyckeln som anges som en del av [förutsättningarna](#prerequisites). Användarnyckeln används för att skapa en säkerhetskopieringsanvändare (AZUREWLBACKUPHANAUSER) i HANA-systemet och användarnyckeln kan tas bort när förregistreringsskriptet har körts.
* AZUREWLBACKUPHANAUSER tilldelas dessa obligatoriska roller och behörigheter:
  * DATABASADMINISTRATÖR (vid MDC) och BACKUP ADMIN (vid SDC): för att skapa nya databaser under återställningen.
  * KATALOG LÄS: för att läsa säkerhetskopieringskatalogen.
  * SAP_INTERNAL_HANA_SUPPORT: för att komma åt några privata bord.
* Skriptet lägger till en nyckel till **hdbuserstore** för AZUREWLBACKUPHANAUSER för hana-säkerhetskopieringen för att hantera alla åtgärder (databasfrågor, återställa åtgärder, konfigurera och köra säkerhetskopiering).

>[!NOTE]
> Du kan uttryckligen skicka användarnyckeln som anges som en del av [förutsättningarna](#prerequisites) som en parameter till skriptet före registrering:`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Om du vill veta vilka andra parametrar skriptet accepterar använder du kommandot`bash msawb-plugin-config-com-sap-hana.sh --help`

Om du vill bekräfta att nyckeln skapas kör du HDBSQL-kommandot på HANA-datorn med SIDADM-autentiseringsuppgifter:

```hdbsql
hdbuserstore list
```

Kommandoutdata ska visa nyckeln {SID}{DBNAME} med användaren som visas som AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Kontrollera att du har en unik uppsättning `/usr/sap/{SID}/home/.hdb/`SSFS-filer under . Det bör bara finnas en mapp i den här sökvägen.

## <a name="create-a-recovery-service-vault"></a>Skapa ett återställningstjänstvalv

Ett Recovery Services-valv är en entitet som lagrar säkerhetskopior och återställningspunkter som skapats över tid. Valvet för återställningstjänster innehåller också de principer för säkerhetskopiering som är associerade med skyddade virtuella datorer.

Så här skapar du ett Recovery Services-valv:

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

2. På den vänstra menyn väljer du **Alla tjänster**

   ![Välj Alla tjänster](./media/tutorial-backup-sap-hana-db/all-services.png)

3. I dialogrutan **Alla tjänster** anger du **Recovery Services**. Listan över resurser filtrerar enligt dina indata. Välj **Recovery Services-valv**i listan över resurser .

   ![Välj Recovery Services-valv](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. På instrumentpanelen **för Återställningstjänster** väljer du **Lägg till**.

   ![Lägg till valvet för återställningstjänster](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Dialogrutan **Återställningstjänster** öppnas. Ange värden för **gruppen Namn, Prenumeration, Resurs** och **Plats**

   ![Skapa Recovery Services-valv](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Namn**: Namnet används för att identifiera återställningstjänsternas valv och måste vara unikt för Azure-prenumerationen. Ange ett namn som har minst två, men inte fler än 50 tecken. Namnet får börja med en bokstav och består endast av bokstäver, siffror och bindestreck. För den här guiden har vi använt namnet **SAPHanaVault**.
   * **Prenumeration**: Välj den prenumeration som ska användas. Om du bara är medlem i en prenumeration visas det namnet. Om du är osäker på vilken prenumeration du ska använda använder du standardprenumerationen (föreslagen). Det finns bara flera alternativ om ditt arbets- eller skolkonto är kopplat till mer än en Azure-prenumeration. Här har vi använt prenumerationen på **SAP HANA-lösningslababonnemanget.**
   * **Resursgrupp**: Använd en befintlig resursgrupp eller skapa en ny. Här har vi använt **SAPHANADemo**.<br>
   Om du vill visa listan över tillgängliga resursgrupper i prenumerationen väljer du **Använd befintlig**och väljer sedan en resurs i listrutan. Om du vill skapa en ny resursgrupp väljer du **Skapa ny** och anger namnet. Fullständig information om resursgrupper finns i [översikt över Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   * **Plats**: Välj det geografiska området för valvet. Valvet måste vara i samma region som den virtuella datorn som kör SAP HANA. Vi har använt **Östra US 2.**

5. Välj **Granska + Skapa**.

   ![Välj granska & skapa](./media/tutorial-backup-sap-hana-db/review-create.png)

Valvet för återställningstjänster har nu skapats.

## <a name="discover-the-databases"></a>Upptäck databaserna

1. Klicka på **Säkerhetskopiering**i Komma **igång**i valvet. I **Var körs din arbetsbelastning?** **SAP HANA in Azure VM**
2. Klicka på **Starta identifiering**. Detta initierar identifiering av oskyddade virtuella Linux-datorer i valvregionen. Du kommer att se den virtuella Azure-datorn som du vill skydda.
3. I **Välj virtuella datorer**klickar du på länken för att hämta skriptet som ger behörighet för Azure Backup-tjänsten för att komma åt SAP HANA-virtuella datorer för databasidentifiering.
4. Kör skriptet på den virtuella datorn som är värd för SAP HANA-databaser som du vill säkerhetskopiera.
5. När du har kört skriptet på den virtuella datorn väljer du den virtuella datorn i **Välj virtuella datorer.** Klicka sedan på **Upptäck DBs**.
6. Azure Backup identifierar alla SAP HANA-databaser på den virtuella datorn. Under identifieringen registrerar Azure Backup den virtuella datorn med valvet och installerar ett tillägg på den virtuella datorn. Ingen agent är installerad i databasen.

   ![Upptäck databaserna](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Nu när de databaser vi vill säkerhetskopiera upptäcks, låt oss aktivera säkerhetskopiering.

1. Klicka på **Konfigurera säkerhetskopiering**.

   ![Konfigurera säkerhetskopiering](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Markera en eller flera databaser som du vill skydda i Markera objekt som **ska säkerhetskopieras**och klicka sedan på **OK**.

   ![Markera objekt som ska säkerhetskopieras](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Skapa en ny principer för säkerhetskopiering för databasen/databaserna i > principer för **säkerhetskopieringsprincip**i enlighet med instruktionerna i nästa avsnitt.

   ![Välj principer för säkerhetskopiering](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. När du har skapat principen klickar du på **Aktivera säkerhetskopiering**på menyn **Säkerhetskopiering**.

   ![Klicka på Aktivera säkerhetskopiering](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Spåra förloppet för säkerhetskopieringskonfigurationen i området **Meddelanden** på portalen.

## <a name="creating-a-backup-policy"></a>Skapa en princip för säkerhetskopiering

En princip för säkerhetskopiering definierar när säkerhetskopieringar görs och hur lång tid de behålls.

* En policy skapas på valvnivå.
* Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.

Ange principinställningarna enligt följande:

1. I **Policynamn** anger du ett namn för den nya policyn. I det här fallet anger du **SAPHANA**.

   ![Ange namn för ny princip](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. I **principen Fullständig säkerhetskopiering**väljer du en **säkerhetskopieringsfrekvens**. Du kan välja **Dagligen** eller **Veckovis**. För den här guiden valde vi den **dagliga** säkerhetskopian.

   ![Välj en frekvensen för säkerhetskopiering](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Konfigurera kvarhållningsinställningar för hela säkerhetskopian i **Kvarhållningsintervall.**
   * Som standard är alla alternativ markerade. Ta bort alla begränsningar för kvarhållningsintervall som du inte vill använda och ange de som du gör.
   * Den minsta kvarhållningsperioden för alla typer av säkerhetskopiering (full/differential/log) är sju dagar.
   * Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
   * Säkerhetskopieringen för en viss dag taggas och behålls baserat på veckolojaliseringsintervallet och inställningen.
   * De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.
4. Klicka på **OK** på **principmenyn Fullständig säkerhetskopiering** för att acceptera inställningarna.
5. Välj sedan **Differentiell säkerhetskopiering** om du vill lägga till en differentiell princip.
6. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning. Vi har aktiverat en differentiell backup varje **söndag** kl **02:00**, som behålls i **30 dagar.**

   ![Policy för differentiell säkerhetskopiering](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Inkrementella säkerhetskopior stöds för närvarande inte.
   >

7. Klicka på **OK** för att spara principen och gå tillbaka till huvudprincipmenyn **För säkerhetskopiering.**
8. Välj **Logga säkerhetskopiering** om du vill lägga till en transaktionsloggsäkerhetsprincip.
   * **Log Backup** är som standard inställd **på Aktivera**. Detta kan inte inaktiveras eftersom SAP HANA hanterar alla loggsäkerhetskopior.
   * Vi har satt **2 timmar** som backup schema och **15 dagars** kvarhållningsperiod.

    ![Logga säkerhetskopieringsprincip](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Loggsäkerhetskopior börjar flöda först när en lyckad fullständig säkerhetskopiering har slutförts.
   >

9. Klicka på **OK** för att spara principen och gå tillbaka till huvudprincipmenyn **För säkerhetskopiering.**
10. När du har definierat säkerhetskopieringsprincipen klickar du på **OK**.

Du har nu konfigurerat säkerhetskopieringar för DINA SAP HANA-databaser.

## <a name="next-steps"></a>Efterföljande moment

* Lär dig hur du [kör säkerhetskopieringar på begäran på SAP HANA-databaser som körs på virtuella Azure-datorer](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Lär dig hur du [återställer SAP HANA-databaser som körs på virtuella Azure-datorer](sap-hana-db-restore.md)
* Lär dig hur du [hanterar SAP HANA-databaser som säkerhetskopieras med Azure Backup](sap-hana-db-manage.md)
* Lär dig hur du [felsöker vanliga problem när du säkerhetskopierar SAP HANA-databaser](backup-azure-sap-hana-database-troubleshoot.md)
