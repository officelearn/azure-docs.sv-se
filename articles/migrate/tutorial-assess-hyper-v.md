---
title: Utvärdera virtuella Hyper-V-datorer för migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du bedömer lokala virtuella Hyper-V-datorer för migrering till Azure med hjälp av Azure Migrate.
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom: mvc
ms.openlocfilehash: e4c505d74ff3bebc21f696b1c4b894afcdaa9974
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238428"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Utvärdera virtuella Hyper-V-datorer med Azure Migrate Server-utvärdering

Den här artikeln visar hur du kan utvärdera lokala virtuella Hyper-V-datorer med hjälp av verktyget Azure Migrate: Server bedömning.

[Azure Migrate](migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV).



Den här självstudien är den andra i en serie som visar hur du bedömer och migrerar virtuella Hyper-V-datorer till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera och registrera en Azure Migrate-apparat.
> * Starta kontinuerlig identifiering av lokala virtuella datorer.
> * Gruppera identifierade virtuella datorer och utvärdera gruppen.
> * Granska utvärderingen.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktions artiklar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

- [Slutför](tutorial-prepare-hyper-v.md) den första självstudien i den här serien. Om du inte gör det fungerar inte instruktionerna i den här självstudien.
- Det här är vad du behöver göra i den första självstudien:
    - [Konfigurera Azure-behörigheter](tutorial-prepare-hyper-v.md#prepare-azure) för Azure Migrate.
    - [Förbered Hyper-V-](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) kluster, värdar och virtuella datorer för utvärdering.
    - [Förbered för distribution](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) av Azure Migrates apparaten, som används för identifiering och utvärdering av virtuella Hyper-V-datorer.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. I Sök resultaten väljer du **Azure Migrate**.
3. I **Översikt** under **Identifiera, utvärdera och migrera servrar** klickar du på **Utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. I **Komma igång** klickar du på **Lägg till verktyg**.
5. På fliken **migrera projekt** väljer du din Azure-prenumeration och skapar en resurs grupp om du inte har någon.
6. I **projekt information**anger du projekt namnet och den region där du vill skapa projektet.


    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-hyper-v/migrate-project.png)

    Du kan skapa ett Azure Migrate-projekt i dessa regioner.

    **Geografi** | **Region**
    --- | ---
    Asien  | Sydostasien
    Europa | Europa, norra eller Europa, västra
    Storbritannien |  Storbritannien, södra eller Storbritannien, västra
    USA | Östra USA, västra USA 2 eller västra centrala USA

    - Projekt regionen används bara för att lagra metadata som samlats in från lokala virtuella datorer.
    - Du kan välja en annan Azure-mål region när du migrerar de virtuella datorerna. Alla Azure-regioner stöds för mål för migrering.

7. Klicka på **Next**.
8. I **Välj bedömnings verktyg**väljer du **Azure Migrate: Server utvärdering** > **Nästa**.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. I **Granska + lägg till verktyg**
granskar du inställningarna och klickar på **Lägg till verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.




## <a name="set-up-the-appliance-vm"></a>Konfigurera den virtuella dator enheten

Azure Migrate Server utvärderingen kör en förenklad Hyper-V VM-enhet.

- Den här installationen utför VM-identifiering och skickar VM-metadata och prestanda data till Azure Migrate: Server utvärdering.
- Så här konfigurerar du den apparat som du:
    - Ladda ned en komprimerad virtuell Hyper-V-hårddisk från Azure Portal.
    - Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
    - Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.

### <a name="download-the-vhd"></a>Ladda ned den virtuella hård disken

Hämta den zippade VHD-mallen för enheten.

1. I **mål för migrering** > **servrar** > **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **identifiera datorer** > **dina datorer virtualiserade?** , klicka på **Ja, med Hyper-V**.
3. Klicka på **Ladda ned** för att ladda ned VHD-filen.

    ![Hämta virtuell dator](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.

2. Kör följande PowerShell-kommando för att generera hash för ZIP-filen
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Exempel på användning: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  För version 2.19.07.30 ska den genererade hashen matcha de här inställningarna.

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den hämtade filen och skapa den virtuella datorn.

1. När du har laddat ned den zippade VHD-filen till Hyper-V-värden som den virtuella dator enheten ska placeras på, extrahera den zippade filen.
    - På den extraherade platsen packas filen upp i en mapp med namnet **AzureMigrateAppliance_VersionNumber**.
    - Den här mappen innehåller en undermapp, som även kallas **AzureMigrateAppliance_VersionNumber**.
    - Den här undermappen innehåller tre ytterligare undermappar – **ögonblicks bilder**, **virtuella hård diskar**och **Virtual Machines**.

2. Öppna Hyper-V Manager. Klicka på **Importera virtuell dator**i **åtgärder**.

    ![Distribuera virtuell hård disk](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. I guiden Importera virtuell dator > **innan du börjar klickar du**på **Nästa**.
3. I **hitta mapp**väljer du mappen **Virtual Machines** . Klicka sedan på **Nästa**.
1. I **Välj virtuell dator**klickar du på **Nästa**.
2. I **Välj import typ**klickar **du på Kopiera den virtuella datorn (skapa ett nytt unikt ID)** . Klicka sedan på **Nästa**.
3. I **Välj mål**låter du standardvärdet vara kvar. Klicka på **Next**.
4. Lämna standardinställningen i **Storage Folders**. Klicka på **Next**.
5. I **Välj nätverk**anger du den virtuella växel som den virtuella datorn ska använda. Växeln behöver Internet anslutning för att skicka data till Azure. [Lär dig mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) om att skapa en virtuell växel.
6. Granska inställningarna i **Sammanfattning**. Klicka sedan på **Slutför**.
7. Starta den virtuella datorn i Hyper-V Manager > **Virtual Machines**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns dator kan ansluta till [Azure-URL: er](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

1. I Hyper-V Manager > **Virtual Machines**högerklickar du på den virtuella datorn > **Anslut**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att klicka på genvägen till appen.
1. I webbappen > **Konfigurera krav**gör du följande:
    - **Licens**: Godkänn licens villkoren och Läs informationen från tredje part.
    - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
      - Klicka på **proxyinställningar**och ange proxyadress och lyssnings port, i formatet http://ProxyIPAddress eller http://ProxyFQDN.
      - Ange autentiseringsuppgifter om proxyn kräver autentisering.
      - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: tiden har verifierats. Tiden för installationen bör vara synkroniserad med Internet-tid för att VM-identifieringen ska fungera korrekt.
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


### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för SMB-VHD: er

Om du kör virtuella hård diskar på SMB: er måste du aktivera delegering av autentiseringsuppgifter från installationen till Hyper-V-värdarna. Detta kräver följande:

- Du aktiverar varje värd så att den fungerar som ett ombud för enheten. Om du har följt självstudierna i ordning gjorde du detta i föregående självstudie när du för beredde Hyper-V för utvärdering och migrering. Du bör antingen konfigurera CredSSP för värdarna [manuellt](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)eller genom att [köra ett skript](tutorial-prepare-hyper-v.md#prepare-with-a-script) som gör detta.
- Aktivera CredSSP-delegering så att Azure Migrate-installationen kan fungera som klienten och delegera autentiseringsuppgifter till en värd.

Aktivera på enheten enligt följande:

#### <a name="option-1"></a>alternativ 1

Kör det här kommandot på den virtuella datorn. HyperVHost1/HyperVHost2 är exempel värd namn.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Exempel: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Alternativ 2

Du kan också göra detta i redigerare för lokalt grupprincipobjekt på enheten:

1. I **princip för den lokala datorn** > **dator konfiguration**klickar du på **administrativa mallar** > **system** > **delegering av autentiseringsuppgifter**.
2. Dubbelklicka på **Tillåt att delegera nya autentiseringsuppgifter**och välj **aktive rad**.
3. I **alternativ**klickar du på **Visa**och lägger till varje Hyper-V-värd som du vill identifiera i listan med **WSMan/** som prefix.
4. I **delegering av autentiseringsuppgifter**dubbelklickar du sedan på **Tillåt att delegera nya AUTENTISERINGSUPPGIFTER med endast NTLM-** serverautentisering. Lägg återigen till varje Hyper-V-värd som du vill identifiera i listan, med **WSMan/** som prefix.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut från enheten till Hyper-V-värdar eller-kluster och starta VM-identifiering.

1. I **användar namn** och **lösen ord**anger du de kontoautentiseringsuppgifter som installationen ska använda för att identifiera virtuella datorer. Ange ett eget namn för autentiseringsuppgifterna och klicka på **Spara information**.
2. Klicka på **Lägg till värd**och ange information om Hyper-V-värd/kluster.
3. Klicka på **Validate** (Validera). Efter verifieringen visas antalet virtuella datorer som kan identifieras på varje värd/kluster.
    - Om verifieringen Miss lyckas för en värd granskar du felet genom att hovra över ikonen i kolumnen **status** . Åtgärda problem och verifiera igen.
    - Om du vill ta bort värdar eller kluster väljer du > **ta bort**.
    - Du kan inte ta bort en speciell värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster även om det finns problem med specifika värdar i klustret.
4. Efter verifieringen klickar du på **Spara och starta identifiering** för att starta identifierings processen.

Detta startar identifieringen. Det tar cirka 1,5 minuter per värd för metadata för identifierade servrar som visas i Azure Portal.

### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När identifieringen är klar kan du kontrol lera att de virtuella datorerna visas i portalen.

1. Öppna instrument panelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar** > **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.

## <a name="set-up-an-assessment"></a>Konfigurera en utvärdering

Det finns två typer av utvärderingar som du kan köra med hjälp av Azure Migrate Server bedömning.

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar baserade på insamlade prestanda data | **Rekommenderad VM-storlek**: baserat på processor-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)** : baserat på IOPS och data flödet för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad VM-storlek**: baserat på den lokala virtuella dator storleken<br/><br> **Rekommenderad disktyp**: baserat på den inställning för lagrings typ som du väljer för utvärderingen.



### <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. Gå igenom [metod tipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. I **servrar** > **Azure Migrate: Server utvärdering**klickar du på **utvärdera**.

    ![Utvärdera](./media/tutorial-assess-hyper-v/assess.png)

3. I **utvärdera servrar**anger du ett namn för utvärderingen.
4. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Bedömnings egenskaper](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. I **Välj eller skapa en grupp**väljer du **Skapa ny** och anger ett grupp namn. En grupp samlar in en eller flera virtuella datorer för utvärdering.
4. I **Lägg till datorer i gruppen**väljer du de virtuella datorer som ska läggas till i gruppen.
5. Klicka på **Skapa utvärdering** för att skapa gruppen och kör utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-hyper-v/assessment-create.png)

6. När utvärderingen har skapats kan du Visa den i **servrar** > **Azure Migrate: Server bedömning**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.


## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskriver:

- **Azure-beredskap**: om virtuella datorer är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: beräknad kostnad för beräkning och lagring för de virtuella datorerna i Azure.
- **Kostnads uppskattning för månatlig lagring**: beräknade kostnader för disk lagring efter migrering.


### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **mål för migrering** >  **servrar** > **Azure Migrate: Server utvärdering**, klicka på **utvärderingar**.
2. I **bedömningar**klickar du på en utvärdering för att öppna den.

    ![Utvärderings Sammanfattning](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Granska Azure readiness

1. I **Azure-beredskap**kontrollerar du om de virtuella datorerna är klara för migrering till Azure.
2. Granska VM-statusen:
    - **Redo för Azure**: Azure Migrate rekommenderar en VM-storlek och kostnads uppskattning för virtuella datorer i utvärderingen.
    - **Klar med villkor**: visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: visar problem och förslag på åtgärder.
    - **Beredskap okänd**: används när Azure Migrate inte kan utvärdera beredskap på grund av problem med data tillgänglighet.

2. Klicka på en status för **Azure-beredskap** . Du kan visa information om VM-beredskap och öka detalj nivån för att se information om virtuella datorer, inklusive beräknings-, lagrings-och nätverks inställningar.

### <a name="review-cost-details"></a>Granska kostnads information

I den här vyn visas den beräknade beräknings-och lagrings kostnaden för virtuella datorer som körs i Azure.

1. Granska kostnader för beräkning och lagring per månad. Kostnaderna sammanställs för alla virtuella datorer i den utvärderade gruppen.

    - Kostnads uppskattningar baseras på storleks rekommendationerna för en dator och dess diskar och egenskaper.
    - Uppskattade månatliga kostnader för beräkning och lagring visas.
    - Kostnads uppskattningen är att köra lokala virtuella datorer som virtuella IaaS-datorer. Azure Migrate Server-utvärderingen beaktar inte PaaS-eller SaaS-kostnader.

2. Du kan granska månads beräkningarna för lagrings kostnader. Den här vyn visar aggregerade lagrings kostnader för den utvärderade gruppen och delas upp över olika typer av lagrings diskar.
3. Du kan öka detalj nivån för att se information om vissa virtuella datorer.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör prestandabaserade utvärderingar tilldelas utvärderingen en förtroende nivå.

![Säkerhetsomdöme](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Fortsätt till den tredje kursen i serien och lär dig hur du migrerar virtuella Hyper-V-datorer till Azure med Azure Migrate Server-migrering.

> [!div class="nextstepaction"]
> [Migrera virtuella Hyper-V-datorer](./tutorial-migrate-hyper-v.md)
