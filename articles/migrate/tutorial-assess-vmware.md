---
title: Utvärdera virtuella VMware-datorer för migrering till Azure
description: Beskriver hur du bedömer lokala virtuella datorer med VMware för migrering till Azure med Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 03/23/2019
ms.openlocfilehash: 944b7c12a353a29a172576974261eece63ebf668
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548751"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Utvärdera virtuella virtuella datorer med hjälp av Azure Migrate Server Assessment

Den här artikeln visar hur du bedömer lokala virtuella VMware-datorer (VMs) med hjälp av verktyget [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Den här självstudien är den andra i en serie som visar hur du bedömer och migrerar virtuella virtuella datorer med VMware till Azure. I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera en Azure Migrate-apparat som körs lokalt för att bedöma virtuella datorer.
> * Starta kontinuerlig identifiering av lokala virtuella datorer. Installationen skickar konfigurations- och prestandadata för identifierade virtuella datorer till Azure.
> * Grupp upptäckte virtuella datorer och utvärdera vm-gruppen.
> * Se över bedömningen.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario så att du snabbt kan ställa in ett proof-of-concept. Självstudier använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i instruktionsartiklarna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.

## <a name="prerequisites"></a>Krav

- [Slutför den första självstudien](tutorial-prepare-vmware.md) i den här serien. Om du inte gör det kommer instruktionerna i den här självstudien inte att fungera.
- Här är vad du borde ha gjort i den första handledningen:
    - [Förbered Azure](tutorial-prepare-vmware.md#prepare-azure) för att arbeta med Azure Migrate.
    - [Förbered VMware för bedömning](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) för bedömning. Detta inkluderar att kontrollera VMware-inställningar, konfigurera ett konto som Azure Migrate kan använda för att komma åt vCenter Server.
    - [Verifiera](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) vad du behöver för att distribuera Azure Migrate-enheten för VMware-utvärdering.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Konfigurera ett nytt Azure Migrate-projekt enligt följande:

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
1. Under **Tjänster** väljer du **Azure Migrate**.
1. I **Översikt**väljer du Utvärdera och migrera servrar under **Upptäck, utvärdera och migrera** **servrar**.

   ![Knapp för att bedöma och migrera servrar](./media/tutorial-assess-vmware/assess-migrate.png)

1. Välj **Lägg till verktyg**i Komma **igång**.
1. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.     
1. I **Projektinformation**anger du projektnamnet och geografin där du vill skapa projektet. Asien, Europa, Storbritannien och USA stöds.

   Projektgeografin används bara för att lagra de metadata som samlas in från lokala virtuella datorer. Du kan välja vilken målregion du vill när du kör en migrering.

   ![Rutor för projektnamn och projektregion](./media/tutorial-assess-vmware/migrate-project.png)

1. Välj **Nästa**.
1. I **Välj bedömningsverktyg**väljer du **Azure Migrate: Serverutvärdering** > **nästa**.

   ![Val för serverbedömningsverktyget](./media/tutorial-assess-vmware/assessment-tool.png)

1. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
1. Granska inställningarna i **Granska + lägg till verktyg**och välj Lägg till **verktyg**.
1. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate-enheten

Azure Migrate:Server Assessment använder en lätt Azure Migrate-installation. Installationen av virtuella datorer utför vm-identifiering och skickar VM-metadata och prestandadata till Azure Migrate.
- Apparaten kan ställas in på en virtuell virtuell VMware-dator med hjälp av en nedladdad OVA-mall. Du kan också ställa in enheten på en virtuell dator eller en fysisk dator med ett PowerShell-installationsskript.
- Den här självstudien använder OVA-mallen. Läs [den här artikeln](deploy-appliance-script.md) om du vill konfigurera apparaten med ett skript.

När du har skapat installationen kontrollerar du att den kan ansluta till Azure Migrate:Server Assessment, konfigurera den för första gången och registrera den med Azure Migrate-projektet.



### <a name="download-the-ova-template"></a>Ladda ner OVA-mallen

1. I **Migreringsmålsservrar** > **Servers** > **Azure Migrera: Serverutvärdering**väljer du **Upptäck**.
1. I **Upptäck-datorer** >  **Yes, with VMWare vSphere hypervisor****Virtualiseras dina datorer?**
1. Välj **Hämta** om du vill hämta OVA-mallfilen.

   ![Val för att ladda ner en OVA-fil](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verifiera säkerhet

Kontrollera att OVA-filen är säker innan du distribuerar den:

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
1. Kör följande kommando för att generera hash för OVA-filen:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

För version 2.19.07.30 ska det genererade hash-värdet matcha dessa värden:

**Algoritm** | **Hash-värde**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>Skapa den virtuella datorn för den använda datorn

Importera den hämtade filen och skapa en virtuell dator:

1. I vSphere-klientkonsolen väljer du > **OVF-mall för filutgjutning**. **File**

   ![Menykommando för distribution av en OVF-mall](./media/tutorial-assess-vmware/deploy-ovf.png)

1. Ange platsen för OVA-filen i guiden Distribuera **OVF-mall > källa.**
1. Ange ett eget namn för den virtuella datorn i **Namn** och **Plats.** Välj det lagerobjekt där den virtuella datorn ska vara värd.
1. I **Host/Cluster**anger du den värd eller det kluster som den virtuella datorn ska köras på.
1. I **Lagring**anger du lagringsmålet för den virtuella datorn.
1. I **Diskformat** anger du disktyp och storlek.
1. I **Nätverksmappning**anger du det nätverk som den virtuella datorn ska ansluta till. Nätverket behöver internetanslutning för att skicka metadata till Azure Migrate Server Assessment.
1. Granska och bekräfta inställningarna och välj sedan **Slutför**.

### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till Azure

Se till att den virtuella datorn för den virtuella enheten kan ansluta till [Azure-url:er](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Konfigurera apparaten

Ställ in apparaten för första gången.

> [!NOTE]
> Om du konfigurerar apparaten med ett [PowerShell-skript](deploy-appliance-script.md) i stället för det nedladdade OVA är de två första stegen i den här proceduren inte relevanta.

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen och välj sedan **Öppna konsol**.
1. Ange språk, tidszon och lösenord för apparaten.
1. Öppna en webbläsare på alla datorer som kan ansluta till den virtuella datorn och öppna url:en för appen för installationen: **https://*apparatens namn eller IP-adress:* 44368**.

   Alternativt kan du öppna appen från apparatens skrivbord genom att välja appgenvägvägen.
1. Gör följande i webbappen > **Konfigurera förutsättningar:**
   - **Licens**: Acceptera licensvillkoren och läs informationen från tredje part.
   - **Anslutning**: Appen kontrollerar att den virtuella datorn har tillgång till internet. Om den virtuella datorn använder en proxy:
     - Välj **Proxyinställningar**och ange proxyadress och lyssningsport i formuläret http://ProxyIPAddress eller http://ProxyFQDN.
     - Ange autentiseringsuppgifter om proxyn kräver autentisering.
     - Observera att endast HTTP-proxy stöds.
   - **Tidssynkronisering:** Tiden på apparaten ska vara synkroniserad med internettiden för att identifieringen ska fungera korrekt.
   - **Installera uppdateringar:** Apparaten ser till att de senaste uppdateringarna är installerade.
   - **Installera VDDK:** Apparaten kontrollerar att VMWare vSphere Virtual Disk Development Kit (VDDK) är installerat. Om den inte är installerad hämtar du VDDK 6.7 från VMware och extraherar det nedladdade zip-innehållet till den angivna platsen på apparaten.

     Azure Migrera servermigrering använder VDDK för att replikera datorer under migreringen till Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera installationen med Azure Migrate

1. Välj **Logga in**. Om den inte visas kontrollerar du att du har inaktiverat popup-blockeraren i webbläsaren.
1. Logga in med ditt Användarnamn och lösenord för Azure på den nya fliken.
   
   Inloggning med en PIN-kod stöds inte.
1. När du har loggat in går du tillbaka till webbappen.
1. Välj den prenumeration där Azure Migrate-projektet skapades och välj sedan projektet.
1. Ange ett namn på apparaten. Namnet ska vara alfanumeriskt med 14 tecken eller färre.
1. Välj **Registrera**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Installationen måste ansluta till vCenter Server för att identifiera konfigurations- och prestandadata för de virtuella datorerna.

### <a name="specify-vcenter-server-details"></a>Ange vCenter Server-information
1. Ange namnet (FQDN) eller IP-adressen för vCenter Server-instansen i **Ange**information om vCenter Server. Du kan lämna standardporten eller ange en anpassad port där vCenter Server lyssnar.
2. I **Användarnamn** och **Lösenord**anger du de autentiseringsuppgifter för vCenter Server-kontot som används för att identifiera virtuella datorer i vCenter Server-instansen. 

    - Du bör ha konfigurerat ett konto med de behörigheter som krävs i [föregående självstudiekurs](tutorial-prepare-vmware.md#set-up-an-account-for-assessment).
    - Om du vill begränsa identifieringen till specifika VMware-objekt (vCenter Server datacenter, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda virtuella datorer.), läser du instruktionerna i den [här artikeln](set-discovery-scope.md) för att begränsa kontot som används av Azure Migrate.

3. Välj **Validera anslutning** för att kontrollera att enheten kan ansluta till vCenter Server.
4. I **Identifiera program och beroenden för virtuella datorer**klickar du eventuellt på Lägg till **autentiseringsuppgifter**och anger vilket operativsystem autentiseringsuppgifterna är relevanta för och användarnamn och lösenord för autentiseringsuppgifter. Klicka sedan på **Lägg till**..

    - Du kan också lägga till autentiseringsuppgifter här om du har skapat ett konto som ska användas för [programidentifieringsfunktionen](how-to-discover-applications.md)eller [funktionen för beroendeanalys utan agent.](how-to-create-group-machine-dependencies-agentless.md)
    - Om du inte använder dessa funktioner kan du hoppa över den här inställningen.
    - Granska de autentiseringsuppgifter som behövs för [appidentifiering](migrate-support-matrix-vmware.md#application-discovery)eller för [agentlös analys](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements).

5. **Spara och starta identifieringen**för att starta vm-identifiering.

Discovery fungerar på följande sätt:
- Det tar cirka 15 minuter innan identifierade VM-metadata visas i portalen.
- Det tar lite tid att identifiera installerade program, roller och funktioner. Varaktigheten beror på antalet virtuella datorer som upptäcks. För 500 virtuella datorer tar det ungefär en timme för programinventeringen att visas i Azure Migrate-portalen.

### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Efter identifieringen kan du kontrollera att de virtuella datorerna visas i Azure-portalen:

1. Öppna instrumentpanelen för Azure Migrate.
1. I **Azure Migrate - Servrar** > **Azure Migrera: Serverutvärdering**väljer du den ikon som visar antalet för **identifierade servrar**.

## <a name="set-up-an-assessment"></a>Ställ in en utvärdering

Du kan skapa två typer av utvärderingar med hjälp av Azure Migrate Server Assessment:

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestationsbaserad** | Bedömningar baserade på insamlade resultatdata | **Rekommenderad VM-storlek:** Baserat på CPU- och minnesanvändningsdata.<br/><br/> **Rekommenderad disktyp (standard eller premiumhanterad disk)**: Baserat på IOPS och dataflöde för de lokala diskarna.
**Som lokalt** | Bedömningar baserade på lokal storlek | **Rekommenderad VM-storlek:** Baserat på den lokala vm-storleken.<br/><br> **Rekommenderad disktyp:** Baserat på den lagringstypinställning som du väljer för utvärderingen.

## <a name="run-an-assessment"></a>Köra en utvärdering

Gör en bedömning på följande sätt:

1. Granska [metodtipsen](best-practices-assessment.md) för att skapa utvärderingar.
1. På fliken **Servrar** väljer du **Utvärdera**i panelen **Azure Migrate: Serverutvärdering.**

   ![Placering av knappen Bedömning](./media/tutorial-assess-vmware/assess.png)

1. Ange ett namn för utvärderingen i **Bedöma servrar.**
1. Välj **Visa alla**och granska sedan bedömningsegenskaperna.

   ![Bedömningsegenskaper](./media/tutorial-assess-vmware/view-all.png)

1. Välj **Skapa ny**i Markera eller skapa **en grupp**och ange ett gruppnamn. En grupp samlar ihop en eller flera virtuella datorer för bedömning.
1. I **Lägg till datorer i gruppen**väljer du virtuella datorer som ska läggas till i gruppen.
1. Välj **Skapa bedömning om** du vill skapa gruppen och köra utvärderingen.

   ![Utvärdera servrar](./media/tutorial-assess-vmware/assessment-create.png)

1. När utvärderingen har skapats visar du den i **Servrar** > **Azure Migrera: Serverutvärderingsutvärderingar** > **Assessments**.
1. Välj **Exportera bedömning** om du vill hämta den som en Excel-fil.

## <a name="review-an-assessment"></a>Granska en bedömning

En bedömning beskriver:

- **Azure-beredskap**: Om virtuella datorer är lämpliga för migrering till Azure.
- **Uppskattning av månadskostnad**: Uppskattade månatliga beräknings- och lagringskostnader för att köra de virtuella datorerna i Azure.
- **Uppskattning av månatliga lagringskostnader**: Uppskattade kostnader för disklagring efter migreringen.

Så här visar du en bedömning:

1. I **Migreringsmålsservrar** > **Servers**väljer du **Utvärderingar** i **Azure Migrera: Serverutvärdering**.
1. Välj en bedömning för att öppna den i **Utvärderingar.**

   ![Sammanfattning av bedömning](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure-beredskap

1. I **Azure-beredskap**kontrollerar du om virtuella datorer är redo för migrering till Azure.
1. Granska vm-statusen:
    - **Klar för Azure:** Används när Azure Migrate rekommenderar en virtuell datorstorlek och kostnadsuppskattningar för virtuella datorer i utvärderingen.
    - **Redo med villkor**: Visar problem och föreslagen reparation.
    - **Inte redo för Azure**: Visar problem och föreslagen reparation.
    - **Okänd beredskap**: Används när Azure Migrate inte kan bedöma beredskapen på grund av problem med datatillgänglighet.

1. Välj en **Azure-beredskapsstatus.** Du kan visa vm-beredskapsinformation. Du kan också öka detaljnivån för att se vm-information, inklusive beräknings-, lagrings- och nätverksinställningar.

### <a name="review-cost-details"></a>Granska kostnadsinformation

Utvärderingssammanfattningen visar den uppskattade beräknings- och lagringskostnaden för att köra virtuella datorer i Azure. Kostnaderna aggregeras för alla virtuella datorer i den bedömda gruppen. Du kan öka detaljnivån för att se kostnadsinformation för specifika virtuella datorer.

> [!NOTE]
> Kostnadsuppskattningar baseras på storleksrekommendationerna för en dator, dess diskar och dess egenskaper. Uppskattningar är för att köra de lokala virtuella datorerna som virtuella IaaS-datorer. Azure Migrate Server Assessment tar inte hänsyn till PaaS eller SaaS kostnader.

De aggregerade lagringskostnaderna för den bedömda gruppen är uppdelade över olika typer av lagringsdiskar. 

### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

Azure Migrate Server Assessment tilldelar en konfidensklassificering till en prestandabaserad bedömning, från 1 stjärna (lägsta) till 5 stjärnor (högst).

![Säkerhetsomdöme](./media/tutorial-assess-vmware/confidence-rating.png)

Konfidensklassificeringen hjälper dig att uppskatta tillförlitligheten i bedömningens storleksrekommendationer. Klassificeringen baseras på tillgången på datapunkter som behövs för att beräkna bedömningen:

**Tillgänglighet för datapunkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

[Läs mer om metodtips](best-practices-assessment.md#best-practices-for-confidence-ratings) för konfidensklassificeringar.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du en Azure Migrate-installation. Du har också skapat och granskat en bedömning.

Om du vill veta hur du migrerar virtuella virtuella VMware-datorer till Azure med hjälp av Azure Migrate Server Migration fortsätter du till den tredje självstudien i serien:

> [!div class="nextstepaction"]
> [Migrera virtuella VMware-datorer](./tutorial-migrate-vmware.md)
