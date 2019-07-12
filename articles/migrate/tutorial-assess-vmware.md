---
title: Utvärdera virtuella VMware-datorer för migrering till Azure med Azure Migrate
description: Beskriver hur du utvärdera lokala virtuella VMware-datorer för migrering till Azure med hjälp av Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 5dc1a05e93bf1e82269a4291f147bac6e8ba657a
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813010"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Utvärdera virtuella VMware-datorer med Azure Migrate: Server-utvärdering

Den här artikeln visar hur du utvärdera lokala virtuella VMware-datorer, med hjälp av Azure Migrate: Server Assessment tool.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en hubb för verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Hubben innehåller Azure Migrate verktyg och erbjudanden för programvara från tredje part för oberoende programvaruleverantörer (ISV).



Den här självstudien är andra i en serie som visar hur du utvärdera och migrera virtuella VMware-datorer till Azure. I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera en Azure Migrate-installation som körs lokalt för att utvärdera virtuella datorer.
> * Starta kontinuerlig identifiering av lokala virtuella datorer. Enheten som skickar configuration-och prestandadata för identifierade virtuella datorer till Azure.
> * Gruppera identifierade virtuella datorer och utvärdera VM-gruppen.
> * Granska utvärderingen.



> [!NOTE]
> Självstudiekurser visar den enklaste distribution sökvägen för ett scenario så att du snabbt kan ställa in proof-of-concept. Använd standardalternativen där det är möjligt självstudier och visas inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionsartiklarna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

- [Fullständig](tutorial-prepare-vmware.md) den första kursen i den här serien. Om du inte fungerar inte anvisningarna i den här kursen.
- Här är vad du bör har gjort i den första kursen:
    - [Konfigurera Azure-behörigheter](tutorial-prepare-vmware.md#prepare-azure) för Azure Migrate.
    - [Förbereda VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) för utvärdering. VMware-inställningarna bör verifieras och du bör ha behörighet att skapa en VMware-VM med en OVA-mall. Du bör också ha ett konto som har ställts in för identifieringen av virtuella datorer. Portar som krävs ska vara tillgängliga och du bör känna till URL: er som behövs för åtkomst till Azure.


## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Ställa in ett nytt Azure Migrate-projekt på följande sätt.

1. I Azure portal > **alla tjänster**, Sök efter **Azure Migrate**.
2. Under **Services**väljer **Azure Migrate**.
3. I **översikt**under **Upptäck, utvärdera och migrera servrar för**, klickar du på **utvärdera och migrera servrar för**.

    ![Identifiera och utvärdera servrar](./media/tutorial-assess-vmware/assess-migrate.png)

4. I **komma igång**, klickar du på **lägga till verktyg**.
5. I **migrera projektet**, Välj din Azure-prenumeration och skapa en resursgrupp om du inte har något.     
6. I **projektinformation**, ange projektnamnet på och geografiska plats där du vill skapa projektet. Asien, Europa, Storbritannien och USA stöds.

    - Geografi för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer.
    - Du kan välja en målregion när du kör en migrering.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-vmware/migrate-project.png)


7. Klicka på **Nästa**.
8. I **väljer utvärderingsvertyget**väljer **Azure Migrate: Server-utvärdering** > **nästa**.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-vmware/assessment-tool.png)

9. I **väljer Migreringsverktyget**väljer **hoppa över att lägga till en Migreringsverktyget för tillfället** > **nästa**.
10. I **granska + Lägg till verktyg**, granskar du inställningarna och klicka på **lägga till verktyg**.
11. Vänta några minuter för Azure Migrate-projektet ska distribueras. Du kommer till projektsidan för. Om du inte ser projektet, du kan komma åt den från **servrar** i instrumentpanelen för Azure Migrate.


## <a name="set-up-the-appliance-vm"></a>Konfigurera den virtuella datorn-installationen

Azure Migrate: Server-utvärdering körs en förenklad VMware VM-installation.

- Den här installationen utför identifieringen av virtuella datorer och skickar VM metadata och prestanda till Azure Migrate Server-utvärdering.
- Du ställer in enheten som du:
    - Ladda ned en mall för OVA-filen och importera dem till vCenter-servern.
    - Skapa och kontrollera att den kan ansluta till Azure Migrate Server-utvärdering.
    - Konfigurera programmet för första gången och registrera den med Azure Migrate-projektet.

### <a name="download-the-ova-template"></a>Hämta OVA-mall

1. I **migrering mål** > **servrar** > **Azure Migrate: Server-utvärdering**, klickar du på **identifiera**.
2. I **identifiera datorer** > **är dina datorer virtualiserade?** , klickar du på **Ja, med VMWare vSphere hypervisor**.
3. Klicka på **hämta** att ladda ned den. OVA mallfilen.

    ![Hämta .OVA-fil](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Kontrollera säkerheten

Kontrollera att OVA-filen är säker, innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera en hash för OVA-filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Den genererade hashen måste matcha värdena för version 1.19.06.27. 

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | 605d208ac5f4173383f616913441144e
  SHA256 | 447d16bd55f20f945164a1189381ef6e98475b573d6d1c694f3e5c172cfc30d4


### <a name="create-the-appliance-vm"></a>Skapa virtuell dator

Importera den nedladdade filen och skapa en virtuell dator.

1. I vSphere-klientkonsolen klickar du på **Arkiv** > **Distribuera OVF-mall**.

    ![Distribuera OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. I guiden Distribuera OVF-mall > **källa**, ange platsen för OVA-filen.
3. I **namn** och **plats**, ange ett eget namn för den virtuella datorn. Välj objektet lager där den virtuella datorn kommer att finnas.
5. I **värden/klustret**, anger du värden eller kluster som den virtuella datorn körs.
6. I **Storage**, ange lagringsplats för den virtuella datorn.
7. I **Diskformat** anger du disktyp och storlek.
8. I **nätverksmappning**, ange det nätverk som den virtuella datorn ska ansluta till. Nätverket måste ha en Internetanslutning för att kunna skicka metadata till Azure Migrate Server-utvärdering.
9. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera installationen åtkomst till Azure

Kontrollera att installationen VM kan ansluta till [Azure URL: er](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Konfigurera programmet

Ställ in den installationen med följande steg.

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2. Ange språk, tidszon och lösenord för installationen.
3. Öppna en webbläsare på en dator som kan ansluta till den virtuella datorn och öppna URL: en för webbappen installation: **https://*installationsnamnet eller IP-adress*: 44368**.

   Du kan också öppna appen från skrivbordet installation genom att klicka på genvägen till appen.
4. I webbapp > **konfigurera förhandskraven**, gör du följande:
    - **Licens**: Acceptera licensvillkoren och läs informationen från tredje part.
    - **Anslutningen**: Appen kontrollerar att den virtuella datorn har Internetåtkomst. Om den virtuella datorn använder en proxyserver:
        - Klicka på **proxyinställningar**, och anger proxyadress och lyssningsport, i formatet http://ProxyIPAddress eller http://ProxyFQDN.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tid synkronisering**: tiden på enheten som ska vara synkroniserad med internettiden för identifiering ska fungera korrekt.
    - **Installera uppdateringar**: Installationen säkerställer att de senaste uppdateringarna installeras.
    - **Installera VDDK**: Installationen kontrollerar som VMWare vSphere virtuell Disk Development Kit (VDDK) är installerad.
        - Azure Migrate: Servermigrering använder VDDK för att replikera virtuella datorer under migreringen till Azure.
        - Hämta VDDK 6.7 från VMware och extrahera hämtade zip-innehållet till den angivna platsen i installationen.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klicka på **logga In**. Om den inte visas kan du kontrollera att du har inaktiverat blockering av popup-fönster i webbläsaren.
2. På den nya fliken kan du logga in med dina autentiseringsuppgifter för Azure.
    - Logga in med ditt användarnamn och lösenord.
    - Logga in med en PIN-kod stöds inte.
3. När du har loggat in kan gå tillbaka till webbappen.
2. Välj den prenumeration som Azure Migrate-projektet har skapats och sedan projektet.
3. Ange ett namn för produkten. Namnet bör vara alfanumeriskt med 14 tecken eller mindre.
4. Klicka på **registrera**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifieringen

Nu kan ansluta från installationen till vCenter-servern och starta identifieringen av virtuella datorer.

1. I **ange vCenter-serverinformationen**, ange namn (FQDN) eller IP-adress för vCenter-servern. Du kan lämna standardporten eller ange en anpassad port som vCenter-servern lyssnar.
2. I **användarnamn** och **lösenord**, ange de skrivskyddade kontoautentiseringsuppgifter som installationen använder för att identifiera virtuella datorer på vCenter-servern. Kontrollera att tjänstkontot har de [behörigheter som krävs för identifiering av](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Klicka på **Validera anslutning** att se till att installationen kan ansluta till vCenter-servern.
4. När anslutningen har upprättats klickar du på **spara och starta identifieringen**.


Detta startar identifieringen. Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer att visas på portalen.

### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Efter identifiering, kan du kontrollera att de virtuella datorerna visas i Azure-portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. I **Azure Migrate - servrar** > **Azure Migrate: Server-utvärdering** klickar du på den ikon som visar antalet för **identifierade servrar**.

## <a name="set-up-an-assessment"></a>Konfigurera en utvärdering

Det finns två typer av utvärderingar kan du skapa med Azure Migrate: Server-utvärdering.

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar utifrån insamlade prestandadata | **Rekommenderad storlek**: Baserat på användningsdata för CPU och minne.<br/><br/> **Rekommenderade disktyp (standard eller premium managed disk)** : Baserat på IOPS och dataflöden för lokala diskar.
**Som lokalt** | Utvärderingar baserat på den lokala storlek. | **Rekommenderad storlek**: Baserat på den lokala VM-storlek<br/><br> **Rekommenderade disktyp**: Baserat på storage-typ du väljer för utvärderingen.


### <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. Granska den [bästa praxis](best-practices-assessment.md) för att skapa utvärderingar.
2. I den **servrar** fliken **Azure Migrate: Server-utvärdering** klickar **utvärdera**.

    ![Utvärdera](./media/tutorial-assess-vmware/assess.png)

2. I **utvärdera servrar**, ange ett namn för utvärderingen.
3. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Utvärderingsegenskaper](./media/tutorial-assess-vmware/view-all.png)

3. I **Välj eller skapa en grupp**väljer **Skapa ny**, och ange ett gruppnamn. En grupp samlar in en eller flera virtuella datorer för utvärdering.
4. I **lägga till datorer i gruppen**, Välj virtuella datorer för att lägga till i gruppen.
5. Klicka på **skapa utvärdering** att skapa gruppen och kör utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-vmware/assessment-create.png)

6. När utvärderingen har skapats kan du visa den i **servrar** > **Azure Migrate: Server-utvärdering** > **utvärderingar**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.



## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskrivs:

- **Azure-beredskap**: Om virtuella datorer är lämpliga för migrering till Azure.
- **Månatliga kostnadsuppskattning**: De uppskattade månatliga beräknings- och kostnaderna för att köra de virtuella datorerna i Azure.
- **Månatliga storage kostnadsuppskattning**: Beräknade kostnader för disklagring efter migreringen.

### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **migrering mål** >  **servrar**, klickar du på **utvärderingar** i **Azure Migrate: Server-utvärdering**.
2. I **utvärderingar**, klicka på en utvärdering och öppna den.

    ![Sammanfattning av uppgraderingsutvärderingen](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure-beredskap

1. I **Azure diagnostisk**, kontrollera om virtuella datorer är klara för migrering till Azure.
2. Granska virtuella datorns status:
    - **Redo för Azure**: Azure Migrate rekommenderar en storlek och kostnad uppskattningar för virtuella datorer i utvärderingen.
    - **Redo på vissa villkor**: Visar problem och förslagna lösningar.
    - **Ej redo för Azure**: Visar problem och förslagna lösningar.
    - **Beredskap okänd**: Används när Azure Migrate inte kan utvärdera beredskap på grund av problem med tillgängligheten för data.

2. Klicka på en **Azure diagnostisk** status. Du kan visa information om VM-beredskap och öka detaljnivån till Se VM information, inklusive beräkning, lagring och nätverksinställningar.



### <a name="review-cost-details"></a>Kontrollera kostnaden informationen

Den här vyn visar den uppskattade kostnaden för beräkning och lagring av virtuella datorer i Azure.

1. Granska månadskostnader för beräkning och lagring. Kostnader räknas samman för alla virtuella datorer i gruppen ha utvärderats.

    - Kostnaden baseras på storleksrekommendationer för en dator och dess diskar och egenskaper.
    - Uppskattade månatliga kostnader för beräkning och lagring visas.
    - Det är en översikt för att köra lokala virtuella datorer som virtuella IaaS-datorer. Azure Migrate Server-utvärdering tar inte hänsyn PaaS och SaaS.

2. Du kan granska varje månad kostnadsuppskattningar för lagring. Detta visar aggregerade lagringskostnader för gruppen utvärderade dela över olika typer av storage-diskar.
3. Du kan öka detaljnivån till mer information för specifika virtuella datorer.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör prestandabaserad utvärderingar, tilldelas ett säkerhetsomdöme utvärderingen.

![Säkerhetsomdöme](./media/tutorial-assess-vmware/confidence-rating.png)

- Du får ett omdöme intervallet 1 stjärna (lägst) till 5 stjärnor (högsta).
- Säkerhetsomdömet kan du beräkna tillförlitligheten för storleksrekommendationer som tillhandahålls av utvärderingen.
- Säkerhetsomdömet baseras på tillgängligheten av datapunkter som behövs för att beräkna utvärderingen.

Förtroende klassificeringar för en utvärdering är som följer.

**Tillgänglighet för datapunkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

[Läs mer](best-practices-assessment.md#best-practices-for-confidence-ratings) om bästa praxis för säker betygsättning.


## <a name="next-steps"></a>Nästa steg

I den här kursen för du göra följande:

> [!div class="checklist"]
> * Konfigurera en Azure Migrate-installation
> * Skapat och granskas av en utvärdering

Fortsätta att den tredje självstudien i serien, och lär dig hur du migrerar virtuella VMware-datorer till Azure med Azure Migrate servermigrering.

> [!div class="nextstepaction"]
> [Migrera virtuella VMware-datorer](./tutorial-migrate-vmware.md)
