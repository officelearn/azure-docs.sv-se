---
title: Utvärdera servrar med hjälp av importerade serverdata med Azure Migrate Server Assessment
description: Beskriver hur du bedömer lokala servrar för migrering till Azure med Azure Migrate Server Assessment med importerade data.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 2a30222902fd8797908202562a04018209842af2
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115062"
---
# <a name="assess-servers-by-using-imported-data"></a>Bedöma servrar med hjälp av importerade data

I den här artikeln beskrivs hur du bedömer lokala servrar med verktyget [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) genom att importera servermetadata i CSV-format (Comma-separated values). Den här bedömningsmetoden kräver inte att du konfigurerar Azure Migrate-enheten för att skapa en utvärdering. Det är användbart om:

- Du vill skapa en snabb, första bedömning innan du distribuerar apparaten.
- Du kan inte distribuera Azure Migrate-enheten i din organisation.
- Du kan inte dela autentiseringsuppgifter som ger åtkomst till lokala servrar.
- Säkerhetsbegränsningar hindrar dig från att samla in och skicka data som samlas in av enheten till Azure. Du kan styra de data som du delar i en importerad fil. Dessutom är mycket av data (till exempel att tillhandahålla IP-adresser) valfritt.

## <a name="before-you-start"></a>Innan du börjar

Var medveten om dessa punkter:

- Du kan lägga till upp till maximalt 20 000 servrar i en enda CSV-fil.
- Du kan lägga till upp till 20 000 servrar i ett Azure Migrate-projekt med hjälp av CSV.
- Du kan ladda upp serverinformation till Serverutvärdering flera gånger med hjälp av CSV.
- Det är användbart att samla in programinformation när du utvärderar den lokala miljön för migrering. Serverutvärdering utför dock för närvarande inte utvärdering på programnivå eller tar hänsyn till program när du skapar en utvärdering.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Fyll i en CSV-fil med serverinformation.
> * Importera filen för att lägga till serverinformation i Serverutvärdering.
> * Skapa och granska en bedömning.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario, så att du snabbt kan ställa in ett konceptbevis. Självstudier använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i instruktionsguiderna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.

## <a name="set-azure-permissions-for-azure-migrate"></a>Ange Azure-behörigheter för Azure Migrate

Ditt Azure-konto behöver behörigheter för att skapa ett Azure Migrate-projekt.

1. Öppna prenumerationen i Azure-portalen och välj **Åtkomstkontroll (IAM)**.
2. Leta reda på det relevanta kontot i **Checkåtkomst**och välj det för att visa behörigheter.
3. Kontrollera att du har **behörighet för deltagare** eller **ägare.**
    - Om du just har skapat ett kostnadsfritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerationsägare arbetar du med ägaren för att tilldela rollen.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Så här konfigurerar du ett nytt Azure Migrate-projekt:

1. Sök efter Azure Migrate i **Alla tjänster**i Azure **Portal.**
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt**väljer du Utvärdera och migrera servrar under **Upptäck, utvärdera och migrera** **servrar**.

    ![Upptäck och utvärdera servrar](./media/tutorial-assess-import/assess-migrate.png)

4. Välj **Lägg till verktyg**i Komma **igång.**
5. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
6. I **PROJEKTINFORMATION**anger du projektnamnet och geografin där du vill skapa projektet. Mer information:

    - Granska [geografiska områden som stöds](migrate-support-matrix.md#supported-geographies). Projektgeografin används bara för att lagra de metadata som samlas in från lokala virtuella datorer.
    - Du kan välja vilken målregion du vill när du kör en migrering.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-import/migrate-project.png)

7. Välj **Nästa**.
8. I **Välj bedömningsverktyg**väljer du **Azure Migrate: Serverutvärdering** > **nästa**.

    ![Skapa en Azure Migrate-utvärdering](./media/tutorial-assess-import/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. Granska inställningarna i **Verktyg för Granskning + lägg till**och välj sedan Lägg till **verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Du kommer sedan till projektsidan. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.

## <a name="prepare-the-csv"></a>Förbered CSV

Hämta CSV-mallen och lägg till serverinformation i den.

### <a name="download-the-template"></a>Ladda ned mallen

1. I **Migreringsmålsservrar** > **Servers** > **Azure Migrera: Serverutvärdering**väljer du **Upptäck**.
2. I **Upptäck-datorer**väljer du **Importera med CSV**.
3. Välj **Hämta** om du vill hämta CSV-mallen. Alternativt kan du [ladda ner den direkt](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Ladda ner CSV-mall](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Lägga till serverinformation

Samla in serverdata och lägg till dem i CSV-filen.

- Om du vill samla in data kan du exportera dem från verktyg som du använder för lokal serverhantering, till exempel VMware vSphere eller cmdb (Configuration-management Database).
- Om du vill granska exempeldata laddar du ned vår [exempelfil](https://go.microsoft.com/fwlink/?linkid=2108405).

I följande tabell sammanfattas de filfält som ska fyllas i:

**Fältnamn** | **Obligatoriska** | **Detaljer**
--- | --- | ---
**Servernamn** | Ja | Vi rekommenderar att du anger det fullständigt kvalificerade domännamnet (FQDN).
**IP-adress** | Inga | Serveradress.
**Kärnor** | Ja | Antal processorkärnor som tilldelats servern.
**Minne** | Ja | Totalt RAM-minne, i MB, allokerat till servern.
**OS-namn** | Ja | Serveroperativsystem. <br/> Operativsystemnamn som matchar eller innehåller namnen i den [här](#supported-operating-system-names) listan känns igen av utvärderingen.
**Operativsystemversion** | Inga | Server operativsystem version.
**Antal diskar** | Inga | Behövs inte om enskilda diskuppgifter tillhandahålls.
**Disk 1 storlek**  | Inga | Maximal storlek på disken, i GB.<br/>Du kan lägga till information om fler diskar genom [att lägga till kolumner](#add-multiple-disks) i mallen. Du kan lägga till upp till åtta diskar.
**Disk 1 läsa ops** | Inga | Diskavläsningsåtgärder per sekund.
**Disk 1 skriv ops** | Inga | Diskskrivningsåtgärder per sekund.
**Disk 1 läsflöde** | Inga | Data som läses från disken per sekund, i MB per sekund.
**Skrivflöde för disk 1** | Inga | Data som skrivs till disk per sekund, i MB per sekund.
**Procentsats för processoranvändning** | Inga | Procentandel processor som används.
**Procentandel av minnesanvändningen** | Inga | Procentandel RAM-minne som används.
**Totalt antal diskar som lästa ops** | Inga | Diskläsningsåtgärder per sekund.
**Totalt antal diskar skriver ops** | Inga | Disk-skrivningsåtgärder per sekund.
**Totalt antal diskar som läsdataflöde** | Inga | Data som läses från disken, i MB per sekund.
**Totalt antal diskar skriver dataflöde** | Inga | Data som skrivs till disk, i MB per sekund.
**Nätverk i dataflöde** | Inga | Data som tas emot av servern, i MB per sekund.
**Dataflöde för nätverk** | Inga | Data som överförs av servern, i MB per sekund.
**Typ av inbyggd programvara** | Inga | Serverns inbyggda programvara. Värden kan vara "BIOS" eller "UEFI".
**MAC-adress**| Inga | Server MAC-adress.


### <a name="add-operating-systems"></a>Lägg till operativsystem

Bedömning känner igen specifika operativsystemnamn. Alla namn som du anger måste exakt matcha en av strängarna i [namnlistan som stöds](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Lägga till flera diskar

Mallen innehåller standardfält för den första disken. Du kan lägga till liknande kolumner för upp till åtta diskar.

Om du till exempel vill ange alla fält för en andra disk lägger du till följande kolumner:

- Disk 2 storlek
- Disk 2 läs ops
- Disk 2 skriva ops
- Disk 2 läsflöde
- Skrivflöde för disk 2


## <a name="import-the-server-information"></a>Importera serverinformationen

När du har lagt till information i CSV-mallen importerar du servrarna till Serverutvärdering.

1. I Azure Migrate går du till den färdiga mallen i **Discover-datorer.**
2. Välj **Importera**.
3. Importstatusen visas.
    - Om varningar visas i statusen kan du antingen åtgärda dem eller fortsätta utan att åtgärda dem.
    - Förbättra serverinformationen i varningar om du vill förbättra bedömningsnoggrannheten.
    - Om du vill visa och åtgärda varningar väljer du **Hämta varningsinformation . CSV**. Den här åtgärden hämtar CSV med varningar ingår. Granska varningarna och åtgärda problem efter behov.
    - Om fel visas i statusen så att importstatusen **misslyckades**måste du åtgärda dessa fel innan du kan fortsätta med importen:
        1. Ladda ner CSV, som nu innehåller felinformation.
        1. Granska och åtgärda felen efter behov. 
        1. Ladda upp den ändrade filen igen.
4. När importstatusen är **slutförd**har serverinformationen importerats.

## <a name="update-server-information"></a>Uppdatera serverinformation

Du kan uppdatera informationen för en server genom att importera data för servern igen med samma **servernamn**. Du kan inte ändra fältet **Servernamn.** Det går inte att ta bort servrar.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrar i portalen

Så här verifierar du att servrarna visas i Azure-portalen efter identifiering:

1. Öppna instrumentpanelen för Azure Migrate.
2. På sidan **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** väljer du den ikon som visar antalet för identifierade **servrar**.
3. Välj fliken **Importera baserat.**

## <a name="set-up-and-run-an-assessment"></a>Ställa in och köra en utvärdering

Du kan skapa två typer av utvärderingar med hjälp av Serverutvärdering.

**Typ av bedömning** | **Detaljer** | **Data**
--- | --- | ---
**Prestationsbaserad** | Utvärderingar baserade på angivna prestandadatavärden. | **Rekommenderad VM-storlek:** Baserat på CPU- och minnesanvändningsdata.<br/><br/> **Rekommenderad disktyp (standard eller premiumhanterad disk)**: Baserat på indata/utdata per sekund (IOPS) och dataflöde för de lokala diskarna.
**Som lokalt** | Bedömningar baserade på lokal storlek. | **Rekommenderad VM-storlek:** Baserat på den angivna serverstorleken.<br/><br> **Rekommenderad disktyp:** Baserat på den lagringstyp som du väljer för utvärderingen.

Så här gör du en bedömning:

1. Granska [metodtipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. På fliken **Servrar** väljer du **Utvärdera**i panelen **Azure Migrate: Serverutvärdering.**

    ![Utvärdera](./media/tutorial-assess-physical/assess.png)

3. Ange ett namn för utvärderingen i **Bedöma servrar.**
4. I **Identifieringskälla**väljer du **Datorer som lagts till via import till Azure Migrate**.
5. Välj **Visa alla** om du vill granska bedömningsegenskaperna.

    ![Bedömningsegenskaper](./media/tutorial-assess-physical/view-all.png)

6. Välj **Skapa ny**i Markera eller skapa **en grupp**och ange ett gruppnamn. En grupp samlar ihop en eller flera virtuella datorer för bedömning.
7. I **Lägg till datorer i gruppen**väljer du servrar som ska läggas till i gruppen.
8. Välj **Skapa bedömning** om du vill skapa gruppen och kör sedan utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-physical/assessment-create.png)

9. När utvärderingen har skapats visar du den i **Servrar** > **Azure Migrera: Serverutvärderingsutvärderingar** > **Assessments**.
10. Välj **Exportera bedömning** om du vill hämta den som en Microsoft Excel-fil.

## <a name="review-an-assessment"></a>Granska en bedömning

En bedömning beskriver:

- **Azure-beredskap**: Om servrar är lämpliga för migrering till Azure.
- **Månatlig kostnadsuppskattning**: Uppskattade månatliga beräknings- och lagringskostnader för att köra servrarna i Azure.
- **Uppskattning av månatliga lagringskostnader**: Uppskattade kostnader för disklagring efter migreringen.

### <a name="view-an-assessment"></a>Visa en bedömning

1. I **Migreringsmålsservrar** > **Servers**väljer du **Utvärderingar** i **Azure Migrera: Serverutvärdering**.
2. Välj en bedömning för att öppna den i **Utvärderingar.**

    ![Sammanfattning av bedömning](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure-beredskap

1. I **Azure-beredskap**bestämmer du om servrarna är redo för migrering till Azure.
2. Granska status:
    - **Klar för Azure:** Azure Migrate rekommenderar en vm-storlek och kostnadsuppskattningar för virtuella datorer i utvärderingen.
    - **Redo med villkor**: Visar problem och föreslagen reparation.
    - **Inte redo för Azure**: Visar problem och föreslagna reparation.
    - **Okänd beredskap**: Azure Migrate kan inte bedöma beredskapen på grund av problem med datatillgänglighet.

3. Välj en **Azure-beredskapsstatus.** Du kan visa information om serverberedskap och öka detaljnivån för att se serverinformation, inklusive beräknings-, lagrings- och nätverksinställningar.

### <a name="review-cost-details"></a>Granska kostnadsinformation

Den här vyn visar den uppskattade beräknings- och lagringskostnaden för att köra virtuella datorer i Azure. Du kan:

- Granska de månatliga beräknings- och lagringskostnaderna. Kostnaderna aggregeras för alla servrar i den bedömda gruppen.

    - Kostnadsuppskattningar baseras på storleksrekommendationerna för en dator och dess diskar och egenskaper.
    - Beräknade månadskostnader för beräkning och lagring visas.
    - Kostnadsuppskattningen är för att köra de lokala servrarna som virtuella infrastruktur-som-en-tjänst (IaaS) virtuella datorer. Serverbedömningen tar inte hänsyn till PaaS-kostnader (Platform-as-a-Service) eller SaaS(Software-as-a-service).

- Granska månatliga uppskattningar av lagringskostnader. Den här vyn visar aggregerade lagringskostnader för den bedömda gruppen, uppdelade mellan olika typer av lagringsdiskar.
- Öka detaljnivån för att se information om specifika virtuella datorer.

> [!NOTE]
> Konfidensklassificeringar tilldelas inte utvärderingar av servrar som importeras till Serverutvärdering med hjälp av CSV.

## <a name="supported-operating-system-names"></a>Namn på operativsystem som stöds

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      Ms-dos
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Röd hatt Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity System eComStation 1<br/>
      Serenity System eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sön Mikrosystem Solaris 8<br/>
      Sön Mikrosystem Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Företag 10<br/>
      SUSE Linux Företag 11<br/>
      SUSE Linux Företag 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Företag 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Tröskelvärde för Windows Server<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Importerade servrar till Azure Migrate: Serverutvärdering med csv.
> * Skapade och granskade en bedömning.

Distribuera [nu en apparat](./migrate-appliance.md) för mer exakta bedömningar och samla servrar till grupper för djupare bedömning med hjälp av [beroendeanalys](./concepts-dependency-visualization.md).
