---
title: Utvärdera Hyper-V-datorer för migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du utvärdera lokala Hyper-V-datorer för migrering till Azure med hjälp av Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 83567a45980b29931f9b68bd6d60df0d427b09de
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813024"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Utvärdera Hyper-V-datorer med Azure Migrate Server-utvärdering

Den här artikeln visar hur du avgör den lokala Hyper-V-datorer med hjälp av Azure Migrate: Server Assessment tool.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en hubb för verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Hubben innehåller Azure Migrate verktyg och erbjudanden för programvara från tredje part för oberoende programvaruleverantörer (ISV).



Den här självstudien är andra i en serie som visar hur du utvärdera och migrera Hyper-V-datorer till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera och registrera en Azure Migrate-installation.
> * Starta kontinuerlig identifiering av lokala virtuella datorer.
> * Gruppera identifierade virtuella datorer och utvärdera gruppen.
> * Granska utvärderingen.

> [!NOTE]
> Självstudiekurser visar den enklaste distribution sökvägen för ett scenario så att du snabbt kan ställa in proof-of-concept. Använd standardalternativen där det är möjligt självstudier och visas inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktionsartiklarna.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Förutsättningar

- [Fullständig](tutorial-prepare-hyper-v.md) den första kursen i den här serien. Om du inte fungerar inte anvisningarna i den här kursen.
- Här är vad du bör har gjort i den första kursen:
    - [Konfigurera Azure-behörigheter](tutorial-prepare-hyper-v.md#prepare-azure) för Azure Migrate.
    - [Förbereda Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) kluster, värdar och virtuella datorer för utvärdering.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

1. I Azure portal > **alla tjänster**, Sök efter **Azure Migrate**.
2. I sökresultaten väljer **Azure Migrate**.
3. I **översikt**under **Upptäck, utvärdera och migrera servrar för**, klickar du på **utvärdera och migrera servrar för**.

    ![Identifiera och utvärdera servrar](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. I **komma igång**, klickar du på **lägga till verktyg**.
5. I den **migrera projektet** fliken, Välj din Azure-prenumeration och skapa en resursgrupp om du inte har något.
6. I **projektinformation**, ange projektnamnet på och den region där du vill skapa projektet.


    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-hyper-v/migrate-project.png)

    Du kan skapa ett Azure Migrate-projekt i dessa regioner.

    **Geografi** | **Region**
    --- | ---
    Asien  | Sydostasien
    Europa | Europa, norra eller Europa, västra
    Storbritannien |  Storbritannien, södra eller Storbritannien, västra
    USA | Östra USA, västra USA 2 eller USA, västra centrala

    - Regionen projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer.
    - Du kan välja en annan Azure-region när du migrerar de virtuella datorerna. Alla Azure-regioner har stöd för migreringsmålet.

7. Klicka på **Nästa**.
8. I **väljer utvärderingsvertyget**väljer **Azure Migrate: Server-utvärdering** > **nästa**.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. I **väljer Migreringsverktyget**väljer **hoppa över att lägga till en Migreringsverktyget för tillfället** > **nästa**.
10. I **granska + Lägg till verktyg**, granskar du inställningarna och klicka på **lägga till verktyg**.
11. Vänta några minuter för Azure Migrate-projektet ska distribueras. Du kommer till projektsidan för. Om du inte ser projektet, du kan komma åt den från **servrar** i instrumentpanelen för Azure Migrate.




## <a name="set-up-the-appliance-vm"></a>Konfigurera den virtuella datorn-installationen

Azure Migrate Server-utvärdering körs en enkel installation av Hyper-V-dator.

- Den här installationen genomför identifieringen av virtuella datorer och skickar data för VM-metadata och prestanda till Azure Migrate: Server-utvärdering.
- Du ställer in enheten som du:
    - Ladda ned en komprimerad Hyper-V virtuell Hårddisk från Azure-portalen.
    - Skapa och kontrollera att den kan ansluta till Azure Migrate Server-utvärdering.
    - Konfigurera programmet för första gången och registrera den med Azure Migrate-projektet.

### <a name="download-the-vhd"></a>Ladda ned den virtuella Hårddisken

Ladda ned den komprimerade VHD-mallen för produkten.

1. I **migrering mål** > **servrar** > **Azure Migrate: Server-utvärdering**, klickar du på **identifiera**.
2. I **identifiera datorer** > **är dina datorer virtualiserade?** , klickar du på **Ja, med Hyper-V**.
3. Klicka på **hämta** att ladda ned VHD-filen.

    ![Hämta virtuell dator](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Kontrollera säkerheten

Kontrollera att den komprimerade filen är säker, innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera en hash för den virtuella Hårddisken
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Den genererade hashen måste matcha nedanstående inställningar för installation version 1.19.06.27.

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | 3681f745fa2b0a0a6910707d85161ec5
  SHA256 | e6ca109afab9657bdcfb291c343b3e3abced9a273d25273059171f9954d25832



### <a name="create-the-appliance-vm"></a>Skapa virtuell dator

Importera den nedladdade filen och skapa den virtuella datorn.

1. Extrahera komprimerade VHD-filen till en mapp på Hyper-V-värden som är värd för den VM-installationen. Tre mappar extraheras.
2. Öppna Hyper-V Manager. I **åtgärder**, klickar du på **Importera virtuell dator**.

    ![Distribuera virtuell Hårddisk](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. I guiden Importera virtuell dator > **innan du börjar**, klickar du på **nästa**.
3. I **mapp**, ange den mapp som innehåller den extrahera virtuella Hårddisken. Klicka sedan på **Nästa**.
1. I **Välj virtuell dator**, klickar du på **nästa**.
2. I **Välj importtyp**, klickar du på **kopiera den virtuella datorn (skapa ett nytt unikt ID)** . Klicka sedan på **Nästa**.
3. I **väljer mål**, lämnar du standardinställningen. Klicka på **Nästa**.
4. I **Storage mappar**, lämnar du standardinställningen. Klicka på **Nästa**.
5. I **Välj nätverk**, ange den virtuella växeln som den virtuella datorn ska använda. Växeln måste ha en Internetanslutning för att skicka data till Azure.
6. I **sammanfattning**, granskar du inställningarna. Klicka sedan på **Slutför**.
7. I Hyper-V Manager > **virtuella datorer**, starta den virtuella datorn.


### <a name="verify-appliance-access-to-azure"></a>Verifiera installationen åtkomst till Azure

Kontrollera att installationen VM kan ansluta till [Azure URL: er](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>Konfigurera programmet

Ställ in installationen för första gången.

1. I Hyper-V Manager > **virtuella datorer**, högerklicka på den virtuella datorn > **Connect**.
2. Ange språk, tidszon och lösenord för installationen.
3. Öppna en webbläsare på en dator som kan ansluta till den virtuella datorn och öppna URL: en för webbappen installation: **https://*installationsnamnet eller IP-adress*: 44368**.

   Du kan också öppna appen från skrivbordet installation genom att klicka på genvägen till appen.
1. I webbapp > **konfigurera förhandskraven**, gör du följande:
    - **Licens**: Acceptera licensvillkoren och läs informationen från tredje part.
    - **Anslutningen**: Appen kontrollerar att den virtuella datorn har Internetåtkomst. Om den virtuella datorn använder en proxyserver:
        - Klicka på **proxyinställningar**, och anger proxyadress och lyssningsport, i formatet http://ProxyIPAddress eller http://ProxyFQDN.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tid synkronisering**: Tid har verifierats. Tiden på enheten som ska vara synkroniserad med internettiden för identifiering av VM ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate Server-utvärdering kontrollerar du att installationen har de senaste uppdateringarna installerade.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klicka på **logga In**. Om den inte visas kan du kontrollera att du har inaktiverat blockering av popup-fönster i webbläsaren.
2. På den nya fliken kan du logga in med dina autentiseringsuppgifter för Azure.
    - Logga in med ditt användarnamn och lösenord.
    - Logga in med en PIN-kod stöds inte.
3. När du har loggat in kan gå tillbaka till webbappen.
4. Välj den prenumeration som Azure Migrate-projektet har skapats. Välj projektet.
5. Ange ett namn för produkten. Namnet bör vara alfanumeriskt med 14 tecken eller mindre.
6. Klicka på **registrera**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för SMB virtuella hårddiskar

Om du använder virtuella hårddiskar på små och medelstora företag, måste du aktivera delegering av autentiseringsuppgifter från installationen till Hyper-V-värdar. Om du inte gör detta på varje värd i den [föregående självstudie](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts), gör den här nu av programmet:

1. Kör det här kommandot på installationen VM. HyperVHost1/HyperVHost2 är exempel värdnamn.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Du kan också göra detta i den lokala Redigeraren för i installationen:
    - I **lokal datorprincip** > **Datorkonfiguration**, klickar du på **Administrationsmallar** > **System**  >  **Autentiseringsuppgifter delegering**.
    - Dubbelklicka på **Tillåt delegering av nya autentiseringsuppgifter**, och välj **aktiverad**.
    - I **alternativ**, klickar du på **visa**, och Lägg till varje Hyper-V-värd som du vill identifiera i listan med **wsman /** som ett prefix.
    - I **delegering av autentiseringsuppgifter**, dubbelklicka på **Tillåt delegering av nya autentiseringsuppgifter med endast NTLM-autentisering**. Igen, lägga till varje Hyper-V-värd som du vill identifiera i listan med **wsman /** som ett prefix.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifieringen

Ansluta från installationen till Hyper-V-värdar eller kluster och starta identifieringen av virtuella datorer.

1. I **användarnamn** och **lösenord**, ange de kontoautentiseringsuppgifter som installationen använder för att identifiera virtuella datorer. Ange ett eget namn för autentiseringsuppgifterna och klicka på **spara information**.
2. Klicka på **Lägg till värd**, och ange information om Hyper-V-värd/kluster.
3. Klicka på **Validera**. Efter valideringen kan visas antalet virtuella datorer som kan identifieras på varje värdkluster.
    - Om valideringen misslyckas för en värd, granska felet genom att hovra över ikonen i den **Status** kolumn. Åtgärda problem och validera igen.
    - Om du vill ta bort värdar eller kluster, Välj > **ta bort**.
    - Du kan inte ta bort en viss värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster, även om det finns problem med specifika värdar i klustret.
4. Efter valideringen kan klickar du på **spara och starta identifieringen** att starta identifieringsprocessen.

Detta startar identifieringen. Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer visas i Azure-portalen.

### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När identifieringen har slutförts kan du kontrollera att de virtuella datorerna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. I **Azure Migrate - servrar** > **Azure Migrate: Server-utvärdering** klickar du på den ikon som visar antalet för **identifierade servrar**.

## <a name="set-up-an-assessment"></a>Konfigurera en utvärdering

Det finns två typer av utvärderingar kan du köra med Azure Migrate Server-utvärdering.

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar utifrån insamlade prestandadata | **Rekommenderad storlek**: Baserat på användningsdata för CPU och minne.<br/><br/> **Rekommenderade disktyp (standard eller premium managed disk)** : Baserat på IOPS och dataflöden för lokala diskar.
**Som lokalt** | Utvärderingar baserat på den lokala storlek. | **Rekommenderad storlek**: Baserat på den lokala VM-storlek<br/><br> **Rekommenderade disktyp**: Baserat på storage-typ du väljer för utvärderingen.



### <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. Granska den [bästa praxis](best-practices-assessment.md) för att skapa utvärderingar.
2. I **servrar** > **Azure Migrate: Server-utvärdering**, klickar du på **utvärdera**.

    ![Utvärdera](./media/tutorial-assess-hyper-v/assess.png)

3. I **utvärdera servrar**, ange ett namn för utvärderingen.
4. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Utvärderingsegenskaper](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. I **Välj eller skapa en grupp**väljer **Skapa ny** och ange ett gruppnamn. En grupp samlar in en eller flera virtuella datorer för utvärdering.
4. I **lägga till datorer i gruppen**, Välj virtuella datorer för att lägga till i gruppen.
5. Klicka på **skapa utvärdering** att skapa gruppen och kör utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-hyper-v/assessment-create.png)

6. När utvärderingen har skapats kan du visa den i **servrar** > **Azure Migrate: Server-utvärdering**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.


## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskrivs:

- **Azure-beredskap**: Om virtuella datorer är lämpliga för migrering till Azure.
- **Månatliga kostnadsuppskattning**: De uppskattade månatliga beräknings- och kostnaderna för att köra de virtuella datorerna i Azure.
- **Månatliga storage kostnadsuppskattning**: Beräknade kostnader för disklagring efter migreringen.


### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **migrering mål** >  **servrar** > **Azure Migrate: Server-utvärdering**, klickar du på **utvärderingar**.
2. I **utvärderingar**, klicka på en utvärdering och öppna den.

    ![Sammanfattning av uppgraderingsutvärderingen](./media/tutorial-assess-hyper-v/assessment-summary.png)


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

![Säkerhetsomdöme](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Fortsätta att den tredje självstudien i serien, och lär dig hur du migrerar Hyper-V-datorer till Azure med Azure Migrate servermigrering.

> [!div class="nextstepaction"]
> [Migrera Hyper-V-datorer](./tutorial-migrate-hyper-v.md)
