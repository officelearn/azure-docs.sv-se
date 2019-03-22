---
title: Identifiera och utvärdera lokala virtuella VMware-datorer för migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du identifierar och utvärderar lokala virtuella VMware-datorer för migrering till Azure med tjänsten Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cdd852e56cf966371cda62f89cee62956551f5c0
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313168"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Utforska och utvärdera lokala virtuella VMware-datorer för migrering till Azure

[Azure Migrate](migrate-overview.md)-tjänsterna utvärderar lokala arbetsbelastningar för migrering till Azure.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett konto som Azure Migrate ska använda till att identifiera de lokala virtuella datorerna
> * Skapa ett Azure Migrate-projekt.
> * Konfigurera en lokal virtuell insamlardator för att identifiera lokala virtuella VMware-datorer som ska utvärderas.
> * Gruppera virtuella datorer och skapa en utvärdering.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- **VMware**: De virtuella datorer som du planerar att migrera måste hanteras av vCenter Server som kör version 5.5, 6.0, 6.5 eller 6.7. Du måste dessutom ha en ESXi-värd som kör version 5.5 eller senare för att kunna distribuera den virtuella insamlardatorn.
- **vCenter Server-konto**: Du behöver ett skrivskyddat konto för att få åtkomst till vCenter Server. Azure Migrate använder kontot till att identifiera de lokala virtuella datorerna.
- **Behörigheter**: På vCenter Server måste du ha behörighet för att kunna skapa en virtuell dator genom att importera en fil i .OVA-format.

## <a name="create-an-account-for-vm-discovery"></a>Skapa ett konto för identifiering av VM

Azure Migrate måste ha åtkomst till VMware-servrar för att automatiskt kunna identifiera virtuella datorer för utvärdering. Skapa ett VMware-konto med följande egenskaper. Du kan ange det här kontot under konfigurationen av Azure Migrate.

- Användartyp: Minst en skrivskyddad användare
- Behörigheter: Data Center-objekt –> Sprid till underordnat objekt, roll = skrivskyddad
- Information: Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.
- Om du vill begränsa åtkomsten tilldelar du rollen Ingen åtkomst med Sprid till underordnat objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).


## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-project"></a>Skapa ett projekt

1. Klicka på **Skapa en resurs** i Azure-portalen.
2. Sök efter **Azure Migrate** och välj tjänsten **Azure Migrate** i sökresultaten. Klicka sedan på **Skapa**.
3. Ange ett projektnamn och Azure-prenumerationen för projektet.
4. Skapa en ny resursgrupp.
5. Ange den geografiska plats där du vill skapa projektet och klicka sedan på **Skapa**. Du kan endast skapa ett Azure Migrate-projekt i nedanstående områden. Du kan dock fortfarande planera migreringen för alla Azure-platser. Den angivna geografiska platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer.

**Geografi** | **Lagringsplats**
--- | ---
Azure Government | Virginia (USA-förvaltad region)
Asien | Sydostasien
Europa | Europa, norra eller Europa, västra
USA | USA, östra eller USA, västra centrala

![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>Hämta insamlingsprogrammet

Azure Migrate skapar en lokal virtuell dator som kallas för insamlarprogram. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om dem till Azure Migrate-tjänsten. Om du vill konfigurera insamlarprogrammet hämtar du en .OVA-fil och importerar den till den lokala vCenter-servern för att skapa den virtuella datorn.

1. I Azure Migrate-projektet klickar du på **Komma igång** > **Identifiera och utvärdera** > **Identifiera datorer**.
2. I **Identifiera datorer** klickar du på **Ladda ned** för att ladda ned installationen.

    Azure Migrate-installationen kommunicerar med vCenter Server och profilerar kontinuerligt den lokala miljön för att samla in användningsdata i realtid för varje virtuell dator. Den samlar in högsta beräkningar för varje mått (processoranvändning, minnesanvändning osv.). Den här modellen är inte beroende av statistikinställningarna för vCenter Server för insamling av prestandadata. Du kan stoppa kontinuerlig profileringen när som helst från programmet.

    > [!NOTE]
    > Installationen för engångsidentifiering är nu inaktuell eftersom den här metoden förlitade sig på vCenter Servers statistikinställningarna för tillgänglighet av prestandadatapunkt och samlade in räknare för genomsnittlig prestanda, vilket resulterade i för små VM-storlekar för migrering till Azure.

    **Snabba bedömningar:** Tack vare installationen för kontinuerlig identifiering kan du omedelbart skapa utvärderingar när identifieringen är klar (det klar några timmar beroende på antalet virtuella datorer). Eftersom prestandadatainsamlingen startar när du påbörjar identifieringen bör du välja storlekskriteriet i utvärderingen som *lokalt* om du behöver göra en snabb bedömning. För prestandabaserade utvärderingar rekommenderas det att du väntar minst en dag efter att identifieringen har påbörjats för att få tillförlitliga storleksrekommendationer.

    Installationen samlar bara in prestandadata kontinuerligt. Den identifierar inte någon konfigurationsändring i den lokala miljön (det vill säga tillägg av virtuell dator, borttagning, disktillägg osv.). Om det finns en konfigurationsändring i den lokala miljön kan du göra följande för att återspegla ändringarna i portalen:

    - Tillägg av objekt (virtuella datorer, diskar, kärnor osv.): Om du vill återspegla dessa ändringar på Azure-portalen kan du stoppa identifieringen från installationen och sedan starta den igen. Då uppdateras ändringarna i Azure Migrate-projektet.

    - Borttagning av virtuella datorer: På grund av hur installationen är utformad återspeglas inte borttagning av virtuella datorer även om du stoppar och startar identifieringen. Det beror på att data från efterföljande identifieringar läggs till äldre identifieringar och inte åsidosätts. I det här fallet kan du helt enkelt ignorera den virtuella datorn genom att ta bort den från gruppen och beräkna utvärderingen.


3. I **Kopiera projektautentiseringsuppgifterna** kopierar du projekt-ID och nyckel. Du behöver dem när du konfigurerar insamlaren.

    ![Hämta .OVA-fil](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Kontrollera insamlingsprogrammet

Kontrollera att .OVA-filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera en hash för OVA-filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Den genererade hashen måste matcha nedanstående inställningar.

#### <a name="continuous-discovery"></a>Kontinuerlig identifiering

  För OVA version 1.0.10.11

  **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | 5f6b199d8272428ccfa23543b0b5f600
    SHA1 | daa530de6e8674a66a728885a7feb3b0a2e8ccb0
    SHA256 | 85da50a21a7a6ca684418a87ccc1dd4f8aab30152c438a17b216ec401ebb3a21

  För OVA-version 1.0.10.9

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | 169f6449cc1955f1514059a4c30d138b
  SHA1 | f8d0a1d40c46bbbf78cd0caa594d979f1b587c8f
  SHA256 | d68fe7d94be3127eb35dd80fc5ebc60434c8571dcd0e114b87587f24d6b4ee4d

  För OVA-version 1.0.10.4

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be


#### <a name="one-time-discovery-deprecated-now"></a>Engångsidentifiering (nu inaktuell)

Den här modellen är nu inaktuell. Stöd för befintliga installationer kommer att tillhandahållas.

  För OVA-version 1.0.9.15

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

  För OVA-version 1.0.9.14

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
  SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
  SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  För OVA-version 1.0.9.12

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | d0363e5d1b377a8eb08843cf034ac28a
  SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
  SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

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
   - Välj det Azure-moln som du planerar att migrera (Azure Global eller Azure Government).
   - Acceptera licensvillkoren och läs informationen från tredje part.
   - Insamlaren kontrollerar att den virtuella datorn har Internetåtkomst.
   - Om den virtuella datorn har åtkomst till Internet via en proxy, klickar du på **Proxyinställningar** där du anger proxyadress och lyssningsport. Ange autentiseringsuppgifter om proxyn kräver autentisering. [Lär dig mer](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-prerequisites) om kraven för Internetanslutning och den [lista över URL:er](https://docs.microsoft.com/azure/migrate/concepts-collector) som insamlaren använder.

     > [!NOTE]
     > Proxyadressen måste anges i formatet http:\//ProxyIPAddress eller http:\//ProxyFQDN. Endast HTTP-proxy stöds. Om du har en spärrande proxy kan Internetanslutningen misslyckas om du inte har importerat proxycertifikatet. [Läs mer](https://docs.microsoft.com/azure/migrate/concepts-collector) om hur du kan åtgärda detta genom att importera proxycertifikatet som ett betrott certifikat på den virtuella datorn för insamling.

   - Insamlaren kontrollerar att insamlingstjänsten körs. Tjänsten installeras som standard på den virtuella insamlardatorn.
   - Ladda ned och installera VMware PowerCLI.

6. Gör följande i **Specify vCenter Server details** (Ange vCenter Server-information):
    - Ange namn (FQDN) eller IP-adress för vCenter-servern.
    - I **Användarnamn** och **Lösenord** anger du de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer på vCenter-servern.
    - I **Samlingens omfattning** väljer du en omfattning för identifieringen av virtuella datorer. Insamlaren kan bara identifiera virtuella datorer i angivet omfång. Omfånget kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1 500 virtuella datorer. [Lär dig mer](how-to-scale-assessment.md) om hur du kan identifiera en större miljö.

7. I **Specify migration project** (Ange migreringsprojekt) anger du det projekt-ID och den nyckel för Azure Migrate som du kopierade från portalen. Om du inte kopierade dem öppnar du Azure Portal från den virtuella insamlardatorn. På projektsidan **Översikt** klickar du på **Identifiera datorer** och kopierar värdena.  
8. Övervaka identifieringsstatus i **visa insamlingsförloppet**. [Lär dig mer](https://docs.microsoft.com/azure/migrate/concepts-collector) om vilka data som samlas in av Azure Migrate-insamlaren.

> [!NOTE]
> Insamlaren har endast stöd för ”Engelska (USA)” som operativsystemspråk och gränssnittsspråk i insamlaren.
> Om du ändrar inställningarna på en dator som du vill utvärdera kör du en identifiering igen innan du kör utvärderingen. Det gör du genom att använda alternativet **Starta insamlingen igen** i insamlaren. När insamlingen är klar väljer du alternativet **Beräkna om** för utvärderingen på portalen för att hämta uppdaterade utvärderingsresultat.


### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Insamlingsinstallationen kommer att kontinuerligt profilera den lokala miljön och fortsätta skicka prestandadata med ett intervall på en timme. Du kan visa datorerna i portalen en timme efter att identifieringen påbörjades.

1. I migrationsprojektet klickar du på **Hantera** > **Datorer**.
2. Kontrollera att de virtuella datorer som du vill identifiera visas i portalen.


## <a name="create-and-view-an-assessment"></a>Skapa och visa en utvärdering

Efter att virtuella datorer har identifierats i portalen grupperar du dem och skapar utvärderingar. Du kan omedelbart skapa som lokala utvärderingar när de virtuella datorerna har identifierats i portalen. Det rekommenderas att du väntar minst en dag innan du skapar några prestandabaserade utvärderingar för att få tillförlitliga storleksrekommendationer.

1. På projektsidan **Översikt** klickar du på **+Skapa utvärdering**.
2. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.
3. Skapa gruppen och ange ett gruppnamn.
4. Välj de datorer som du vill lägga till i gruppen.
5. Klicka på **Skapa utvärdering** för att skapa gruppen och utvärderingen.
6. När utvärderingen har skapats kan du se den i **Översikt** > **Instrumentpanel**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.

> [!NOTE]
> Det rekommenderas starkt att du väntar minst en dag efter du har startat identifieringen innan du skapar en utvärdering. Om du vill uppdatera en befintlig utvärdering med de senaste prestandadata, kan du använda kommandot **beräkna om** för utvärdering för att uppdatera den.

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

Varje prestandabaserade utvärdering i Azure Migrate är kopplad till ett säkerhetsomdöme i intervallet 1 stjärna till 5 stjärnor (1 stjärna är lägst och 5 stjärnor är högst). Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen. Med säkerhetsomdömet kan du beräkna tillförlitligheten i de storleksrekommendationer som anges av Azure Migrate. Säkerhetsomdömet är inte tillämpligt på lokala utvärderingar ”i befintligt skick”.

För prestandabaserade storleksändringar behöver Azure Migrate användningsdata för CPU, minne och den virtuella datorn. För varje disk som är ansluten till den virtuella datorn krävs dessutom information om IOPS och dataflöden. Precis som för varje nätverkskort som är kopplat till en virtuell dator så måste Azure Migrate ha åtkomst till nätverkets in-/utdata för att utföra prestandabaserade storleksändringar. Om några av ovanstående användningsnummer inte är tillgängliga i vCenter Server så är kanske storleksrekommendationen från Azure Migrate inte är tillförlitlig. Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls säkerhetsomdömet för utvärderingen, som du ser nedan:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

En utvärdering kanske inte har tillgång till alla datapunkter på grund av någon av följande orsaker:

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du skapar utvärderingen med varaktigheten inställd på 1 dag, måste du vänta minst en dag efter att du börjar identifieringen för att alla datapunkter ska ha samlats in.

- Några virtuella datorer stängdes av under perioden som utvärderingen utfördes. Om några virtuella datorer stängdes av under en viss period så kommer vi inte att kunna samla in prestandadata för den perioden.

- Några virtuella datorer skapades under perioden som utvärderingen utförs. Om du till exempel skapar en utvärdering för prestandahistoriken för den senaste månaden, men några virtuella datorer skapades i miljön för en vecka sedan. I sådana fall är prestandahistoriken för de nya virtuella datorerna inte med för hela perioden.

> [!NOTE]
> Om förtroendeklassificeringen för någon utvärdering är mindre än 5 stjärnor väntar du minst en dag för att installationen ska profilera miljön och *beräknar sedan utvärderingen på nytt*. Om det föregående inte kan utföras kan prestandabaserade storleksändringar vara mindre tillförlitliga och därför rekommenderar vi att du byter till *storleksändringar av typen "som lokalt"* genom att ändra utvärderingsegenskaperna.

## <a name="next-steps"></a>Nästa steg

- [Lär dig](how-to-modify-assessment.md) hur du anpassar en bedömning baserat på dina krav.
- Lär dig hur du skapar utvärderingsgrupper med hög exakthet med hjälp av [datorberoende mappning](how-to-create-group-machine-dependencies.md)
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
- [Läs](how-to-scale-assessment.md) hur du identifierar och utvärderar en stor VMware-miljö.
- [Läs mer](resources-faq.md) om vanliga frågor och svar för Azure Migrate
