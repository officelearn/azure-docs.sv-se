---
title: Utvärdera virtuella virtuella hyper-v-datorer för migrering till Azure med Azure Migrate | Microsoft-dokument
description: Beskriver hur du bedömer lokala virtuella hyper-v-datorer för migrering till Azure med Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 03/23/2020
ms.custom: mvc
ms.openlocfilehash: cb3c29e01b7917a6d639b6b2a53fc2842efc2172
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336775"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Utvärdera virtuella virtuella hyper virtuella datorer med Utvärdering av Azure Migrate Server

Den här artikeln visar hur du bedömer lokala virtuella virtuella hyper-virtuella datorer med hjälp av verktyget [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Den här självstudien är den andra i en serie som visar hur du bedömer och migrerar virtuella hyper-virtuella datorer till Azure. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera och registrera en Azure Migrate-installation.
> * Starta kontinuerlig identifiering av lokala virtuella datorer.
> * Grupp upptäckte virtuella datorer och utvärdera gruppen.
> * Se över bedömningen.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario så att du snabbt kan ställa in ett proof-of-concept. Självstudier använder standardalternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade instruktioner finns i instruktionsartiklarna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.


## <a name="prerequisites"></a>Krav

- [Slutför](tutorial-prepare-hyper-v.md) den första självstudien i den här serien. Om du inte gör det kommer instruktionerna i den här självstudien inte att fungera.
- Här är vad du borde ha gjort i den första handledningen:
    - [Förbered Azure](tutorial-prepare-hyper-v.md#prepare-azure) för att arbeta med Azure Migrate.
    - [Förbered bedömning av Hyper-V-värdar](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) och virtuella datorer.
    - [Verifiera](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) vad du behöver för att distribuera Azure Migrate-enheten för Hyper-V-utvärdering.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Välj **Azure Migrate**i sökresultaten .
3. I **Översikt** under **Identifiera, utvärdera och migrera servrar** klickar du på **Utvärdera och migrera servrar**.

    ![Upptäck och utvärdera servrar](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. I **Komma igång** klickar du på **Lägg till verktyg**.
5. På **fliken Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
6. Ange projektnamnet och den region där du vill skapa projektet i **Projektinformation.** [Granska](migrate-support-matrix.md#supported-geographies) de regioner där du kan skapa Azure Migrate-projekt.

    - Projektregionen används endast för att lagra metadata som samlats in från lokala virtuella datorer.
    - Du kan välja en annan Azure-målregion när du migrerar de virtuella datorerna. Alla Azure-regioner stöds för migreringsmål.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Klicka på **Nästa**.
8. I **Välj bedömningsverktyg**väljer du **Azure Migrate: Serverutvärdering** > **nästa**.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. Granska inställningarna i **Verktyg för Granskning + lägg till**och klicka på Lägg till **verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurera Azure Migrate-enheten

Azure Migrate:server Assessment använder en lätt Azure Migrate-installation. Installationen av virtuella datorer utför vm-identifiering och skickar VM-metadata och prestandadata till Azure Migrate.
- Apparaten kan ställas in på en Virtuell Hyper-V med en nedladdad Hyper-V VHD. Du kan också ställa in enheten på en virtuell dator eller en fysisk dator med ett PowerShell-installationsskript.
- Den här självstudien använder den virtuella hårddisken. Läs [den här artikeln](deploy-appliance-script.md) om du vill konfigurera apparaten med ett skript.

När du har skapat installationen kontrollerar du att den kan ansluta till Azure Migrate:Server Assessment, konfigurera den för första gången och registrera den med Azure Migrate-projektet.

### <a name="download-the-vhd"></a>Ladda ner den virtuella hårddisken

Ladda ner den zippade VHD-mallen för apparaten.

1. Klicka på **Identifiera**i **migreringsmålsservrar:** > **Servers** > **Serverutvärdering.**
2. **I** **Upptäck-datorer** > **Virtualiseras dina datorer?**
3. Klicka på **Hämta** om du vill hämta VHD-filen.

    ![Ladda ner vm](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrollera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.

2. Kör följande PowerShell-kommando för att generera hash för ZIP-filen
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Exempel på användning: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  För apparat version 2.19.07.30 bör den genererade hashen matcha dessa inställningar.

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>Skapa den virtuella datorn för den använda datorn

Importera den hämtade filen och skapa den virtuella datorn.

1. När du har hämtat den zippade VHD-filen till Hyper-V-värden där den virtuella datorn för apparaten kommer att placeras, extrahera filen med blixtlås.
    - På den extraherade platsen packas filen upp i en mapp som heter **AzureMigrateAppliance_VersionNumber**.
    - Den här mappen innehåller en undermapp, även kallad **AzureMigrateAppliance_VersionNumber**.
    - Den här undermappen innehåller ytterligare tre undermappar - **ögonblicksbilder,** **virtuella hårddiskar**och **virtuella datorer**.

2. Öppna Hyper-V Manager. Klicka på **Importera virtuell dator i** **Åtgärder**.

    ![Distribuera VIRTUELLD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Klicka på **Nästa**i > guiden Importera virtuell dator **innan du börjar**.
3. Välj mappen Virtuella **datorer** i **Hitta mapp.** Klicka sedan på **Nästa**.
1. Klicka på **Nästa** **i Välj virtuell dator**.
2. Klicka på Kopiera **den virtuella datorn (skapa ett nytt unikt ID)** i **Välj importtyp**. Klicka sedan på **Nästa**.
3. Lämna standardinställningen i **Välj mål.** Klicka på **Nästa**.
4. Lämna standardinställningen i **Lagringsmappar.** Klicka på **Nästa**.
5. Ange den virtuella växel som den virtuella datorn ska använda i **Välj nätverk.** Växeln behöver internetanslutning för att skicka data till Azure. [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) om hur du skapar en virtuell växel.
6. Granska inställningarna i **Sammanfattning.** Klicka sedan på **Slutför**.
7. Starta den virtuella datorn i Hyper-V **Manager-> virtuella datorer.**


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till Azure

Se till att den virtuella datorn för den virtuella enheten kan ansluta till [Azure-url:er](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Konfigurera apparaten

Ställ in apparaten för första gången.

> [!NOTE]
> Om du konfigurerar enheten med ett [PowerShell-skript](deploy-appliance-script.md) i stället för den hämtade virtuella hårddisken är de två första stegen i den här proceduren inte relevanta.

1. Högerklicka på den virtuella datorn **Virtual Machines**> > **Connect**i Hyper-V Manager-hanteraren.
2. Ange språk, tidszon och lösenord för apparaten.
3. Öppna en webbläsare på alla datorer som kan ansluta till den virtuella datorn och öppna url:en för appen för installationen: **https://*apparatens namn eller IP-adress:* 44368**.

   Alternativt kan du öppna appen från apparatens skrivbord genom att klicka på appgenvägen.
1. Gör följande i webbappen > **Konfigurera förutsättningar:**
    - **Licens**: Acceptera licensvillkoren och läs informationen från tredje part.
    - **Anslutning**: Appen kontrollerar att den virtuella datorn har tillgång till internet. Om den virtuella datorn använder en proxy:
      - Klicka på **Proxyinställningar**och ange proxyadress och http://ProxyIPAddress lyssningsport i formuläret eller http://ProxyFQDN.
      - Ange autentiseringsuppgifter om proxyn kräver autentisering.
      - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: Tiden är verifierad. Tiden på apparaten ska vara synkroniserad med internettiden för att vm-identifiering ska fungera korrekt.
    - **Installera uppdateringar:** Azure Migrate Server Assessment kontrollerar att installationen har de senaste uppdateringarna installerade.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera installationen med Azure Migrate

1. Klicka på **Logga in**. Om den inte visas kontrollerar du att du har inaktiverat popup-blockeraren i webbläsaren.
2. Logga in med dina Azure-autentiseringsuppgifter på den nya fliken.
    - Logga in med ditt användarnamn och lösenord.
    - Inloggning med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen.
4. Välj den prenumeration där Azure Migrate-projektet skapades. Välj sedan projektet.
5. Ange ett namn på apparaten. Namnet ska vara alfanumeriskt med högst 14 tecken.
6. Klicka på **Registrera**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för virtuella SMB-hårddiskar

Om du kör virtuella hårddiskar på små och medelstora företag måste du aktivera delegering av autentiseringsuppgifter från enheten till Hyper-V-värdarna. Detta kräver följande:

- Du kan aktivera varje värd för att fungera som ombud för apparaten. Om du följde självstudierna i ordning, gjorde du detta i föregående handledning, när du förberedde Hyper-V för bedömning och migrering. Du bör antingen ha ställt in CredSSP för värdarna [manuellt](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)eller genom att [köra ett skript](tutorial-prepare-hyper-v.md#prepare-with-a-script) som gör detta.
- Aktivera CredSSP-delegering så att Azure Migrate-enheten kan fungera som klient och delegera autentiseringsuppgifter till en värd.

Aktivera på apparaten enligt följande:

#### <a name="option-1"></a>Alternativ 1

Kör det här kommandot på den virtuella datorn. HyperVHost1/HyperVHost2 är exempelvärden.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Exempel: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Alternativ 2

Du kan också göra detta i redigeraren för lokala grupprinciper på apparaten:

1. Klicka på **Administrativa mallar** > **Systemautentiseringsuppgifter****System** > delegation i Konfiguration av lokal**datorprincipdator.** **Local Computer Policy** > 
2. Dubbelklicka på **Tillåt delegering av nya autentiseringsuppgifter**och välj **Aktiverad**.
3. I **Alternativ**klickar du på **Visa**och lägger till varje Hyper-V-värd som du vill identifiera i listan, med **wsman/** som prefix.
4. Dubbelklicka sedan på **Tillåt delegera nya autentiseringsuppgifter med NTLM-serverautentisering**i **autentiseringsuppgifter.** Lägg till varje Hyper-V-värd som du vill identifiera i listan, med **wsman/** som prefix.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut från installationen till Hyper-V-värdar eller kluster och starta vm-identifiering.

1. I **Användarnamn** och **Lösenord**anger du de kontouppgifter som används för att identifiera virtuella datorer. Ange ett eget namn för autentiseringsuppgifterna och klicka på **Spara information**.
2. Klicka på **Lägg till värd**och ange information om Hyper-V-värd/kluster.
3. Klicka på **Validate** (Validera). Efter verifiering visas antalet virtuella datorer som kan identifieras på varje värd/kluster.
    - Om valideringen misslyckas för en värd granskar du felet genom att hovra över ikonen i kolumnen **Status.** Åtgärda problem och verifiera igen.
    - Om du vill ta bort värdar eller kluster markerar du > **Ta bort**.
    - Du kan inte ta bort en viss värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster, även om det finns problem med specifika värdar i klustret.
4. När du har validerat klickar du på **Spara och startar identifieringen** för att starta identifieringsprocessen.

Detta startar upptäckt. Det tar cirka 1,5 minuter per värd för metadata för identifierade servrar att visas i Azure-portalen.

### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När identifieringen är klar kan du kontrollera att de virtuella datorerna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. I **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** sida, klicka på ikonen som visar antalet för identifierade **servrar**.

## <a name="set-up-an-assessment"></a>Ställ in en utvärdering

Det finns två typer av utvärderingar som du kan köra med Azure Migrate Server Assessment.

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestationsbaserad** | Bedömningar baserade på insamlade resultatdata | **Rekommenderad VM-storlek:** Baserat på CPU- och minnesanvändningsdata.<br/><br/> **Rekommenderad disktyp (standard eller premiumhanterad disk)**: Baserat på IOPS och dataflöde för de lokala diskarna.
**Som lokalt** | Bedömningar baserade på lokal storlek. | **Rekommenderad VM-storlek:** Baserat på den lokala vm-storleken<br/><br> **Rekommenderad disktyp**: Baserat på den lagringstyp som du väljer för utvärderingen.



### <a name="run-an-assessment"></a>Köra en utvärdering

Gör en bedömning på följande sätt:

1. Granska [metodtipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. Klicka på **Utvärdera**i **Servrar** > **Azure Migrera: Serverutvärdering.**

    ![Utvärdera](./media/tutorial-assess-hyper-v/assess.png)

3. Ange ett namn för utvärderingen i **Bedömningsservrar.**
4. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Bedömningsegenskaper](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. Välj **Skapa ny**i Markera eller skapa **en grupp**och ange ett gruppnamn. En grupp samlar ihop en eller flera virtuella datorer för bedömning.
4. I **Lägg till datorer i gruppen**väljer du virtuella datorer som ska läggas till i gruppen.
5. Klicka på **Skapa utvärdering** om du vill skapa gruppen och köra utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-hyper-v/assessment-create.png)

6. När utvärderingen har skapats visar du den i **Servrar** > **Azure Migrera: Serverutvärdering**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.


## <a name="review-an-assessment"></a>Granska en bedömning

En bedömning beskriver:

- **Azure-beredskap**: Om virtuella datorer är lämpliga för migrering till Azure.
- **Uppskattning av månadskostnad**: Uppskattade månatliga beräknings- och lagringskostnader för att köra de virtuella datorerna i Azure.
- **Uppskattning av månatliga lagringskostnader**: Uppskattade kostnader för disklagring efter migreringen.


### <a name="view-an-assessment"></a>Visa en bedömning

1. Klicka på **Utvärderingar**i **migreringsmål** >  **servrar** > **Azure Migrera: Serverutvärdering.**
2. I **Bedömningar**klickar du på en bedömning för att öppna den.

    ![Sammanfattning av bedömning](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Granska Azure-beredskap

1. I **Azure-beredskap**kontrollerar du om virtuella datorer är redo för migrering till Azure.
2. Granska vm-statusen:
    - **Klar för Azure:** Azure Migrate rekommenderar en vm-storlek och kostnadsuppskattningar för virtuella datorer i utvärderingen.
    - **Redo med villkor**: Visar problem och föreslagen reparation.
    - **Inte redo för Azure**: Visar problem och föreslagen reparation.
    - **Okänd beredskap**: Används när Azure Migrate inte kan bedöma beredskapen på grund av problem med datatillgänglighet.

2. Klicka på en **Azure-beredskapsstatus.** Du kan visa information om vm-beredskap och öka detaljnivån för att se vm-information, inklusive beräknings-, lagrings- och nätverksinställningar.

### <a name="review-cost-details"></a>Granska kostnadsinformation

Den här vyn visar den uppskattade beräknings- och lagringskostnaden för att köra virtuella datorer i Azure.

1. Granska de månatliga beräknings- och lagringskostnaderna. Kostnaderna aggregeras för alla virtuella datorer i den bedömda gruppen.

    - Kostnadsuppskattningar baseras på storleksrekommendationerna för en dator och dess diskar och egenskaper.
    - Beräknade månadskostnader för beräkning och lagring visas.
    - Kostnadsuppskattningen är för att köra de lokala virtuella datorerna som virtuella IaaS-datorer. Azure Migrate Server Assessment tar inte hänsyn till PaaS eller SaaS kostnader.

2. Du kan granska uppskattningar av månatliga lagringskostnader. Den här vyn visar aggregerade lagringskostnader för den bedömda gruppen, uppdelade över olika typer av lagringsdiskar.
3. Du kan öka detaljnivån för att se information om specifika virtuella datorer.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör resultatbaserade utvärderingar tilldelas en konfidensklassificering till utvärderingen.

![Säkerhetsomdöme](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Fortsätt till den tredje självstudien i serien om du vill lära dig hur du migrerar virtuella hyper-V-datorer till Azure med Azure Migrate Server Migration.

> [!div class="nextstepaction"]
> [Migrera virtuella Hyper-V-datorer](./tutorial-migrate-hyper-v.md)
