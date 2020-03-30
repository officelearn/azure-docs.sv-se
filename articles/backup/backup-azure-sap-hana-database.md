---
title: Säkerhetskopiera en SAP HANA-databas till Azure med Azure Backup
description: I den här artikeln kan du läsa om hur du säkerhetskopierar en SAP HANA-databas till virtuella Azure-datorer med Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248065"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Säkerhetskopiera SAP HANA-databaser i virtuella Azure-datorer

SAP HANA-databaser är kritiska arbetsbelastningar som kräver ett mål med låg återställningspunkt (RPO) och långsiktig kvarhållning. Du kan säkerhetskopiera SAP HANA-databaser som körs på virtuella Azure-datorer (VMs) med hjälp av [Azure Backup](backup-overview.md).

Den här artikeln visar hur du säkerhetskopierar SAP HANA-databaser som körs på virtuella Azure-datorer till ett Azure Backup Recovery Services-valv.

I den här artikeln får du lära dig hur du:
> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv
> * Upptäck databaser
> * Konfigurera säkerhetskopior
> * Kör ett säkerhetskopieringsjobb på begäran

>[!NOTE]
>**Mjuk borttagning för SQL-server i Azure VM och mjuk borttagning för SAP HANA i Azure VM-arbetsbelastningar** är nu tillgänglig i förhandsversion.<br>
>För att registrera dig för förhandsgranskningen, skriv till oss påAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Krav

Se [förutsättningarna](tutorial-backup-sap-hana-db.md#prerequisites) och [avsnitten Vad förregistrering gör](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) för att konfigurera databasen för säkerhetskopiering.

### <a name="set-up-network-connectivity"></a>Konfigurera nätverksanslutning

För alla åtgärder kräver SAP HANA-vm-anslutningen till offentliga Azure-IP-adresser. VM-åtgärder (databasidentifiering, konfigurera säkerhetskopior, schemalägga säkerhetskopior, återställa återställningspunkter och så vidare) misslyckas utan anslutning till offentliga Azure-IP-adresser.

Upprätta anslutning med något av följande alternativ:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Tillåt IP-intervall för Azure-datacenter

Med det här alternativet kan [IP-intervallen](https://www.microsoft.com/download/details.aspx?id=41653) i den hämtade filen. Om du vill komma åt en nätverkssäkerhetsgrupp (NSG) använder du cmdleten Set-AzureNetworkSecurityRule. Om listan med betrodda mottagare endast innehåller regionspecifika IPs måste du också uppdatera listan över betrodda mottagare i Azure Active Directory (Azure AD) för att aktivera autentisering.

#### <a name="allow-access-using-nsg-tags"></a>Tillåt åtkomst med NSG-taggar

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

#### <a name="private-endpoints"></a>Privata slutpunkter

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Upptäck databaserna

1. Klicka på **Säkerhetskopiering**i Komma **igång**i valvet. I **Var körs din arbetsbelastning?** **SAP HANA in Azure VM**
2. Klicka på **Starta identifiering**. Detta initierar identifiering av oskyddade virtuella Linux-datorer i valvregionen.

   * Efter identifiering visas oskyddade virtuella datorer i portalen, listade efter namn och resursgrupp.
   * Om en virtuell dator inte visas som förväntat kontrollerar du om den redan har säkerhetskopierats i ett valv.
   * Flera virtuella datorer kan ha samma namn men de tillhör olika resursgrupper.

3. I **Välj virtuella datorer**klickar du på länken för att hämta skriptet som ger behörighet för Azure Backup-tjänsten för att komma åt SAP HANA-virtuella datorer för databasidentifiering.
4. Kör skriptet på varje VM som är värd för SAP HANA-databaser som du vill säkerhetskopiera.
5. När du har kört skriptet på de virtuella datorerna väljer du de virtuella datorerna i **Välj virtuella datorer.** Klicka sedan på **Upptäck DBs**.
6. Azure Backup identifierar alla SAP HANA-databaser på den virtuella datorn. Under identifieringen registrerar Azure Backup den virtuella datorn med valvet och installerar ett tillägg på den virtuella datorn. Ingen agent är installerad i databasen.

    ![Upptäck SAP HANA-databaser](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering  

Aktivera nu säkerhetskopiering.

1. I steg 2 klickar du på **Konfigurera säkerhetskopiering**.

    ![Konfigurera säkerhetskopiering](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Markera alla databaser som du vill skydda > **OK**i Markera objekt som **ska säkerhetskopieras**.

    ![Markera objekt som ska säkerhetskopieras](./media/backup-azure-sap-hana-database/select-items.png)
3. Skapa en ny princip för säkerhetskopiering för databaserna i principer för **säkerhetskopieringsprincip** > i säkerhetskopiera**princip.**

    ![Välj principer för säkerhetskopiering](./media/backup-azure-sap-hana-database/backup-policy.png)
4. När du har skapat principen klickar du på **Aktivera säkerhetskopiering**på menyn **Säkerhetskopiering** .

    ![Aktivera säkerhetskopiering](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Spåra förloppet för säkerhetskopieringskonfigurationen i området **Meddelanden** på portalen.

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En princip för säkerhetskopiering definierar när säkerhetskopieringar görs och hur lång tid de behålls.

* En policy skapas på valvnivå.
* Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.

Ange principinställningarna enligt följande:

1. I **Policynamn** anger du ett namn för den nya policyn.

   ![Ange principnamn](./media/backup-azure-sap-hana-database/policy-name.png)
2. I **Policy för fullständig säkerhetskopia** väljer du en **Säkerhetskopieringsfrekvens** och väljer **Dagligen** eller **Varje vecka**.
   * **Dagligen**: Välj den timme och tidszon där säkerhetskopieringsjobbet börjar.
       * Du måste köra en fullständig säkerhetskopia. Du kan inte stänga av det här alternativet.
       * Klicka på **Fullständig säkerhetskopia** för att visa policyn.
       * Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.
   * **Varje vecka**: Välj den dag i veckan, timmen och tidszonen där säkerhetskopieringsjobbet körs.

   ![Välj säkerhetskopieringsfrekvens](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. Konfigurera kvarhållningsinställningar för hela säkerhetskopian i **Kvarhållningsintervall.**
    * Som standard är alla alternativ markerade. Ta bort alla begränsningar för kvarhållningsintervall som du inte vill använda och ange de som du gör.
    * Den minsta kvarhållningsperioden för alla typer av säkerhetskopiering (full/differential/log) är sju dagar.
    * Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    * Säkerhetskopieringen för en viss dag taggas och behålls baserat på veckolojaliseringsintervallet och inställningen.
    * De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.

4. Klicka på **OK** på **principmenyn Fullständig säkerhetskopiering** för att acceptera inställningarna.
5. Välj **Differentiell säkerhetskopiering** om du vill lägga till en differentiell princip.
6. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning.
    * Du kan endast utlösa en differentiell säkerhetskopia per dag.
    * Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

    ![Policy för differentiell säkerhetskopiering](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Inkrementella säkerhetskopior stöds för närvarande inte.

7. Klicka på **OK** för att spara principen och gå tillbaka till huvudprincipmenyn **För säkerhetskopiering.**
8. Välj **Logga säkerhetskopiering** om du vill lägga till en transaktionsloggsäkerhetsprincip.
    * Välj **Aktivera**i **Log Backup**.  Detta kan inte inaktiveras eftersom SAP HANA hanterar alla loggsäkerhetskopior.
    * Ställ in frekvens- och kvarhållningskontrollerna.

    > [!NOTE]
    > Loggsäkerhetskopior börjar flöda först när en lyckad fullständig säkerhetskopiering har slutförts.

9. Klicka på **OK** för att spara principen och gå tillbaka till huvudprincipmenyn **För säkerhetskopiering.**
10. När du har definierat säkerhetskopieringsprincipen klickar du på **OK**.

> [!NOTE]
> Varje loggsäkerhetskopiering är fastkedjad vid den tidigare fullständiga säkerhetskopieringen för att skapa en återställningskedja. Den fullständiga säkerhetskopian behålls tills kvarhållningen av den senaste loggsäkerhetskopiningen har upphört att gälla. Detta kan innebära att hela säkerhetskopian behålls under en extra period för att se till att alla loggar kan återställas. Låt oss anta att användaren har en vecka full backup, daglig differential och 2 timmars loggar. Alla är kvar i 30 dagar. Utom, den veckovis full kanna bli verkligen rensat upp/ utplånat kort efter den nästa full backningen är tillgänglig i.e., efter 30 + 7 dag. Säg, en vecka full backup händer den 16 november. Enligt bevarandepolicyn bör den behållas fram till den 16 december. Den sista loggen backup för denna fullständiga händer innan nästa schemalagda full, den 22 november. Tills den här loggen är tillgänglig fram till 22 december, kan den 16 november full inte tas bort. Så är den 16 november full kvar fram till 22 december.

## <a name="run-an-on-demand-backup"></a>Kör en säkerhetskopiering på begäran

Säkerhetskopior körs i enlighet med principschemat. Du kan köra en säkerhetskopia på begäran enligt följande:

1. Klicka på **Säkerhetsobjekt**på arkivmenyn.
2. I **Säkerhetskopieringsobjekt**väljer du den virtuella datorn som kör SAP HANA-databasen och klickar sedan på **Säkerhetskopiering nu**.
3. I **Säkerhetskopiering nu**använder du kalenderkontrollen för att välja den sista dagen då återställningspunkten ska behållas. Klicka sedan på **OK**.
4. Övervaka portalmeddelandena. Du kan övervaka jobbframsteget i instrumentpanelen för valvet >**pågående** **säkerhetskopieringsjobb** > . Beroende på databasens storlek kan det ta en stund att skapa den första säkerhetskopian.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Kör SAP HANA Studio-säkerhetskopian i en databas med Azure Backup aktiverat

Om du vill göra en lokal säkerhetskopia (med HANA Studio) av en databas som säkerhetskopieras med Azure Backup gör du följande:

1. Vänta tills alla fullständiga säkerhetskopior eller loggsäkerhetskopior för att databasen ska slutföras. Kontrollera statusen i SAP HANA Studio / Cockpit.
2. Inaktivera loggsäkerhetskopior och ställ in säkerhetskopieringskatalogen till filsystemet för relevant databas.
3. Det gör du genom att dubbelklicka på Databasfiltret för**Configuration** > **konfigurationsval** > av **systemdb** > **(logg).**
4. Ange **enable_auto_log_backup** till **Nej**.
5. Ställ in **log_backup_using_backint** på **False**.
6. Gör en fullständig säkerhetskopiering av databasen på begäran.
7. Vänta tills säkerhetskopieringen och katalogsäkerhetskopiorna är klara.
8. Återställ de tidigare inställningarna till de för Azure:
    * Ställ in **enable_auto_log_backup** på **Ja**.
    * Ställ **in log_backup_using_backint** på **True**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer SAP HANA-databaser som körs på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Lär dig hur du [hanterar SAP HANA-databaser som säkerhetskopieras med Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
