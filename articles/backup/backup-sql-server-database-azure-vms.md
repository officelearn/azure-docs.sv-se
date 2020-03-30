---
title: Säkerhetskopiera SQL Server-databaser i virtuella Azure-datorer
description: I den här artikeln kan du läsa om hur du säkerhetskopierar SQL Server-databaser på virtuella Azure-datorer med Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5b10907738feeecbec06669175e82578f2915f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273337"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Säkerhetskopiera SQL Server-databaser i virtuella Azure-datorer

SQL Server-databaser är kritiska arbetsbelastningar som kräver ett mål med låg återställningspunkt (RPO) och långsiktig kvarhållning. Du kan säkerhetskopiera SQL Server-databaser som körs på virtuella Azure-datorer (VMs) med hjälp av [Azure Backup](backup-overview.md).

Den här artikeln visar hur du säkerhetskopierar en SQL Server-databas som körs på en Virtuell Azure-dator till ett Azure Backup Recovery Services-valv.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv.
> * Identifiera databaser och konfigurera säkerhetskopior.
> * Konfigurera automatiskt skydd för databaser.

>[!NOTE]
>**Mjuk borttagning för SQL-server i Azure VM och mjuk borttagning för SAP HANA i Azure VM-arbetsbelastningar** är nu tillgänglig i förhandsversion.<br>
>För att registrera dig för förhandsgranskningen, skriv till oss påAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Krav

Kontrollera följande villkor innan du säkerhetskopierar en SQL Server-databas:

1. Identifiera eller skapa ett [Recovery Services-valv](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) i samma region och prenumeration som den virtuella datorn som är värd för SQL Server-instansen.
2. Kontrollera att den virtuella datorn har [nätverksanslutning](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Kontrollera att SQL Server-databaserna följer [riktlinjerna för databasnamngivning för Azure Backup](#database-naming-guidelines-for-azure-backup).
4. Kontrollera att du inte har några andra säkerhetskopieringslösningar aktiverade för databasen. Inaktivera alla andra SQL Server-säkerhetskopior innan du säkerhetskopierar databasen.

> [!NOTE]
> Du kan aktivera Azure Backup för en Virtuell Azure och även för en SQL Server-databas som körs på den virtuella datorn utan konflikt.

### <a name="establish-network-connectivity"></a>Etablera nätverksanslutning

För alla åtgärder kräver en VIRTUELL SQL Server anslutning till offentliga Azure-IP-adresser. VM-åtgärder (databasidentifiering, konfigurera säkerhetskopior, schemalägga säkerhetskopior, återställa återställningspunkter och så vidare) misslyckas utan anslutning till offentliga Azure-IP-adresser.

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

**Distribuera en HTTP-proxyserver för att dirigera trafik**. När du säkerhetskopierar en SQL Server-databas på en Virtuell Azure-dator använder säkerhetskopieringstillägget på den virtuella datorn HTTPS-API:erna för att skicka hanteringskommandon till Azure Backup och data till Azure Storage. Tillägget för säkerhetskopiering använder också Azure AD för autentisering. Dirigera trafiken för säkerhetskopieringstillägget för dessa tre tjänster via HTTP-proxyn. Det finns inga jokerteckendomäner som används med Azure Backup att lägga till i listan över tillåta för dina proxyregler. Du måste använda de offentliga IP-intervallen för dessa tjänster som tillhandahålls av Azure. Tilläggen är den enda komponent som är konfigurerad för åtkomst till det offentliga internet.

Anslutningsalternativen omfattar följande fördelar och nackdelar:

**Alternativet** | **Fördelar** | **Nackdelar**
--- | --- | ---
Tillåta IP-intervall | Inga extra kostnader | Komplex att hantera eftersom IP-adressintervallen ändras med tiden <br/><br/> Ger åtkomst till hela Azure, inte bara Azure Storage
Använda NSG-tjänsttaggar | Enklare att hantera när intervalländringar slås samman automatiskt <br/><br/> Inga extra kostnader <br/><br/> | Kan endast användas med NSG <br/><br/> Ger tillgång till hela tjänsten
Använda FQDN-taggar för Azure Firewall | Enklare att hantera eftersom de nödvändiga FQDN:erna hanteras automatiskt | Kan endast användas med Azure-brandväggen
Använda en HTTP-proxy | En enda punkt för internetåtkomst till virtuella datorer <br/> | Ytterligare kostnader för att köra en virtuell dator med proxyprogramvaran <br/> Inga publicerade FQDN-adresser, tillåta regler kommer att vara föremål för Azure IP-adressändringar

#### <a name="private-endpoints"></a>Privata slutpunkter

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Riktlinjer för databasnamngivning för Azure Backup

Undvik att använda följande element i databasnamn:

* Avslutande och ledande utrymmen
* Avslutande utropstecken (!)
* Avslutande hakparenteser (])
* Semikolon ';'
* Snedstrecket framåt'/'

Aliasing är tillgängligt för tecken som inte stöds, men vi rekommenderar att du undviker dem. Mer information finns i [Understanding the Table Service Data Model](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN) (Så här fungerar datamodellen för Table Storage).

>[!NOTE]
>Åtgärden **Konfigurera skydd** för databaser med specialtecken som "+" eller "&" i namnet stöds inte. Du kan antingen ändra databasnamnet eller aktivera **Automatiskt skydd**, som kan skydda dessa databaser.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Identifiera SQL Server-databaser

Så här identifierar du databaser som körs på en virtuell dator:

1. I [Azure-portalen](https://portal.azure.com) öppnar du det Recovery Services-valv som du använder för att säkerhetskopiera databasen.

2. I **instrumentpanelen** **för Återställningstjänster** väljer du Säkerhetskopiering .

   ![Välj Säkerhetskopiering för att öppna menyn Säkerhetskopieringsmål](./media/backup-azure-sql-database/open-backup-menu.png)

3. I **Säkerhetskopieringsmål**anger du Var **Azure**körs **din arbetsbelastning?**

4. I **Vad vill du säkerhetskopiera** väljer du **SQL Server på Azure VM**.

    ![Välj SQL Server på Azure VM för säkerhetskopieringen](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. I **Säkerhetskopieringsmål** > **Upptäck DBs i virtuella datorer**väljer du Starta **identifiering** för att söka efter oskyddade virtuella datorer i prenumerationen. Den här sökningen kan ta en stund, beroende på antalet oskyddade virtuella datorer i prenumerationen.

   * Oskyddade virtuella datorer bör visas i listan efter identifiering, sorterade efter namn och resursgrupp.
   * Om en virtuell dator inte visas som förväntat kan du se om den redan har säkerhetskopierats i ett valv.
   * Flera virtuella datorer kan ha samma namn, men de tillhör olika resursgrupper.

     ![Säkerhetskopieringen väntar under sökningen efter databaser på virtuella datorer](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. I listan över virtuella datorer väljer du den virtuella dator som kör SQL Server-databasen > **Identifiera databaser**.

7. Spåra databasidentifiering i **Meddelanden**. Hur länge som krävs för den här åtgärden beror på antalet VM-databaser. När de valda databaserna identifieras visas ett meddelande.

    ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup identifierar alla SQL Server-databaser på den virtuella datorn. Under identifieringen inträffar följande element i bakgrunden:

    * Azure Backup registrerar den virtuella datorn med valvet för säkerhetskopiering av arbetsbelastning. Alla databaser på den registrerade virtuella datorn kan endast säkerhetskopieras till det här valvet.
    * Azure Backup installerar tillägget AzureBackupWindowsWorkload på den virtuella datorn. Ingen agent är installerad i en SQL-databas.
    * Azure Backup skapar tjänstkontot NT Service\AzureWLBackupPluginSvc på den virtuella datorn.
      * Alla åtgärder för säkerhetskopiering och återställning använder tjänstkontot.
      * NT-tjänst\AzureWLBackupPluginSvc kräver SQL-systemadminbehörigheter. Alla virtuella SQL Server-datorer som skapas på Marketplace levereras med SqlIaaSExtension installerat. Tillägget AzureBackupWindowsWorkload använder SQLIaaSExtension för att automatiskt hämta de behörigheter som krävs.
    * Om du inte skapade den virtuella datorn från Marketplace eller om du är på SQL 2008 och 2008 R2, kanske den virtuella datorn inte har SqlIaaSExtension installerat och identifieringsåtgärden misslyckas med felmeddelandet UserErrorSQLNoSysAdminMembership. Lös problemet genom att följa instruktionerna under [Ange vm-behörigheter](backup-azure-sql-database.md#set-vm-permissions).

        ![Välj den virtuella datorn och databasen](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering  

1. I > **målsteg 2 för säkerhetskopiering: Konfigurera säkerhetskopiering**väljer du **Konfigurera säkerhetskopiering**. **Backup Goal**

   ![Välj Konfigurera säkerhetskopiering](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. I **Välj objekt som ska säkerhetskopieras**visas alla grupper för registrerad tillgänglighet och fristående SQL Server-instanser. Markera pilen till vänster om en rad om du vill expandera listan över alla oskyddade databaser i den instansen eller gruppen Alltid på tillgänglighet.  

    ![Visa alla SQL Server-instanser med fristående databaser](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Välj alla databaser som du vill skydda och välj sedan **OK**.

   ![Skydda databasen](./media/backup-azure-sql-database/select-database-to-protect.png)

   För att optimera säkerhetskopieringsbelastningar anger Azure Backup ett maximalt antal databaser i ett säkerhetskopieringsjobb till 50.

     * Om du vill skydda fler än 50 databaser konfigurerar du flera säkerhetskopieringar.
     * Om du vill [aktivera](#enable-auto-protection) hela instansen eller gruppen Alltid på tillgänglighet väljer du **PÅ**i listrutan **AUTOPROTECT** och väljer SEDAN **OK**.

    > [!NOTE]
    > Funktionen [för automatiskt skydd](#enable-auto-protection) möjliggör inte bara skydd för alla befintliga databaser samtidigt, utan skyddar också automatiskt alla nya databaser som läggs till i den instansen eller tillgänglighetsgruppen.  

4. Välj **OK** för att öppna **principer för säkerhetskopiering**.

    ![Aktivera automatiskt skydd för gruppen Alltid på tillgänglighet](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Välj en princip i **principen Säkerhetskopiering**och välj sedan **OK**.

   * Välj standardprincipen som HourlyLogBackup.
   * Välj en befintlig säkerhetskopieringspolicy som har skapats för SQL.
   * Definiera en ny policy baserat på ditt RPO och kvarhållningsintervall.

     ![Välja säkerhetskopieringspolicy](./media/backup-azure-sql-database/select-backup-policy.png)

6. Välj Aktivera **säkerhetskopiering**i **Säkerhetskopiering**.

    ![Aktivera den valda säkerhetskopieringspolicyn](./media/backup-azure-sql-database/enable-backup-button.png)

7. Spåra konfigurationsförloppet i **meddelandefältet** på portalen.

    ![Meddelandefältet](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En säkerhetskopieringspolicy definierar när säkerhetskopior skapas och hur länge de behålls.

* En policy skapas på valvnivå.
* Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.
* När du skapar en säkerhetskopieringspolicy används en daglig fullständig säkerhetskopia som standard.
* Du kan lägga till en differentiell säkerhetskopia, men endast om du konfigurerar så att fullständiga säkerhetskopior utförs varje vecka.
* Läs mer om [olika typer av principer för säkerhetskopiering](backup-architecture.md#sql-server-backup-types).

Så här skapar du en säkerhetskopieringspolicy:

1. Välj **Säkerhetskopieringsprinciper** > **Lägg till**i valvet .
2. Välj SQL **Server i Azure VM i** Lägg **till**för att definiera principtypen.

   ![Välj en policytyp för den nya säkerhetskopieringspolicyn](./media/backup-azure-sql-database/policy-type-details.png)

3. I **Policynamn** anger du ett namn för den nya policyn.
4. I **principen Fullständig säkerhetskopiering**väljer du en **säkerhetskopieringsfrekvens**. Välj antingen **dagligen** eller **veckovis**.

   * För **Dagligen** väljer du den timme och den tidszon då säkerhetskopieringsjobbet börjar.
   * För **Varje vecka** väljer du den veckodag, timme och tidszon då säkerhetskopieringsjobbet börjar.
   * Kör en fullständig säkerhetskopia eftersom du inte kan stänga av alternativet **Fullständig säkerhetskopiering.**
   * Välj **Fullständig säkerhetskopiering** om du vill visa principen.
   * Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.

     ![Nya fält för säkerhetskopieringspolicy](./media/backup-azure-sql-database/full-backup-policy.png)  

5. I **RETENTION RANGE**är alla alternativ markerade som standard. Ta bort eventuella begränsningar för kvarhållningsintervall som du inte vill ha och ange sedan de intervall som ska användas.

    * Minsta kvarhållningsperiod för alla typer av säkerhetskopiering (fullständig, differentiell och log) är sju dagar.
    * Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    * Säkerhetskopieringen för en viss dag taggas och behålls baserat på veckolagringsintervallet och inställningen för kvarhållning varje vecka.
    * Månatliga och årliga retention intervall beter sig på ett liknande sätt.

       ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)

6. På menyn **Policy för fullständig säkerhetskopia** väljer du **OK** för att acceptera inställningarna.
7. Om du vill lägga till en policy för differentiell säkerhetskopia väljer du **Differentiell säkerhetskopia**.

   ![Inställningar för kvarhållningsintervall](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Öppna menyn för policy för differentiell säkerhetskopia](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning.

    * Du kan bara utlösa en differentiell säkerhetskopiering per dag.
    * Differentiella säkerhetskopior kan behållas i upp till 180 dagar. För längre kvarhållning använder du fullständiga säkerhetskopior.

9. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

10. Om du vill lägga till en policy för loggsäkerhetskopia väljer du **Loggsäkerhetskopia**.
11. I **Loggsäkerhetskopia** väljer du **Aktivera** och anger kontrollerna för frekvens och kvarhållning. Log säkerhetskopior kan ske så ofta som var 15 minuter och kan behållas i upp till 35 dagar.
12. Välj **OK** för att spara policyn och återgå till huvudmenyn **Säkerhetskopieringspolicy**.

    ![Redigera policyn för loggsäkerhetskopia](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. På **principmenyn Säkerhetskopiering** väljer du om du vill aktivera **SQL Backup Compression** eller inte. Det här alternativet är inaktiverat som standard. Om det är aktiverat skickar SQL Server en komprimerad säkerhetskopieringsström till VDI.  Observera att Azure Backup åsidosätter instansnivåstandardinställningar med KOMPRIMERINGS-/NO_COMPRESSION-satsen beroende på värdet för den här kontrollen.

14. När du har slutfört redigeringarna i säkerhetskopieringspolicyn väljer du **OK**.

> [!NOTE]
> Varje loggsäkerhetskopiering är fastkedjad vid den tidigare fullständiga säkerhetskopieringen för att skapa en återställningskedja. Den fullständiga säkerhetskopian behålls tills kvarhållningen av den senaste loggsäkerhetskopiningen har upphört att gälla. Detta kan innebära att hela säkerhetskopian behålls under en extra period för att se till att alla loggar kan återställas. Låt oss anta att användaren har en vecka full backup, daglig differential och 2 timmars loggar. Alla är kvar i 30 dagar. Utom, den veckovis full kanna bli verkligen rensat upp/ utplånat kort efter den nästa full backningen är tillgänglig i.e., efter 30 + 7 dag. Säg, en vecka full backup händer den 16 november. Enligt bevarandepolicyn bör den behållas fram till den 16 december. Den sista loggen backup för denna fullständiga händer innan nästa schemalagda full, den 22 november. Tills den här loggen är tillgänglig fram till 22 december, kan den 16 november full inte tas bort. Så är den 16 november full kvar fram till 22 december.

## <a name="enable-auto-protection"></a>Aktivera automatiskt skydd  

Du kan aktivera automatiskt skydd för att automatiskt säkerhetskopiera alla befintliga och framtida databaser till en fristående SQL Server-instans eller till en alltid på tillgänglighetsgrupp.

* Det finns ingen gräns för hur många databaser du kan välja för automatiskt skydd på en gång.
* Du kan inte selektivt skydda eller utesluta databaser från skydd i en instans när du aktiverar automatiskt skydd.
* Om din instans redan innehåller vissa skyddade databaser förblir de skyddade enligt sina respektive principer även när du har aktiverat automatiskt skydd. Alla oskyddade databaser som läggs till senare har bara en enda princip som du definierar när du aktiverar automatiskt skydd, som anges under **Konfigurera säkerhetskopiering**. Du kan dock ändra principen som är associerad med en automatiskt skyddad databas senare.  

Så här aktiverar du automatiskt skydd:

  1. I **Items to backup** (Objekt som ska säkerhetskopieras) väljer du den instans som du vill aktivera automatiskt skydd för.
  2. Markera listrutan under **AUTOPROTECT**, välj **PÅ**och välj sedan **OK**.

      ![Aktivera automatiskt skydd i tillgänglighetsgruppen](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Säkerhetskopiering konfigureras för alla databaser tillsammans och kan spåras i **Säkerhetskopieringsjobb**.

Om du behöver inaktivera automatiskt skydd markerar du förekomstnamnet under **Konfigurera säkerhetskopiering**och väljer sedan **Inaktivera autoskydd** för instansen. Alla databaser fortsätter att säkerhetskopieras, men framtida databaser skyddas inte automatiskt.

![Inaktivera automatiskt skydd för den instansen](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Nästa steg

Lär dig att:

* [Återställa säkerhetskopierade SQL Server-databaser](restore-sql-database-azure-vm.md)
* [Hantera säkerhetskopierade SQL Server-databaser](manage-monitor-sql-database-backup.md)
