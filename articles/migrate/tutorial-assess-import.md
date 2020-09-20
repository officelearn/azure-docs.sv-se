---
title: Utvärdera servrar med hjälp av importerade serverdata i Serverutvärdering i Azure Migrate
description: Beskriver hur du utvärderar lokala servrar för migrering till Azure med Serverutvärdering i Azure Migrate med hjälp av importerade data.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: ff7e423063859a6cdc1a4362fb030c0deb75eb32
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658689"
---
# <a name="assess-servers-by-using-imported-data"></a>Utvärdera servrar med importerade data

Artikeln beskriver hur du utvärderar lokala servrar med verktyget [Azure Migrate: Serverutvärdering](migrate-services-overview.md#azure-migrate-server-assessment-tool) genom att importera servermetadata i CSV-format (kommaavgränsade värden). Utvärderingsmetoden kräver inte att du konfigurerar Azure Migrate-enheten för att kunna skapa en utvärdering. Metoden passar bra om:

- Du vill skapa en snabb första utvärdering innan du distribuerar enheten.
- Det inte går att distribuera Azure Migrate-enheten i din organisation.
- Du inte kan dela autentiseringsuppgifter som ger åtkomst till lokala servrar.
- Säkerhetsbegränsningar förhindrar att du samlar in och skickar data som samlas in av enheten till Azure. Du kan kontrollera vilka data du delar i en importerad fil. Dessutom är mycket av datan (till exempel IP-adresser) valfri.

## <a name="before-you-start"></a>Innan du börjar

Tänk på följande:

- Du kan lägga till maximalt 20 000 servrar i en enskild CSV-fil.
- Du kan lägga till maximalt 20 000 servrar i ett Azure Migrate-projekt med hjälp av CSV.
- Du kan ladda upp serverinformation till Serverutvärdering flera gånger med hjälp av CSV.
- Att samla in programinformation är användbart när du ska utvärdera din lokala miljö för migrering. Serverutvärderingen utför dock för närvarande inte någon utvärdering på programnivå, eller tar program i beaktande när en utvärdering skapas.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Fyll i serverinformation i en CSV-fil.
> * Importera filen för att lägga till serverinformationen i Serverutvärdering.
> * Skapa och granska en utvärdering.

> [!NOTE]
> I självstudierna visas den enklaste distributionsvägen för ett scenario, så att du snabbt kan konfigurera ett test. Självstudierna använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionerna.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="set-azure-permissions-for-azure-migrate"></a>Ange Azure-behörigheter för Azure Migrate

Ditt Azure-konto måste ha behörighet att skapa ett Azure Migrate-projekt.

1. I Azure-portalen öppnar du prenumerationen och väljer **Åtkomstkontroll (IAM)** .
2. Leta upp det relevanta kontot i **Kontrollera åtkomst** och välj det sedan för att se behörigheterna.
3. Kontrollera att du har behörigheten **Deltagare** eller **Ägare**.
    - Om du nyligen skapade ett kostnadsfritt Azure-konto är du ägare av prenumerationen.
    - Om du inte är prenumerationens ägare kan du be ägaren tilldela dig rollen.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Konfigurera ett nytt Azure Migrate-projekt:

1. I Azure-portalen i **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** under **Identifiera, utvärdera och migrera servrar**, klickar du på **Utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/tutorial-assess-import/assess-migrate.png)

4. I **Komma igång** väljer du **Lägg till verktyg**.
5. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
6. I **Projektinformation** anger du projektnamnet och det geografiska område där du vill skapa projektet. Mer information:

    - Granska de geografiska områden som stöds för [offentliga moln](migrate-support-matrix.md#supported-geographies-public-cloud) och [myndighetsmoln](migrate-support-matrix.md#supported-geographies-azure-government).
    - Du kan välja vilken målregion du vill när du kör en migrering.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-import/migrate-project.png)

7. Välj **Nästa**.
8. I **Välj utvärderingsverktyg** väljer du **Azure Migrate: Serverutvärdering** > **Nästa**.

    ![Skapa en Azure Migrate-utvärdering](./media/tutorial-assess-import/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. I **Granska och lägg till verktyg** granskar du inställningarna och väljer sedan **Lägg till verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.

## <a name="prepare-the-csv"></a>Förbereda CSV-filen

Ladda ned CSV-mallen och lägg till serverinformation i den.

### <a name="download-the-template"></a>Ladda ned mallen

1. I **Migreringsmål** > **Servrar** > **Azure Migrate: Serverutvärdering** väljer du **Identifiera**.
2. I **Identifiera datorer** väljer du **Importera med CSV**.
3. Välj **Ladda ned** för att ladda ned CSV-mallen. Du kan också [ladda ned den direkt](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Ladda ned CSV-mall](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Lägg till serverinformation

Samla in serverdata och lägg till den i CSV-filen.

- Samla in data genom att exportera den från verktyg du använder till lokal serverhantering, till exempel VMware vSphere eller din databas för konfigurationshantering (CMDB).
- Om du vill granska dataexemplen laddar du ned vår [exempelfil](https://go.microsoft.com/fwlink/?linkid=2108405).

Följande tabell sammanfattar de filfält som ska fyllas i:

**Fältnamn** | **Obligatorisk** | **Information**
--- | --- | ---
**Servernamn** | Yes | Vi rekommenderar att det fullständiga domännamnet (FQDN) anges.
**IP-adress** | No | Serveradress.
**Kärnor** | Yes | Antal processorkärnor som har allokerats till servern.
**Minne** | Yes | Totalt RAM-minne, i MB, som har allokerats till servern.
**Operativsystemets namn** | Yes | Serverns operativsystem. <br/> Operativsystemnamn som matchar eller innehåller namnen i [den här](#supported-operating-system-names) listan identifieras av utvärderingen.
**Operativsystemversion** | No | Serverns operativsystemversion.
**Operativsystemarkitektur** | No | Serverns operativsystemarkitektur <br/> Giltiga värden är: x64, x86, amd64, 32-bitars eller 64-bitars
**Antal diskar** | No | Behövs inte om separat diskinformation anges.
**Disk 1, storlek**  | No | Maximal diskstorlek i GB.<br/>Du kan lägga till information för fler diskar genom att [lägga till kolumner](#add-multiple-disks) i mallen. Du kan lägga till upp till åtta diskar.
**Disk 1, läsåtgärder** | No | Diskens läsåtgärder per sekund.
**Disk 1, skrivåtgärder** | No | Diskens skrivåtgärder per sekund.
**Disk 1, läsningsdataflöde** | No | Data som läses från disken per sekund, i MB per sekund.
**Disk 1, skrivningsdataflöde** | No | Data som skrivs till disk per sekund, i MB per sekund.
**Processoranvändning i procent** | No | Procentandelen CPU som används.
**Minnesanvändning i procent** | No | Procentandel RAM som används.
**Totalt antal diskar, läsåtgärder** | No | Läsåtgärder per sekund för alla diskar tillsammans. <br/> Använd det här fältet om du inte kan ange data på disknivå. 
**Totalt antal diskar, skrivåtgärder** | No | Skrivåtgärder per sekund för alla diskar tillsammans. <br/> Använd det här fältet om du inte kan ange data på disknivå.
**Totalt antal diskar, läsningsdataflöde** | No | Data som läses från alla diskar, i MB per sekund. <br/> Använd det här fältet om du inte kan ange data på disknivå. 
**Totalt antal diskar, skrivningsdataflöde** | No | Data som skrivs till alla diskar, i MB per sekund. <br/> Använd det här fältet om du inte kan ange data på disknivå.
**Inkommande nätverksdataflöde** | No | Data som tas emot av servern, i MB per sekund.
**Utgående nätverksdataflöde** | No | Data som skickas av servern, i MB per sekund.
**Typ av inbyggd programvara** | No | Serverns inbyggda programvara. Värdena kan vara ”BIOS” eller ”UEFI”.
**MAC-adress**| No | Serverns MAC-adress.


### <a name="add-operating-systems"></a>Lägga till operativsystem

Utvärderingen identifierar specifika operativsystemnamn. Det namn som du anger måste exakt matcha en av strängarna i [listan med namn som stöds](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Lägga till flera diskar

Mallen innehåller standardfält för den första disken. Du kan lägga till liknande kolumner för upp till åtta diskar.

Om du till exempel vill ange alla fält för en andra disk lägger du till följande kolumner:

- Disk 2, storlek
- Disk 2, läsåtgärder
- Disk 2, skrivåtgärder
- Disk 2, läsningsdataflöde
- Disk 2, skrivningsdataflöde

Om du inte kan ange data på disknivå, kan du ange diskens prestandadata per server med hjälp av nedanstående fält. Mer information om varje fält finns i [det här](#add-server-information) avsnittet.
- Totalt antal diskar, läsåtgärder
- Totalt antal diskar, skrivåtgärder
- Totalt antal diskar, läsningsdataflöde
- Totalt antal diskar, skrivningsdataflöde

## <a name="import-the-server-information"></a>Importera serverinformationen

När du har lagt till information i CSV-mallen, importerar du servrarna till Serverutvärdering.

1. I Azure Migrate, i **Identifiera datorer**, går du till den färdiga mallen.
2. Välj **Importera**.
3. Importstatusen visas.
    - Om det visas några varningar i statusen, kan du antingen åtgärda dem eller fortsätta utan att hantera dem.
    - Förbättra utvärderingsprecisionen genom att förbättra serverinformationen enligt förslagen i varningarna.
    - Om du vill visa och åtgärda varningar väljer du **Ladda ned varningsinformation .CSV**. Åtgärden hämtar CSV-filen med varningarna. Granska varningarna och åtgärda problemen vid behov.
    - Om fel visas i statusen så att importens status är **Misslyckades**, måste du åtgärda felen innan du kan fortsätta med importen:
        1. Ladda ned CSV-filen som nu innehåller felinformation.
        1. Granska och åtgärda felen vid behov. 
        1. Ladda upp den ändrade filen igen.
4. När importstatusen är **Slutförd** har serverinformationen importerats.

## <a name="update-server-information"></a>Uppdatera serverinformation

Du kan uppdatera informationen för en server genom att importera data för servern på nytt med samma **Servernamn**. Det går inte att ändra fältet **Servernamn**. Det finns för närvarande inget stöd för att ta bort servrar.

## <a name="verify-servers-in-the-portal"></a>Verifiera servrarna i portalen

Så här kontrollerar du att servrarna visas i Azure-portalen efter identifieringen:

1. Öppna instrumentpanelen för Azure Migrate.
2. På sidan **Azure Migrate – Servrar** > **Azure Migrate: Serverutvärdering** väljer du den ikon som visar antalet **Identifierade servrar**.
3. Välj fliken **Importbaserat**.

## <a name="set-up-and-run-an-assessment"></a>Konfigurera och köra en utvärdering

Du kan skapa två typer av utvärderingar med hjälp av Serverutvärdering.


**Utvärderingstyp** | **Information**
--- | --- 
**Azure VM** | Utvärderingar som migrerar dina lokala servrar till virtuella Azure-datorer. <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md), [virtuella Hyper-V-datorer](how-to-set-up-appliance-hyper-v.md) och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till Azure med denna utvärderingstyp.
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware Solution (AVS) med hjälp av den här utvärderingstypen.[Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

### <a name="sizing-criteria"></a>Storlekskriterier

Serverutvärderingen innehåller två alternativ för storleksändring:

**Storlekskriterier** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar som gör rekommendationer baserat på insamlade prestandadata | **Azure VM-utvärdering**: Rekommendationen för VM-storlek baseras på processor- och minnesanvändningsdata.<br/><br/> Rekommendation för disktyp (Standard HDD/SSD eller premiumhanterade diskar) baseras på IOPS och dataflödet för de lokala diskarna.<br/><br/> **Utvärdering med Azure VMware Solution (AVS)** : Rekommendationen för AVS-noder baseras på processor- och minnesanvändningsdata.
**Som lokalt** | Utvärderingar som inte använder prestandadata till att göra rekommendationer. | **Azure VM-utvärdering**: Rekommendationen för VM-storlek baseras på den lokala virtuella datorstorleken<br/><br> Rekommenderad disktyp baseras på vad du väljer i lagringstypens inställning vid utvärderingen.<br/><br/> **Utvärdering med Azure VMware Solution (AVS)** : Rekommendationen för AVS-noder baseras på den lokala virtuella datorstorleken.


Köra en utvärdering:

1. Granska [metodtipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. På fliken **Servrar** i panelen **Azure Migrate: Serverutvärdering** väljer du **Utvärdera**.

    ![Utvärdera](./media/tutorial-assess-physical/assess.png)

3. I **Utvärdera servrar** anger du utvärderingsnamnet och väljer typen för **utvärderingen** som *Azure VM* om du planerar att utföra utvärderingar av virtuella Azure-datorer, eller *Azure VMware Solution (AVS)* om du planerar att utföra AVS-utvärderingar.

    ![Grunder i utvärderingar](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. I **Identifieringskälla** väljer du **Datorer som har lagts till via import till Azure Migrate**.

5. Välj **Visa alla** för att granska utvärderingsegenskaperna.

    ![Utvärderingsegenskaper](./media/tutorial-assess-physical/view-all.png)

6. Klicka på **Nästa** och **Välj datorer att utvärdera**. I **Välj eller skapa en grupp** väljer du **Skapa ny** och anger ett gruppnamn. En grupp samlar en eller flera virtuella datorer för utvärdering.
7. I **Lägg till datorer i gruppen** väljer du de servrar som ska läggas till i gruppen.
8. Klicka på **Nästa** för att **Granska och skapa utvärdering** och granska utvärderingsinformationen.
9. Klicka på **Skapa utvärdering** för att skapa gruppen och kör sedan utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-physical/assessment-create.png)

9. När utvärderingen har skapats kan du se den i **Servrar** > **Azure Migrate: Serverutvärdering** > **Utvärderingar**.
10. Välj **Exportera utvärdering** för att ladda ned den som en Microsoft Excel-fil.

Om du vill veta mer om utvärderingar i **Azure VMware Solution (AVS)** kan du läsa [här](how-to-create-azure-vmware-solution-assessment.md). 

## <a name="review-an-azure-vm-assessment"></a>Granska en Azure VM-utvärdering

En Azure VM-utvärdering beskriver:

- **Azure-beredskap**: Om servrarna är lämpliga för migrering till Azure.
- **Uppskattad månadskostnad**: Uppskattade månatliga beräknings- och lagringskostnader för att köra servrarna i Azure.
- **Uppskattad månatlig lagringskostnad**: Uppskattade kostnader för disklagring efter migreringen.

### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **Migreringsmål** > **Servrar** väljer du **Utvärderingar** i **Azure Migrate: Serverutvärdering**.
2. I **Utvärderingar** väljer du en utvärdering för att öppna den.

    ![Sammanfattning av utvärdering](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure-beredskap

1. I **Azure-beredskap** kontrollerar du om servrarna är klara för migrering till Azure.
2. Granska statusen:
    - **Redo för Azure**: Azure Migrate rekommenderar en VM-storlek och kostnadsuppskattning för virtuella datorer i utvärderingen.
    - **Redo på vissa villkor**: Visar problem och rekommenderad åtgärd.
    - **Ej redo för Azure**: Visar problem och rekommenderad åtgärd.
    - **Beredskap okänd**: Azure Migrate kan inte utvärdera beredskapen på grund av problem med datatillgängligheten.

3. Välj en status för **Azure-beredskap**. Du kan se information om serverberedskap och öka detaljnivån för att se serverinformation, inklusive beräknings-, lagrings- och nätverksinställningar.

### <a name="review-cost-details"></a>Granska kostnadsinformation

Vyn visar uppskattad beräknings- och lagringskostnad för att köra de virtuella datorerna i Azure. Du kan:

- Granska kostnader för beräkning och lagring per månad. Kostnaderna aggregeras för alla servrar i den utvärderade gruppen.

    - Kostnadsuppskattningarna baseras på storleksrekommendationerna för en dator, samt dess diskar och egenskaper.
    - Uppskattade månatliga kostnader för beräkning och lagring visas.
    - Kostnadsberäkningen avser att köra de lokala servrarna som virtuella IaaS-datorer (infrastruktur som en tjänst). Serverutvärderingen tar inte med några kostnader för PaaS (plattform som en tjänst) eller SaaS (programvara som en tjänst).

- Granska uppskattad månadskostnad för lagring. I vyn visas aggregerade lagringskostnader för den utvärderade gruppen, uppdelade mellan olika typer av lagringsdiskar.
- Öka detaljnivån om du vill se mer information om en specifik virtuell dator.

> [!NOTE]
> Tillförlitlighetsklassificeringar har inte tilldelats till utvärderingar av servrar som importeras till Serverutvärdering med hjälp av CSV.

## <a name="supported-operating-system-names"></a>Namn på operativsystem som stöds

Operativsystemnamnen som anges i CSV-filen måste matcha eller innehålla namnen i den här listan. Detta är nödvändigt för att de namn som anges ska anses vara giltiga i utvärderingen.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A–H**
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
      **I–R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
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
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S–T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
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
      **U–Z**
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
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Importerade servrar till Azure Migrate: Serverutvärdering med hjälp av CSV.
> * Skapade och granskade en utvärdering.

Nu ska du [distribuera en enhet](./migrate-appliance.md) för att få mer exakta utvärderingar och samla servrar i grupper för djupare utvärdering med hjälp av [beroendeanalys](./concepts-dependency-visualization.md).
