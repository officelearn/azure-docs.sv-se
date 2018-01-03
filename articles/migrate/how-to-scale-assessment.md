---
title: "Skala identifiering och bedömning med hjälp av Azure migrera | Microsoft Docs"
description: "Beskriver hur du utvärdera stort antal lokala datorer med hjälp av Azure migrera tjänsten."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/19/2017
ms.author: raynew
ms.openlocfilehash: 9b457252fdb7a1ad62b7e6038b341451df2e1590
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Identifiera och utvärdera en stor VMware-miljön

Den här artikeln beskriver hur du utvärdera stort antal lokala virtuella datorer (VM) med hjälp av [Azure migrera](migrate-overview.md). Azure migrera utvärderar datorer för att kontrollera om de är lämpliga för migrering till Azure. Tjänsten innehåller storlek och kostnad uppskattningar för datorerna som körs i Azure.

## <a name="prerequisites"></a>Förutsättningar

- **VMware**: på virtuella datorer som du planerar att migrera måste hanteras av vCenter Server version 5.5, 6.0 eller 6.5. Du måste dessutom en ESXi-värd kör version 5.0 eller senare distribution insamlaren VM.
- **vCenter konto**: du behöver ett konto för skrivskyddad åtkomst till vCenter-servern. Azure migrera använder kontot för att identifiera de lokala virtuella datorerna.
- **Behörigheter**: I vCenter Server som du behöver behörighet att skapa en virtuell dator genom att importera en fil i ägg format.
- **Inställningar för statistik**: statistik inställningarna för vCenter-servern ska vara inställd på nivå 3 innan du börjar distributionen. Om nivån är lägre än 3 bedömning fungerar, men kommer inte att samlas in prestandadata för lagring och nätverk. Rekommendationer för storlek baseras i det här fallet på prestandadata för CPU och minne och konfigurationsdata för disk och nätverkskort.

## <a name="plan-azure-migrate-projects"></a>Planera Azure migrera projekt

Planera identifieringar och utvärderingar baserat på följande begränsningar:

| **Entitet** | **Gränsen för datorn** |
| ---------- | ----------------- |
| Project    | 1,500              | 
| Identifiering  | 1,000              |
| Utvärdering | 400               |

- Om du har färre än 400 datorer att identifiera och utvärdera behöver du ett projekt och en enkel identifiering. Beroende på dina krav kan du utvärdera alla datorer i en enda bedömning eller dela upp datorerna i flera bedömningar. 
- Om du har 400 till 1 000 datorer att identifiera, måste ett projekt med en enda identifiering. Men du behöver flera bedömningar att utvärdera dessa datorer eftersom en enda bedömning kan innehålla upp till 400 datorer.
- Om du har 1,001 till 1 500 datorer, måste ett projekt med två identifieringar i den.
- Om du har mer än 1 500 datorer måste du skapa flera projekt och utföra flera identifieringar, baserat på dina krav. Exempel:
    - Om du har 3 000 datorer, kan du konfigurera två projekt med två identifieringar eller tre projekt med en enda identifiering.
    - Om du har 5 000 datorer, kan du konfigurera fyra projekt: två med en identifiering av 1 500 datorer och en med en identifiering av 500 datorer. Du kan också ställa in fem projekt med en enda identifiering i var och en. 

## <a name="plan-multiple-discoveries"></a>Planera flera identifieringar

Du kan använda samma Azure migrera insamlaren för att göra flera identifieringar till en eller flera projekt. Tänk på följande överväganden:
 
- När du gör en identifiering med hjälp av Azure migrera insamlaren kan ange du identifieringsomfånget till en mapp för vCenter-Server, datacenter, kluster eller en värddator.
- Kontrollera i vCenter Server som de virtuella datorerna som du vill identifiera i mappar, Datacenter, kluster eller värdar som har stöd för begränsning av 1 000 datorer om du vill göra mer än en identifiering.
- Vi rekommenderar att vid bedömningen, du behåller datorer med beroenden inom samma projekt och assessment. Kontrollera att beroende datorer finns i samma mapp, datacenter eller kluster för att bedöma i vCenter Server.


## <a name="create-a-project"></a>Skapa ett projekt

Skapa ett Azure migrera projekt utifrån dina behov:

1. Välj i Azure-portalen **skapar du en resurs**.
2. Sök efter **Azure migrera**, och välj tjänsten **Azure migrera (förhandsgranskning)** i sökresultaten. Välj sedan **Skapa**.
3. Ange ett projektnamn och Azure-prenumeration för projektet.
4. Skapa en ny resursgrupp.
5. Ange platsen där du vill skapa projektet och välj sedan **skapa**. Observera att du fortfarande kan utvärdera dina virtuella datorer för en annan målplats. Den angivna platsen för projektet används för att lagra metadata som samlats in från lokala virtuella datorer.

## <a name="set-up-the-collector-appliance"></a>Ställ in insamlaren-enhet

Azure migrera skapar en lokal virtuell dator som kallas insamlaren installation. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om dem till tjänsten Azure migrera. Om du vill konfigurera insamlaren anordningen hämtningen ägg och importera dem till den lokala vCenter Server-instansen.

### <a name="download-the-collector-appliance"></a>Hämta insamlaren-enhet

Om du har flera projekt måste du hämta insamlaren anordningen bara en gång till vCenter-servern. När du hämtar och ställa in anordningen du kör den för varje projekt och du ange unika projekt-ID och nyckel.

1. Markera i projektet Azure migrera **komma igång** > **Discover & Bedöm** > **identifiera datorer**.
2. I **identifiera datorer**väljer **hämta**, för att hämta filen ägg.
3. I **Kopiera projekt autentiseringsuppgifterna**, kopiera ID: T och nyckeln för projektet. Du behöver dem när du konfigurerar insamlaren.

   
### <a name="verify-the-collector-appliance"></a>Kontrollera insamlaren-enhet

Kontrollera att filen ägg är säker innan du distribuerar det:

1. Öppna ett kommandofönster administratör på datorn som du laddat ned filen.
2. Kör följande kommando för att generera hash för ägg:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Exempel på användning:```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Kontrollera att den genererade hashen matchar följande inställningar.
 
    För version ägg 1.0.8.38:
    **Algoritmen** | **Hash-värde**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    För version ägg 1.0.8.40:
    **Algoritmen** | **Hash-värde**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad



## <a name="create-the-collector-vm"></a>Skapa insamlaren VM

Importera den hämta filen till vCenter-servern:

1. Välj i vSphere-klientkonsolen **filen** > **distribuera OVF mallen**.

    ![Distribuera OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. I guiden Distribuera OVF mall > **källa**, ange platsen för filen ägg.
3. I **namn** och **plats**, ange ett eget namn för VM-insamlare och inventeringen objekt som den virtuella datorn kommer att finnas.
5. I **värden/klustret**, anger du värden eller kluster som insamlaren VM körs.
7. Ange lagringsplats för insamlare VM i lagring.
8. I **diskformat**, ange disktyp och storlek.
9. I **nätverksmappning**, ange som kommer ansluta till insamlaren VM-nätverk. Nätverket måste internet-anslutning för att skicka metadata till Azure. 
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
|CPU.Usage.Average                        | 1       |Ej tillämpligt                |Rekommenderade VM-storlek och kostnad                    |
|Mem.Usage.Average                        | 1       |Ej tillämpligt                |Rekommenderade VM-storlek och kostnad                    |
|virtualDisk.read.average                 | 2       |2                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|virtualDisk.write.average                | 2       |2                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Diskstorleken och lagringskostnaden är VM-storlek         |
|NET.Received.Average                     | 2       |3                 |VM-storlek och nätverket kostnad                        |
|NET.Transmitted.Average                  | 2       |3                 |VM-storlek och nätverket kostnad                        |

> [!WARNING]
> Om du precis har högre statistik, tar det till en dag att generera prestandaräknare. Därför rekommenderar vi att du kör identifieringen efter en dag.

## <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

För varje identifiering som du behöver utföra kör insamlaren för att identifiera virtuella datorer i området krävs. Kör identifierade en efter en. Samtidiga identifieringar stöds inte och varje identifiering måste ha ett annat omfång.

1. Högerklicka på den virtuella datorn i klientkonsolen vSphere > **öppna konsolen**.
2. Ange språk, tidszon och lösenord inställningar för produkten.
3. På skrivbordet, Välj den **kör insamlaren** genväg.
4. Öppna i Azure migrera-insamlaren **uppfylla krav** och sedan:

   a. Acceptera licensvillkoren och läsa information från tredje part.

   Insamlaren kontrollerar att den virtuella datorn har Internetåtkomst.
   
   b. Om den virtuella datorn har åtkomst till internet via en proxyserver, väljer **proxyinställningar**, och ange proxyadress och lyssningsport. Ange autentiseringsuppgifter om proxyn behöver autentisering.

   Insamlaren kontrollerar att collector-tjänsten körs. Tjänsten installeras som standard på VM-insamlaren.

   c. Hämta och installera VMware PowerCLI.

5. I **ange vCenter-serverinformationen**, gör du följande:
    - Ange namn (FQDN) eller IP-adressen för vCenter-servern.
    - I **användarnamn** och **lösenord**, ange de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer i vCenter Server.
    - I **väljer omfattning**, väljer du en omfattning för identifiering av virtuell dator. Insamlaren kan identifiera virtuella datorer i angivet omfång. Omfång kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1 000 virtuella datorer. 
    - I **vCenter Taggkategori för att gruppera**väljer **ingen**.

    ![Välj omfattning](./media/how-to-scale-assessment/select-scope.png)

6. I **ange migrering projektet**anger ID: T och nyckeln för projektet. Om du inte kopierar dem, öppna Azure-portalen från VM-insamlaren. På projektets **översikt** väljer **identifiera datorer** och kopiera värdena.  
7. I **Visa förloppet för samlingen**, övervaka identifieringsprocessen och kontrollera att metadata som samlas in från de virtuella datorerna är i ett omfång. Insamlaren ger en tid för ungefärlig identifiering.


### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Identifiering av tiden beror på hur många virtuella datorer som du identifierar. Normalt för 100 virtuella datorer kan identifieringen är klar runt en timme när insamlaren är klar. 

1. Markera i projektet migrering Planner **hantera** > **datorer**.
2. Kontrollera att de virtuella datorerna som du vill identifiera visas på portalen.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar du en grupp](how-to-create-a-group.md) för utvärdering.
- [Lär dig mer](concepts-assessment-calculation.md) om hur bedömningar beräknas.
