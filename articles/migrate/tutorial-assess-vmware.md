---
title: Utvärdera virtuella VMware-datorer med Azure Migrate Server-utvärdering
description: Beskriver hur du bedömer lokala virtuella VMware-datorer för migrering till Azure med hjälp av Azure Migrate Server bedömning.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 8c2784e999e751972883b6c9ffba2485bb9fe9e1
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950093"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>Utvärdera virtuella VMware-datorer med Server Assessment

Den här artikeln visar hur du kan utvärdera lokala virtuella VMware-datorer (VM: ar) med hjälp av verktyget [Azure Migrate: Server bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) .


Den här självstudien är den andra i en serie som visar hur du bedömer och migrerar virtuella VMware-datorer till Azure. I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera en Azure Migrate-apparat som körs lokalt för att utvärdera de virtuella datorerna.
> * Starta kontinuerlig identifiering av lokala virtuella datorer. Enheten skickar konfigurations-och prestanda data för identifierade virtuella datorer till Azure.
> * Gruppera identifierade virtuella datorer och utvärdera VM-gruppen.
> * Granska utvärderingen.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktions artiklar.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- [Slutför den första självstudien](tutorial-prepare-vmware.md) i den här serien. Om du inte gör det fungerar inte instruktionerna i den här självstudien.
- Det här är vad du behöver göra i den första självstudien:
    - [Förbered Azure](tutorial-prepare-vmware.md#prepare-azure) för att arbeta med Azure Migrate.
    - [Förbered VMware för utvärdering](tutorial-prepare-vmware.md#prepare-for-assessment) av utvärdering. Detta innefattar att kontrol lera VMware-inställningar, konfigurera ett konto som Azure Migrate kan använda för att få åtkomst till vCenter Server.
    - [Kontrol lera](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) vad du behöver för att kunna distribuera Azure Migrate-installationen för VMware-utvärdering.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Konfigurera ett nytt Azure Migrate projekt enligt följande:

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt**, under **Upptäck, utvärdera och migrera servrar**, väljer du **utvärdera och migrera servrar**.

   ![Knapp för att utvärdera och migrera servrar](./media/tutorial-assess-vmware/assess-migrate.png)

4. I **komma igång**väljer du **Lägg till verktyg**.
5. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.     
6. I **projekt information**anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Rutor för projekt namn och region](./media/tutorial-assess-vmware/migrate-project.png)

7. Välj **Nästa**.
8. I **Välj bedömnings verktyg**väljer du **Azure Migrate: Server utvärdering**  >  **Nästa**.

   ![Val för Server bedömnings verktyget](./media/tutorial-assess-vmware/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. I **Granska + Lägg till verktyg**granskar du inställningarna och väljer **Lägg till verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate-enheten

Azure Migrate: Server utvärderingen använder en förenklad Azure Migrate-apparat. Installationen utför VM-identifiering och skickar metadata för virtuella datorer och prestanda data till Azure Migrate. Installationen kan ställas in på flera olika sätt.

- Konfigurera på en virtuell VMware-dator med en Hämtad mall för ägg. **Detta är den metod som används i den här självstudien.**
- Konfigurera på en virtuell VMware-dator eller en fysisk dator med ett PowerShell-skript. [Den här metoden](deploy-appliance-script.md) ska användas om du inte kan konfigurera en virtuell dator med en behållar mall eller om du är i Azure Government.

När du har skapat enheten kontrollerar du att den kan ansluta till Azure Migrate: Server utvärdering, konfigurera den för första gången och registrera den med Azure Migrate projektet.


### <a name="generate-the-azure-migrate-project-key"></a>Generera Azure Migrate projekt nyckel

1. I **mål**  >  **servrar**för migrering  >  **Azure Migrate: Server utvärdering**väljer du **identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?** väljer du **Ja, med VMware vSphere hypervisor**.
3. I **1: generera Azure Migrate projekt nyckel**anger du ett namn för Azure Migrate-installationen som ska konfigureras för identifiering av virtuella VMware-datorer. namnet måste vara alfanumeriskt med 14 tecken eller färre.
1. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera datorer när du skapar resurser.
1. När Azure-resurserna har skapats skapas en **Azure Migrate projekt nyckel** .
1. Kopiera nyckeln på samma sätt som du behöver den för att slutföra registreringen av enheten under konfigurationen.

### <a name="download-the-ova-template"></a>Ladda ned mallen för ägg
I **2: Ladda ned Azure Migrate-enheten**väljer du. ÄGG filen och klicka på **Hämta**. 


   ![Val för identifiering av datorer](./media/tutorial-assess-vmware/servers-discover.png)


   ![Val för generera nyckel](./media/tutorial-assess-vmware/generate-key-vmware.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att ägg filen är säker innan du distribuerar den:

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den ägg filen:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Kontrol lera de senaste versions-och hash-värdena för produkten:

    - För det offentliga Azure-molnet:
    
        **Integritetsalgoritm** | **Ladda ned** | **SHA256**
        --- | --- | ---
        VMware (11,6 GB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - För Azure Government:
    
        **Integritetsalgoritm** | **Ladda ned** | **SHA256**
        --- | --- | ---
        VMware (85 MB) | [Senaste version](https://go.microsoft.com/fwlink/?linkid=2140337) | 7dab9445a89b47302994d6de4caddaa092c1c582c8f3c1fc5b9c4908c7d2f9f7


### <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den nedladdade filen och skapa en virtuell dator:

1. I klient konsolen för vSphere väljer du **fil**  >  **distribution OVF-mall**.

   ![Meny kommando för att distribuera en OVF-mall](./media/tutorial-assess-vmware/deploy-ovf.png)

2. I guiden Distribuera OVF-mall > **källa**anger du platsen för ägg filen.
3. I **namn** och **plats**anger du ett eget namn för den virtuella datorn. Välj det lager objekt som den virtuella datorn ska vara värd för.
4. I **värd/kluster**anger du den värd eller det kluster som den virtuella datorn ska köras på.
5. I **lagring**anger du lagrings målet för den virtuella datorn.
6. I **Diskformat** anger du disktyp och storlek.
7. I **nätverks mappning**anger du det nätverk som den virtuella datorn ska ansluta till. Nätverket måste ha Internet anslutning för att kunna skicka metadata till Azure Migrate Server bedömning.
8. Granska och bekräfta inställningarna och välj sedan **Slutför**.

## <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns virtuella datorer kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.

### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

> [!NOTE]
> Om du ställer in installationen med hjälp av ett [PowerShell-skript](deploy-appliance-script.md) i stället för hämtade ägg, är de två första stegen i den här proceduren inte relevanta.

1. I klient konsolen för vSphere högerklickar du på den virtuella datorn och väljer sedan **Öppna konsol**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att välja genvägen till appen.
1. Godkänn **licens villkoren**och Läs informationen från tredje part.
1. I webbappen > **Konfigurera krav**gör du följande:
   - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
     - Klicka på **Konfigurera proxy** för att ange proxyadress (i formuläret http://ProxyIPAddress eller http://ProxyFQDN) lyssnande port.
     - Ange autentiseringsuppgifter om proxyn kräver autentisering.
     - Endast HTTP-proxy stöds.
     - Om du har lagt till proxyinformation eller inaktiverat proxyn och/eller autentiseringen, klickar du på **Spara** för att utlösa anslutnings kontrollen igen.
   - **Tidssynkronisering**: tiden för installationen bör vara synkroniserad med Internet-tid för att identifieringen ska fungera korrekt.
   - **Installera uppdateringar: installationen**säkerställer att de senaste uppdateringarna är installerade. När kontrollen är klar kan du klicka på **Visa apparat-tjänster** för att se status och versioner för komponenterna som körs på produkten.
   - **Installera vddk: installations**programmet kontrollerar att VMware vSphere Virtual Disk Development Kit (vddk) har installerats. Om den inte är installerad laddar du ned VDDK 6,7 från VMware och extraherar det hämtade ZIP-innehållet till den angivna platsen på enheten, enligt **anvisningarna i installations anvisningarna**.

     Azure Migrate Server-migrering använder VDDK för att replikera datorer under migreringen till Azure. 
1. Om du vill kan du **köra nödvändiga komponenter** när som helst under installationen av konfigurationen för att kontrol lera om installationen uppfyller alla krav.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klistra in **Azure Migrate projekt nyckeln** som har kopierats från portalen. Om du inte har nyckeln går du till **Server utvärdering> identifiera> hantera befintliga apparater**, väljer det installations namn som du angav vid tidpunkten för att generera nyckeln och kopierar motsvarande nyckel.
1. Klicka på **Logga**in. En Azure-inloggning visas i en ny flik i webbläsaren. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
1. På fliken nytt loggar du in med ditt användar namn och lösen ord för Azure.
   
   Inloggning med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen. 
4. Om Azure-användarkontot som används för loggning har rätt [behörigheter](tutorial-prepare-vmware.md#prepare-azure) för de Azure-resurser som skapades under den här nyckeln, initieras registrerings enheten.
1. När installationen av enheten har registrerats kan du se registrerings informationen genom att klicka på **Visa information**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Installations programmet måste ansluta till vCenter Server för att identifiera konfigurations-och prestanda data för de virtuella datorerna.

1. I **steg 1: ange vCenter Server autentiseringsuppgifter**klickar du på **Lägg till autentiseringsuppgifter** för att ange ett eget namn för autentiseringsuppgifter, Lägg till **användar namn** och **lösen ord** för det vCenter servers konto som ska användas för att identifiera virtuella datorer på vCenter Server-instansen.
    - Du bör ha skapat ett konto med de behörigheter som krävs i [föregående självstudie](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Om du vill begränsa identifieringen till specifika VMware-objekt (vCenter Server Data Center, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda virtuella datorer) läser du anvisningarna i [den här artikeln](set-discovery-scope.md) för att begränsa det konto som används av Azure Migrate.
1. I **steg 2: ange vCenter Server information**klickar du på **Lägg till identifierings källa** och väljer det egna namnet för autentiseringsuppgifter i list rutan. Ange **IP-adressen/FQDN** för vCenter Server-instansen. Du kan lämna **porten** till standard (443) eller ange en anpassad Port där vCenter Server lyssnar och klicka på **Spara**.
1. När du klickar på Spara kommer installations programmet att försöka verifiera anslutningen till vCenter Server med de angivna autentiseringsuppgifterna och visa **verifierings status** i tabellen mot vCenter Server IP-adress/FQDN.
1. Du kan **omverifiera** anslutningen till vCenter Server när som helst innan du påbörjar identifieringen.
1. I **steg 3: ange autentiseringsuppgifter för virtuella datorer för att identifiera installerade program och utföra en agent lös beroende mappning**, klickar du på **Lägg till autentiseringsuppgifter**och anger det operativ system för vilket autentiseringsuppgifterna anges, eget namn för autentiseringsuppgifter och **användar namn** och **lösen ord**. Klicka sedan på **Spara**.

    - Du kan också lägga till autentiseringsuppgifter här om du har skapat ett konto som ska användas för [program identifierings funktionen](how-to-discover-applications.md)eller [funktionen för beroende analys av agent](how-to-create-group-machine-dependencies-agentless.md).
    - Om du inte vill använda dessa funktioner kan du klicka på skjutreglaget för att hoppa över steget. Du kan ändra avsikten när som helst senare.
    - Granska de autentiseringsuppgifter som krävs för [identifiering av program](migrate-support-matrix-vmware.md#application-discovery-requirements)eller för analys av [beroenden för agenter](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Klicka på **Starta identifiering**för att starta identifiering av virtuell dator. När identifieringen har startats kan du kontrol lera identifierings statusen mot vCenter Server IP-adress/FQDN i tabellen.

Identifiering fungerar på följande sätt:
- Det tar ungefär 15 minuter för identifierade VM-metadata som visas i portalen.
- Det tar lite tid att identifiera installerade program, roller och funktioner. Varaktigheten beror på antalet virtuella datorer som identifieras. För 500 virtuella datorer tar det cirka en timme för program inventeringen att visas i Azure Migrate portalen.

### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Efter identifieringen kan du kontrol lera att de virtuella datorerna visas i Azure Portal:

1. Öppna instrument panelen för Azure Migrate.
2. I **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering**väljer du den ikon som visar antalet för **identifierade servrar**.

## <a name="set-up-an-assessment"></a>Konfigurera en utvärdering

Du kan skapa två typer av utvärderingar genom att använda Azure Migrate Server utvärdering:

**Bedömnings typ** | **Information**
--- | --- 
**Azure VM** | Utvärderingar för att migrera dina lokala servrar till Azure Virtual Machines. <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md), [virtuella Hyper-V-datorer](how-to-set-up-appliance-hyper-v.md)och [fysiska servrar](how-to-set-up-appliance-physical.md) för migrering till Azure med hjälp av den här utvärderings typen. [Läs mer](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Utvärderingar för att migrera dina lokala servrar till [Azure VMware-lösningen (AVS)](../azure-vmware/introduction.md). <br/><br/> Du kan utvärdera dina lokala [virtuella VMware-datorer](how-to-set-up-appliance-vmware.md) för migrering till Azure VMware-lösningen (AVS) med den här utvärderings typen. [Läs mer](concepts-azure-vmware-solution-assessment-calculation.md)

Server utvärderingen innehåller två alternativ för storleks ändring:

**Storleks villkor** | **Information** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar som gör rekommendationer baserat på insamlade prestanda data | **Utvärdering**av virtuell Azure-dator: rekommendation för virtuell dator baseras på data från processor-och minnes användning.<br/><br/> Disk typs rekommendation (standard HDD/SSD eller Premium-Managed Disks) baseras på IOPS och data flödet för lokala diskar.<br/><br/> **Azure VMware-lösning (AVS)-utvärdering**: AVS-nodernas rekommendation baseras på data från processor-och minnes användning.
**Som lokal** | Utvärderingar som inte använder prestanda data för att göra rekommendationer. | **Utvärdering**av virtuell Azure-dator: rekommendationen för VM-storlek baseras på den lokala virtuella dator storleken<br/><br> Den rekommenderade disk typen baseras på vad du väljer i inställningen lagrings typ för utvärderingen.<br/><br/> **Azure VMware Solution (AVS)-utvärdering**: AVS-nodernas rekommendation baseras på den lokala virtuella dator storleken.

## <a name="run-an-assessment"></a>Köra en utvärdering

Kör en *utvärdering av Azure VM* på följande sätt:

1. Gå igenom [metod tipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. På fliken **servrar** i panelen **Azure Migrate: Server bedömning** väljer du **utvärdera**.

   ![Utvärderings knappens placering](./media/tutorial-assess-vmware/assess.png)

3. I **utvärdera servrar**väljer du bedömnings typ som "Azure VM", väljer identifierings källa och anger bedömnings namnet.

    ![Grundläggande om bedömning](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. Välj **Visa alla**och granska sedan utvärderings egenskaperna.

   ![Bedömnings egenskaper](./media/tutorial-assess-vmware/view-all.png)

5. Klicka på **Nästa** för att **välja datorer att utvärdera**. I **Välj eller skapa en grupp**väljer du **Skapa ny**och anger ett grupp namn. En grupp samlar in en eller flera virtuella datorer för utvärdering.
6. I **Lägg till datorer i gruppen**väljer du de virtuella datorer som ska läggas till i gruppen.
7. Klicka på **Nästa** för att **Granska + skapa utvärdering** för att granska utvärderings informationen.
8. Välj **Skapa utvärdering** för att skapa gruppen och köra utvärderingen.

   ![Utvärdera servrar](./media/tutorial-assess-vmware/assessment-create.png)

8. När utvärderingen har skapats kan du Visa den på **servrar**  >  **Azure Migrate:**  >  **utvärderingar**av Server bedömning.
9. Välj **Exportera utvärdering** för att ladda ned den som en Excel-fil.

Om du vill köra en **Azure VMware-lösning (AVS)-utvärdering**följer du stegen som beskrivs [här](how-to-create-azure-vmware-solution-assessment.md).


## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskriver:

- **Azure-beredskap**: om virtuella datorer är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: beräknad kostnad för beräkning och lagring för de virtuella datorerna i Azure.
- **Kostnads uppskattning för månatlig lagring**: beräknade kostnader för disk lagring efter migrering.

Så här visar du en utvärdering:

1. I **mål**  >  **servrar**för migrering väljer du **utvärderingar** i **Azure Migrate: Server utvärdering**.
2. I **utvärderingar**väljer du en utvärdering för att öppna den.

   ![Utvärderings Sammanfattning](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure readiness

1. I **Azure-beredskap**kontrollerar du om de virtuella datorerna är klara för migrering till Azure.
2. Granska VM-statusen:
    - **Redo för Azure**: används när Azure Migrate rekommenderar en VM-storlek och kostnads uppskattning för virtuella datorer i utvärderingen.
    - **Klar med villkor**: visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: visar problem och förslag på åtgärder.
    - **Beredskap okänd**: används när Azure Migrate inte kan utvärdera beredskap på grund av data tillgänglighets problem.

3. Välj en status för **Azure-beredskap** . Du kan visa information om VM-beredskap. Du kan också öka detalj nivån för att se information om virtuella datorer, inklusive beräknings-, lagrings-och nätverks inställningar.

### <a name="review-cost-details"></a>Granska kostnads information

Utvärderings sammanfattningen visar den beräknade beräknings-och lagrings kostnaden för virtuella datorer som körs i Azure. Kostnaderna sammanställs för alla virtuella datorer i den utvärderade gruppen. Du kan öka detalj nivån för att se kostnads information för vissa virtuella datorer.

> [!NOTE]
> Kostnads uppskattningar baseras på storleks rekommendationerna för en dator, dess diskar och dess egenskaper. Uppskattningar används för att köra lokala virtuella datorer som virtuella IaaS-datorer. Azure Migrate Server-utvärderingen beaktar inte PaaS-eller SaaS-kostnader.

De aggregerade lagrings kostnaderna för den utvärderade gruppen delas av olika typer av lagrings diskar. 

### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

Azure Migrate Server Assessment tilldelar en tillförlitlig bedömning till en prestanda baserad utvärdering, från en stjärna (lägst) till fem stjärnor (högst).

![Säkerhetsomdöme](./media/tutorial-assess-vmware/confidence-rating.png)

Förtroende omdömet hjälper dig att beräkna tillförlitligheten för utvärderingens storleks rekommendationer. Omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen:

**Tillgänglighet för data punkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

[Lär dig mer om metod tips](best-practices-assessment.md#best-practices-for-confidence-ratings) för Tillförlitlighets klassificering.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapar du en Azure Migrate-apparat. Du har också skapat och granskat en utvärdering.

Om du vill lära dig hur du migrerar virtuella VMware-datorer till Azure med hjälp av Azure Migrate Server-migrering, fortsätter du till den tredje kursen i serien:

> [!div class="nextstepaction"]
> [Migrera virtuella VMware-datorer](./tutorial-migrate-vmware.md)
