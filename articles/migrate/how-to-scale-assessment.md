---
title: Skala identifiering och bedömning med hjälp av Azure migrera | Microsoft Docs
description: Beskriver hur du utvärdera stort antal lokala datorer med hjälp av Azure migrera tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: 89c9cfd4bdc1c483764983c886ba9f96cc75c69e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736838"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Upptäck och utvärdera en stor VMware-miljö

Azure migrera har en gräns på 1 500 datorer per projekt, men den här artikeln beskriver hur du utvärdera stort antal lokala virtuella datorer (VM) med hjälp av [Azure migrera](migrate-overview.md).   

## <a name="prerequisites"></a>Förutsättningar

- **VMware**: på virtuella datorer som du planerar att migrera måste hanteras av vCenter Server version 5.5, 6.0 eller 6.5. Du måste dessutom en ESXi-värd kör version 5.0 eller senare distribution insamlaren VM.
- **vCenter konto**: du behöver ett konto för skrivskyddad åtkomst till vCenter-servern. Azure Migrate använder kontot till att identifiera de lokala virtuella datorerna.
- **Behörigheter**: I vCenter Server som du behöver behörighet att skapa en virtuell dator genom att importera en fil i ägg format.
- **Inställningar för statistik**: statistik inställningarna för vCenter-servern ska vara inställd på nivå 3 innan du börjar distributionen. Om nivån är lägre än 3 bedömning fungerar, men kommer inte att samlas in prestandadata för lagring och nätverk. Rekommendationer för storlek baseras i det här fallet på prestandadata för CPU och minne och konfigurationsdata för disk och nätverkskort.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planera migrering projekt och identifieringar

En enda Azure migrera insamlare stöder identifiering från flera vCenter-servrar (efter varandra) och också stöd för identifiering av flera migrering projekt (efter varandra). Insamlaren fungerar i en brand och glömmer modellen när identifiering är klar kan du använda samma insamlaren att samla in data från en annan vCenter-Server eller skicka det till ett annat migreringsprojekt.

Planera identifieringar och utvärderingar baserat på följande begränsningar:

| **Entitet** | **Gränsen för datorn** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Identifiering  | 1,500             |
| Utvärdering | 1,500             |

Tänk på följande överväganden:

- När du gör en identifiering med hjälp av Azure migrera insamlaren kan ange du identifieringsomfånget till en mapp för vCenter-Server, datacenter, kluster eller en värddator.
- Kontrollera i vCenter Server som de virtuella datorerna som du vill identifiera i mappar, Datacenter, kluster eller värdar som har stöd för begränsning av 1 500 datorer om du vill göra mer än en identifiering.
- Vi rekommenderar att vid bedömningen, du behåller datorer med beroenden inom samma projekt och assessment. Kontrollera att beroende datorer finns i samma mapp, datacenter eller kluster för att bedöma i vCenter Server.

Beroende på scenario kan dela du din identifieringar som anges nedan:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Flera vCenter-servrar med färre än 1500 virtuella datorer

Om du har flera vCenter-servrar i din miljö, och det totala antalet virtuella datorer är mindre än 1 500, kan du använda en enda insamlare och ett enda migreringsprojekt för att identifiera alla virtuella datorer över alla vCenter-servrar. Eftersom insamlaren identifierar en vCenter-Server åt gången, kan du köra samma insamlaren mot alla vCenter-servrar, efter varandra och peka samma migreringsprojekt insamlaren. När alla identifieringar är klar kan skapa du sedan bedömningar för datorer.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Flera vCenter-servrar med mer än 1500 virtuella datorer

Om du har flera vCenter-servrar med färre än 1500 virtuella datorer per vCenter-servern, men mer än 1500 virtuella datorer mellan alla vCenter fungerar, måste du skapa flera migrering projekt (en migreringsprojekt kan innehålla endast 1500 VMs). Du kan åstadkomma detta genom att skapa ett migreringsprojekt per vCenter-servern och dela identifieringarna. Du kan använda en enda insamlare för att identifiera varje vCenter-Server (efter varandra). Om du vill identifieringar att starta samtidigt kan du distribuera flera installationer och parallell körning av identifieringar.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Mer än 1500 datorer i en enda vCenter-Server

Om du har mer än 1500 virtuella datorer i en enda vCenter-Server, måste du dela identifieringen i flera projekt för migrering. Om du vill dela identifieringar kan du utnyttja fältet Scope i anordningen och anger värden, kluster, mappar eller datacenter som du vill identifiera. Om du har två mappar i vCenter Server, en med 1000 till exempel virtuella datorer (Mapp1) och andra med 800 virtuella datorer (mapp2) du kan använda en enda insamlare och utföra två identifieringar. I den första identifieringen du anger Mapp1 som scope och peka till projektet migrering när den första identifieringen är klar, du kan använda samma insamlaren, ändra sitt omfång till Mapp2 och migrering av informationen i andra migrering projektet och göra andra identifieringen.

### <a name="multi-tenant-environment"></a>Klientmiljön

Om du har en miljö som delas mellan klienter och du inte vill identifiera de virtuella datorerna i en klient i en annan innehavarens prenumeration, kan du använda fältet Scope i insamlaren anordningen att definiera omfattningen av identifieringen. Om klienterna delar värdar, skapar du autentiseringsuppgifter som har skrivskyddad åtkomst till endast de virtuella datorerna som hör till specifika klienten och använder dessa autentiseringsuppgifter i anordningen insamlaren och ange omfånget som värd för att göra identifieringen. Alternativt kan du också skapa mappar i vCenter Server (anta att Mapp1 för tenant1 och mapp2 för tenant2), under delade värden, flytta de virtuella datorerna för tenant1 i Mapp1 och tenant2 till Mapp2 och omfång identifieringar i insamlaren i enlighet med detta genom att ange rätt mapp.

## <a name="discover-on-premises-environment"></a>Identifiera lokala miljö

När du är klar med planen kan sedan starta identifiering av lokala virtuella datorer:

### <a name="create-a-project"></a>Skapa ett projekt

Skapa ett Azure migrera projekt utifrån dina behov:

1. Välj i Azure-portalen **skapar du en resurs**.
2. Sök efter **Azure Migrate** och välj tjänsten **Azure Migrate (förhandsversion)** i sökresultaten. Välj sedan **Skapa**.
3. Ange ett projektnamn och Azure-prenumeration för projektet.
4. Skapa en ny resursgrupp.
5. Ange platsen där du vill skapa projektet och välj sedan **skapa**. Observera att du fortfarande kan utvärdera dina virtuella datorer för en annan målplats. Den angivna platsen för projektet används för att lagra metadata som samlats in från lokala virtuella datorer.

### <a name="set-up-the-collector-appliance"></a>Ställ in insamlaren-enhet

Azure Migrate skapar en lokal virtuell dator som kallas för insamlarprogram. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om dem till tjänsten Azure migrera. Om du vill konfigurera insamlaren anordningen hämtningen ägg och importera dem till den lokala vCenter Server-instansen.

#### <a name="download-the-collector-appliance"></a>Hämta insamlingsprogrammet

Om du har flera projekt måste du hämta insamlaren anordningen bara en gång till vCenter-servern. När du hämtar och ställa in anordningen du kör den för varje projekt och du ange unika projekt-ID och nyckel.

1. Markera i projektet Azure migrera **komma igång** > **Discover & Bedöm** > **identifiera datorer**.
2. I **identifiera datorer**väljer **hämta**, för att hämta filen ägg.
3. I **Kopiera projekt autentiseringsuppgifterna**, kopiera ID: T och nyckeln för projektet. Du behöver dem när du konfigurerar insamlaren.


#### <a name="verify-the-collector-appliance"></a>Kontrollera insamlingsprogrammet

Kontrollera att filen ägg är säker innan du distribuerar det:

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.

2. Kör följande kommando för att generera en hash för OVA-filen:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Kontrollera att den genererade hashen matchar följande inställningar.

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

### <a name="create-the-collector-vm"></a>Skapa den virtuella insamlardatorn

Importera den hämta filen till vCenter-servern:

1. Välj i vSphere-klientkonsolen **filen** > **distribuera OVF mallen**.

    ![Distribuera OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. I guiden Distribuera OVF mall > **källa**, ange platsen för filen ägg.
3. I **Namn** och **Plats** anger du ett eget namn för den virtuella insamlardatorn och lagerobjektet där den virtuella datorn kommer att finnas.
4. I **Värd/kluster** anger du den värd eller det kluster där den virtuella insamlardatorn körs.
5. Ange lagringsplats för den virtuella insamlardatorn i lagringen.
6. I **Diskformat** anger du disktyp och storlek.
7. I **Nätverksmappning** anger du det nätverk som den virtuella insamlardatorn kommer att ansluta till. Nätverket måste internet-anslutning för att skicka metadata till Azure.
8. Granska och bekräfta inställningarna och markera **Slutför**.

### <a name="identify-the-id-and-key-for-each-project"></a>Identifiera-ID och nyckel för varje projekt

Om du har flera projekt måste du identifiera-ID och nyckel för varje kriterium. Du måste nyckeln när du kör insamlaren för att identifiera de virtuella datorerna.

1. I projektet, Välj **komma igång** > **Discover & Bedöm** > **identifiera datorer**.
2. I **Kopiera projekt autentiseringsuppgifterna**, kopiera ID: T och nyckeln för projektet.
    ![Kopiera projekt autentiseringsuppgifter](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>Att ställa in vCenter statistik
Följande är en lista över prestandaräknare som samlas in under identifieringen. Räknarna är som standard som är tillgängliga på olika nivåer i vCenter Server.

Vi rekommenderar att du vanliga filegenskaper (3) för statistik nivån så att alla räknare samlas in korrekt. Om du har vCenter på en lägre nivå kan bara några räknare samlas helt, med de övriga anges som 0. Bedömning kan sedan visa ofullständiga data.

I följande tabell visas också utvärderingsresultat som kommer att påverkas om räknaren inte samlas in.

| Räknare                                 | Nivå | Nivå per enhet | Bedömning påverkan                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| CPU.Usage.Average                       | 1     | Ej tillämpligt               | Rekommenderade VM-storlek och kostnad         |
| Mem.Usage.Average                       | 1     | Ej tillämpligt               | Rekommenderade VM-storlek och kostnad         |
| virtualDisk.read.average                | 2     | 2                | Diskstorleken och lagringskostnaden är VM-storlek |
| virtualDisk.write.average               | 2     | 2                | Diskstorleken och lagringskostnaden är VM-storlek |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | Diskstorleken och lagringskostnaden är VM-storlek |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | Diskstorleken och lagringskostnaden är VM-storlek |
| NET.Received.Average                    | 2     | 3                | VM-storlek och nätverket kostnad             |
| NET.Transmitted.Average                 | 2     | 3                | VM-storlek och nätverket kostnad             |

> [!WARNING]
> Om du precis har högre statistik, tar det till en dag att generera prestandaräknare. Därför rekommenderar vi att du kör identifieringen efter en dag.

### <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

För varje identifiering som du behöver utföra kör insamlaren för att identifiera virtuella datorer i området krävs. Kör identifierade en efter en. Samtidiga identifieringar stöds inte och varje identifiering måste ha ett annat omfång.

1.  Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2.  Ange språk, tidszon och lösenordsinställningar för produkten.
3.  På skrivbordet, Välj den **kör insamlaren** genväg.
4.  Öppna i Azure migrera-insamlaren **uppfylla krav** och sedan:

    a. Acceptera licensvillkoren och läs informationen från tredje part.

    Insamlaren kontrollerar att den virtuella datorn har Internetåtkomst.

    b. Om den virtuella datorn har åtkomst till internet via en proxyserver, väljer **proxyinställningar**, och ange proxyadress och lyssningsport. Ange autentiseringsuppgifter om proxyn kräver autentisering.

    Insamlaren kontrollerar att insamlingstjänsten körs. Tjänsten installeras som standard på den virtuella insamlardatorn.

    c. Ladda ned och installera VMware PowerCLI.

5.  Gör följande i **Specify vCenter Server details** (Ange vCenter Server-information):
    - Ange namn (FQDN) eller IP-adressen för vCenter-servern.
    - I **användarnamn** och **lösenord**, ange de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer i vCenter Server.
    - I **Välj omfattning** väljer du en omfattning för identifieringen av virtuella datorer. Insamlaren kan identifiera virtuella datorer i angivet omfång. Omfånget kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1 000 virtuella datorer.

6.  I **ange migrering projektet**anger ID: T och nyckeln för projektet. Om du inte kopierar dem, öppna Azure-portalen från VM-insamlaren. På projektets **översikt** väljer **identifiera datorer** och kopiera värdena.  
7.  I **Visa förloppet för samlingen**, övervaka identifieringsprocessen och kontrollera att metadata som samlas in från de virtuella datorerna är i ett omfång. Insamlaren visar en ungefärlig identifieringstid.


#### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Identifieringstiden beror på hur många virtuella datorer du identifierar. Normalt för 100 virtuella datorer kan identifieringen är klar runt en timme när insamlaren är klar.

1. Markera i projektet migrering Planner **hantera** > **datorer**.
2. Kontrollera att de virtuella datorer som du vill identifiera visas i portalen.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar du en grupp](how-to-create-a-group.md) för utvärdering.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
