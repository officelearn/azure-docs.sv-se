---
title: "Skala identifiering och bedömning med hjälp av Azure migrera | Microsoft Docs"
description: "Beskriver hur du utvärdera stort antal lokala datorer med hjälp av Azure migrera tjänsten."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: ab66bc9c90950070e69c2486bf09b7664b1c9ad2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Upptäck och utvärdera en stor VMware-miljö

Den här artikeln beskriver hur du utvärdera stort antal lokala virtuella datorer (VM) med hjälp av [Azure migrera](migrate-overview.md). Azure migrera utvärderar datorer för att kontrollera om de är lämpliga för migrering till Azure. Tjänsten innehåller storlek och kostnad uppskattningar för datorerna som körs i Azure.

## <a name="prerequisites"></a>Förutsättningar

- **VMware**: på virtuella datorer som du planerar att migrera måste hanteras av vCenter Server version 5.5, 6.0 eller 6.5. Du måste dessutom en ESXi-värd kör version 5.0 eller senare distribution insamlaren VM.
- **vCenter konto**: du behöver ett konto för skrivskyddad åtkomst till vCenter-servern. Azure Migrate använder kontot till att identifiera de lokala virtuella datorerna.
- **Behörigheter**: I vCenter Server som du behöver behörighet att skapa en virtuell dator genom att importera en fil i ägg format.
- **Inställningar för statistik**: statistik inställningarna för vCenter-servern ska vara inställd på nivå 3 innan du börjar distributionen. Om nivån är lägre än 3 bedömning fungerar, men kommer inte att samlas in prestandadata för lagring och nätverk. Rekommendationer för storlek baseras i det här fallet på prestandadata för CPU och minne och konfigurationsdata för disk och nätverkskort.

## <a name="plan-azure-migrate-projects"></a>Planera Azure migrera projekt

Planera identifieringar och utvärderingar baserat på följande begränsningar:

| **Entitet** | **Gränsen för datorn** |
| ---------- | ----------------- |
| Project    | 1,500              | 
| Identifiering  | 1,500              |
| Utvärdering | 1,500               |

<!-- 
- If you have fewer than 400 machines to discover and assess, you need a single project and a single discovery. Depending on your requirements, you can either assess all the machines in a single assessment or split the machines into multiple assessments. 
- If you have 400 to 1,000 machines to discover, you need a single project with a single discovery. But you will need multiple assessments to assess these machines, because a single assessment can hold up to 400 machines.
- If you have 1,001 to 1,500 machines, you need a single project with two discoveries in it.
- If you have more than 1,500 machines, you need to create multiple projects, and perform multiple discoveries, according to your requirements. For example:
    - If you have 3,000 machines, you can set up two projects with two discoveries, or three projects with a single discovery.
    - If you have 5,000 machines, you can set up four projects: three with a discovery of 1,500 machines, and one with a discovery of 500 machines. Alternatively, you can set up five projects with a single discovery in each one. 
-->

## <a name="plan-multiple-discoveries"></a>Planera flera identifieringar

Du kan använda samma Azure migrera insamlaren för att göra flera identifieringar till en eller flera projekt. Tänk på följande överväganden:
 
- När du gör en identifiering med hjälp av Azure migrera insamlaren kan ange du identifieringsomfånget till en mapp för vCenter-Server, datacenter, kluster eller en värddator.
- Kontrollera i vCenter Server som de virtuella datorerna som du vill identifiera i mappar, Datacenter, kluster eller värdar som har stöd för begränsning av 1 000 datorer om du vill göra mer än en identifiering.
- Vi rekommenderar att vid bedömningen, du behåller datorer med beroenden inom samma projekt och assessment. Kontrollera att beroende datorer finns i samma mapp, datacenter eller kluster för att bedöma i vCenter Server.


## <a name="create-a-project"></a>Skapa ett projekt

Skapa ett Azure migrera projekt utifrån dina behov:

1. Välj i Azure-portalen **skapar du en resurs**.
2. Sök efter **Azure Migrate** och välj tjänsten **Azure Migrate (förhandsversion)** i sökresultaten. Välj sedan **Skapa**.
3. Ange ett projektnamn och Azure-prenumeration för projektet.
4. Skapa en ny resursgrupp.
5. Ange platsen där du vill skapa projektet och välj sedan **skapa**. Observera att du fortfarande kan utvärdera dina virtuella datorer för en annan målplats. Den angivna platsen för projektet används för att lagra metadata som samlats in från lokala virtuella datorer.

## <a name="set-up-the-collector-appliance"></a>Ställ in insamlaren-enhet

Azure Migrate skapar en lokal virtuell dator som kallas för insamlarprogram. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om dem till tjänsten Azure migrera. Om du vill konfigurera insamlaren anordningen hämtningen ägg och importera dem till den lokala vCenter Server-instansen.

### <a name="download-the-collector-appliance"></a>Hämta insamlingsprogrammet

Om du har flera projekt måste du hämta insamlaren anordningen bara en gång till vCenter-servern. När du hämtar och ställa in anordningen du kör den för varje projekt och du ange unika projekt-ID och nyckel.

1. Markera i projektet Azure migrera **komma igång** > **Discover & Bedöm** > **identifiera datorer**.
2. I **identifiera datorer**väljer **hämta**, för att hämta filen ägg.
3. I **Kopiera projekt autentiseringsuppgifterna**, kopiera ID: T och nyckeln för projektet. Du behöver dem när du konfigurerar insamlaren.

   
### <a name="verify-the-collector-appliance"></a>Kontrollera insamlingsprogrammet

Kontrollera att filen ägg är säker innan du distribuerar det:

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera en hash för OVA-filen:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Kontrollera att den genererade hashen matchar följande inställningar.

    För ägg version 1.0.9.2

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

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
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>Skapa den virtuella insamlardatorn

Importera den hämta filen till vCenter-servern:

1. Välj i vSphere-klientkonsolen **filen** > **distribuera OVF mallen**.

    ![Distribuera OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. I guiden Distribuera OVF mall > **källa**, ange platsen för filen ägg.
3. I **Namn** och **Plats** anger du ett eget namn för den virtuella insamlardatorn och lagerobjektet där den virtuella datorn kommer att finnas.
5. I **Värd/kluster** anger du den värd eller det kluster där den virtuella insamlardatorn körs.
7. Ange lagringsplats för den virtuella insamlardatorn i lagringen.
8. I **Diskformat** anger du disktyp och storlek.
9. I **Nätverksmappning** anger du det nätverk som den virtuella insamlardatorn kommer att ansluta till. Nätverket måste internet-anslutning för att skicka metadata till Azure. 
10. Granska och bekräfta inställningarna och markera **Slutför**.

## <a name="identify-the-id-and-key-for-each-project"></a>Identifiera-ID och nyckel för varje projekt

Om du har flera projekt måste du identifiera-ID och nyckel för varje kriterium. Du måste nyckeln när du kör insamlaren för att identifiera de virtuella datorerna.

1. I projektet, Välj **komma igång** > **Discover & Bedöm** > **identifiera datorer**.
2. I **Kopiera projekt autentiseringsuppgifterna**, kopiera ID: T och nyckeln för projektet. 
    ![Kopiera projekt autentiseringsuppgifter](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>Att ställa in vCenter statistik
Följande är en lista över prestandaräknare som samlas in under identifieringen. Räknarna är som standard som är tillgängliga på olika nivåer i vCenter Server. 

Vi rekommenderar att du vanliga filegenskaper (3) för statistik nivån så att alla räknare samlas in korrekt. Om du har vCenter på en lägre nivå kan bara några räknare samlas helt, med de övriga anges som 0. Bedömning kan sedan visa ofullständiga data. 

I följande tabell visas också utvärderingsresultat som kommer att påverkas om räknaren inte samlas in.

|Räknaren                                  |Nivå    |Nivå per enhet  |Bedömning påverkan                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |Ej tillämpligt                |Rekommenderade VM-storlek och kostnad                    |
|mem.usage.average                        | 1       |Ej tillämpligt                |Rekommenderade VM-storlek och kostnad                    |
|virtualDisk.read.average                 | 2       |2                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|virtualDisk.write.average                | 2       |2                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|net.received.average                     | 2       |3                 |VM-storlek och nätverket kostnad                        |
|net.transmitted.average                  | 2       |3                 |VM-storlek och nätverket kostnad                        |

> [!WARNING]
> Om du precis har högre statistik, tar det till en dag att generera prestandaräknare. Därför rekommenderar vi att du kör identifieringen efter en dag.

## <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

För varje identifiering som du behöver utföra kör insamlaren för att identifiera virtuella datorer i området krävs. Kör identifierade en efter en. Samtidiga identifieringar stöds inte och varje identifiering måste ha ett annat omfång.

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2. Ange språk, tidszon och lösenordsinställningar för produkten.
3. På skrivbordet, Välj den **kör insamlaren** genväg.
4. Öppna i Azure migrera-insamlaren **uppfylla krav** och sedan:

   a. Acceptera licensvillkoren och läs informationen från tredje part.

   Insamlaren kontrollerar att den virtuella datorn har Internetåtkomst.
   
   b. Om den virtuella datorn har åtkomst till internet via en proxyserver, väljer **proxyinställningar**, och ange proxyadress och lyssningsport. Ange autentiseringsuppgifter om proxyn kräver autentisering.

   Insamlaren kontrollerar att insamlingstjänsten körs. Tjänsten installeras som standard på den virtuella insamlardatorn.

   c. Hämta och installera VMware PowerCLI.

5. Gör följande i **Specify vCenter Server details** (Ange vCenter Server-information):
    - Ange namn (FQDN) eller IP-adressen för vCenter-servern.
    - I **användarnamn** och **lösenord**, ange de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer i vCenter Server.
    - I **väljer omfattning**, väljer du en omfattning för identifiering av virtuell dator. Insamlaren kan identifiera virtuella datorer i angivet omfång. Omfånget kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1 000 virtuella datorer. 

6. I **ange migrering projektet**anger ID: T och nyckeln för projektet. Om du inte kopierar dem, öppna Azure-portalen från VM-insamlaren. På projektets **översikt** väljer **identifiera datorer** och kopiera värdena.  
7. I **Visa förloppet för samlingen**, övervaka identifieringsprocessen och kontrollera att metadata som samlas in från de virtuella datorerna är i ett omfång. Insamlaren visar en ungefärlig identifieringstid.


### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Identifieringstiden beror på hur många virtuella datorer du identifierar. Normalt för 100 virtuella datorer kan identifieringen är klar runt en timme när insamlaren är klar. 

1. Markera i projektet migrering Planner **hantera** > **datorer**.
2. Kontrollera att de virtuella datorer som du vill identifiera visas i portalen.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar du en grupp](how-to-create-a-group.md) för utvärdering.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
