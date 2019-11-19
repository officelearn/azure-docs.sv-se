---
title: Utvärdera virtuella VMware-datorer för migrering till Azure med Azure Migrate
description: Beskriver hur du bedömer lokala virtuella VMware-datorer för migrering till Azure med hjälp av Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: 7187cb41fa7ea499035c57e83e04038b1269b418
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158665"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Utvärdera virtuella VMware-datorer med Azure Migrate: Server utvärdering

Den här artikeln visar hur du kan utvärdera lokala virtuella VMware-datorer med hjälp av verktyget Azure Migrate: Server bedömning.

[Azure Migrate](migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate-verktyg och oberoende program varu leverantörer från tredje part (ISV).



Den här självstudien är den andra i en serie som visar hur du bedömer och migrerar virtuella VMware-datorer till Azure. I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera en Azure Migrate-apparat som körs lokalt för att utvärdera de virtuella datorerna.
> * Starta kontinuerlig identifiering av lokala virtuella datorer. Enheten skickar konfigurations-och prestanda data för identifierade virtuella datorer till Azure.
> * Gruppera identifierade virtuella datorer och utvärdera VM-gruppen.
> * Granska utvärderingen.



> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktions artiklar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.


## <a name="prerequisites"></a>Krav

- [Slutför](tutorial-prepare-vmware.md) den första självstudien i den här serien. Om du inte gör det fungerar inte instruktionerna i den här självstudien.
- Det här är vad du behöver göra i den första självstudien:
    - [Konfigurera Azure-behörigheter](tutorial-prepare-vmware.md#prepare-azure) för Azure Migrate.
    - [Förbered VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) för utvärdering. Inställningarna för VMware bör verifieras och du bör ha behörighet att skapa en virtuell VMware-dator med en ägg-mall. Du bör också ha ett konto som har kon figurer ATS för identifiering av virtuella datorer. De portar som krävs bör vara tillgängliga och du bör vara medveten om de URL: er som behövs för åtkomst till Azure.


## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Konfigurera ett nytt Azure Migrate-projekt enligt följande.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.
3. I **Översikt** under **Identifiera, utvärdera och migrera servrar** klickar du på **Utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/tutorial-assess-vmware/assess-migrate.png)

4. I **Komma igång** klickar du på **Lägg till verktyg**.
5. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.     
6. I **projekt information**anger du projekt namnet och geografin som du vill skapa projektet i. Asien, Europa, Storbritannien och USA stöds.

    - Projektgeografin används bara för att lagra de metadata som samlas in från lokala virtuella datorer.
    - Du kan välja vilken målregion du vill när du kör en migrering.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-vmware/migrate-project.png)


7. Klicka på **Nästa**.
8. I **Välj bedömnings verktyg**väljer du **Azure Migrate: Server utvärdering** > **Nästa**.

    ![Skapa ett Azure Migrate-projekt](./media/tutorial-assess-vmware/assessment-tool.png)

9. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
10. I **Granska + lägg till verktyg**
granskar du inställningarna och klickar på **Lägg till verktyg**.
11. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.


## <a name="set-up-the-appliance-vm"></a>Konfigurera den virtuella dator enheten

Azure Migrate: Server utvärderingen kör en förenklad VMware VM-enhet.

- Den här installationen utför VM-identifiering och skickar VM-metadata och prestanda data till Azure Migrate Server utvärderingen.
- Så här konfigurerar du den apparat som du:
    - Hämta en mall för en embryo-fil och importera den till vCenter Server.
    - Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
    - Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.
- Du kan konfigurera flera apparater för ett enda Azure Migrate-projekt. För alla enheter stöds identifiering av upp till 35 000 virtuella datorer. Högst 10 000 servrar kan identifieras per apparat.

### <a name="download-the-ova-template"></a>Ladda ned mallen för ägg

1. I **mål för migrering** > **servrar** > **Azure Migrate: Server utvärdering**, klicka på **identifiera**.
2. I **Identifiera datorer** > **Är dina datorer virtualiserade?** klickar du på **Ja, med VMware vSphere Hypervisor-programmet**.
3. Klicka på **Ladda ned** för att ladda ned .OVA-mallfilen.

    ![Hämta .OVA-fil](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att ägg filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera en hash för OVA-filen:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. För version 2.19.07.30 ska den genererade hashen matcha dessa värden.

  **Algoritm** | **Hash-värde**
  --- | ---
  MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
  SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c


### <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den nedladdade filen och skapa en virtuell dator.

1. I vSphere-klientkonsolen klickar du på **Arkiv** > **Distribuera OVF-mall**.

    ![Distribuera OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. I guiden Distribuera OVF-mall > **källa**anger du platsen för ägg filen.
3. I **namn** och **plats**anger du ett eget namn för den virtuella datorn. Välj det lager objekt som den virtuella datorn ska vara värd för.
5. I **värd/kluster**anger du den värd eller det kluster som den virtuella datorn ska köras på.
6. I **lagring**anger du lagrings målet för den virtuella datorn.
7. I **Diskformat** anger du disktyp och storlek.
8. I **nätverks mappning**anger du det nätverk som den virtuella datorn ska ansluta till. Nätverket måste ha Internet anslutning för att kunna skicka metadata till Azure Migrate Server bedömning.
9. Granska och bekräfta inställningarna. Klicka sedan på **Slutför**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns dator kan ansluta till [Azure-URL: er](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera installationen med hjälp av följande steg.

1. Högerklicka på den virtuella datorn i vSphere-klientkonsolen > **Öppna konsol**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att klicka på genvägen till appen.
4. I webbappen > **Konfigurera krav**gör du följande:
    - **Licens**: Godkänn licens villkoren och Läs informationen från tredje part.
    - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
        - Klicka på **proxyinställningar**och ange proxyadress och lyssnings port, i formatet http://ProxyIPAddress eller http://ProxyFQDN.
        - Ange autentiseringsuppgifter om proxyn kräver autentisering.
        - Endast HTTP-proxy stöds.
    - **Tidssynkronisering**: tiden för installationen bör vara synkroniserad med Internet-tid för att identifieringen ska fungera korrekt.
    - **Installera uppdateringar: installationen**säkerställer att de senaste uppdateringarna är installerade.
    - **Installera vddk**: enheten kontrollerar att VMware vSphere Virtual Disk Development Kit (vddk) har installerats.
        - Azure Migrate: Server-migreringen använder VDDK för att replikera datorer under migreringen till Azure.
        - Hämta VDDK 6,7 från VMware och extrahera det hämtade ZIP-innehållet till den angivna platsen på enheten.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klicka på **Logga**in. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
2. På fliken nytt loggar du in med dina Azure-autentiseringsuppgifter.
    - Logga in med ditt användar namn och lösen ord.
    - Inloggning med en PIN-kod stöds inte.
3. När du har loggat in går du tillbaka till webbappen.
2. Välj den prenumeration där Azure Migrate projektet skapades och välj sedan projektet.
3. Ange ett namn för enheten. Namnet måste vara alfanumeriskt med 14 tecken eller mindre.
4. Klicka på **Registrera**.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Installations programmet måste ansluta till vCenter Server för att identifiera konfigurations-och prestanda data för de virtuella datorerna.

### <a name="specify-vcenter-server-details"></a>Ange vCenter Server-information
1. I **ange vCenter Server information**anger du namnet (FQDN) eller IP-adressen för vCenter Server. Du kan lämna standard porten eller ange en anpassad port som vCenter Server lyssnar på.
2. I **användar namn** och **lösen ord**anger du de skrivskyddade kontoautentiseringsuppgifter som installeras av enheten för att identifiera virtuella datorer på vCenter-servern. Kontrol lera att kontot har de [behörigheter som krävs för identifiering](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Du kan begränsa identifieringen genom att begränsa åtkomsten till vCenter-kontot enligt detta. Lär dig mer om omfattnings identifiering [här](tutorial-assess-vmware.md#scoping-discovery).
3. Klicka på **Verifiera anslutning** för att kontrol lera att installationen kan ansluta till vCenter Server.

### <a name="specify-vm-credentials"></a>Ange autentiseringsuppgifter för virtuell dator
För identifiering av program, roller och funktioner och visualisering av beroenden för de virtuella datorerna kan du ange en VM-autentiseringsuppgift som har åtkomst till de virtuella VMware-datorerna. Du kan lägga till en autentiseringsuppgift för virtuella Windows-datorer och en autentiseringsuppgift för virtuella Linux-datorer. [Läs mer](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) om de behörigheter som krävs.

> [!NOTE]
> Den här indatamängden är valfri och behövs för att aktivera program identifiering och visualisering av beroenden för agenter.

1. I **identifiera program och beroenden på virtuella datorer**klickar du på **Lägg till autentiseringsuppgifter**.
2. Välj **operativ system**.
3. Ange ett eget namn för autentiseringsuppgiften.
4. I **användar namn** och **lösen ord**anger du ett konto som har minst gäst åtkomst på de virtuella datorerna.
5. Klicka på **Lägg till**.

När du har angett vCenter Server och autentiseringsuppgifter för virtuella datorer (valfritt), klickar du på **Spara och starta identifiering** för att starta identifieringen av den lokala miljön.

Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer som visas i portalen. Identifiering av installerade program, roller och funktioner tar lite tid, tiden beror på antalet virtuella datorer som identifieras. För 500 virtuella datorer tar det cirka 1 timme för program inventeringen att visas i Azure Migrate portalen.

### <a name="scoping-discovery"></a>Omfattnings identifiering

Identifiering kan begränsas genom att begränsa åtkomsten till det vCenter-konto som används för identifiering. Du kan ange ett omfång för att vCenter Server Data Center, kluster, kluster kluster, värdar, värdbaserade värdar eller enskilda virtuella datorer.

Om du vill ange omfånget måste du utföra följande steg:
1.  Skapa ett vCenter-användarkonto.
2.  Definiera en ny roll med nödvändiga privilegier. (<em>krävs för Server-migrering utan agent</em>)
3.  Tilldela behörigheter till användar kontot för vCenter-objekt.

**Skapa ett vCenter-användarkonto**
1.  Logga in på vSphere-webbklient som vCenter Server administratör.
2.  Klicka på **Administration** > **SSO-användare och grupper** > **användare** -fliken.
3.  Klicka på ikonen **ny användare** .
4.  Fyll i den information som krävs för att skapa en ny användare och klicka på **OK**.

**Definiera en ny roll med nödvändiga behörigheter** (<em>krävs för migrering utan server</em>)
1.  Logga in på vSphere-webbklienten som vCenter Server administratör.
2.  Bläddra till **Administration** > **roll hanteraren**.
3.  Välj vCenter Server på den nedrullningsbara menyn.
4.  Klicka på **skapa roll** åtgärd.
5.  Ange ett namn för den nya rollen. (till exempel <em>Azure_Migrate</em>).
6.  Tilldela de här [behörigheterna](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) till den nyligen definierade rollen.
7.  Klicka på **OK**.

**Tilldela behörigheter för vCenter-objekt**

Det finns två metoder för att tilldela behörigheter för inventerings objekt i vCenter till vCenter-användarkontot med en tilldelad roll.
- För Server utvärdering måste en **skrivskyddad** roll tillämpas på vCenter-användarkontot för alla överordnade objekt där de virtuella datorer som ska identifieras finns. Alla överordnade objekt – värd, mapp med värdar, kluster, mapp med kluster i hierarkin upp till data centret ska inkluderas. Dessa behörigheter ska spridas till underordnade objekt i hierarkin.

    På samma sätt som för Server migrering, kan en användardefinierad roll (som heter <em>Azure _Migrate</em>) med dessa [privilegier](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) tilldelas till vCenter-användarkontot för alla överordnade objekt där de virtuella datorer som ska migreras finns.

![Tilldela behörigheter](./media/tutorial-assess-vmware/assign-perms.png)

- Den alternativa metoden är att tilldela användar kontot och rollen rollen på data center nivå och sprida dem till de underordnade objekten. Ge sedan kontot **Ingen åtkomst** roll för varje objekt (t. ex. virtuella datorer) som du inte vill identifiera/migrera. Den här konfigurationen är besvärlig. Det visar oavsiktlig åtkomst kontroll, eftersom varje nytt underordnat objekt automatiskt beviljas åtkomst som ärvs från den överordnade. Därför rekommenderar vi att du använder den första metoden.

> [!NOTE]
> I dag kan Server utvärderingen inte identifiera virtuella datorer om vCenter-kontot har åtkomst som beviljats på nivån vCenter VM Folder. Om du vill begränsa identifieringen av VM-mappar kan du göra det genom att se till att vCenter-kontot har skrivskyddad åtkomst som tilldelats på en VM-nivå.  Följande är instruktioner om hur du kan göra detta:
>
> 1. Tilldela skrivskyddade behörigheter för alla virtuella datorer i de VM-mappar som du vill använda för att begränsa identifieringen.
> 2. Bevilja skrivskyddad åtkomst till alla överordnade objekt där de virtuella datorerna finns. Alla överordnade objekt – värd, mapp med värdar, kluster, kluster kluster – i hierarkin till data centret ska inkluderas. Du behöver inte sprida behörigheterna till alla underordnade objekt.
> 3. Använd autentiseringsuppgifterna för identifiering som väljer datacenter som *samlings omfång*. Med RBAC-inställningarna ser du till att motsvarande vCenter-användare bara har åtkomst till klient datorer som är användarspecifika.
>
> Observera att mappen med värdar och kluster stöds.

### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Efter identifieringen kan du kontrol lera att de virtuella datorerna visas i Azure Portal.

1. Öppna instrument panelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar** > **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.

## <a name="set-up-an-assessment"></a>Konfigurera en utvärdering

Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: Server utvärdering.

**Utvärdering** | **Information** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar baserade på insamlade prestanda data | **Rekommenderad VM-storlek**: baserat på processor-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)** : baserat på IOPS och data flödet för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad VM-storlek**: baserat på den lokala virtuella dator storleken<br/><br> **Rekommenderad disktyp**: baserat på den inställning för lagrings typ som du väljer för utvärderingen.


### <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. Gå igenom [metod tipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. Klicka på **utvärdera**i panelen **Azure Migrate: Server bedömning** på fliken **servrar** .

    ![Utvärdera](./media/tutorial-assess-vmware/assess.png)

2. I **utvärdera servrar**anger du ett namn för utvärderingen.
3. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Bedömnings egenskaper](./media/tutorial-assess-vmware/view-all.png)

3. I **Välj eller skapa en grupp**väljer du **Skapa ny**och anger ett grupp namn. En grupp samlar in en eller flera virtuella datorer för utvärdering.
4. I **Lägg till datorer i gruppen**väljer du de virtuella datorer som ska läggas till i gruppen.
5. Klicka på **Skapa utvärdering** för att skapa gruppen och kör utvärderingen.

    ![Skapa en utvärdering](./media/tutorial-assess-vmware/assessment-create.png)

6. När utvärderingen har skapats kan du Visa den i **servrar** > **Azure Migrate: Server bedömning** > **utvärderingar**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.



## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskriver:

- **Azure-beredskap**: om virtuella datorer är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: beräknad kostnad för beräkning och lagring för de virtuella datorerna i Azure.
- **Kostnads uppskattning för månatlig lagring**: beräknade kostnader för disk lagring efter migrering.

### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **mål för migrering** >  - **servrar**klickar du på **utvärderingar** i **Azure Migrate: Server utvärdering**.
2. I **bedömningar**klickar du på en utvärdering för att öppna den.

    ![Utvärderings Sammanfattning](./media/tutorial-assess-vmware/assessment-summary.png)

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

![Säkerhetsomdöme](./media/tutorial-assess-vmware/confidence-rating.png)

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

I den här kursen har du:

> [!div class="checklist"]
> * Konfigurera en Azure Migrate-apparat
> * Skapat och granskat en utvärdering

Fortsätt till den tredje kursen i serien och lär dig hur du migrerar virtuella VMware-datorer till Azure med Azure Migrate Server-migrering.

> [!div class="nextstepaction"]
> [Migrera virtuella VMware-datorer](./tutorial-migrate-vmware.md)
