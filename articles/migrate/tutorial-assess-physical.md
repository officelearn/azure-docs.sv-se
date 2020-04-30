---
title: Utvärdera fysiska servrar för migrering till Azure med Azure Migrate Server-utvärdering
description: Beskriver hur du bedömer lokala fysiska servrar för migrering till Azure med hjälp av Azure Migrate Server bedömning.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: b36cba18bd154cd5d14e16a9f8bf85cda6bf87a8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535442"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Utvärdera fysiska servrar med Azure Migrate: Server utvärdering

Den här artikeln visar hur du kan utvärdera lokala fysiska servrar med hjälp av verktyget Azure Migrate: Server bedömning.

[Azure Migrate](migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV).

Den här självstudien är den andra i en serie som visar hur du bedömer och migrerar fysiska servrar till Azure. I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera en Azure Migrate-apparat som körs lokalt för att utvärdera fysiska servrar.
> * Starta kontinuerlig identifiering av lokala fysiska servrar. Enheten skickar konfigurations-och prestanda data för identifierade servrar till Azure.
> * Gruppera identifierade servrar och utvärdera Server gruppen.
> * Granska utvärderingen.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktions artiklar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.


## <a name="prerequisites"></a>Krav

- [Slutför](tutorial-prepare-physical.md) den första självstudien i den här serien. Om du inte gör det fungerar inte instruktionerna i den här självstudien.
- Det här är vad du behöver göra i den första självstudien:
    - [Konfigurera Azure-behörigheter](tutorial-prepare-physical.md) för Azure Migrate.
    - [Förbered fysiska servrar](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) för utvärdering. Installations kraven bör kontrol leras. Du bör också ha ett konto som har kon figurer ATS för fysisk server identifiering. De portar som krävs bör vara tillgängliga och du bör vara medveten om de URL: er som behövs för åtkomst till Azure.




## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Konfigurera ett nytt Azure Migrate-projekt enligt följande.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** under **Identifiera, utvärdera och migrera servrar** klickar du på **Utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/tutorial-assess-physical/assess-migrate.png)

4. I **Komma igång** klickar du på **Lägg till verktyg**.
5. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.  
6. I **projekt information**anger du projekt namnet och geografin som du vill skapa projektet i. Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

    - Projektets geografi används bara för att lagra metadata som samlats in från lokala servrar.
    - Du kan välja vilken målregion du vill när du kör en migrering.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-physical/migrate-project.png)


7. Klicka på **Nästa**.
8. I **Välj bedömnings verktyg**väljer du **Azure Migrate: Server utvärdering** > **Nästa**.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-physical/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. I **Granska + Lägg till verktyg**granskar du inställningarna och klickar på **Lägg till verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.


## <a name="set-up-the-appliance"></a>Konfigurera installationen

Azure Migrate: Server utvärderingen kör en förenklad installation.

- Den här installationen utför fysisk server identifiering och skickar serverns metadata och prestanda data till Azure Migrate Server utvärderingen.
- Så här konfigurerar du den apparat som du:
    - Ladda ned en zippad fil med Azure Migrate Installer-skript från Azure Portal.
    - Extrahera innehållet från den zippade filen. Starta PowerShell-konsolen med administratörs behörighet.
    - Kör PowerShell-skriptet för att starta webb programmet för installationen.
    - Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.
- Du kan konfigurera flera apparater för ett enda Azure Migrate-projekt. Du kan identifiera valfritt antal fysiska servrar i alla apparater. Högst 250 servrar kan identifieras per apparat.

### <a name="download-the-installer-script"></a>Ladda ned installations skriptet

Ladda ned den zippade filen för enheten.

1. I **mål** > **servrar** > för migrering**Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **identifiera datorer** > **är dina datorer virtualiserade?**, klicka på **inte virtualiserad/annan**.
3. Klicka på **Ladda ned** för att ladda ned den zippade filen.

    ![Hämta installations program](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den zippade filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning för offentligt moln:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Exempel på användning av myndighets moln:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Verifiera hash-värden:
 
    - För det offentliga molnet (för den senaste versionen av produkten):

        **Integritetsalgoritm** | **Hash-värde**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - För Azure-myndigheter (för den senaste versionen av produkten):

        **Integritetsalgoritm** | **Hash-värde**
          --- | ---
          MD5 | f81c155fc4a1409901caea948713913f

### <a name="run-the-azure-migrate-installer-script"></a>Kör installations skriptet för Azure Migrate

Installations skriptet gör följande:

- Installerar agenter och ett webb program för identifiering och utvärdering av fysiska servrar.
- Installera Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Hämta och installera en skrivbar IIS-modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en register nyckel (HKLM) med beständig inställnings information för Azure Migrate.
- Skapar följande filer under sökvägen:
    - **Config-filer**:%programdata%\Microsoft Azure\Config
    - **Loggfiler**:%programdata%\Microsoft Azure\Logs

Kör skriptet på följande sätt:

1. Extrahera den zippade filen till en mapp på den server som ska vara värd för-enheten.  Kontrol lera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på servern med administratörs behörighet (förhöjt).
3. Ändra PowerShell-katalogen till den mapp där innehållet har extraherats från den hämtade zippade filen.
4. Kör skriptet med namnet **AzureMigrateInstaller. ps1** genom att köra följande kommando:

    - För det offentliga molnet:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - För Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Skriptet startar webb programmet för installationen när det har slutförts.

Om du kommer över alla problem kan du komma åt skript loggarna på C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log för fel sökning.

### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Kontrol lera att enheten kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.


### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

1. Öppna en webbläsare på vilken dator som helst som kan ansluta till installationen och öppna URL: en för installations programmets webbapp: **https://*-enhetens namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från Skriv bordet genom att klicka på genvägen till appen.
2. I webbappen > **Konfigurera krav**gör du följande:
    - **Licens**: Godkänn licens villkoren och Läs informationen från tredje part.
    - **Anslutning**: appen kontrollerar att servern är ansluten till Internet. Om servern använder en proxyserver:
        - Klicka på **proxyinställningar**och ange proxyadress och lyssnings port i formuläret http://ProxyIPAddress eller. http://ProxyFQDN
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: tiden har verifierats. Tiden för installationen bör vara synkroniserad med Internet tid för att Server identifieringen ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate Server Assessment kontrollerar att installations programmet har de senaste uppdateringarna installerade.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klicka på **Logga**in. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
2. På fliken nytt loggar du in med dina Azure-autentiseringsuppgifter.
    - Logga in med ditt användar namn och lösen ord.
    - Inloggning med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen.
4. Välj den prenumeration där Azure Migrate projektet skapades. Välj sedan projektet.
5. Ange ett namn för enheten. Namnet måste vara alfanumeriskt med 14 tecken eller mindre.
6. Klicka på **Registrera**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut nu från installationen till de fysiska servrarna som ska identifieras och starta identifieringen.

1. Klicka på **Lägg till autentiseringsuppgifter** för att ange de kontoautentiseringsuppgifter som installationen ska använda för att identifiera servrar.  
2. Ange **operativ systemet**, ett eget namn för autentiseringsuppgifterna och användar namn och lösen ord. Klicka sedan på **Lägg till**.
Du kan lägga till en uppsättning autentiseringsuppgifter var för Windows-och Linux-servrar.
4. Klicka på **Lägg till Server**och ange Server information – FQDN/IP-adress och eget namn på autentiseringsuppgifter (en post per rad) för att ansluta till servern.
3. Klicka på **Validate** (Validera). Efter verifieringen visas en lista över de servrar som kan identifieras.
    - Om verifieringen Miss lyckas för en server kan du granska felet genom att hovra över ikonen i kolumnen **status** . Åtgärda problem och verifiera igen.
    - Om du vill ta bort en server väljer du > **ta bort**.
4. Efter verifieringen klickar du på **Spara och starta identifiering** för att starta identifierings processen.

Detta startar identifieringen. Det tar cirka 1,5 minuter per server för metadata om identifierad server som visas i Azure Portal.

### <a name="verify-servers-in-the-portal"></a>Verifiera servrar i portalen

Efter identifieringen kan du kontrol lera att servrarna visas i Azure Portal.

1. Öppna instrument panelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar** > **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.

## <a name="set-up-an-assessment"></a>Konfigurera en utvärdering

Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: Server utvärdering.

**Utvärdering** | **Information** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar baserade på insamlade prestanda data | **Rekommenderad VM-storlek**: baserat på processor-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)**: baserat på IOPS och data flödet för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad VM-storlek**: baserat på den lokala server storleken<br/><br> **Rekommenderad disktyp**: baserat på den inställning för lagrings typ som du väljer för utvärderingen.


### <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. Gå igenom [metod tipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. Klicka på **utvärdera**i panelen **Azure Migrate: Server bedömning** på fliken **servrar** .

    ![Utvärdera](./media/tutorial-assess-physical/assess.png)

2. I **utvärdera servrar**anger du ett namn för utvärderingen.
3. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Bedömnings egenskaper](./media/tutorial-assess-physical/view-all.png)

3. I **Välj eller skapa en grupp**väljer du **Skapa ny**och anger ett grupp namn. En grupp samlar in en eller flera servrar tillsammans för utvärdering.
4. I **Lägg till datorer i gruppen**väljer du de servrar som ska läggas till i gruppen.
5. Klicka på **Skapa utvärdering** för att skapa gruppen och kör utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-physical/assessment-create.png)

6. När utvärderingen har skapats kan du Visa den på **servrar** > **Azure Migrate:** > **utvärderingar**av Server bedömning.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.



## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskriver:

- **Azure-beredskap**: om servrarna är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: de uppskattade kostnaderna för beräkning och lagring för att köra servrarna i Azure.
- **Kostnads uppskattning för månatlig lagring**: beräknade kostnader för disk lagring efter migrering.

### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **mål** >  **Server**för migrering klickar du på **utvärderingar** i **Azure Migrate: Server bedömning**.
2. I **bedömningar**klickar du på en utvärdering för att öppna den.

    ![Utvärderings Sammanfattning](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure readiness

1. I **Azure-beredskap**kontrollerar du om servrarna är klara för migrering till Azure.
2. Granska statusen:
    - **Redo för Azure**: Azure Migrate rekommenderar en VM-storlek och kostnads uppskattning för virtuella datorer i utvärderingen.
    - **Klar med villkor**: visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: visar problem och förslag på åtgärder.
    - **Beredskap okänd**: används när Azure Migrate inte kan utvärdera beredskap på grund av problem med data tillgänglighet.

2. Klicka på en status för **Azure-beredskap** . Du kan visa information om Server beredskap och öka detalj nivån för att se Server information, inklusive beräknings-, lagrings-och nätverks inställningar.



### <a name="review-cost-details"></a>Granska kostnads information

I den här vyn visas den beräknade beräknings-och lagrings kostnaden för virtuella datorer som körs i Azure.

1. Granska kostnader för beräkning och lagring per månad. Kostnaderna sammanställs för alla servrar i den utvärderade gruppen.

    - Kostnads uppskattningar baseras på storleks rekommendationerna för en dator och dess diskar och egenskaper.
    - Uppskattade månatliga kostnader för beräkning och lagring visas.
    - Kostnads uppskattningen är att köra lokala servrar som virtuella IaaS-datorer. Azure Migrate Server-utvärderingen beaktar inte PaaS-eller SaaS-kostnader.

2. Du kan granska månads beräkningarna för lagrings kostnader. Den här vyn visar aggregerade lagrings kostnader för den utvärderade gruppen och delas upp över olika typer av lagrings diskar.
3. Du kan öka detalj nivån för att se information om vissa servrar.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör prestandabaserade utvärderingar tilldelas utvärderingen en förtroende nivå.

![Säkerhetsomdöme](./media/tutorial-assess-physical/confidence-rating.png)

- En klassificering från 1 – stjärna (lägsta) till 5-stjärnor (högst) tilldelas.
- Förtroende omdömet hjälper dig att beräkna tillförlitligheten för de storleks rekommendationer som tillhandahålls av utvärderingen.
- Förtroende omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.

Tillförlitlighets klassificeringar för en utvärdering är följande.

**Tillgänglighet för data punkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

[Lär dig mer](best-practices-assessment.md#best-practices-for-confidence-ratings) om metod tips för Tillförlitlighets klassificering.


## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera en Azure Migrate-apparat
> * Skapat och granskat en utvärdering

Fortsätt till den tredje kursen i serien och lär dig hur du migrerar fysiska servrar till Azure med Azure Migrate: Server-migrering.

> [!div class="nextstepaction"]
> [Migrera fysiska servrar](./tutorial-migrate-physical-virtual-machines.md)
