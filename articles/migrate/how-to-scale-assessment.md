---
title: Skala identifiering och en utvärdering med hjälp av Azure Migrate | Microsoft Docs
description: Beskriver hur du avgör stort antal lokala datorer med hjälp av Azure Migrate-tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: 06905a2f16a23e5a7c4612f4b567aaf86322924d
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054377"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Upptäck och utvärdera en stor VMware-miljö

Azure Migrate har en gräns på 1500 datorer per projekt, den här artikeln beskrivs hur du avgör stort antal lokala virtuella datorer (VM) med hjälp av [Azure Migrate](migrate-overview.md).   

## <a name="prerequisites"></a>Förutsättningar

- **VMware**: de virtuella datorer som du planerar att migrera måste hanteras av vCenter Server version 5.5, 6.0 eller 6.5. Du måste även en ESXi-värd som kör version 5.0 eller senare för att distribuera den Virtuella insamlardatorn.
- **vCenter-kontot**: du behöver ett konto för skrivskyddad åtkomst till vCenter-servern. Azure Migrate använder kontot till att identifiera de lokala virtuella datorerna.
- **Behörigheter**: I vCenter-servern, behöver du behörighet att skapa en virtuell dator genom att importera en fil i OVA-formatet.
- **Inställningar för statistik**: statistikinställningarna för vCenter-servern bör vara inställda på nivå 3 innan du påbörjar distributionen. Om kompatibilitetsnivå är lägre än 3 fungerar utvärderingen, men prestandadata för lagring och nätverk samlas inte in. Storleksrekommendationer som baseras i det här fallet på prestandadata för CPU och minne och konfigurationsdata för disk och nätverkskort.


### <a name="set-up-permissions"></a>Konfigurera behörigheter

Azure Migrate måste ha åtkomst till VMware-servrar för att automatiskt kunna identifiera virtuella datorer för utvärdering. VMware-kontot måste ha följande behörigheter:

- Användartyp: Minst en skrivskyddad användare
- Behörigheter: Datacenter-objekt –> Sprid till underordnat objekt, roll = skrivskyddad
- Information: Användaren tilldelas på datacenternivå och har åtkomst till alla objekt i datacentret.
- Om du vill begränsa åtkomsten tilldelar du rollen Ingen åtkomst med Sprid till underordnat objekt till underordnade objekt (vSphere-värdar, datalager, virtuella datorer och nätverk).

Om du distribuerar i en miljö organisationer, är här ett sätt att konfigurera detta:

1.  Skapa en användare per klient och använder [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), tilldela läsbehörighet till Virtuella datorer som tillhör en viss klient. Sedan Använd dessa autentiseringsuppgifter för identifiering. RBAC säkerställer att motsvarande vCenter-användaren har åtkomst till endast specifika VM-klient.
2. Du konfigurerar RBAC för olika klientanvändare enligt beskrivningen i följande exempel för användaren nr 1 och 2 för användaren:

    - I **användarnamn** och **lösenord**, anger du de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer i
    - Datacenter1 - ge läsbehörighet till 1 användare och användare #2. Inte Sprid dessa behörigheter till alla underordnade objekt eftersom du ska ange behörigheter för enskilda Virtuella datorer.

      - VM1 (klient #1) (endast läsbehörighet till användare #1)
      - VM2 (klient #1) (endast läsbehörighet till användare #1)
      - VM3 (klient #2) (endast läsbehörighet till användaren nr. 2)
      - VM4 (klient #2) (endast läsbehörighet till användaren nr. 2)

   - Om du utför identifieringen med autentiseringsuppgifter för användare #1 kan sedan identifieras endast VM1 och VM2.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planera ditt migreringsprojekt och identifieringar

En enda Azure Migrate collector stöder identifiering från flera vCenter-servrar (efter varandra) och stöder flera migreringsprojekt identifieringen (efter varandra). Insamlaren fungerar i fire and -forget-modell, när identifiering är klar kan du använda samma insamlaren för att samla in data från en annan vCenter-Server eller skicka det till en annan migreringsprojekt.

Planera dina upptäckter och utvärderingar som baseras på följande begränsningar:

| **Entitet** | **Gränsen för datorn** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Identifiering  | 1,500             |
| Utvärdering | 1,500             |

Tänk på dessa överväganden:

- När du gör en identifiering med hjälp av Azure Migrate collector kan du ange identifieringsomfånget till en vCenter Server-mapp, datacenter, kluster eller en värddator.
- Om du vill göra mer än en identifiering, verifiera i vCenter Server som de virtuella datorerna som du vill identifiera i mappar, Datacenter, kluster eller värdar som har stöd för begränsning av 1 500 datorer.
- Vi rekommenderar att för utvärdering, du behåller datorer med beroenden inom samma projekt och utvärdering. Kontrollera att beroende datorer finns i samma mapp, datacenter eller kluster för utvärdering i vCenter Server.

Beroende på ditt scenario kan du dela upp dina identifieringar enligt nedan:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>Flera vCenter-servrar med mindre än 1 500 virtuella datorer

Om du har flera vCenter-servrar i din miljö och det totala antalet virtuella datorer är mindre än 1500, kan du använda en enda insamlare och en enda migreringsprojekt för att identifiera alla virtuella datorer mellan alla vCenter-servrar. Eftersom insamlaren identifierar en vCenter-Server i taget, kan du köra samma insamlaren mot alla vCenter-servrar, efter varandra och peka insamlaren på samma migreringsprojekt. När alla identifieringar är klar kan skapa du sedan utvärderingar för datorer.

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>Flera vCenter-servrar med mer än 1 500 virtuella datorer

Om du har flera vCenter-servrar med mindre än 1 500 virtuella datorer per vCenter-servern, men mer än 1 500 virtuella datorer mellan alla vCenter-servrar kan behöva du skapa flera migration-projekt (en migration-projekt kan innehålla endast 1 500 virtuella datorer). Du kan åstadkomma detta genom att skapa ett migreringsprojekt per vCenter-servern och dela identifieringar. Du kan använda en enda insamlare för att identifiera varje vCenter-Server (efter varandra). Om du vill identifieringar att starta på samma gång, kan du också distribuera flera enheter och köra identifieringar parallellt.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Mer än 1500 datorer i en enda vCenter-Server

Om du har fler än 1 500 virtuella datorer i en enda vCenter-Server, måste du dela upp identifieringen i flera migreringsprojekt. Om du vill dela identifieringar, kan du utnyttja fältet omfång i installationen och anger värden, kluster, mapp eller datacenter som du vill identifiera. Exempel: Om du har två mappar i vCenter Server, en med 1000 virtuella datorer (Mapp1) och andra med 800 virtuella datorer (mapp2), du kan använda en enda insamlare och utföra två identifieringar. I den första identifieringen kan du ange Mapp1 som omfång och peka den första migreringsprojekt när den första identifieringen är klar, du kan använda samma insamlaren genom att ändra sitt omfång till Mapp2 och migrering projektinformation till andra migreringsprojekt och göra andra identifiering.

### <a name="multi-tenant-environment"></a>Miljö för flera innehavare

Om du har en miljö som delas mellan klienter och du inte vill identifiera de virtuella datorerna i en klient i en annan klient prenumeration, kan du använda fältet omfång i insamlingsprogrammet att definiera omfattningen av identifieringen. Om klienterna delar värdar, skapar du autentiseringsuppgifter som har skrivskyddad åtkomst till endast de virtuella datorerna som hör till specifik klient och använda den här autentiseringsuppgiften i insamlingsprogrammet och ange omfång som värden för att göra identifieringen. Du kan också du kan också skapa mappar i vCenter Server (vi antar att Mapp1 för tenant1 och mapp2 för tenant2), under den dela värden, flytta de virtuella datorerna för tenant1 i Mapp1 och tenant2 till Mapp2 och omfång identifieringar i insamlaren i enlighet med detta genom att ange mappen.

## <a name="discover-on-premises-environment"></a>Identifiera lokala miljö

Du kan sedan starta identifiering av lokala virtuella datorer när du är klar med din plan:

### <a name="create-a-project"></a>Skapa ett projekt

Skapa ett Azure Migrate-projekt utifrån dina behov:

1. I Azure-portalen väljer du **Skapa en resurs**.
2. Sök efter **Azure Migrate** och välj tjänsten **Azure Migrate** i sökresultaten. Välj sedan **Skapa**.
3. Ange ett projektnamn och Azure-prenumerationen för projektet.
4. Skapa en ny resursgrupp.
5. Ange platsen där du vill skapa projektet och välj sedan **skapa**. Observera att du kan fortfarande utvärdera dina virtuella datorer för en annan målplats. Den angivna platsen för projektet används för att lagra de metadata som samlats in från lokala virtuella datorer.

### <a name="set-up-the-collector-appliance"></a>Konfigurera insamlingsprogrammet

Azure Migrate skapar en lokal virtuell dator som kallas för insamlarprogram. Den här virtuella datorn identifierar lokala virtuella VMware-datorer och skickar den metadata om dem till Azure Migrate-tjänsten. Om du vill konfigurera insamlingsprogrammet hämta OVA-filen och importera den till en lokal vCenter Server-instansen.

#### <a name="download-the-collector-appliance"></a>Hämta insamlingsprogrammet

Om du har flera projekt kan behöva du hämta insamlingsprogrammet bara en gång till vCenter Server. När du laddar ned och konfigurera installationen kan du köra den för varje projekt och anger du unika projekt-ID och nyckel.

1. I Azure Migrate-projekt väljer **komma igång** > **identifiera och utvärdera** > **identifiera datorer**.
2. I **identifiera datorer**väljer **hämta**, för att hämta OVA-filen.
3. I **kopiera projektautentiseringsuppgifterna**, kopiera-ID och nyckel för projektet. Du behöver dem när du konfigurerar insamlaren.


#### <a name="verify-the-collector-appliance"></a>Kontrollera insamlingsprogrammet

Kontrollera att OVA-filen är säker innan du distribuerar den:

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.

2. Kör följande kommando för att generera en hash för OVA-filen:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Se till att den genererade hashen matchar följande inställningar.

    För OVA-version 1.0.9.12

    **Algoritm** | **Hash-värde**
    --- | ---
    MD5 | d0363e5d1b377a8eb08843cf034ac28a
    SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
    SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

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

Importera den nedladdade filen till vCenter Server:

1. I vSphere-klientkonsolen väljer **filen** > **distribuera OVF-mall**.

    ![Distribuera OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. I guiden Distribuera OVF-mall > **källa**, ange platsen för OVA-filen.
3. I **Namn** och **Plats** anger du ett eget namn för den virtuella insamlardatorn och lagerobjektet där den virtuella datorn kommer att finnas.
4. I **Värd/kluster** anger du den värd eller det kluster där den virtuella insamlardatorn körs.
5. Ange lagringsplats för den virtuella insamlardatorn i lagringen.
6. I **Diskformat** anger du disktyp och storlek.
7. I **Nätverksmappning** anger du det nätverk som den virtuella insamlardatorn kommer att ansluta till. Nätverket måste ha en Internetanslutning för att skicka metadata till Azure.
8. Granska och bekräfta inställningarna och välj sedan **Slutför**.

### <a name="identify-the-id-and-key-for-each-project"></a>Identifiera-ID och nyckel för varje projekt

Om du har flera projekt måste du identifiera-ID och nyckel för var och en. Du behöver nyckeln när du kör insamlaren att identifiera de virtuella datorerna.

1. I projektet, väljer **komma igång** > **identifiera och utvärdera** > **identifiera datorer**.
2. I **kopiera projektautentiseringsuppgifterna**, kopiera-ID och nyckel för projektet.
    ![Kopiera projektautentiseringsuppgifterna](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>Ange statistiknivån
Följande är listan över prestandaräknare som samlas in under identifieringen. Räknarna ingår som standard som är tillgängliga på olika nivåer i vCenter Server.

Vi rekommenderar att du ställer in vanliga filegenskaper (3) för statistik nivå så att alla prestandaräknare som samlas in korrekt. Om du har satt till en lägre nivå vCenter, kan endast några räknare samlas helt, med resten har angetts till 0. Utvärderingen kan sedan visa ofullständiga data.

I följande tabell visas även utvärderingsresultat som kommer att påverkas om en särskild räknare inte har samlats in.

| Räknare                                 | Nivå | Nivå per enhet | Utvärdering av påverkan                    |
| --------------------------------------- | ----- | ---------------- | ------------------------------------ |
| CPU.Usage.Average                       | 1     | Ej tillämpligt               | Rekommenderad storlek och kostnad         |
| Mem.Usage.Average                       | 1     | Ej tillämpligt               | Rekommenderad storlek och kostnad         |
| virtualDisk.read.average                | 2     | 2                | Diskens storlek, kostnaden för lagring och VM-storlek |
| virtualDisk.write.average               | 2     | 2                | Diskens storlek, kostnaden för lagring och VM-storlek |
| virtualDisk.numberReadAveraged.average  | 1     | 3                | Diskens storlek, kostnaden för lagring och VM-storlek |
| virtualDisk.numberWriteAveraged.average | 1     | 3                | Diskens storlek, kostnaden för lagring och VM-storlek |
| NET.Received.Average                    | 2     | 3                | VM-storlek och nätverk kostnad             |
| NET.Transmitted.Average                 | 2     | 3                | VM-storlek och nätverk kostnad             |

> [!WARNING]
> Om du precis har högre statistik, ska det ta upp till en dag att generera prestandaräknare. Därför rekommenderar vi att du kör identifieringen efter en dag.

### <a name="run-the-collector-to-discover-vms"></a>Kör insamlaren för att identifiera virtuella datorer

För varje typ av upptäckt du behöver utföra, kör insamlaren för att identifiera virtuella datorer i det nödvändiga omfånget. Kör identifieringar som efter varandra. Samtidiga identifieringar stöds inte och varje identifiering måste ha ett annat omfång.

1.  Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2.  Ange språk, tidszon och lösenordsinställningar för produkten.
3.  På skrivbordet, Välj den **kör insamlare** genväg.
4.  Öppna i Azure Migrate-insamlaren **konfigurera förhandskraven** och sedan:

    a. Acceptera licensvillkoren och läs informationen från tredje part.

    Insamlaren kontrollerar att den virtuella datorn har Internetåtkomst.

    b. Om den virtuella datorn har åtkomst till internet via en proxyserver, väljer **proxyinställningar**, och anger proxyadress och lyssningsport. Ange autentiseringsuppgifter om proxyn kräver autentisering.

    Insamlaren kontrollerar att insamlingstjänsten körs. Tjänsten installeras som standard på den virtuella insamlardatorn.

    c. Ladda ned och installera VMware PowerCLI.

5.  Gör följande i **Specify vCenter Server details** (Ange vCenter Server-information):
    - Ange namn (FQDN) eller IP-adress för vCenter-servern.
    - I **användarnamn** och **lösenord**, anger du de skrivskyddade kontoautentiseringsuppgifter som insamlaren använder för att identifiera virtuella datorer i vCenter Server.
    - I **Välj omfattning** väljer du en omfattning för identifieringen av virtuella datorer. Insamlaren kan identifiera virtuella datorer i angivet omfång. Omfånget kan anges till en viss mapp, ett datacenter eller ett kluster. Det får inte innehålla fler än 1 000 virtuella datorer.

6.  I **ange migreringsprojekt**, ange ID och nyckel för projektet. Om du inte kopierade dem öppnar du Azure-portalen från den Virtuella insamlardatorn. På projektets **översikt** väljer **identifiera datorer** och kopierar värdena.  
7.  I **visa insamlingsförloppet**, övervaka identifieringsprocessen och kontrollera att metadata som samlas in från de virtuella datorerna är i omfattningen. Insamlaren visar en ungefärlig identifieringstid.


#### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Identifieringstiden beror på hur många virtuella datorer du identifierar. 100 virtuella datorer har vanligtvis identifiering cirka en timme när insamlaren är klar.

1. Välj i projektet Migreringshanteraren **hantera** > **datorer**.
2. Kontrollera att de virtuella datorer som du vill identifiera visas i portalen.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en grupp](how-to-create-a-group.md) för utvärdering.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
