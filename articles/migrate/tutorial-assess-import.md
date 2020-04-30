---
title: Utvärdera servrar med hjälp av importerade Server data med Azure Migrate Server utvärdering
description: Beskriver hur du bedömer lokala servrar för migrering till Azure med Azure Migrate Server bedömning med hjälp av importerade data.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 484dfd7834a206dce6805dc38b0eabeae2ee352a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82114572"
---
# <a name="assess-servers-by-using-imported-data"></a>Utvärdera servrar med hjälp av importerade data

Den här artikeln förklarar hur du bedömer lokala servrar med [Azure Migrate: Server utvärderings](migrate-services-overview.md#azure-migrate-server-assessment-tool) verktyget genom att importera serverns metadata i CSV-format (kommaavgränsade värden). Den här bedömnings metoden kräver inte att du konfigurerar Azure Migrate-installationen för att skapa en utvärdering. Det är användbart om:

- Du vill skapa en snabb, första utvärdering innan du distribuerar installationen.
- Du kan inte Distribuera Azure Migrate-enheten i din organisation.
- Du kan inte dela autentiseringsuppgifter som tillåter åtkomst till lokala servrar.
- Säkerhets begränsningar förhindrar att du samlar in och skickar data som samlas in av produkten till Azure. Du kan kontrol lera de data som du delar i en importerad fil. Dessutom är mycket av data (till exempel att tillhandahålla IP-adresser) valfria.

## <a name="before-you-start"></a>Innan du börjar

Tänk på följande punkter:

- Du kan lägga till upp till högst 20 000 servrar i en enda CSV-fil.
- Du kan lägga till upp till 20 000-servrar i ett Azure Migrate-projekt med hjälp av CSV.
- Du kan ladda upp Server information till Server utvärdering flera gånger med hjälp av CSV.
- Att samla in programinformation är användbart när du ska utvärdera din lokala miljö för migrering. Server utvärderingen utför dock för närvarande inte utvärdering på program nivå eller tar program i beaktande när en utvärdering skapas.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Fyll i en CSV-fil med Server information.
> * Importera filen för att lägga till Server information i Server utvärderingen.
> * Skapa och granska en utvärdering.

> [!NOTE]
> Självstudier visar dig den enklaste distributions vägen för ett scenario, så att du snabbt kan konfigurera ett koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktions guiderna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.

## <a name="set-azure-permissions-for-azure-migrate"></a>Ange Azure-behörigheter för Azure Migrate

Ditt Azure-konto måste ha behörighet att skapa ett Azure Migrate-projekt.

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och välj sedan det för att visa behörigheter.
3. Kontrol lera att du har behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Så här konfigurerar du ett nytt Azure Migrate-projekt:

1. I Azure Portal i **alla tjänster**söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt**, under **Upptäck, utvärdera och migrera servrar**, väljer du **utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/tutorial-assess-import/assess-migrate.png)

4. I **komma igång**väljer du **Lägg till verktyg**.
5. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
6. I **projekt information**anger du projekt namnet och geografin som du vill skapa projektet i. Mer information:

    - Granska de geografiska områden som stöds för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).
    - Du kan välja vilken målregion du vill när du kör en migrering.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-import/migrate-project.png)

7. Välj **Nästa**.
8. I **Välj bedömnings verktyg**väljer du **Azure Migrate: Server utvärdering** > **Nästa**.

    ![Skapa en Azure Migrate-utvärdering](./media/tutorial-assess-import/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. I **Granska och Lägg till verktyg**granskar du inställningarna och väljer sedan **Lägg till verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Sedan kommer du till sidan projekt. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.

## <a name="prepare-the-csv"></a>Förbered CSV

Hämta CSV-mallen och Lägg till Server information till den.

### <a name="download-the-template"></a>Ladda ned mallen

1. I **mål** > **servrar** > för migrering**Azure Migrate: Server utvärdering**väljer du **identifiera**.
2. I **identifiera datorer**väljer du **Importera med hjälp av CSV**.
3. Välj **Ladda ned** för att ladda ned CSV-mallen. Alternativt kan du [Ladda ned den direkt](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Hämta CSV-mall](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Lägg till Server information

Samla in Server data och Lägg till dem i CSV-filen.

- Om du vill samla in data kan du exportera dem från verktyg som du använder för lokal server hantering, till exempel VMware vSphere eller din databas för konfigurations hantering (CMDB).
- Om du vill granska exempel data laddar du ned [exempel filen](https://go.microsoft.com/fwlink/?linkid=2108405).

I följande tabell sammanfattas fil fälten som ska fyllas i:

**Fältnamn** | **Erforderlig** | **Information**
--- | --- | ---
**Server namn** | Ja | Vi rekommenderar att du anger det fullständigt kvalificerade domän namnet (FQDN).
**IP-adress** | Nej | Server adress.
**Kärnor** | Ja | Antal processor kärnor som har allokerats till servern.
**Minnesoptimerade** | Ja | Totalt RAM-minne, i MB, allokeras till servern.
**OS-namn** | Ja | Serveroperativ system. <br/> Operativ system namn som matchar eller innehåller namnen [i listan känns](#supported-operating-system-names) igen av utvärderingen.
**OS-version** | Nej | Serverns operativ system version.
**Antal diskar** | Nej | Behövs inte om enskilda disk uppgifter anges.
**Disk 1-storlek**  | Nej | Maximal disk storlek i GB.<br/>Du kan lägga till information om fler diskar genom [att lägga till kolumner](#add-multiple-disks) i mallen. Du kan lägga till upp till åtta diskar.
**Disk 1, Läs OPS** | Nej | Disk läsnings åtgärder per sekund.
**Disk 1 Skriv OPS** | Nej | Disk skrivnings åtgärder per sekund.
**Disk 1 Läs data flöde** | Nej | Data läses från disken per sekund, i MB per sekund.
**Disk 1 Skriv data flöde** | Nej | Data som skrivs till disk per sekund, i MB per sekund.
**Procent andel CPU-användning** | Nej | Procent andel CPU som används.
**Procent andel minnes användning** | Nej | Procent andelen RAM-minne som används.
**Totalt antal diskar Read OPS** | Nej | Disk läsnings åtgärder per sekund.
**Skriv Ops totalt antal diskar** | Nej | Disk-Skriv åtgärder per sekund.
**Totalt antal diskar läsnings data flöde** | Nej | Data läses från disken, i MB per sekund.
**Totalt antal diskar Skriv data flöde** | Nej | Data som skrivs till disk, i MB per sekund.
**Nätverk i data flöde** | Nej | Data som tagits emot av servern, i MB per sekund.
**Nätverks utflöde** | Nej | Data som överförs av servern, i MB per sekund.
**Typ av inbyggd program vara** | Nej | Serverns inbyggda program vara. Värdena kan vara "BIOS" eller "UEFI".
**MAC-adress**| Nej | Serverns MAC-adress.


### <a name="add-operating-systems"></a>Lägg till operativ system

Utvärderingen identifierar olika operativ system namn. Ett namn som du anger måste exakt matcha en av strängarna i [listan över namn som stöds](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Lägg till flera diskar

Mallen innehåller standard fält för den första disken. Du kan lägga till likartade kolumner för upp till åtta diskar.

Om du till exempel vill ange alla fält för en annan disk lägger du till följande kolumner:

- Disk 2-storlek
- Disk 2, Läs OPS
- Disk 2 Skriv OPS
- Disk 2 Läs data flöde
- Disk 2 Skriv data flöde


## <a name="import-the-server-information"></a>Importera Server informationen

När du har lagt till information i CSV-mallen importerar du servrarna till Server utvärderingen.

1. I Azure Migrate går du till den färdiga mallen i **identifiera datorer**.
2. Välj **Importera**.
3. Import status visas.
    - Om varningar visas i statusen kan du antingen åtgärda dem eller fortsätta utan att behöva adressera dem.
    - Förbättra utvärderings precisionen genom att förbättra Server informationen som föreslås i varningar.
    - Om du vill visa och åtgärda varningar väljer du **Hämta varnings information. CSV**. Den här åtgärden hämtar CSV med varningar som ingår. Granska varningarna och åtgärda problemen vid behov.
    - Om fel visas i statusen så att import status är **misslyckad**måste du åtgärda dessa fel innan du kan fortsätta med importen:
        1. Hämta CSV-filen som nu innehåller fel information.
        1. Granska och åtgärda felen vid behov. 
        1. Överför den ändrade filen igen.
4. När import statusen är **slutförd**har Server informationen importer ATS.

## <a name="update-server-information"></a>Uppdatera Server information

Du kan uppdatera informationen för en server genom att importera data för servern igen med samma **Server namn**. Du kan inte ändra fältet **Server namn** . Det finns för närvarande inte stöd för att ta bort servrar.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrar i portalen

Så här kontrollerar du att servrarna visas i Azure Portal efter identifiering:

1. Öppna instrument panelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar** > **Azure Migrate: Server utvärdering** väljer du den ikon som visar antalet för **identifierade servrar**.
3. Välj fliken **Importera baserad** .

## <a name="set-up-and-run-an-assessment"></a>Konfigurera och kör en utvärdering

Du kan skapa två typer av utvärderingar med hjälp av Server utvärdering.

**Bedömnings typ** | **Information** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar baserat på prestanda data värden som anges. | **Rekommenderad VM-storlek**: baserat på processor-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)**: baserat på indata/utdata per sekund (IOPS) och data flödet för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad storlek på virtuell dator**: baserat på angiven server storlek.<br/><br> **Rekommenderad disktyp**: baserat på den typ av lagrings typ som du väljer för utvärderingen.

Så här kör du en utvärdering:

1. Gå igenom [metod tipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. På fliken **servrar** i panelen **Azure Migrate: Server bedömning** väljer du **utvärdera**.

    ![Utvärdera](./media/tutorial-assess-physical/assess.png)

3. I **utvärdera servrar**anger du ett namn för utvärderingen.
4. I **identifierings källa**väljer **du datorer som har lagts till via import till Azure Migrate**.
5. Klicka på **Visa alla** för att granska utvärderings egenskaperna.

    ![Bedömnings egenskaper](./media/tutorial-assess-physical/view-all.png)

6. I **Välj eller skapa en grupp**väljer du **Skapa ny**och anger ett grupp namn. En grupp samlar in en eller flera virtuella datorer för utvärdering.
7. I **Lägg till datorer i gruppen**väljer du de servrar som ska läggas till i gruppen.
8. Välj **Skapa utvärdering** för att skapa gruppen och kör sedan utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-physical/assessment-create.png)

9. När utvärderingen har skapats kan du Visa den på **servrar** > **Azure Migrate:** > **utvärderingar**av Server bedömning.
10. Välj **export-utvärdering** för att ladda ned den som en Microsoft Excel-fil.

## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskriver:

- **Azure-beredskap**: om servrarna är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: Beräknad månatlig beräknings-och lagrings kostnad för att köra servrarna i Azure.
- **Kostnads uppskattning för månatlig lagring**: beräknade kostnader för disk lagring efter migrering.

### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **mål** > **servrar**för migrering väljer du **utvärderingar** i **Azure Migrate: Server utvärdering**.
2. I **utvärderingar**väljer du en utvärdering för att öppna den.

    ![Utvärderings Sammanfattning](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure readiness

1. I **Azure-beredskap**kontrollerar du om servrarna är klara för migrering till Azure.
2. Granska statusen:
    - **Redo för Azure**: Azure Migrate rekommenderar en VM-storlek och kostnads uppskattning för virtuella datorer i utvärderingen.
    - **Klar med villkor**: visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: visar problem och Rekommenderad reparation.
    - **Beredskap okänd**: Azure Migrate kan inte utvärdera beredskap på grund av data tillgänglighets problem.

3. Välj en status för **Azure-beredskap** . Du kan visa information om Server beredskap och öka detalj nivån för att se Server information, inklusive beräknings-, lagrings-och nätverks inställningar.

### <a name="review-cost-details"></a>Granska kostnads information

I den här vyn visas den beräknade beräknings-och lagrings kostnaden för virtuella datorer som körs i Azure. Du kan:

- Granska kostnader för beräkning och lagring per månad. Kostnaderna sammanställs för alla servrar i den utvärderade gruppen.

    - Kostnads uppskattningar baseras på storleks rekommendationerna för en dator och dess diskar och egenskaper.
    - Uppskattade månatliga kostnader för beräkning och lagring visas.
    - Kostnads uppskattningen är att köra lokala servrar som virtuella IaaS-datorer (Infrastructure-as-a-Service). Server utvärderingen betraktar inte PaaS-(Platform-as-a-Service) eller SaaS-kostnader (Software-as-a-Service).

- Granska månads Visa lagrings kostnader. I den här vyn visas aggregerade lagrings kostnader för den utvärderade gruppen, delas mellan olika typer av lagrings diskar.
- Granska nedåt för att se information om vissa virtuella datorer.

> [!NOTE]
> Tillförlitlighets klassificeringar har inte tilldelats utvärderingar av servrar som importeras till Server utvärdering med hjälp av CSV.

## <a name="supported-operating-system-names"></a>Operativ system namn som stöds

Operativ system namn som anges i CSV-filen måste matcha eller innehålla namnen i den här listan. Detta är nödvändigt för de namn som anges som giltiga av utvärderingen.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A – H**
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
      Core-Linux
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
      **I-R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      Initieringsfiler
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
      Red Hat-Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S-T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UNIX-7
   :::column-end:::
   :::column span="":::
      Serenity system eComStation 1<br/>
      Serenity system eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U-Z**
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
> * Importerade servrar till Azure Migrate: Server utvärdering med hjälp av CSV.
> * Skapade och granskade en utvärdering.

Nu kan du [distribuera en](./migrate-appliance.md) installation för mer exakta utvärderingar och samla in servrar i grupper för djupare utvärdering genom att använda [beroende analys](./concepts-dependency-visualization.md).
