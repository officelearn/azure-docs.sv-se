---
title: Identifiera och utvärdera lokala virtuella VMware-datorer för migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du identifierar och utvärderar lokala virtuella VMware-datorer för migrering till Azure med tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 06/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e8d4380087e826a4f1332c0a39670c2309a10861
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236152"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Utforska och utvärdera lokala virtuella VMware-datorer för migrering till Azure

[Azure Migrate](migrate-overview.md)-tjänsterna utvärderar lokala arbetsbelastningar för migrering till Azure.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett konto som Azure Migrate ska använda till att identifiera de lokala virtuella datorerna
> * Skapa ett Azure Migrate-projekt.
> * Konfigurera en lokal virtuell insamlardator för att identifiera lokala virtuella VMware-datorer som ska utvärderas.
> * Gruppera virtuella datorer och skapa en utvärdering.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Nödvändiga komponenter

- **VMware**: De virtuella datorer som du planerar att migrera måste hanteras av vCenter Server som kör version 5.5, 6.0 eller 6.5. Du måste dessutom ha en ESXi-värd som kör version 5.0 eller senare för att kunna distribuera den virtuella insamlardatorn.

> [!NOTE]
> Stöd för Hyper-V planeras och kommer att aktiveras inom kort.

- **vCenter Server-konto**: Du behöver ett skrivskyddat konto för att få åtkomst till vCenter Server. Azure Migrate använder kontot till att identifiera de lokala virtuella datorerna.
- **Behörigheter**: På vCenter Server måste du ha behörighet för att kunna skapa en virtuell dator genom att importera en fil i .OVA-format.
- **Statistikinställningar**: Statistikinställningarna för vCenter Server ska vara inställda på nivå 3 innan du startar distributionen. Om det är lägre än nivå 3 kommer utvärderingen att fungera, men prestandadata för lagring och nätverk samlas inte in. Storleksrekommendationerna i det här fallet kommer att göras baserat på prestandadata för CPU och minne, samt konfigurationsdata för disk och nätverkskort.

## <a name="create-an-account-for-vm-discovery"></a>Skapa ett konto för identifiering av VM

Azure Migrate måste ha åtkomst till VMware-servrar för att automatiskt kunna identifiera virtuella datorer för utvärdering. Skapa ett VMware-konto med följande egenskaper. Du kan ange det här kontot under konfigurationen av Azure Migrate.

- Användartyp: Minst en skrivskyddad användare
- Behörigheter: Datacenter-objekt –> Sprid till underordnat objekt, roll = skrivskyddad
- Information: Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.
- Om du vill begränsa åtkomsten tilldelar du rollen Ingen åtkomst med Sprid till underordnat objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-project"></a>Skapa ett projekt

1. Klicka på **Skapa en resurs** i Azure Portal.
2. Sök efter **Azure Migrate** och välj tjänsten **Azure Migrate** i sökresultaten. Klicka sedan på **Skapa**.
3. Ange ett projektnamn och Azure-prenumerationen för projektet.
4. Skapa en ny resursgrupp.
5. Ange platsen där du vill skapa projektet och klicka sedan på **Skapa**. Du kan bara skapa ett Azure Migrate-projekt i regionen USA, västra centrala eller USA, östra. Du kan dock fortfarande planera migreringen för alla Azure-platser. Den angivna platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer.

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)



## <a name="download-the-collector-appliance"></a>Hämta insamlingsprogrammet

Azure Migrate skapar en lokal virtuell dator som kallas för insamlarprogram. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om dem till Azure Migrate-tjänsten. Om du vill konfigurera insamlarprogrammet hämtar du en .OVA-fil och importerar den till den lokala vCenter-servern för att skapa den virtuella datorn.

1. I Azure Migrate-projektet klickar du på **Komma igång** > **Identifiera och utvärdera** > **Identifiera datorer**.
2. I **Identifiera datorer** klickar du på **Ladda ned** för att ladda ned .OVA-filen.
3. I **Kopiera projektautentiseringsuppgifterna** kopierar du projekt-ID och nyckel. Du behöver dem när du konfigurerar insamlaren.

    ![Hämta .OVA-fil](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Kontrollera insamlingsprogrammet

Kontrollera att .OVA-filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera en hash för OVA-filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Den genererade hashen måste matcha nedanstående inställningar.

  För OVA-version 1.0.9.8

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

    För OVA-version 1.0.9.7

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

    För OVA-version 1.0.9.5

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    För OVA-version 1.0.9.2

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

## <a name="create-the-collector-vm"></a>Skapa den virtuella insamlardatorn

Importera den nedladdade filen till vCenter Server.

1. I vSphere-klientkonsolen klickar du på **Arkiv** > **Distribuera OVF-mall**.

    ![Distribuera OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. I Guiden Distribuera OVF-mall > **Källa** anger du platsen för .OVA-filen.
3. I **Namn** och **Plats** anger du ett eget namn för den virtuella insamlardatorn och lagerobjektet där den virtuella datorn kommer att finnas.
5. I **Värd/kluster** anger du den värd eller det kluster där den virtuella insamlardatorn körs.
7. Ange lagringsplats för den virtuella insamlardatorn i lagringen.
8. I **Diskformat** anger du disktyp och storlek.
9. I **Nätverksmappning** anger du det nätverk som den virtuella insamlardatorn kommer att ansluta till. Nätverket måste ha en Internetanslutning för att kunna skicka metadata till Azure.
10. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.

## <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2. Ange språk, tidszon och lösenordsinställningar för produkten.
3. På skrivbordet klickar du på genvägen **Kör insamlare**.
4. Klicka på **Sök efter uppdateringar** i det översta fältet i UI-insamlaren och kontrollera att insamlaren körs med den senaste versionen. Du kan annars ladda ner det senaste uppgraderingspaketet från länken och uppdatera insamlaren.
5. I Azure Migrate Collector öppnar du **Set up prerequisites** (Ange förutsättningar).
    - Acceptera licensvillkoren och läs informationen från tredje part.
    - Insamlaren kontrollerar att den virtuella datorn har Internetåtkomst.
    - Om den virtuella datorn har åtkomst till Internet via en proxy, klickar du på **Proxyinställningar** där du anger proxyadress och lyssningsport. Ange autentiseringsuppgifter om proxyn kräver autentisering. [Lär dig mer](https://docs.microsoft.com/en-us/azure/migrate/concepts-collector#internet-connectivity) om Internetanslutningskraven och URL-listan som insamlaren använder.

    > [!NOTE]
    > Proxyadressen måste anges i formatet http://ProxyIPAddress eller http://ProxyFQDN. Endast HTTP-proxy stöds.

    - Insamlaren kontrollerar att insamlingstjänsten körs. Tjänsten installeras som standard på den virtuella insamlardatorn.
    - Ladda ned och installera VMware PowerCLI.

6. Gör följande i **Specify vCenter Server details** (Ange vCenter Server-information):
    - Ange namn (FQDN) eller IP-adress för vCenter-servern.
    - I **Användarnamn** och **Lösenord** anger du de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer på vCenter-servern.
    - I **Samlingens omfattning** väljer du en omfattning för identifieringen av virtuella datorer. Insamlaren kan bara identifiera virtuella datorer i angivet omfång. Omfånget kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1 500 virtuella datorer. [Lär dig mer](how-to-scale-assessment.md) om hur du kan identifiera en större miljö.

7. I **Specify migration project** (Ange migreringsprojekt) anger du det projekt-ID och den nyckel för Azure Migrate som du kopierade från portalen. Om du inte kopierade dem öppnar du Azure Portal från den virtuella insamlardatorn. På projektsidan **Översikt** klickar du på **Identifiera datorer** och kopierar värdena.  
8. I **View collection progress** (Visa insamlingsförlopp) övervakar du identifieringen och kontrollerar att de metadata som samlas in från virtuella datorer finns i ett omfång. Insamlaren visar en ungefärlig identifieringstid. [Lär dig mer](https://docs.microsoft.com/en-us/azure/migrate/concepts-collector#what-data-is-collected) om vilka data som samlas in av Azure Migrate-insamlaren.

> [!NOTE]
> Insamlaren har endast stöd för ”Engelska (USA)” som operativsystemspråk och gränssnittsspråk i insamlaren. Stöd för fler språk kommer snart.


### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Identifieringstiden beror på hur många virtuella datorer du identifierar. Vid 100 virtuella datorer tar det cirka en timme att slutföra identifieringen efter att insamlaren är klar.

1. I projektet Migreringshanteraren klickar du på **Hantera** > **Datorer**.
2. Kontrollera att de virtuella datorer som du vill identifiera visas i portalen.


## <a name="create-and-view-an-assessment"></a>Skapa och visa en utvärdering

Efter att virtuella datorer har identifierats, kan du gruppera dem och skapa en utvärdering.

1. På projektsidan **Översikt** klickar du på **+Skapa utvärdering**.
2. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.
3. Skapa gruppen och ange ett gruppnamn.
4. Välj de datorer som du vill lägga till i gruppen.
5. Klicka på **Skapa utvärdering** för att skapa gruppen och utvärderingen.
6. När utvärderingen har skapats kan du se den i **Översikt** > **Instrumentpanel**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.

### <a name="assessment-details"></a>Information om utvärdering

En utvärdering innehåller information om ifall de lokala virtuella datorerna är kompatibla med Azure, vad som är rätt VM-storlek för att köra den virtuella datorn i Azure samt de beräknade kostnaderna för Azure per månad.

![Utvärderingsrapport](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure-beredskap

Vyn för Azure-beredskap i utvärderingen visar beredskapsstatus för varje virtuell dator. Beroende på egenskaperna för den virtuella datorn kan varje virtuell dator markeras som:
- Redo för Azure
- Villkorligt redo för Azure
- Ej redo för Azure
- Beredskap okänd

För förberedda virtuella datorer rekommenderar Azure Migrate en VM-storlek i Azure. Storleksrekommendationen som utförs av Azure Migrate är beroende av storlekskriteriet som anges i utvärderingsegenskaperna. Om storlekskriteriet är prestandabaserat utförs storleksrekommendationen med hänsyn till de virtuella datorerna (CPU och minne) och diskarnas (IOPS och dataflöde) prestandahistorik. Om storlekskriteriet är ”på plats” tar Azure Migrate inte hänsyn till prestandadata för den virtuella datorn och diskarna. Rekommendationen för storleken på de virtuella datorerna i Azure görs genom att titta på storleken på virtuella datorer på plats och diskstorleken baseras på lagringstypen som angetts i utvärderingsegenskaperna (premiumdiskar är standard). [Lär dig mer](concepts-assessment-calculation.md) om storlekar i Azure Migrate.

För virtuella datorer som inte är redo eller endast är villkorligt redo för Azure beskriver Azure Migrate beredskapsproblem och visar de reparationssteg som kan utföras.

De virtuella datorer för vilka Azure Migrate inte kan identifiera Azure-beredskap (eftersom det inte finns data) markeras som ”beredskap okänd”.

Förutom Azure-beredskap och storlek föreslår Azure Migrate även verktyg som du kan använda för att migrera den virtuella datorn. Detta kräver en djupare identifiering av den lokala miljön. [Lär dig mer](how-to-get-migration-tool.md) om hur du kan göra en djupare identifiering genom att installera agenter på lokala datorer. Om agenterna inte är installerade på de lokala datorerna, föreslås Lift and Shift-migrering med [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Om agenterna är installerade på den lokala datorn, tittar Azure Migrate på de processer som körs i datorn och identifierar om datorn är en databasdator eller inte. Om datorn är en databasdator föreslås [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview), annars föreslås Azure Site Recovery som migreringsverktyg.

  ![Utvärderingsberedskap](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Uppskattad månadskostnad

Den här vyn visar total beräknings- och lagringskostnad för att köra de virtuella datorerna i Azure, samt information för varje dator. Kostnaden beräknas utifrån de storleksrekommendationer som görs av Azure Migrate för en dator, dess diskar samt utvärderingsegenskaper.

> [!NOTE]
> Kostnadsberäkningen från Azure Migrate gäller för att köra de lokala virtuella datorerna som virtuella Azure IaaS-datorer (Infrastruktur som en tjänst). Azure Migrate beräknar inte några kostnader för PaaS (Plattform som en tjänst) eller SaaS (Programvara som en tjänst).

Uppskattade månatliga kostnader för beräkning och lagring sammanställs för alla virtuella datorer i gruppen.

![Utvärdering av VM-kostnad](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Säkerhetsomdöme

Varje utvärdering i Azure Migrate är kopplad till ett säkerhetsomdöme i intervallet 1 stjärna till 5 stjärnor (1 stjärna är lägst och 5 stjärnor är högst). Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen. Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate.

Förtroendeklassificering av en bedömning är mer användbar för bedömningar där storlekskriteriet är prestandabaserad storleksändring. För prestandabaserade storleksändringar behöver Azure Migrate användningsdata för CPU, minne och den virtuella datorn. För varje disk som är ansluten till den virtuella datorn krävs dessutom information om IOPS och dataflöden. Precis som för varje nätverkskort som är kopplat till en virtuell dator så måste Azure Migrate ha åtkomst till nätverkets in-/utdata för att utföra prestandabaserade storleksändringar. Om några av ovanstående användningsnummer inte är tillgängliga i vCenter Server så är kanske storleksrekommendationen från Azure Migrate inte är tillförlitlig. Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls säkerhetsomdömet för utvärderingen, som du ser nedan:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

En utvärdering kanske inte har tillgång till alla datapunkter på grund av någon av följande orsaker:
- Statistikinställningen i vCenter Server har inte angetts till nivå 3. Om statistikinställningen i vCenter Server är lägre än nivå 3 så görs ingen insamling av prestandadata för disk och nätverk från vCenter Server. I det här fallet är rekommendationen från Azure Migrate för disk och nätverk inte användningsbaserad. Utan att överväga diskens IOPS/dataflöde kan inte Azure Migrate veta om disken behöver en Premium-disk i Azure. Därför rekommenderar Azure Migrate Standard-diskar för alla diskar.
- Statistikinställningen i vCenter Server var inställd på nivå 3 under en kort period, innan identifieringen drog igång. Vi tänker oss exempelvis ett scenario där du ändrar statistikinställningen till nivå 3 i dag och sätter igång identifieringen med insamlingsprogrammet i morgon (efter 24 timmar). Om du skapar en utvärdering för en dag, har du alla datapunkter och säkerhetsomdömet för utvärderingen blir 5 stjärnor. Men om du ändrar varaktigheten i utvärderingsegenskaperna till en månad går säkerhetsomdömet ned om prestandadata från disken och nätverket för den senaste månaden inte är tillgängliga. Om du vill undersöka prestandadata för den senaste månaden rekommenderar vi att du behåller statistikinställningen för vCenter Server på nivå 3 i en månad innan du startar identifieringen.
- Några virtuella datorer stängdes av under perioden som utvärderingen utfördes. Om några virtuella datorer stängdes av under en viss period så kommer vCenter Server inte att ha prestandadata för den perioden.
- Några virtuella datorer skapades under perioden som utvärderingen utförs. Om du till exempel skapar en utvärdering för prestandahistoriken för den senaste månaden, men några virtuella datorer skapades i miljön för en vecka sedan. I sådana fall är prestandahistoriken för de nya virtuella datorerna inte med för hela perioden.

> [!NOTE]
> Om säkerhetsomdömet för någon utvärdering är lägre än 4 stjärnor rekommenderar vi att du ändrar nivån för statistikinställningar i vCenter Server till 3, väntar så länge som du vill att utvärderingen ska utvärdera (en dag/en vecka/en månad) och sedan utför en identifiering och en utvärdering. Om det föregående inte kan utföras kan prestandabaserade storleksändringar vara mindre tillförlitliga och därför rekommenderar vi att du byter till *storleksändringar av typen "som lokalt"* genom att ändra utvärderingsegenskaperna.

## <a name="next-steps"></a>Nästa steg

- [Lär dig](how-to-modify-assessment.md) hur du anpassar en bedömning baserat på dina krav.
- Lär dig hur du skapar utvärderingsgrupper med hög exakthet med hjälp av [datorberoende mappning](how-to-create-group-machine-dependencies.md)
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
- [Läs](how-to-scale-assessment.md) hur du identifierar och utvärderar en stor VMware-miljö.
- [Läs mer](resources-faq.md) om vanliga frågor och svar för Azure Migrate
