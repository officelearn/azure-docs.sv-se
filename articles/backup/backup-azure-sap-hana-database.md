---
title: Säkerhetskopiera en SAP HANA-databas till Azure med Azure Backup | Microsoft Docs
description: I den här självstudien beskrivs hur du säkerhetskopierar en SAP HANA-databas till Azure med tjänsten Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: dacurwin
ms.openlocfilehash: 50fbd0a2169fb120424d76e786a6269243eeb3e1
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273944"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>Säkerhetskopiera en SAP HANA-databas till Azure

[Azure Backup](backup-overview.md) stöder säkerhets kopiering av SAP HANA-databaser till Azure.

> [!NOTE]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion. Den är inte produktions klar och har inget garanterat service avtal.

## <a name="scenario-support"></a>Scenariostöd

**Support** | **Detaljer**
--- | ---
**Geografiska områden som stöds** | Sydöstra Australien, östra Australien <br> Södra Brasilien <br> Kanada, centrala, Östra Kanada <br> Asien, sydöstra Asien, östra <br> Östra USA, östra USA 2, västra centrala USA, västra USA, västra USA 2, norra centrala USA, centrala USA, södra centrala USA<br> Indien, centrala, södra Indien <br> Östra Japan, västra Japan<br> Sydkorea, centrala; Sydkorea, södra <br> Nordeuropa, Västeuropa <br> Storbritannien, södra Storbritannien, västra
**Virtuella dator operativ system som stöds** | SLES 12 med SP2, SP3 eller SP4.
**HANA-versioner som stöds** | SDC på HANA 1. x, MDC på HANA 2. x < = SPS04 rev 43

### <a name="current-limitations"></a>Aktuella begränsningar

- Du kan bara säkerhetskopiera SAP HANA databaser som körs på virtuella Azure-datorer.
- Du kan bara säkerhetskopiera SAP HANA instans som körs på en enda virtuell Azure-dator. Det finns för närvarande inte stöd för flera HANA-instanser i samma virtuella Azure-dator.
- Du kan bara säkerhetskopiera databaser i skalnings läge. Att skala ut en HANA-instans på flera virtuella Azure-datorer stöds för närvarande inte för säkerhets kopiering.
- Det går inte att säkerhetskopiera SAP HANA-instansen med dynamisk skiktning i utökad server, d.v.s. dynamisk nivå som finns på en annan nod. Detta är i stort sett skalbart vilket inte stöds.
- Du kan inte säkerhetskopiera SAP HANA-instansen med dynamisk skiktning aktive rad på samma server. Dynamisk skiktning stöds inte för närvarande.
- Du kan bara konfigurera SAP HANA säkerhets kopiering i Azure Portal. Det går inte att konfigurera funktionen med PowerShell, CLI.
- Du kan säkerhetskopiera databas loggar var 15: e minut. Logg säkerhets kopior börjar bara att flyta efter en lyckad fullständig säkerhets kopiering för databasen har slutförts.
- Du kan göra fullständiga och differentiella säkerhets kopieringar. Stegvis säkerhets kopiering stöds inte för närvarande.
- Du kan inte ändra säkerhets kopierings principen när du har tillämpat den för SAP HANA säkerhets kopieringar. Om du vill säkerhetskopiera med olika inställningar skapar du en ny princip eller tilldelar en annan princip.
  - Om du vill skapa en ny princip går du till valvet och klickar på **principer** > **säkerhets kopierings principer** >  **+ Lägg till** > -**SAP HANA i Azure VM**och anger princip inställningar.
  - Om du vill tilldela en annan princip klickar du på det aktuella princip namnet i egenskaperna för den virtuella datorn som kör-databasen. På sidan **säkerhets kopierings policy** kan du sedan välja en annan princip som ska användas för säkerhets kopieringen.

## <a name="prerequisites"></a>Krav

Kontrol lera att du gör följande innan du konfigurerar säkerhets kopior:

1. Installera och aktivera ODBC-drivrutinshanteraren från det officiella SLES-paketet/mediet med zypper på den virtuella datorn som kör SAP HANA-databasen, enligt följande:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

2. Tillåt anslutning från den virtuella datorn till Internet, så att den kan komma åt Azure, enligt beskrivningen i proceduren [nedan](#set-up-network-connectivity).

3. Kör skriptet för för registrering på den virtuella datorn där HANA är installerat som en rot användare. Skriptet anges [i portalen](#discover-the-databases) i flödet och krävs för att ställa in [rätt behörigheter](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions).

### <a name="set-up-network-connectivity"></a>Konfigurera nätverks anslutning

För alla åtgärder behöver den SAP HANA virtuella datorn anslutning till offentliga Azure-IP-adresser. VM-åtgärder (databas identifiering, konfigurera säkerhets kopiering, schemalägga säkerhets kopiering, återställning av återställnings punkter osv.) fungerar inte utan anslutning. Upprätta anslutningar genom att tillåta åtkomst till Azure Data Center IP-intervall: 

- Du kan hämta [IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653) för Azure-datacenter och sedan ge åtkomst till dessa IP-adresser.
- Om du använder nätverks säkerhets grupper (NSG: er) kan du använda AzureCloud [-tjänst tag gen](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) för att tillåta alla offentliga Azure-IP-adresser. Du kan använda [cmdleten Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) för att ändra NSG-regler.
- 443-porten ska vara vit listas eftersom transporten är via HTTPS.

## <a name="onboard-to-the-public-preview"></a>Publicera till den offentliga för hands versionen

Publicera till den offentliga för hands versionen enligt följande:

- I portalen registrerar du ditt prenumerations-ID till Recovery Services tjänst leverantören genom att [följa den här artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- För PowerShell kör du denna cmdlet. Den bör slutföras som "registrerad".

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Identifiera databaserna

1. Klicka på **säkerhetskopiera**i **komma igång**i valvet. I **var kör din arbets belastning?** väljer du **SAP HANA i virtuell Azure-dator**.
2. Klicka på **Starta identifiering**. Detta initierar identifiering av oskyddade virtuella Linux-datorer i valv regionen.

   - Efter identifieringen visas oskyddade virtuella datorer i portalen, listade efter namn och resurs grupp.
   - Om en virtuell dator inte visas som förväntat kontrollerar du om den redan har säkerhetskopierats i ett valv.
   - Flera virtuella datorer kan ha samma namn, men de tillhör olika resurs grupper.

3. I **välj Virtual Machines**klickar du på länken för att hämta skriptet som ger behörighet till tjänsten Azure Backup för att få åtkomst till de SAP HANA virtuella datorerna för databas identifiering
4. Kör skriptet på varje virtuell dator som är värd för SAP HANA databaser som du vill säkerhetskopiera.
5. När skriptet har körts på de virtuella datorerna väljer du de virtuella datorerna i **välj Virtual Machines**. Klicka sedan på **identifiera databaser**.
6. Azure Backup identifierar alla SAP HANA-databaser på den virtuella datorn. Under identifieringen registrerar Azure Backup den virtuella datorn med valvet och installerar ett tillägg på den virtuella datorn. Ingen agent är installerad på databasen.

    ![Identifiera SAP HANA databaser](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering  

Aktivera nu säkerhets kopiering.

1. I steg 2 klickar du på **Konfigurera säkerhets kopiering**.
2. I **Välj objekt att säkerhetskopiera**väljer du alla databaser som du vill skydda > **OK**.
3. I **säkerhets kopierings princip** > **Välj säkerhets kopierings princip**, skapa en ny säkerhets kopierings princip för databaserna enligt anvisningarna nedan.
4. När du har skapat principen klickar du på **Aktivera säkerhets kopiering**på menyn **säkerhets kopiering** .
5. Spåra förloppet för säkerhets kopierings konfigurationen i området **meddelanden** i portalen.

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

En säkerhets kopierings princip definierar när säkerhets kopieringar görs och hur länge de ska behållas.

- En policy skapas på valvnivå.
- Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.

Ange princip inställningarna enligt följande:

1. I **Policynamn** anger du ett namn för den nya policyn.
2. I **Policy för fullständig säkerhetskopia** väljer du en **Säkerhetskopieringsfrekvens** och väljer **Dagligen** eller **Varje vecka**.
   - **Varje dag**: Välj den timme och den tidszon som säkerhets kopierings jobbet ska starta.
   
       - Du måste köra en fullständig säkerhets kopiering. Du kan inte inaktivera det här alternativet.
       - Klicka på **Fullständig säkerhetskopia** för att visa policyn.
       - Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.
       
   - **Varje vecka**: Välj den veckodag, timme och tidszon som säkerhets kopierings jobbet körs i.
3. Konfigurera inställningar för kvarhållning för fullständig säkerhets kopiering i **kvarhållningsintervall**.
    - Som standard är alla alternativ markerade. Rensa eventuella gränser för kvarhållningsintervall som du inte vill använda och ange de som du gör.
    - Den minsta Retentions perioden för någon typ av säkerhets kopiering (fullständig/differentiell/log) är sju dagar.
    - Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    - Säkerhets kopian för en angiven dag märks och bevaras baserat på vecko kvarhållningsintervallet och-inställningen.
    - De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.

4. I menyn **fullständig säkerhets kopierings policy** klickar du på **OK** för att godkänna inställningarna.
5. Välj **differentiell säkerhets kopiering** för att lägga till en differentiell princip.
6. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning.
    - Du kan endast utlösa en differentiell säkerhetskopia per dag.
    - Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

    > [!NOTE]
    > Stegvisa säkerhets kopieringar stöds inte för närvarande. 

7. Klicka på **OK** för att spara principen och återgå till huvud menyn för **säkerhets kopierings policyn** .
8. Välj **logg säkerhets kopiering** för att lägga till en transaktions logg princip för säkerhets kopiering
    - I **logg säkerhets kopiering**väljer du **Aktivera**.
    - Ange frekvens och bevarande kontroller.

    > [!NOTE]
    > Logg säkerhets kopior börjar bara att flyta efter en lyckad fullständig säkerhets kopiering har slutförts.

9. Klicka på **OK** för att spara principen och återgå till huvud menyn för **säkerhets kopierings policyn** .
10. När du är klar med att definiera säkerhets kopierings principen klickar du på **OK**.


## <a name="run-an-on-demand-backup"></a>Köra en säkerhets kopiering på begäran

Säkerhets kopieringar körs enligt princip schemat. Du kan köra en säkerhets kopiering på begäran på följande sätt:


1. I menyn valv klickar du på **säkerhets kopierings objekt**.
2. I **säkerhets kopierings objekt**väljer du den virtuella dator som kör SAP HANA databasen och klickar sedan på **Säkerhetskopiera nu**.
3. I **Säkerhetskopiera nu**använder du kalender kontrollen för att välja den sista dagen som återställnings punkten ska behållas. Klicka på **OK**.
4. Övervaka Portal meddelanden. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb** > pågår. Det kan ta en stund att skapa den första säkerhets kopieringen, beroende på databasens storlek.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Köra säkerhets kopiering av SAP HANA Studio på en databas med Azure Backup aktiverat

Om du vill ta en lokal säkerhets kopia (med HANA Studio) av en databas som säkerhets kopie ras med Azure Backup gör du följande:

1. Vänta tills alla säkerhets kopieringar eller loggar för databasen har slutförts. Kontrol lera statusen i SAP HANA Studio.
2. Inaktivera logg säkerhets kopior och ange säkerhets kopierings katalogen till fil systemet för relevant databas.
3. Det gör du genom att dubbelklicka på **systemdb** > **konfiguration** > **Välj databas** > **filter (logg)** .
4. Ange **enable_auto_log_backup** till **Nej**.
5. Ange **log_backup_using_backint** till **false**.
6. Ta en ad hoc fullständig säkerhets kopiering av databasen.
7. Vänta tills den fullständiga säkerhets kopieringen och katalog säkerhets kopieringen har slutförts.
8. Återställ tidigare inställningar tillbaka till dem för Azure:
    - Ange **enable_auto_log_backup** till **Ja**.
    - Ange **log_backup_using_backint** till **True**.


## <a name="upgrading-protected-10-dbs-to-20"></a>Uppgradera skyddad 1,0 databaser till 2,0

Om du skyddar SAP HANA 1,0-databaser och vill uppgradera till 2,0 utför du stegen som beskrivs nedan.

- Stoppa skyddet med Behåll data för gammal SDC-databas.
- Kör ett för registrerings skript igen med korrekt information om (sid och MDC). 
- Omregistrera tillägg (säkerhets kopiering > Visa information – > väljer den relevanta Azure-> Omregistrera). 
- Klicka på identifiera databaser igen för samma virtuella dator. Detta ska visa den nya databaser i steg 2 med rätt information (SYSTEMDB och klient databas, inte SDC). 
- Skydda dessa nya databaser.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](backup-azure-sap-hana-database-troubleshoot.md) hur du felsöker vanliga fel när du använder SAP HANA säkerhets kopiering på virtuella Azure-datorer.
[Lär dig hur](backup-azure-arm-vms-prepare.md) du säkerhetskopierar virtuella Azure-datorer.
