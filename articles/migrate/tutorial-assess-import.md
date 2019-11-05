---
title: Utvärdera servrar med hjälp av importerade Server data med Azure Migrate Server utvärdering
description: Beskriver hur du bedömer lokala servrar för migrering till Azure med Azure Migrate Server bedömning med hjälp av importerade data.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 86d61dbd75363f68ee4651bfb36f940810909c00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501850"
---
# <a name="assess-servers-using-imported-data"></a>Utvärdera servrar med hjälp av importerade data

Den här artikeln förklarar hur du bedömer lokala servrar med [Azure Migrate: Server utvärdering](migrate-services-overview.md#azure-migrate-server-assessment-tool)genom att importera serverns metadata med hjälp av CSV. Med den här bedömnings metoden behöver du inte konfigurera Azure Migrate-enheten för att skapa en utvärdering. Detta är användbart om: 

- Du vill skapa en snabb första utvärdering innan du distribuerar enheten.
- Du kan inte Distribuera Azure Migrate-enheten i din organisation.
- Du kan inte dela autentiseringsuppgifter som tillåter åtkomst till lokala servrar.
- Säkerhets begränsningar förhindrar att du samlar in och skickar data som samlas in av produkten till Azure. Med en importerad fil kan du kontrol lera de data som du delar och många data (till exempel att tillhandahålla IP-adresser) är valfria.


## <a name="before-you-start"></a>Innan du börjar

Tänk på följande:

- Du kan lägga till upp till högst 20000 servrar i en enda CSV-fil.
- Du kan lägga till upp till 20000-servrar i ett Azure Migrate-projekt med hjälp av CSV.
- Du kan ladda upp Server information med CSV flera gånger för att Azure Migrate Server utvärderingen.
- Även om det är användbart att samla in programinformation när du utvärderar din lokala miljö för migrering, utför Azure Migrate Server utvärderingen för närvarande inte program nivå utvärdering och tar inte med program i beaktande när skapar en utvärdering.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Fyll i en CSV-fil med Server information.
> * Importera filen för att lägga till Server information i Azure Migrate Server utvärderingen.
> * Skapa och granska en utvärdering.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktions artiklar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="set-azure-permissions-for-azure-migrate"></a>Ange Azure-behörigheter för Azure Migrate 

Ditt Azure-konto måste ha behörighet att skapa ett Azure Migrate-projekt.

1. Öppna prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)** .
2. Leta upp det relevanta kontot i **kontrol lera åtkomst**och klicka på det för att visa behörigheter.
3. Du bör ha behörighet som **deltagare** eller **ägare** .
    - Om du precis har skapat ett kostnads fritt Azure-konto är du ägare till din prenumeration.
    - Om du inte är prenumerations ägare kan du samar beta med ägaren för att tilldela rollen.


## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Konfigurera ett nytt Azure Migrate-projekt enligt följande.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** under **Identifiera, utvärdera och migrera servrar** klickar du på **Utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/tutorial-assess-import/assess-migrate.png)

4. I **Komma igång** klickar du på **Lägg till verktyg**.
5. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.     
6. I **projekt information**anger du projekt namnet och geografin som du vill skapa projektet i.

    - [Granska](migrate-support-matrix.md#supported-geographies) stödda geografiska områden. Projektgeografin används bara för att lagra de metadata som samlas in från lokala virtuella datorer.
    - Du kan välja vilken målregion du vill när du kör en migrering.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-import/migrate-project.png)


7. Klicka på **Nästa**.
8. I **Välj bedömnings verktyg**väljer du **Azure Migrate: Server utvärdering** > **Nästa**.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-import/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. I **Granska + lägg till verktyg**
granskar du inställningarna och klickar på **Lägg till verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.


## <a name="prepare-the-csv"></a>Förbered CSV

Hämta CSV-mallen och Lägg till Server information till den.


### <a name="download-the-template"></a>Ladda ned mallen

1. I **mål för migrering** > **servrar** > **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **identifiera datorer**väljer du **Importera med. CSV**.
3. Klicka på **Ladda ned** för att ladda ned. CSV-mall. Alternativt kan du [Ladda ned den direkt](https://go.microsoft.com/fwlink/?linkid=2108404).

    ![Hämta. CSV-mall](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>Lägg till Server information

Samla in Server data och Lägg till dem i CSV-filen.

- Om du vill samla in data kan du exportera dem från verktyg som du använder för lokal server hantering, till exempel VMware vSphere eller din konfigurations hanterings databas (CMDB).
- Om du vill granska exempel data laddar du ned [exempel filen](https://go.microsoft.com/fwlink/?linkid=2108405).


I följande tabell sammanfattas de fil fält som ska fyllas i.

**Fält namn** | **Erforderlig** | **Information**
--- | --- | ---
**Servernamn** | Ja | Vi rekommenderar att du anger FQDN. 
**IP-adress** | Nej | Server adress.
**Antal kärnor** | Ja | Antalet processor kärnor som har allokerats till servern.
**Minnesoptimerade** | Ja | Totalt RAM-minne (MB) som allokerats till servern.
**OS-namn** | Ja | Serveroperativ system.
**Operativsystemversion** | Nej | Serverns operativ system version.
**Antal diskar** | Nej | Behövs inte om enskilda disk uppgifter anges.
**Disk 1-storlek**  | Nej | Maximal disk storlek (GB)<br/> Du kan lägga till information om fler diskar genom [att lägga till kolumner](#add-multiple-disks) i mallen. Du kan lägga till upp till åtta diskar.
**Disk 1, Läs OPS** | Nej | Disk läsnings åtgärder per sekund.
**Disk 1 Skriv OPS** | Nej | Disk skrivnings åtgärder per sekund.
**Disk 1 Läs data flöde** | Nej | Data läses från disken per sekund i MB per sekund.
**Disk 1 Skriv data flöde** | Nej | Data som skrivs till disk per sekund i MB per sekund.
**Procent andel CPU-användning** | Nej | PROCESSOR användning i procent.
**Procent andel minnes användning** | Nej | Användning av RAM-minne i procent.
**Totalt antal diskar Read OPS** | Nej | Disk läsnings åtgärder per sekund.
**Skriv Ops totalt antal diskar** | Nej | Disk skrivnings åtgärder per sekund.
**Totalt antal diskar läsnings data flöde** | Nej | Data läses från disken i MB per sekund.
**Totalt antal diskar Skriv data flöde** | Nej | Data som skrivs till disk i MB per sekund.
**Nätverk – inkommande data flöde** | Nej | Data som tagits emot av servern i MB per sekund.
**Nätverk – utgående data flöde** | Nej | Data som överförs av servern i MB per sekund.
**Typ av inbyggd program vara** | Nej | Serverns inbyggda program vara. Värdena kan vara "BIOS" eller "UEFI"
**Servertyp** | Nej | Värdena kan vara fysiska eller virtuella.
**Hypervisor** | Nej | Hypervisor som datorn körs på. <br/> Värdena kan vara "VMware", "Hyper-V", "xen", "AWS", "GCP" eller "other".
**Versions nummer för hypervisor** | Nej | Hypervisor-version.
**ID för virtuell dator** | Nej | VM-ID. Det här är **InstanceUUid** för VMware vCenter VM eller **ID för Hyper-v VM** för Hyper-v.
**Virtual Machine Manager-ID** | Nej | Detta är **InstanceUUid** för VMware vCenter. Krävs inte för Hyper-V.
**MAC-adress**| Nej | Serverns MAC-adress.
**BIOS-ID** | Nej | Serverns BIOS-ID.
**ID för anpassad server**| Nej | Lokala unika Server-ID: n lokalt. <br/> Användbart för att spåra den importerade servern med hjälp av lokalt ID. 
**Namn på program 1** | Nej | Namn på arbets belastningar som körs på servern.<br/> Du kan lägga till information för fler appar genom [att lägga till kolumner](#add-multiple-applications) i mallen. Du kan lägga till upp till fem program.
**Program 1-typ** | Nej | Typ av arbets belastning som körs på servern
**Program 1-version** | Nej | Den version av arbets belastningen som körs på servern.
**Licens förfallo datum för program 1** | Nej | Licensen upphör för arbets belastningen (om tillämpligt).
**Affär senhet** | Nej | Affär senheten som servern tillhör.
**Företags ägare** | Nej | Ägare av affär senhet.
**Affärs program namn** | Nej | Namnet på programmet som appen tillhör.
**Plats** | Nej | Data Center där-servern finns.
**Serverns inställnings datum** | Nej | Inställnings datum för den fysiska servern eller den underliggande fysiska servern för den virtuella servern

### <a name="add-operating-systems"></a>Lägg till operativ system

Utvärderingen identifierar olika operativ system namn. Alla operativ system namn som du anger måste matcha något av alternativen i listan över [namn som stöds](#supported-operating-system-names) .


### <a name="add-multiple-disks"></a>Lägg till flera diskar

Mallen innehåller standard fält för den första disken.  Du kan lägga till likartade kolumner för upp till 8 diskar. 

Om du till exempel vill ange alla fält för en annan disk lägger du till kolumnerna:

Disk 2 storlek disk 2 Läs Ops disk 2 Skriv Ops disk 2 Läs data flödes disk 2 Skriv data flöde

Du kan också lägga till vissa fält för en disk.


### <a name="add-multiple-applications"></a>Lägg till flera program

Mallen innehåller fält för ett enda program. Du kan lägga till likartade kolumner för upp till fem appar.  

Om du till exempel vill ange alla fält för en andra app lägger du till kolumnerna:

Program 2 namn program 2 Typ program 2 version 2 version program 2 licens upphör att gälla


Du kan också lägga till vissa fält för en app.

> [!NOTE]
> Information om appar är användbart när du utvärderar din lokala miljö för migrering. Azure Migrate Server utvärderingen utför dock för närvarande inte utvärdering på program nivå och tar inte appar i beaktande när en utvärdering skapas.


## <a name="upload-the-server-information"></a>Ladda upp Server informationen

När du har lagt till information i CSV-mallen importerar du servrarna till Azure Migrate: Server utvärdering.

1. Bläddra till den ifyllda mallen i Azure Migrate > **identifiera datorer**.
2. Klicka på **Importera**.
3. Import status visas. 
    - Om varningar visas i statusen kan du antingen åtgärda dem eller fortsätta utan att behöva adressera dem.
    - Att förbättra Server informationen som föreslås i varningar förbättrar utvärderings precisionen.
    - Om du vill visa och åtgärda varningar klickar du på **Hämta varnings information. CSV**. Detta laddar ned CSV-filen med varningar tillagda. Du kan granska varningarna och åtgärda problem vid behov. 
    Om fel visas i statusen (import status är **misslyckad**) måste du åtgärda dessa innan du kan fortsätta med importen. Det gör du genom att hämta CSV-filen som nu har lagt till fel information. Granska och åtgärda felen efter behov. Ladda sedan upp den ändrade filen igen.
4. När import statusen är **klar**importeras Server informationen.


> [!NOTE]
> Om du vill uppdatera Server information som laddats upp till Azure Migrate laddar du upp data för servern igen med samma **Server namn**. Observera att fältet **Server namn** inte kan ändras efter att mallen har importer ATS. Borttagning av servrar stöds inte för närvarande.

## <a name="updating-server-information"></a>Uppdaterar Server information

Du kan uppdatera en server information genom att överföra data för servern igen med samma **Server namn**. Du kan inte ändra fältet **Server namn** . 

Det finns för närvarande inte stöd för att ta bort servrar.

### <a name="verify-servers-in-the-portal"></a>Verifiera servrar i portalen

Efter identifieringen kan du kontrol lera att servrarna visas i Azure Portal.

1. Öppna instrument panelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar** > **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.
3. Klicka på fliken **Importera baserad** .

## <a name="set-up-an-assessment"></a>Konfigurera en utvärdering

Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: Server utvärdering.

**Utvärdering** | **Information** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar baserat på prestanda data värden som anges | **Rekommenderad VM-storlek**: baserat på processor-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)** : baserat på IOPS och data flödet för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad storlek på virtuell dator**: baserat på angiven server storlek<br/><br> **Rekommenderad disktyp**: baserat på den inställning för lagrings typ som du väljer för utvärderingen.


### <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. Gå igenom [metod tipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. Klicka på **utvärdera**i panelen **Azure Migrate: Server bedömning** på fliken **servrar** .

    ![Utvärdera](./media/tutorial-assess-physical/assess.png)

2. I **utvärdera servrar**anger du ett namn för utvärderingen.
3. I **identifierings källa**väljer **du datorer som lagts till via import till Azure Migrate**
3. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Bedömnings egenskaper](./media/tutorial-assess-physical/view-all.png)

3. I **Välj eller skapa en grupp**väljer du **Skapa ny**och anger ett grupp namn. En grupp samlar in en eller flera virtuella datorer för utvärdering.
4. I **Lägg till datorer i gruppen**väljer du de servrar som ska läggas till i gruppen.
5. Klicka på **Skapa utvärdering** för att skapa gruppen och kör utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-physical/assessment-create.png)

6. När utvärderingen har skapats kan du Visa den i **servrar** > **Azure Migrate: Server bedömning** > **utvärderingar**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.



## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskriver:

- **Azure-beredskap**: om servrarna är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: de uppskattade kostnaderna för beräkning och lagring för att köra servrarna i Azure.
- **Kostnads uppskattning för månatlig lagring**: beräknade kostnader för disk lagring efter migrering.

### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **mål för migrering** >  - **servrar**klickar du på **utvärderingar** i **Azure Migrate: Server utvärdering**.
2. I **bedömningar**klickar du på en utvärdering för att öppna den.

    ![Utvärderings Sammanfattning](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure readiness

1. I **Azure-beredskap**kontrollerar du om servrarna är klara för migrering till Azure.
2. Granska statusen:
    - **Redo för Azure**: Azure Migrate rekommenderar en VM-storlek och kostnads uppskattning för virtuella datorer i utvärderingen.
    - **Klar med villkor**: visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: visar problem och förslag på åtgärder.
    - **Beredskap okänd**: används när Azure Migrate inte kan utvärdera beredskap på grund av problem med data tillgänglighet.

2. Klicka på en status för **Azure-beredskap** . Du kan visa information om Server beredskap och öka detalj nivån för att se Server information, inklusive beräknings-, lagrings-och nätverks inställningar.

### <a name="review-cost-details"></a>Granska kostnads information

I den här vyn visas den beräknade beräknings-och lagrings kostnaden för virtuella datorer som körs i Azure.

1. Granska kostnader för beräkning och lagring per månad. Kostnaderna sammanställs för alla servrar i den utvärderade gruppen.

    - Kostnads uppskattningar baseras på storleks rekommendationerna för en dator och dess diskar och egenskaper.
    - Uppskattade månatliga kostnader för beräkning och lagring visas.
    - Kostnads uppskattningen är att köra lokala servrar som virtuella IaaS-datorer. Azure Migrate Server-utvärderingen beaktar inte PaaS-eller SaaS-kostnader.

2. Du kan granska månads beräkningarna för lagrings kostnader. Den här vyn visar aggregerade lagrings kostnader för den utvärderade gruppen och delas upp över olika typer av lagrings diskar.
3. Du kan öka detalj nivån för att se information om vissa virtuella datorer.

> [!NOTE]
> Tillförlitlighets klassificeringar har inte tilldelats utvärderingar av servrar som importer ATS till Azure Migrate Server utvärdering med hjälp av CSV.


## <a name="supported-operating-system-names"></a>Operativ system namn som stöds

Namn | Namn
--- | ---
**A – H** | 
Apple Mac OS X 10 | Asianux 3<br/>Asianux 4<br/>Asianux 5
CentOS<br/>CentOS 4/5 | Core-Linux 
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD 
**I-R** | 
IBM OS/2 | Initieringsfiler |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11 
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat-Fedora | 
**S-T** | 
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UNIX-7 | Serenity system eComStation 1<br/>Serenity system eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** | 
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Tröskelvärde för Windows Server<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional
    

## <a name="next-steps"></a>Nästa steg

I den här kursen för du göra följande:

> [!div class="checklist"]
> * Importerade servrar till Azure Migrate: Server utvärdering med hjälp av CSV.
> * Skapat och granskat en utvärdering

Nu kan du [distribuera en](./migrate-appliance.md) installation för mer exakta utvärderingar och samla in servrar i grupper för djupare bedömning med hjälp av [beroende analys](./concepts-dependency-visualization.md).
