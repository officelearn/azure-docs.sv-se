---
title: "Identifiera och utvärdera lokala virtuella VMware-datorer för migrering till Azure med Azure Migrate | Microsoft Docs"
description: "Beskriver hur du identifierar och utvärderar lokala virtuella VMware-datorer för migrering till Azure med tjänsten Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 06/02/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0c82eeaeb17fb670b6d277d1b703b44b84343877
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Utforska och utvärdera lokala virtuella VMware-datorer för migrering till Azure

[Azure Migrate](migrate-overview.md)-tjänsterna utvärderar lokala arbetsbelastningar för migrering till Azure.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
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

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-project"></a>Skapa ett projekt

1. Klicka på **Skapa en resurs** i Azure Portal.
2. Sök efter **Azure Migrate** och välj tjänsten **Azure Migrate (förhandsversion)** i sökresultaten. Klicka sedan på **Skapa**.
3. Ange ett projektnamn och Azure-prenumerationen för projektet.
4. Skapa en ny resursgrupp.
5. Ange platsen där du vill skapa projektet och klicka sedan på **Skapa**. Du kan endast skapa ett Azure Migrate-projekt i regionen västra centrala USA i den här förhandsversionen. Du kan dock fortfarande planera migreringen för alla Azure-platser. Den angivna platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer. 

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
    
    För OVA-version 1.0.8.59

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | 71139e24a532ca67669260b3062c3dad
    SHA1 | 1bdf0666b3c9c9a97a07255743d7c4a2f06d665e
    SHA256 | 6b886d23b24c543f8fc92ff8426cd782a77efb37750afac397591bda1eab8656  

    För OVA-version 1.0.8.49
    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8 
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    För OVA-version 1.0.8.40:

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

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
4. I Azure Migrate Collector öppnar du **Set up prerequisites** (Ange förutsättningar).
    - Acceptera licensvillkoren och läs informationen från tredje part.
    - Insamlaren kontrollerar att den virtuella datorn har Internetåtkomst.
    - Om den virtuella datorn har åtkomst till Internet via en proxy, klickar du på **Proxyinställningar** där du anger proxyadress och lyssningsport. Ange autentiseringsuppgifter om proxyn kräver autentisering.

    > [!NOTE]
    > Proxyadressen måste anges i formatet http://ProxyIPAddress eller http://ProxyFQDN. Endast HTTP-proxy stöds.

    - Insamlaren kontrollerar att insamlingstjänsten körs. Tjänsten installeras som standard på den virtuella insamlardatorn.
    - Ladda ned och installera VMware PowerCLI.

5. Gör följande i **Specify vCenter Server details** (Ange vCenter Server-information):
    - Ange namn (FQDN) eller IP-adress för vCenter-servern.
    - I **Användarnamn** och **Lösenord** anger du de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer på vCenter-servern.
    - I **Samlingens omfattning** väljer du en omfattning för identifieringen av virtuella datorer. Insamlaren kan bara identifiera virtuella datorer i angivet omfång. Omfånget kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1 000 virtuella datorer. 

6. I **Specify migration project** (Ange migreringsprojekt) anger du det projekt-ID och den nyckel för Azure Migrate som du kopierade från portalen. Om du inte kopierade dem öppnar du Azure Portal från den virtuella insamlardatorn. På projektsidan **Översikt** klickar du på **Identifiera datorer** och kopierar värdena.  
7. I **View collection progress** (Visa insamlingsförlopp) övervakar du identifieringen och kontrollerar att de metadata som samlas in från virtuella datorer finns i ett omfång. Insamlaren visar en ungefärlig identifieringstid.

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

För förberedda virtuella datorer rekommenderar Azure Migrate en VM-storlek i Azure. Storleksrekommendationen som utförs av Azure Migrate är beroende av storlekskriteriet som anges i utvärderingsegenskaperna. Om storlekskriteriet är prestandabaserat utförs storleksrekommendationen med hänsyn till de virtuella datorernas prestandahistorik. Om storlekskriteriet är ”som lokalt”, utförs rekommendationen genom att titta på storleken för den virtuella datorn lokalt (storlek i befintligt skick). Användningsdata tas inte med i beräkningen i det här fallet. [Lär dig mer](concepts-assessment-calculation.md) om storlekar i Azure Migrate. 

För virtuella datorer som inte är redo eller endast är villkorligt redo för Azure beskriver Azure Migrate beredskapsproblem och visar de reparationssteg som kan utföras. 

De virtuella datorer för vilka Azure Migrate inte kan identifiera Azure-beredskap (eftersom det inte finns data) markeras som ”beredskap okänd”.

Förutom Azure-beredskap och storlek föreslår Azure Migrate även verktyg som du kan använda för att migrera den virtuella datorn. Om datorn är lämplig för Lift and Shift-migrering rekommenderas tjänsten [Azure Site Recovery]. Azure Database Migration Service rekommenderas om det är en databasdator.

  ![Utvärderingsberedskap](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Uppskattad månadskostnad

Den här vyn visar total beräknings- och lagringskostnad för att köra de virtuella datorerna i Azure, samt information för varje dator. Kostnaden beräknas med hjälp av rekommendationer för prestandabaserad storlek för en dator och dess diskar, samt utvärderingsegenskaper. 

> [!NOTE]
> Kostnadsberäkningen från Azure Migrate gäller för att köra de lokala virtuella datorerna som virtuella Azure IaaS-datorer (Infrastruktur som en tjänst). Azure Migrate beräknar inte några kostnader för PaaS (Plattform som en tjänst) eller SaaS (Programvara som en tjänst). 

Uppskattade månatliga kostnader för beräkning och lagring sammanställs för alla virtuella datorer i gruppen. 

![Utvärdering av VM-kostnad](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

#### <a name="confidence-rating"></a>Säkerhetsomdöme

Varje utvärdering i Azure Migrate är kopplad till ett säkerhetsomdöme i intervallet 1 stjärna till 5 stjärnor (1 stjärna är lägst och 5 stjärnor är högst). Säkerhetsomdömet tilldelas en utvärdering baserat på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen. Det hjälper dig att beräkna tillförlitligheten för storleksrekommendationer som tillhandahålls av Azure Migrate. 

Säkerhetsomdömen är användbara när du gör *prestandabaserade storleksändringar* eftersom alla datapunkter kanske inte är tillgängliga. När *storleksändringar av typen "som lokalt"* utförs är säkerhetsomdömet alltid 5 stjärnor eftersom Azure Migrate har tillgång till alla data som behövs för att sätta rätt storlek på den virtuella datorn. 

För prestandabaserade storleksändringar behöver Azure Migrate användningsdata för CPU och minne. För varje disk som är ansluten till den virtuella datorn krävs lästa/skrivna IOPS och dataflödet för att utföra prestandabaserade storleksändringar. Precis som för varje nätverkskort som är kopplat till den virtuella datorn så måste Azure Migrate ha åtkomst till nätverkets in-/utdata för att utföra prestandabaserade storleksändringar. Om några av ovanstående användningsnummer inte är tillgängliga i vCenter Server så är kanske storleksrekommendationen från Azure Migrate inte är tillförlitlig. Beroende på procentandelen datapunkter som är tillgängliga tillhandahålls säkerhetsomdömet för utvärderingen:

   **Tillgänglighet för datapunkter** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

En utvärdering kanske inte har tillgång till alla datapunkter på grund av någon av följande orsaker:
- Statistikinställningen i vCenter Server är inte inställd på nivå 3 och utvärderingen har storlekskriteriet inställt på prestandabaserade storleksändringar. Om statistikinställningen i vCenter Server är lägre än nivå 3 så görs ingen insamling av prestandadata för disk och nätverk från vCenter Server. I det här fallet baseras rekommendationen från Azure Migrate för disk och nätverk endast på det som allokerats lokalt. För lagring rekommenderar Azure Migrate standarddiskar eftersom det inte går att identifiera om disken har högt IOPS/dataflöde och behöver premiumdiskar.
- Statistikinställningen i vCenter Server var inställd på nivå 3 under en kort period, innan identifieringen drog igång. Om du till exempel ändrar statistikinställningen till nivå 3 idag och sätter igång identifieringen med insamlingsprogrammet imorgon (efter 24 timmar) så får du med alla datapunkter om du skapar en utvärdering för en dag. Men om du ändrar varaktigheten i utvärderingsegenskaperna till en månad går säkerhetsomdömet ned om prestandadata från disken och nätverket för den senaste månaden inte är tillgängliga. Om du vill undersöka prestandadata för den senaste månaden rekommenderar vi att du behåller statistikinställningen för vCenter Server på nivå 3 i en månad innan du startar identifieringen. 
- Några virtuella datorer stängdes av under perioden som utvärderingen utfördes. Om några virtuella datorer stängdes av under en viss period så kommer vCenter Server inte att ha prestandadata för den perioden. 
- Några virtuella datorer skapades under perioden som utvärderingen utförs. Om du till exempel skapar en utvärdering för prestandahistoriken för den senaste månaden, men några virtuella datorer skapades i miljön för en vecka sedan. I sådana fall är prestandahistoriken för de nya virtuella datorerna inte med för hela perioden.

> [!NOTE]
> Om säkerhetsomdömet för någon utvärdering är lägre än 3 stjärnor rekommenderar vi att du ändrar nivån för statistikinställningar i vCenter Server till 3, väntar så länge som du vill att utvärderingen ska utvärdera (en dag/en vecka/en månad) och sedan utför en identifiering och en utvärdering. Om det föregående inte kan utföras kan prestandabaserade storleksändringar vara mindre tillförlitliga och därför rekommenderar vi att du byter till *storleksändringar av typen "som lokalt"* genom att ändra utvärderingsegenskaperna.
 
## <a name="next-steps"></a>Nästa steg

- [Läs](how-to-scale-assessment.md) hur du identifierar och utvärderar en stor VMware-miljö.
- Lär dig hur du skapar utvärderingsgrupper med hög exakthet med hjälp av [datorberoende mappning](how-to-create-group-machine-dependencies.md)
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
