---
title: Säkerhetskopiera en SAP HANA-databas till Azure med Azure Backup
description: I den här artikeln lär du dig hur du säkerhetskopierar en SAP HANA-databas till virtuella Azure-datorer med tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: ed47f18c9dabc685d6fbe02804562ef86a93190a
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285839"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer

SAP HANA-databaser är kritiska arbets belastningar som kräver återställnings punkt mål och långsiktig kvarhållning. Du kan säkerhetskopiera SAP HANA databaser som körs på virtuella datorer i Azure med hjälp av [Azure Backup](backup-overview.md).

Den här artikeln visar hur du säkerhetskopierar SAP HANA databaser som körs på virtuella Azure-datorer till ett Azure Backup Recovery Services-valv.

I den här artikeln får du lära dig hur du:
> [!div class="checklist"]
>
> * Skapa och konfigurera ett valv
> * Identifiera databaser
> * Konfigurera säkerhets kopior
> * Köra ett säkerhets kopierings jobb på begäran

## <a name="prerequisites"></a>Krav

Se avsnitten [krav](tutorial-backup-sap-hana-db.md#prerequisites) och [konfiguration av behörigheter](tutorial-backup-sap-hana-db.md#setting-up-permissions) för att konfigurera databasen för säkerhets kopiering.

### <a name="set-up-network-connectivity"></a>Konfigurera nätverks anslutning

För alla åtgärder behöver den SAP HANA virtuella datorn anslutning till offentliga Azure-IP-adresser. VM-åtgärder (databas identifiering, konfigurera säkerhets kopiering, schemalägga säkerhets kopiering, återställning av återställnings punkter osv.) fungerar inte utan anslutning. Upprätta anslutningar genom att tillåta åtkomst till Azure Data Center IP-intervall:

* Du kan hämta [IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653) för Azure-datacenter och sedan ge åtkomst till dessa IP-adresser.
* Om du använder nätverks säkerhets grupper (NSG: er) kan du använda AzureCloud [-tjänst tag gen](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) för att tillåta alla offentliga Azure-IP-adresser. Du kan använda [cmdleten Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) för att ändra NSG-regler.
* Port 443 ska läggas till i listan över tillåtna eftersom transporten är via HTTPS.

## <a name="onboard-to-the-public-preview"></a>Publicera till den offentliga för hands versionen

Publicera till den offentliga för hands versionen enligt följande:

* I portalen registrerar du ditt prenumerations-ID till Recovery Services tjänst leverantören genom att [följa den här artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal).
* För PowerShell kör du denna cmdlet. Den bör slutföras som "registrerad".

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Identifiera databaserna

1. Klicka på **säkerhetskopiera**i **komma igång**i valvet. I **var kör din arbets belastning?** väljer du **SAP HANA i virtuell Azure-dator**.
2. Klicka på **Starta identifiering**. Detta initierar identifiering av oskyddade virtuella Linux-datorer i valv regionen.

   * Efter identifieringen visas oskyddade virtuella datorer i portalen, listade efter namn och resurs grupp.
   * Om en virtuell dator inte visas som förväntat kontrollerar du om den redan har säkerhetskopierats i ett valv.
   * Flera virtuella datorer kan ha samma namn, men de tillhör olika resurs grupper.

3. I **välj Virtual Machines**klickar du på länken för att hämta skriptet som ger behörighet till tjänsten Azure Backup för att få åtkomst till de SAP HANA virtuella datorerna för databas identifiering.
4. Kör skriptet på varje virtuell dator som är värd för SAP HANA databaser som du vill säkerhetskopiera.
5. När skriptet har körts på de virtuella datorerna väljer du de virtuella datorerna i **välj Virtual Machines**. Klicka sedan på **identifiera databaser**.
6. Azure Backup identifierar alla SAP HANA-databaser på den virtuella datorn. Under identifieringen registrerar Azure Backup den virtuella datorn med valvet och installerar ett tillägg på den virtuella datorn. Ingen agent är installerad på databasen.

    ![Identifiera SAP HANA databaser](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering  

Aktivera nu säkerhets kopiering.

1. I steg 2 klickar du på **Konfigurera säkerhets kopiering**.

    ![Konfigurera säkerhetskopiering](./media/backup-azure-sap-hana-database/configure-backup.png)
2. I **Välj objekt att säkerhetskopiera**väljer du alla databaser som du vill skydda > **OK**.

    ![Välj objekt som ska säkerhets kopie ras](./media/backup-azure-sap-hana-database/select-items.png)
3. I **säkerhets kopierings policy** > **väljer du säkerhets kopierings princip**, skapar en ny säkerhets kopierings princip för databaserna enligt anvisningarna nedan.

    ![Välj säkerhets kopierings princip](./media/backup-azure-sap-hana-database/backup-policy.png)
4. När du har skapat principen klickar du på **Aktivera säkerhets kopiering**på menyn **säkerhets kopiering** .

    ![Aktivera säkerhets kopiering](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Spåra förloppet för säkerhets kopierings konfigurationen i området **meddelanden** i portalen.

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En säkerhets kopierings princip definierar när säkerhets kopieringar görs och hur länge de ska behållas.

* En policy skapas på valvnivå.
* Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.

Ange princip inställningarna enligt följande:

1. I **Policynamn** anger du ett namn för den nya policyn.

   ![Ange princip namn](./media/backup-azure-sap-hana-database/policy-name.png)
2. I **Policy för fullständig säkerhetskopia** väljer du en **Säkerhetskopieringsfrekvens** och väljer **Dagligen** eller **Varje vecka**.
   * **Varje dag**: Välj den timme och den tidszon som säkerhets kopierings jobbet ska starta.
       * Du måste köra en fullständig säkerhets kopiering. Du kan inte inaktivera det här alternativet.
       * Klicka på **Fullständig säkerhetskopia** för att visa policyn.
       * Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.
   * **Varje vecka**: Välj den veckodag, timme och tidszon som säkerhets kopierings jobbet körs i.

   ![Välj säkerhets kopierings frekvens](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. Konfigurera inställningar för kvarhållning för fullständig säkerhets kopiering i **kvarhållningsintervall**.
    * Som standard är alla alternativ markerade. Rensa eventuella gränser för kvarhållningsintervall som du inte vill använda och ange de som du gör.
    * Den minsta Retentions perioden för någon typ av säkerhets kopiering (fullständig/differentiell/log) är sju dagar.
    * Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    * Säkerhets kopian för en angiven dag märks och bevaras baserat på vecko kvarhållningsintervallet och-inställningen.
    * De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.

4. I menyn **fullständig säkerhets kopierings policy** klickar du på **OK** för att godkänna inställningarna.
5. Välj **differentiell säkerhets kopiering** för att lägga till en differentiell princip.
6. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning.
    * Du kan endast utlösa en differentiell säkerhetskopia per dag.
    * Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

    ![Princip för differentiell säkerhets kopiering](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Stegvisa säkerhets kopieringar stöds inte för närvarande.

7. Klicka på **OK** för att spara principen och återgå till huvud menyn för **säkerhets kopierings policyn** .
8. Välj **logg säkerhets kopiering** för att lägga till en transaktions logg princip för säkerhets kopiering
    * I **logg säkerhets kopiering**väljer du **Aktivera**.  Detta kan inte inaktive ras eftersom SAP HANA hanterar alla logg säkerhets kopior.
    * Ange frekvens och bevarande kontroller.

    > [!NOTE]
    > Logg säkerhets kopior börjar bara att flyta efter en lyckad fullständig säkerhets kopiering har slutförts.

9. Klicka på **OK** för att spara principen och återgå till huvud menyn för **säkerhets kopierings policyn** .
10. När du är klar med att definiera säkerhets kopierings principen klickar du på **OK**.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhets kopiering på begäran

Säkerhets kopieringar körs enligt princip schemat. Du kan köra en säkerhets kopiering på begäran på följande sätt:

1. I menyn valv klickar du på **säkerhets kopierings objekt**.
2. I **säkerhets kopierings objekt**väljer du den virtuella dator som kör SAP HANA databasen och klickar sedan på **Säkerhetskopiera nu**.
3. I **Säkerhetskopiera nu**använder du kalender kontrollen för att välja den sista dagen som återställnings punkten ska behållas. Klicka på **OK**.
4. Övervaka Portal meddelanden. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb** > **pågår**. Det kan ta en stund att skapa den första säkerhets kopieringen, beroende på databasens storlek.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Köra säkerhets kopiering av SAP HANA Studio på en databas med Azure Backup aktiverat

Om du vill ta en lokal säkerhets kopia (med HANA Studio) av en databas som säkerhets kopie ras med Azure Backup gör du följande:

1. Vänta tills alla säkerhets kopieringar eller loggar för databasen har slutförts. Kontrol lera statusen i SAP HANA Studio/cockpit.
2. Inaktivera logg säkerhets kopior och ange säkerhets kopierings katalogen till fil systemet för relevant databas.
3. Det gör du genom att dubbelklicka på **systemdb** > **konfiguration** > **välja databas** > **filter (logg)** .
4. Ange **enable_auto_log_backup** till **Nej**.
5. Ange **log_backup_using_backint** till **false**.
6. Utför en fullständig säkerhets kopiering på begäran av databasen.
7. Vänta tills den fullständiga säkerhets kopieringen och katalog säkerhets kopieringen har slutförts.
8. Återställ tidigare inställningar tillbaka till dem för Azure:
    * Ange **enable_auto_log_backup** till **Ja**.
    * Ange **log_backup_using_backint** till **Sant**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer SAP HANA databaser som körs på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Lär dig hur du [hanterar SAP HANA databaser som säkerhets kopie ras med Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
