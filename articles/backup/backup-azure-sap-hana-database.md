---
title: Säkerhetskopiera en SAP HANA-databas till Azure med Azure Backup | Microsoft Docs
description: Den här självstudien beskrivs hur du säkerhetskopierar en SAP HANA-databas till Azure med Azure Backup-tjänsten.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: 5ed41013535e4591d88bff5c017c1fcf4c4053cc
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237814"
---
# <a name="back-up-an-sap-hana-database"></a>Säkerhetskopiera en SAP HANA-databas

[Azure Backup](backup-overview.md) stöd för säkerhetskopiering av SAP HANA-databaser till Azure.

> [!NOTE]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion. Det är för närvarande inte klara för produktion och har en garanterad serviceavtal. 


## <a name="scenario-support"></a>Scenariostöd

**Support** | **Detaljer**
--- | ---
**Geografiska områden som stöds** | Australien, sydöstra Australien, östra <br> Brasilien, södra <br> Kanada, centrala Kanada, östra <br> Sydostasien, Östasien <br> Östra USA, östra USA 2, USA, västra centrala, USA, västra, USA, västra 2, norra centrala USA, centrala USA, södra centrala USA<br> Indien, centrala Indien, södra <br> Östra Japan, västra Japan<br> Sydkorea, centrala; Sydkorea, södra <br> Norra Europa, västra Europa <br> Storbritannien, södra, Storbritannien, västra
**VM-operativsystem som stöds** | SLES 12 SP2 eller SP3.
**HANA-versioner som stöds** | SSDC on HANA 1.x, MDC on HANA 2.x <= SPS03


### <a name="current-limitations"></a>Aktuella begränsningar

- Du kan bara säkerhetskopiera SAP HANA-databaser som körs på virtuella Azure-datorer.
- Du kan bara konfigurera säkerhetskopiering för SAP HANA i Azure-portalen. Funktionen kan inte konfigureras med PowerShell, CLI eller REST API.
- Du kan bara säkerhetskopiera databaser på upp-läge.
- Du kan säkerhetskopiera databasloggar var 15: e minut. Loggsäkerhetskopior endast börjar flöda när en fullständig säkerhetskopia för databasen har slutförts.
- Du kan ta fullständiga och differentiella säkerhetskopieringar. Inkrementell säkerhetskopiering stöds inte för närvarande.
- Du kan inte ändra principen för säkerhetskopiering när du har tillämpat den för SAP HANA-säkerhetskopiering. Om du vill säkerhetskopiera med olika inställningar, skapa en ny princip eller tilldela en annan princip. 
    - Om du vill skapa en ny princip i valvet klickar du på **principer** > **principer för säkerhetskopiering** > **+ Lägg till** > **SAP HANA i Azure VM**, och ange andra inställningar.
    - Om du vill tilldela en annan princip i egenskaperna för den virtuella datorn som kör databasen, klickar du på principnamnet på aktuella. På den **Säkerhetskopieringsprincipen** sidan som du kan välja en annan princip som ska användas för säkerhetskopiering.




## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du gör följande innan du konfigurerar säkerhetskopieringar:

1. På den virtuella datorn som kör SAP HANA-databasen, installera den officiella Microsoft [.NET Core Runtime 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current) paketet. Tänk på följande:
    - Du behöver bara den **dotnet-runtime-2.1** paketet. Du behöver inte **aspnetcore-runtime-2.1**.
    - Om den virtuella datorn inte är ansluten till internet komma åt, spegling eller ange en offlinecache för dotnet-runtime-2.1 (och alla beroende rpm) från Microsoft-paketet feed anges på sidan.
    - Under installationen, kan du bli ombedd att ange ett alternativ. I så, fall ange **lösning 2**.

        ![Alternativ för installation av paket](./media/backup-azure-sap-hana-database/hana-package.png)

2.  På den virtuella datorn, installera och aktivera ODBC-drivrutinspaket från de officiella SLES paket/media med zypper, enligt följande:

    ``` 
    sudo zypper update
    sudo zypper install unixODBC
    ```
4.  Tillåta anslutning från den virtuella datorn till internet, så att den kan nå Azure, enligt beskrivningen i proceduren nedan.


### <a name="set-up-network-connectivity"></a>Konfigurera nätverksanslutningar

SAP HANA VM ha anslutning till Azure offentliga IP-adresser för alla åtgärder. VM-åtgärder (databasidentifiering, säkerhetskopieringar, schemalägga säkerhetskopieringar, återställa återställningspunkter och så vidare) fungerar inte utan anslutning. Upprätta en anslutning genom att tillåta åtkomst till Azure-datacenter IP-adressintervall: 

- Du kan ladda ned den [IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653) för Azure-datacenter, och sedan tillåta åtkomst till dessa IP-adresser.
- Om du använder nätverkssäkerhetsgrupper (NSG), kan du använda AzureCloud [servicetagg](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) så att alla Azure offentlig IP-adresser. Du kan använda den [cmdlet Set-AzureNetworkSecurityRule](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) ändra NSG-regler.

## <a name="onboard-to-the-public-preview"></a>Kom igång med den offentliga förhandsversionen

Kom igång med den offentliga förhandsversionen på följande sätt:

- I portalen, registrera ditt prenumerations-ID till Recovery Services-leverantören av [följa den här artikeln](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal). 
- PowerShell kör du denna cmdlet. Det bör vara klart som ”Registered”.

    ```
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Identifiera databaser


1. I valvet i **komma igång**, klickar du på **Backup**. I **var körs din arbetsbelastning?** väljer **SAP HANA i Azure VM**.
2. Klicka på **starta identifieringen**. Detta startar en identifiering av oskyddade virtuella Linux-datorer i valvregionen.

   - Efter identifieringen, oskyddade virtuella datorer visas i portalen visas efter namn och resursgrupp.
   - Om en virtuell dator inte visas som förväntat, måste du kontrollera om det redan har säkerhetskopierats i ett valv.
   - Flera virtuella datorer kan ha samma namn men de tillhör olika resursgrupper.

3. I **Välj virtuella datorer**, klickar du på länken för att hämta det skript som ger behörigheter för Azure Backup-tjänsten att få åtkomst till de virtuella datorerna SAP HANA för databasidentifiering av
4. Kör skriptet på varje virtuell dator som är värd för SAP HANA-databaser som du vill säkerhetskopiera.
5. När skriptet har körts på de virtuella datorerna i **Välj virtuella datorer**, Välj de virtuella datorerna. Klicka sedan på **identifiera databaser**.
6. Azure Backup identifierar alla SAP HANA-databaser på den virtuella datorn. Vid identifiering, Azure Backup registrerar den virtuella datorn med valvet och installerar ett tillägg på den virtuella datorn. Ingen agent är installerad på databasen.

    ![Identifiera SAP HANA-databaser](./media/backup-azure-sap-hana-database/hana-discover.png)
    
## <a name="configure-backup"></a>Konfigurera säkerhetskopiering  

Nu kan du aktivera säkerhetskopiering.

1. I steg 2, klickar du på **Konfigurera säkerhetskopiering**.
2. I **väljer objekt att säkerhetskopiera**, väljer du databaserna som du vill skydda > **OK**.
3. I **Säkerhetskopieringsprincipen** > **Välj säkerhetskopieringspolicy**, skapa en ny säkerhetskopieringsprincip för databaser i enlighet med instruktionerna nedan.
4. När du har skapat principen på den **säkerhetskopiering** -menyn klickar du på **Aktivera säkerhetskopiering**.
5. Spåra förloppet för konfiguration av säkerhetskopiering i den **meddelanden** området i portalen.

### <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy
En princip för säkerhetskopiering definierar när säkerhetskopior tas och hur länge de är kvar.

- En policy skapas på valvnivå.
- Flera valv kan använda samma säkerhetskopieringspolicy, men du måste tillämpa säkerhetskopieringspolicyn på varje valv.

Ange inställningarna för principen enligt följande:

1. I **Policynamn** anger du ett namn för den nya policyn.
2. I **Policy för fullständig säkerhetskopia** väljer du en **Säkerhetskopieringsfrekvens** och väljer **Dagligen** eller **Varje vecka**.
   - **Dagliga**: Välj timme och tidszon som säkerhetskopieringsjobbet börjar.
   
       - Du måste köra en fullständig säkerhetskopia. Du kan inte inaktivera det här alternativet.
       - Klicka på **Fullständig säkerhetskopia** för att visa policyn.
       - Du kan inte skapa differentiella säkerhetskopior för dagliga fullständiga säkerhetskopior.
       
   - **Varje vecka**: Välj vilken dag i veckan, timme och tidszon som jobbet körs.
3. I **Kvarhållningsintervall**, konfigurera kvarhållningsinställningar för för fullständig säkerhetskopiering.
    - Som standard markeras alla alternativ. Avmarkera alla gräns för datakvarhållning-intervall som du inte vill använda och ange de som du gör.
    - Minsta kvarhållningsperioden för alla typer av säkerhetskopiering (fullständig/differentiell/loggning) är sju dagar.
    - Återställningspunkter taggas för kvarhållning baserat på deras kvarhållningsintervall. Om du till exempel väljer en daglig fullständig säkerhetskopia utlöses endast en fullständig säkerhetskopia varje dag.
    - Säkerhetskopiering baserat för en viss dag är märkta och bevaras på varje vecka Kvarhållningsintervall och inställningen.
    - De månatliga och årliga kvarhållningsintervallen fungerar på liknande sätt.

4. I den **princip för fullständig säkerhetskopiering** -menyn klickar du på **OK** att acceptera inställningarna.
5. Välj **differentiell säkerhetskopiering** att lägga till en princip för differentiell.
6. I **Policy för differentiell säkerhetskopia** väljer du **Aktivera** för att öppna kontrollerna för frekvens och kvarhållning.
    - Du kan endast utlösa en differentiell säkerhetskopia per dag.
    - Differentiella säkerhetskopior kan behållas i upp till 180 dagar. Om du behöver längre kvarhållning måste du använda fullständiga säkerhetskopior.

    > [!NOTE]
    > Inkrementell säkerhetskopiering stöds inte för närvarande. 

7. Klicka på **OK** att spara principen och återgå till huvudsidan **säkerhetskopieringspolicy** menyn.
8. Välj **Loggsäkerhetskopior** att lägga till en transaktionell log princip för säkerhetskopiering
    - I **Loggsäkerhetskopiering**väljer **aktivera**.
    - Ange frekvens och kvarhållning kontroller.

    > [!NOTE]
    > Loggsäkerhetskopior endast börjar flöda när en fullständig säkerhetskopia är klar.

9. Klicka på **OK** att spara principen och återgå till huvudsidan **säkerhetskopieringspolicy** menyn.
10. När du har definierat principen för säkerhetskopiering, klickar du på **OK**.


## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Säkerhetskopiering köras i enlighet med principschemat. Du kan köra en säkerhetskopiering på begäran på följande sätt:


1. I menyn valvet klickar du på **Säkerhetskopiera objekt**.
2. I **Säkerhetskopieringsobjekt**, Välj den virtuella datorn som kör SAP HANA-databasen och klicka sedan på **Säkerhetskopiera nu**.
3. I **Säkerhetskopiera nu**, använder kalenderkontrollen för att välja den sista dagen som återställningspunkten ska behållas. Klicka sedan på **OK**.
4. Meddelandena som portalen. Du kan övervaka jobbförloppet i instrumentpanelen för valvet > **säkerhetskopieringsjobb** > pågår. Beroende på databasens storlek kan det ta en stund att skapa den första säkerhetskopian.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Kör SAP HANA-Studio säkerhetskopiering för en databas med Azure Backup aktiverad

Om du vill säkerhetskopiera lokala (med HANA Studio) av en databas som säkerhetskopieras med Azure Backup, gör du följande:

1. Vänta tills alla fullständig eller loggsäkerhetskopior att slutföra-databasen. Kontrollera statusen i SAP HANA-Studio.
2. Inaktivera loggsäkerhetskopior och ange säkerhetskopieringskatalogen till filsystemet för relevanta databasen.
3. Gör detta genom att dubbelklicka på **systemdb** > **Configuration** > **Välj databas** > **Filter (loggning)** .
4. Ange **enable_auto_log_backup** till **nr**.
5. Ange **log_backup_using_backint** till **FALSKT**.
6. Ta en ad hoc-och fullständig säkerhetskopiering av databasen.
7. Vänta tills fullständig säkerhetskopiering och catalog säkerhetskopieringen har avslutats.
8. Återställa de tidigare inställningarna återställs till de för Azure:
    - Ange **enable_auto_log_backup** till **Ja**.
    - Ange **log_backup_using_backint** till **SANT**.



## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](backup-azure-arm-vms-prepare.md) säkerhetskopiering av virtuella Azure-datorer.


