---
title: "Skala identifiering och bedömning med Azure migrera | Microsoft Docs"
description: "Beskriver hur du utvärdera stort antal lokala datorer med tjänsten Azure migrera."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: e28a2144dd102fcd2ec05531432cac0df250ae01
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Identifiera och utvärdera en stor VMware-miljön

Den här artikeln beskriver hur du utvärdera stort antal lokala datorer med [Azure migrera](migrate-overview.md). Azure migrera utvärderar datorer för att kontrollera om de är lämplig för migrering till Azure och ger storlek och kostnad beräkningar för att köra datorn i Azure.

## <a name="prerequisites"></a>Krav

- **VMware**: du behöver minst en VMware VM finns på en ESXi-värd eller kluster som kör version 5.0 eller senare. Värd eller kluster måste hanteras av en vCenter-server som kör version 5.5 eller 6.0.
- **vCenter konto**: du behöver ett skrivskyddat konto med administratörsbehörighet för vCenter-servern. Azure migrera använder kontot för att identifiera virtuella datorer.
- **Behörigheter**: på vCenter-server du behöver för att skapa en virtuell dator genom att importera en fil i. ÄGG format.
- **Inställningar för statistik**: statistik inställningarna för vCenter-servern ska anges till nivå 2 eller högre, innan du börjar distributionen.

## <a name="plan-azure-migrate-projects"></a>Planera Azure migrera projekt

Ett Azure migrera projekt kan bedöma upp till 1500 datorer. En enda identifiering i ett projekt kan identifiera upp till 1 000 datorer.

- Om du har färre än 1000 datorer att identifiera måste ett projekt med en enda identifiering.
- Om du har mellan 1000 och 1500 datorer, måste ett projekt med två identifieringar i den.
- Om du har mer än 1500 datorer måste du skapa flera projekt och utföra flera identifieringar, baserat på dina krav. Exempel:
    - Om du har 3000 datorer kan du ställa in två projekt med två identifieringar eller tre projekt med en enda identifiering.
    - Om du har 5000 datorer kan du ställa in fyra projekt. Två med en identifiering av 1500 datorer och en med en identifiering av 500 datorer. Alternativt kan du ställa in fem projekt med en enda identifiering i var och en. 
- När du gör en identifiering i Azure migrerar kan du ange identifieringsomfånget för VMware-mappen, datacenter eller i klustret.
- Om du vill göra mer än en identifiering, kontrollera i vCenter att de virtuella datorerna som du vill identifiera i mappar, Datacenter eller kluster som har stöd för begränsning av 1000 datorer.
- Vi rekommenderar att vid bedömningen, du behåller datorer med mellan beroenden inom samma projekt och assessment. Så i vCenter, se till att beroende datorer finns i samma mapp, datacenter eller kluster för utvärdering.


## <a name="create-a-project"></a>Skapa ett projekt

Skapa ett Azure migrera projekt utifrån dina behov.

1. I Azure-portalen klickar du på **skapar du en resurs**.
2. Sök efter **Azure migrera**, och välj tjänsten (**Azure migrera (förhandsgranskning)** i sökresultaten. Klicka sedan på **Skapa**.
3. Ange ett projektnamn och Azure-prenumeration för projektet.
4. Skapa en ny resursgrupp.
5. Ange regionen där du vill skapa projektet och klicka sedan på **skapa**. Metadata som samlats in från lokala virtuella datorer lagras i den här regionen.

## <a name="set-up-the-collector-appliance"></a>Ställ in insamlaren-enhet

Azure migrera skapar en lokal virtuell dator som kallas insamlaren installation. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar metadata om dem till tjänsten Azure migrera. Om du vill konfigurera insamlaren-enhet som du hämtar ett. ÄGG, och importera den till vCenter-servern lokalt för att skapa den virtuella datorn.

### <a name="download-the-collector-appliance"></a>Hämta insamlaren-enhet

Om du har flera projekt, behöver du bara hämta insamlaren anordningen en gång till vCenter-servern. När du hämtar och konfigurerar anordningen, kör för varje projekt och ange unika projekt-ID och nyckel.

1. I Azure migrera-projektet klickar du på **komma igång** > **Discover & Bedöm** > **identifiera datorer**.
2. I **identifiera datorer**, klickar du på **hämta**, för att ladda ned den. ÄGG fil.
3. I **Kopiera projekt autentiseringsuppgifterna**, kopiera ID: T och nyckeln för projektet. Du behöver dem när du konfigurerar insamlaren.

   
### <a name="verify-the-collector-appliance"></a>Kontrollera insamlaren-enhet

Kontrollera att den. ÄGG filen är säker, innan du distribuerar den.

1. Öppna ett kommandofönster administratör på datorn som du laddat ned filen.
2. Kör följande kommando för att generera hash för ägg:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning:```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Den genererade hashen måste matcha inställningarna.

    **Algoritmen** | **Hash-värde**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>Skapa insamlaren VM

Importera den hämta filen till vCenter-servern.

1. I konsolen vSphere-klienten klickar du på **filen** > **distribuera OVF mallen**.

    ![Distribuera OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. I guiden Distribuera OVF mall > **källa**, ange platsen för filen .ova.
3. I **namn** och **plats**, ange ett eget namn för VM-insamlare och inventeringen objekt som den virtuella datorn kommer att finnas.
5. I **värden/klustret**, anger du värden eller kluster som insamlaren VM körs.
7. Ange lagringsplats för insamlare VM i lagring.
8. I **diskformat**, ange disktyp och storlek.
9. I **nätverksmappning**, ange som kommer ansluta till insamlaren VM-nätverk. Nätverket måste internet-anslutning att skicka metadata till Azure. 
10. Granska och bekräfta inställningarna och klicka på **Slutför**.

## <a name="identify-the-key-and-id-for-each-project"></a>Identifiera nyckel och ID för varje projekt

Om du har flera projekt kan du kontrollera att du identifiera-ID och nyckel för var och en. Du måste nyckeln när du kör insamlaren för att identifiera de virtuella datorerna.

1. I projektet, klickar du på **komma igång** > **Discover & Bedöm** > **identifiera datorer**.
2. I **Kopiera projekt autentiseringsuppgifterna**, kopiera ID: T och nyckeln för projektet. 
    ![Projekt-ID](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>vCenter statistik nivå för att samla in prestandaräknare
Följande är listan över räknare som samlas in under identifieringen. Räknarna är som standard som är tillgängliga på olika nivå i vCenter-servern. Vi rekommenderar att du anger högsta vanliga nivån (nivå 3) för statistik nivån så att alla räknare har samlats in på rätt sätt. Om du har vCenter på en lägre nivå kan bara några räknare kan hämta samlas in fullständigt med övriga anges som 0. Därför kan bedömningen visar ofullständiga data. I tabellen nedan visar också utvärderingsresultat som påverkas om räknaren inte samlas in.

|Räknaren                                  |Nivå    |Per enhetsnivå  |Bedömning påverkan                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|CPU.Usage.Average                        | 1       |Ej tillämpligt                |Rekommenderade VM-storlek och kostnad                    |
|Mem.Usage.Average                        | 1       |Ej tillämpligt                |Rekommenderade VM-storlek och kostnad                    |
|virtualDisk.read.average                 | 2       |2                 |Diskstorleken, lagringskostnaden och VM-storlek         |
|virtualDisk.write.average                | 2       |2                 |Diskstorleken, lagringskostnaden och VM-storlek         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Diskstorleken, lagringskostnaden och VM-storlek         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Diskstorleken, lagringskostnaden och VM-storlek         |
|NET.Received.Average                     | 2       |3                 |Storlek på Virtuellt minne och nätverk kostnad                        |
|NET.Transmitted.Average                  | 2       |3                 |Storlek på Virtuellt minne och nätverk kostnad                        |

> [!WARNING]
> Om du precis har högre statistik, tar upp till en dag för att generera prestandaräknare. Därför rekommenderas det att du kör identifieringen efter en dag.

## <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

För varje identifiering som du behöver utföra måste du köra insamlaren till identifiering av virtuella datorer i området krävs. Kör identifierade en efter en. Samtidiga identifieringar stöds inte och varje identifiering måste ha ett annat omfång.

1. Högerklicka på den virtuella datorn i klientkonsolen vSphere > **öppna konsolen**.
2. Ange språk, tidszon och lösenord inställningar för produkten.
3. På skrivbordet klickar du på den **kör insamlaren** genväg.
4. Öppna i Azure migrera insamlaren **in krav**.
    - Acceptera licensvillkoren och läsa information från tredje part.
    - Insamlaren kontrollerar att den virtuella datorn har Internetåtkomst.
    - Om den virtuella datorn har åtkomst till internet via en proxyserver, klickar du på **proxyinställningar**, och ange proxyadress och lyssningsport. Ange autentiseringsuppgifter om proxyn behöver autentisering.
    - Insamlaren kontrollerar att profileraren Windows-tjänsten körs. Tjänsten installeras som standard på VM-insamlaren.
    - Hämta och installera VMware PowerCLI.
. I **identifiera datorer**, gör du följande:
    - Ange namn (FQDN) eller IP-adressen för vCenter-servern.
    - I **användarnamn** och **lösenord**, ange de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer på vCenter server.
    - I **samling scope**, väljer du en omfattning för identifiering av virtuell dator. Insamlaren kan bara identifiera virtuella datorer i angivet omfång. Omfång kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1000 virtuella datorer. 
    - n **Taggkategori för att gruppera**väljer **ingen**.

        ![Välj scope](./media/how-to-scale-assessment/select-scope.png)

1. I **Välj projekt**anger ID: T och nyckeln för projektet. Om inte kopiera dem, öppna Azure-portalen från VM-insamlaren. I projektet **översikt** klickar du på **identifiera datorer**, och kopiera värdena.  
I **fullständig identifiering**, övervaka identifieringsprocessen och kontrollera att metadata som samlas in från de virtuella datorerna är i sitt omfång. Insamlaren ger en tid för ungefärlig identifiering.


### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Identifiering av tiden beror på hur många virtuella datorer som du identifierar. Normalt tar för 100 virtuella datorer, när insamlaren är klar kör det runt en timme för att slutföra identifieringen. 

1. Klicka i projektet migrering Planner **hantera** > **datorer**.
2. Kontrollera att de virtuella datorerna som du vill identifiera visas på portalen.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar du en grupp](how-to-create-a-group.md) för utvärdering.
- [Lär dig mer](concepts-assessment-calculation.md) om hur bedömningar beräknas.