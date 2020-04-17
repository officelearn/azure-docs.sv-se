---
title: Utvärdera fysiska servrar för migrering till Azure med Azure Migrate Server Assessment
description: Beskriver hur du bedömer lokala fysiska servrar för migrering till Azure med Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: b36cba18bd154cd5d14e16a9f8bf85cda6bf87a8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535442"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Utvärdera fysiska servrar med Azure Migrate:Server Assessment

Den här artikeln visar hur du bedömer lokala fysiska servrar med hjälp av verktyget Azure Migrate:Server Assessment.

[Azure Migrate](migrate-services-overview.md) innehåller ett nav med verktyg som hjälper dig att identifiera, bedöma och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Navet innehåller Azure Migrate-verktyg och ISV-erbjudanden (Independent Software Vendor) från tredje part.

Den här självstudien är den andra i en serie som visar hur du bedömer och migrerar fysiska servrar till Azure. I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera en Azure Migrate-installation som körs lokalt för att bedöma fysiska servrar.
> * Starta kontinuerlig identifiering av lokala fysiska servrar. Installationen skickar konfigurations- och prestandadata för identifierade servrar till Azure.
> * Gruppera identifierade servrar och utvärdera servergruppen.
> * Se över bedömningen.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario så att du snabbt kan ställa in ett proof-of-concept. Självstudier använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i instruktionsartiklarna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.


## <a name="prerequisites"></a>Krav

- [Slutför](tutorial-prepare-physical.md) den första självstudien i den här serien. Om du inte gör det kommer instruktionerna i den här självstudien inte att fungera.
- Här är vad du borde ha gjort i den första handledningen:
    - [Konfigurera Azure-behörigheter](tutorial-prepare-physical.md) för Azure Migrate.
    - [Förbered fysiska servrar](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) för bedömning. Apparatkraven bör kontrolleras. Du bör också ha ett konto konfigurerat för fysisk serveridentifiering. Obligatoriska portar bör vara tillgängliga och du bör vara medveten om de webbadresser som behövs för åtkomst till Azure.




## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Konfigurera ett nytt Azure Migrate-projekt enligt följande.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** under **Identifiera, utvärdera och migrera servrar** klickar du på **Utvärdera och migrera servrar**.

    ![Upptäck och utvärdera servrar](./media/tutorial-assess-physical/assess-migrate.png)

4. I **Komma igång** klickar du på **Lägg till verktyg**.
5. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.  
6. Ange projektnamnet och den geografi där du vill skapa projektet i **Projektinformation.** Granska geografiska områden som stöds för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [statliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

    - Projektets geografi används endast för att lagra metadata som samlats in från lokala servrar.
    - Du kan välja vilken målregion du vill när du kör en migrering.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-physical/migrate-project.png)


7. Klicka på **Nästa**.
8. I **Välj bedömningsverktyg**väljer du **Azure Migrate: Serverutvärdering** > **nästa**.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-physical/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. Granska inställningarna i **Verktyg för Granskning + lägg till**och klicka på Lägg till **verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.


## <a name="set-up-the-appliance"></a>Ställ in apparaten

Azure Migrate: Serverutvärdering kör en lättviktsinstallation.

- Den här installationen av den fysiska serveridentifieringen och skickar servermetadata och prestandadata till Azure Migrate Server Assessment.
- Så här ställer du in apparaten:
    - Hämta en zippad fil med Azure Migrate-installationsskript från Azure-portalen.
    - Extrahera innehållet från den zippade filen. Starta PowerShell-konsolen med administratörsbehörighet.
    - Kör PowerShell-skriptet för att starta programmet för apparatwebb.
    - Konfigurera installationen för första gången och registrera den med Azure Migrate-projektet.
- Du kan ställa in flera enheter för ett enda Azure Migrate-projekt. I alla apparater kan du upptäcka valfritt antal fysiska servrar. Högst 250 servrar kan upptäckas per apparat.

### <a name="download-the-installer-script"></a>Hämta installationsskriptet

Ladda ner den zippade filen för apparaten.

1. Klicka på **Identifiera**i **migreringsmålsservrar:** > **Servers** > **Serverutvärdering.**
2. **I** **Upptäck-datorer** > **Virtualiseras dina datorer?**
3. Klicka på **Hämta** om du vill hämta filen med dragkedja.

    ![Hämta installationsprogrammet](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrollera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hash för den zippade filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning för offentligt moln:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Exempel på användning för myndighetsmoln:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Verifiera hash-värden:
 
    - För det offentliga molnet (för den senaste installationen):

        **Algoritm** | **Hash-värde**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - För Azure-myndighet (för den senaste installationen):

        **Algoritm** | **Hash-värde**
          --- | ---
          MD5 | f81c155fc4a1409901caea948713913f

### <a name="run-the-azure-migrate-installer-script"></a>Köra Azure Migrate-installationsskriptet

Installationsskriptet gör följande:

- Installerar agenter och ett webbprogram för identifiering och bedömning av fysiska servrar.
- Installera Windows-roller, inklusive Windows Activation Service, IIS och PowerShell ISE.
- Ladda ner och installerar en IIS rewritable modul. [Läs mer](https://www.microsoft.com/download/details.aspx?id=7435).
- Uppdaterar en registernyckel (HKLM) med beständig inställningsinformation för Azure Migrate.
- Skapar följande filer under sökvägen:
    - **Config-filer**: %Programdata%\Microsoft Azure\Config
    - **Loggfiler**: %Programdata%\Microsoft Azure\Loggar

Kör skriptet på följande sätt:

1. Extrahera den zippade filen till en mapp på servern som ska vara värd för apparaten.  Kontrollera att du inte kör skriptet på en dator på en befintlig Azure Migrate-installation.
2. Starta PowerShell på ovanstående server med administrativ (förhöjd) behörighet.
3. Ändra PowerShell-katalogen till mappen där innehållet har extraherats från den nedladdade zippade filen.
4. Kör skriptet **AzureMigrateInstaller.ps1** genom att köra följande kommando:

    - För det offentliga molnet:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - För Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Skriptet startar programmet för apparat när det är klart.

Om du stöter på några problem kan du komma åt skriptloggarna på C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>tidsstämpel</em>.log för felsökning.

### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till Azure

Se till att installationen kan ansluta till Azure-url:er för [offentliga](migrate-appliance.md#public-cloud-urls) moln och [myndighetsmoln.](migrate-appliance.md#government-cloud-urls)


### <a name="configure-the-appliance"></a>Konfigurera apparaten

Ställ in apparaten för första gången.

1. Öppna en webbläsare på alla datorer som kan ansluta till apparaten och öppna webbadressen till appen för apparaten: **https://*apparatens namn eller IP-adress:* 44368**.

   Alternativt kan du öppna appen från skrivbordet genom att klicka på appgenvägen.
2. Gör följande i webbappen > **Konfigurera förutsättningar:**
    - **Licens**: Acceptera licensvillkoren och läs informationen från tredje part.
    - **Anslutning**: Appen kontrollerar att servern har tillgång till internet. Om servern använder en proxy:
        - Klicka på **Proxyinställningar**och ange proxyadress och http://ProxyIPAddress lyssningsport i formuläret eller http://ProxyFQDN.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: Tiden är verifierad. Tiden på apparaten ska vara synkroniserad med internettiden för att serveridentifiering ska fungera korrekt.
    - **Installera uppdateringar:** Azure Migrate Server Assessment kontrollerar att installationen har de senaste uppdateringarna installerade.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera installationen med Azure Migrate

1. Klicka på **Logga in**. Om den inte visas kontrollerar du att du har inaktiverat popup-blockeraren i webbläsaren.
2. Logga in med dina Azure-autentiseringsuppgifter på den nya fliken.
    - Logga in med ditt användarnamn och lösenord.
    - Logga in med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen.
4. Välj den prenumeration där Azure Migrate-projektet skapades. Välj sedan projektet.
5. Ange ett namn på apparaten. Namnet ska vara alfanumeriskt med högst 14 tecken.
6. Klicka på **Registrera**.


## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut nu från apparaten till de fysiska servrar som ska upptäckas och starta identifieringen.

1. Klicka på **Lägg till autentiseringsuppgifter** om du vill ange vilka kontouppgifter som används för att identifiera servrar.  
2. Ange **operativsystemet**, ett eget namn för autentiseringsuppgifterna och användarnamn och lösenord. Klicka sedan på **Lägg till**.
Du kan lägga till en uppsättning autentiseringsuppgifter vardera för Windows- och Linux-servrar.
4. Klicka på **Lägg till server**och ange serverinformation- FQDN/IP-adress och eget namn på autentiseringsuppgifter (en post per rad) för att ansluta till servern.
3. Klicka på **Validate** (Validera). Efter valideringen visas listan över servrar som kan identifieras.
    - Om valideringen misslyckas för en server granskar du felet genom att hovra över ikonen i kolumnen **Status.** Åtgärda problem och verifiera igen.
    - Om du vill ta bort en server markerar du > **Ta bort**.
4. När du har validerat klickar du på **Spara och startar identifieringen** för att starta identifieringsprocessen.

Detta startar upptäckt. Det tar cirka 1,5 minuter per server för metadata för den identifierade servern att visas i Azure-portalen.

### <a name="verify-servers-in-the-portal"></a>Verifiera servrar i portalen

Efter identifiering kan du kontrollera att servrarna visas i Azure-portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. I **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** sida, klicka på ikonen som visar antalet för identifierade **servrar**.

## <a name="set-up-an-assessment"></a>Ställ in en utvärdering

Det finns två typer av utvärderingar som du kan skapa med Azure Migrate: Server Assessment.

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestationsbaserad** | Bedömningar baserade på insamlade resultatdata | **Rekommenderad VM-storlek:** Baserat på CPU- och minnesanvändningsdata.<br/><br/> **Rekommenderad disktyp (standard eller premiumhanterad disk)**: Baserat på IOPS och dataflöde för de lokala diskarna.
**Som lokalt** | Bedömningar baserade på lokal storlek. | **Rekommenderad VM-storlek:** Baserat på den lokala serverstorleken<br/><br> **Rekommenderad disktyp**: Baserat på den lagringstyp som du väljer för utvärderingen.


### <a name="run-an-assessment"></a>Köra en utvärdering

Gör en bedömning på följande sätt:

1. Granska [metodtipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. Klicka på **Utvärdera**i panelen **Serverutvärdering** på fliken **Servrar.**

    ![Utvärdera](./media/tutorial-assess-physical/assess.png)

2. Ange ett namn för utvärderingen i **Bedöma servrar.**
3. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Bedömningsegenskaper](./media/tutorial-assess-physical/view-all.png)

3. Välj **Skapa ny**i Markera eller skapa **en grupp**och ange ett gruppnamn. En grupp samlar ihop en eller flera servrar för bedömning.
4. I **Lägg till datorer i gruppen**väljer du servrar som ska läggas till i gruppen.
5. Klicka på **Skapa utvärdering** om du vill skapa gruppen och köra utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-physical/assessment-create.png)

6. När utvärderingen har skapats visar du den i **Servrar** > **Azure Migrera: Serverutvärderingsutvärderingar** > **Assessments**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.



## <a name="review-an-assessment"></a>Granska en bedömning

En bedömning beskriver:

- **Azure-beredskap**: Om servrar är lämpliga för migrering till Azure.
- **Uppskattning av månadskostnad:** Beräknade månatliga beräknings- och lagringskostnader för att köra servrarna i Azure.
- **Uppskattning av månatliga lagringskostnader**: Uppskattade kostnader för disklagring efter migreringen.

### <a name="view-an-assessment"></a>Visa en bedömning

1. Klicka på **Utvärderingar** i **Azure Migrera: Serverutvärdering**i servrar för **migreringsmål.** >  **Servers**
2. I **Bedömningar**klickar du på en bedömning för att öppna den.

    ![Sammanfattning av bedömning](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure-beredskap

1. I **Azure-beredskap**kontrollerar du om servrarna är redo för migrering till Azure.
2. Granska status:
    - **Klar för Azure:** Azure Migrate rekommenderar en vm-storlek och kostnadsuppskattningar för virtuella datorer i utvärderingen.
    - **Redo med villkor**: Visar problem och föreslagen reparation.
    - **Inte redo för Azure**: Visar problem och föreslagen reparation.
    - **Okänd beredskap**: Används när Azure Migrate inte kan bedöma beredskapen på grund av problem med datatillgänglighet.

2. Klicka på en **Azure-beredskapsstatus.** Du kan visa information om serverberedskap och öka detaljnivån för att se serverinformation, inklusive beräknings-, lagrings- och nätverksinställningar.



### <a name="review-cost-details"></a>Granska kostnadsinformation

Den här vyn visar den uppskattade beräknings- och lagringskostnaden för att köra virtuella datorer i Azure.

1. Granska de månatliga beräknings- och lagringskostnaderna. Kostnaderna aggregeras för alla servrar i den bedömda gruppen.

    - Kostnadsuppskattningar baseras på storleksrekommendationerna för en dator och dess diskar och egenskaper.
    - Beräknade månadskostnader för beräkning och lagring visas.
    - Kostnadsuppskattningen är för att köra de lokala servrarna som virtuella IaaS-datorer. Azure Migrate Server Assessment tar inte hänsyn till PaaS eller SaaS kostnader.

2. Du kan granska uppskattningar av månatliga lagringskostnader. Den här vyn visar aggregerade lagringskostnader för den bedömda gruppen, uppdelade över olika typer av lagringsdiskar.
3. Du kan öka detaljnivån för att se information om specifika servrar.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör resultatbaserade utvärderingar tilldelas en konfidensklassificering till utvärderingen.

![Säkerhetsomdöme](./media/tutorial-assess-physical/confidence-rating.png)

- Betyg från 1-stjärniga (lägsta) till 5-stjärniga (högsta) delas ut.
- Konfidensklassificeringen hjälper dig att uppskatta tillförlitligheten hos de storleksrekommendationer som tillhandahålls av bedömningen.
- Konfidensklassificeringen baseras på tillgången på datapunkter som behövs för att beräkna bedömningen.

Förtroendebetyg för en bedömning är följande.

**Tillgänglighet för datapunkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

[Läs mer](best-practices-assessment.md#best-practices-for-confidence-ratings) om metodtips för förtroendeklassificeringar.


## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera en Azure Migrate-apparat
> * Skapade och granskade en bedömning

Fortsätt till den tredje självstudien i serien om du vill lära dig hur du migrerar fysiska servrar till Azure med Azure Migrate: Servermigrering.

> [!div class="nextstepaction"]
> [Migrera fysiska servrar](./tutorial-migrate-physical-virtual-machines.md)
