---
title: Utvärdera virtuella VMware-datorer för migrering till Azure med hjälp av Azure Migrate Server bedömning
description: Beskriver hur du bedömer lokala virtuella VMware-datorer för migrering till Azure med hjälp av Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: hamusa
ms.openlocfilehash: 7f161afe13bad8c548806d4b4ceb9372dc511cc3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239324"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Utvärdera virtuella VMware-datorer med hjälp av Azure Migrate Server bedömning

Den här artikeln visar hur du kan utvärdera lokala virtuella VMware-datorer (VM) med hjälp av Server Assessment-verktyget i Azure Migrate.

[Azure Migrate](migrate-services-overview.md) innehåller en hubb med verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbets belastningar till Microsoft Azure. Hubben omfattar Azure Migrate verktyg och oberoende program varu leverantörer (ISV) från Microsoft-partner.

Den här självstudien är den andra i en serie som visar hur du bedömer och migrerar virtuella VMware-datorer till Azure. I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Konfigurera ett Azure Migrate-projekt.
> * Konfigurera en Azure Migrate-apparat som körs lokalt för att utvärdera de virtuella datorerna.
> * Starta kontinuerlig identifiering av lokala virtuella datorer. Enheten skickar konfigurations-och prestanda data för identifierade virtuella datorer till Azure.
> * Gruppera identifierade virtuella datorer och utvärdera VM-gruppen.
> * Granska utvärderingen.

> [!NOTE]
> Självstudier visar dig den enklaste distributions Sök vägen för ett scenario så att du snabbt kan konfigurera ett koncept för koncept bevis. Självstudier använder standard alternativ där det är möjligt, och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i instruktions artiklar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[Slutför den första självstudien](tutorial-prepare-vmware.md) i den här serien. Om du inte gör det fungerar inte instruktionerna i den här självstudien.

Det här är vad du behöver göra i den första självstudien:

- [Konfigurera Azure-behörigheter](tutorial-prepare-vmware.md#prepare-azure) för Azure Migrate.
- [Förbered VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) för utvärdering:
   - [Verifiera](migrate-support-matrix-vmware.md#vmware-requirements) VMware-inställningar.
   - Konfigurera behörigheter i VMware för att skapa en virtuell VMware-dator med en ägg-mall.
   - Konfigurera ett [konto för identifiering av virtuell dator](migrate-support-matrix-vmware.md#vmware-requirements). 
   - Gör de [nödvändiga portarna](migrate-support-matrix-vmware.md#port-access) tillgängliga.
   - Tänk på vilka [URL: er som behövs](migrate-replication-appliance.md#url-access) för åtkomst till Azure.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurera ett Azure Migrate-projekt

Konfigurera ett nytt Azure Migrate projekt enligt följande:

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
1. Under **Tjänster** väljer du **Azure Migrate**.
1. I **Översikt**, under **Upptäck, utvärdera och migrera servrar**, väljer du **utvärdera och migrera servrar**.

   ![Knapp för att utvärdera och migrera servrar](./media/tutorial-assess-vmware/assess-migrate.png)

1. I **komma igång**väljer du **Lägg till verktyg**.
1. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.     
1. I **projekt information**anger du projekt namnet och geografin som du vill skapa projektet i. Asien, Europa, Storbritannien och USA stöds.

   Projektgeografin används bara för att lagra de metadata som samlas in från lokala virtuella datorer. Du kan välja vilken målregion du vill när du kör en migrering.

   ![Rutor för projekt namn och region](./media/tutorial-assess-vmware/migrate-project.png)

1. Välj **Nästa**.
1. I **Välj bedömnings verktyg**väljer du **Azure Migrate: Server utvärdering** > **Nästa**.

   ![Val för Server bedömnings verktyget](./media/tutorial-assess-vmware/assessment-tool.png)

1. I **Välj migreringsverktyg** väljer du **Hoppa över att lägga till ett migreringsverktyg just nu** > **Nästa**.
1. I **Granska + Lägg till verktyg**granskar du inställningarna och väljer **Lägg till verktyg**.
1. Vänta några minuter tills Azure Migrate-projektet har distribuerats. Projektsidan öppnas. Om du inte ser projektet kan du öppna det från **Servrar** i Azure Migrate-instrumentpanelen.

## <a name="set-up-the-appliance-vm"></a>Konfigurera den virtuella dator enheten

Azure Migrate Server utvärderingen kör en enkel VMware VM-installation. Den här enheten utför VM-identifiering och samlar in VM-metadata och prestanda data.

Konfigurera installationen genom att:

- Hämta en mall för en embryo-fil och importera den till vCenter Server.
- Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
- Konfigurera enheten för första gången och registrera den med Azure Migrate-projektet.

Du kan konfigurera flera apparater för ett enda Azure Migrate-projekt. För alla apparater stöder Server utvärdering identifiering av upp till 35 000 virtuella datorer. Det kan identifiera högst 10 000 servrar per apparat.

### <a name="download-the-ova-template"></a>Ladda ned mallen för ägg

1. I **mål för migrering** > **servrar** > **Azure Migrate: Server utvärdering**, Välj **identifiera**.
1. I **identifiera datorer** > **dina datorer virtualiserade? väljer du** **Ja, med VMware vSphere hypervisor**.
1. Välj **Ladda ned** för att ladda ned filer för embryon.

   ![Alternativ för att ladda ned en ägg fil](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att ägg filen är säker innan du distribuerar den:

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
1. Kör följande kommando för att generera hashen för den ägg filen:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Exempel på användning: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

För version 2.19.07.30 ska den genererade hashen matcha dessa värden:

**Algoritm** | **Hash-värde**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den nedladdade filen och skapa en virtuell dator:

1. I klient konsolen för vSphere väljer du **fil** > **distribuera OVF-mall**.

   ![Meny kommando för att distribuera en OVF-mall](./media/tutorial-assess-vmware/deploy-ovf.png)

1. I guiden Distribuera OVF-mall > **källa**anger du platsen för ägg filen.
1. I **namn** och **plats**anger du ett eget namn för den virtuella datorn. Välj det lager objekt som den virtuella datorn ska vara värd för.
1. I **värd/kluster**anger du den värd eller det kluster som den virtuella datorn ska köras på.
1. I **lagring**anger du lagrings målet för den virtuella datorn.
1. I **Diskformat** anger du disktyp och storlek.
1. I **nätverks mappning**anger du det nätverk som den virtuella datorn ska ansluta till. Nätverket måste ha Internet anslutning för att kunna skicka metadata till Azure Migrate Server bedömning.
1. Granska och bekräfta inställningarna och välj sedan **Slutför**.

### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns dator kan ansluta till [Azure-URL: er](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera installationen med hjälp av följande steg:

1. I klient konsolen för vSphere högerklickar du på den virtuella datorn och väljer sedan **Öppna konsol**.
1. Ange språk, tidszon och lösen ord för enheten.
1. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens*namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att välja genvägen till appen.
1. I webbappen > **Konfigurera krav**gör du följande:
   - **Licens**: Godkänn licens villkoren och Läs informationen från tredje part.
   - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
     - Välj **proxyinställningar**och ange proxyadress och lyssnings port i formatet http://ProxyIPAddress eller http://ProxyFQDN.
     - Ange autentiseringsuppgifter om proxyn kräver autentisering.
     - Observera att endast HTTP-proxy stöds.
   - **Tidssynkronisering**: tiden för installationen bör vara synkroniserad med Internet-tid för att identifieringen ska fungera korrekt.
   - **Installera uppdateringar: installationen**säkerställer att de senaste uppdateringarna är installerade.
   - **Installera vddk**: enheten kontrollerar att VMware vSphere Virtual Disk Development Kit (vddk) har installerats. Om den inte är installerad laddar du ned VDDK 6,7 från VMware och extraherar det hämtade ZIP-innehållet till den angivna platsen på enheten.

     Azure Migrate Server-migrering använder VDDK för att replikera datorer under migreringen till Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Välj **Logga**in. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
1. På fliken nytt loggar du in med ditt användar namn och lösen ord för Azure.
   
   Inloggning med en PIN-kod stöds inte.
1. När du har loggat in går du tillbaka till webbappen.
1. Välj den prenumeration där Azure Migrate projektet skapades och välj sedan projektet.
1. Ange ett namn för enheten. Namnet måste vara alfanumeriskt med 14 tecken eller färre.
1. Välj **Registrera**.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Installations programmet måste ansluta till vCenter Server för att identifiera konfigurations-och prestanda data för de virtuella datorerna.

### <a name="specify-vcenter-server-details"></a>Ange vCenter Server-information
1. I **ange vCenter Server information**anger du namnet (FQDN) eller IP-adressen för vCenter Server-instansen. Du kan lämna standard porten eller ange en anpassad port som vCenter Server lyssnar på.
1. I **användar namn** och **lösen ord**anger du vCenter Server kontoautentiseringsuppgifter som installeras av installations programmet för att identifiera virtuella datorer på vCenter Server-instansen. 

   Kontrol lera att kontot har de [behörigheter som krävs för identifiering](migrate-support-matrix-vmware.md#vmware-requirements). Du kan begränsa [identifieringen](tutorial-assess-vmware.md#set-the-scope-of-discovery) genom att begränsa åtkomsten till vCenter-kontot.
1. Välj **Verifiera anslutning** för att kontrol lera att enheten kan ansluta till vCenter Server.

### <a name="specify-vm-credentials"></a>Ange autentiseringsuppgifter för virtuell dator
För identifiering av program, roller och funktioner och för visualisering av beroenden för de virtuella datorerna kan du ange autentiseringsuppgifter för virtuella datorer som har åtkomst till de virtuella VMware-datorerna. Du kan lägga till en autentiseringsuppgift för virtuella Windows-datorer och en autentiseringsuppgift för virtuella Linux-datorer. [Läs mer](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) om de nödvändiga åtkomst behörigheterna.

> [!NOTE]
> Den här indatamängden är valfri, men du behöver det om du vill aktivera program identifiering och visualisering av en agent lös beroende visualisering.

1. I **identifiera program och beroenden på virtuella datorer**väljer du **Lägg till autentiseringsuppgifter**.
1. Gör ett val för **operativ system**.
1. Ange ett eget namn för autentiseringsuppgiften.
1. I **användar namn** och **lösen ord**anger du ett konto som har minst gäst åtkomst på de virtuella datorerna.
1. Välj **Lägg till**.

När du har angett vCenter Server-instansen och autentiseringsuppgifter för virtuella datorer (valfritt), väljer du **Spara och starta identifiering** för att starta identifieringen av den lokala miljön.

Det tar ungefär 15 minuter för metadata för identifierade virtuella datorer som visas i portalen. Det tar lite tid att identifiera installerade program, roller och funktioner. Varaktigheten beror på antalet virtuella datorer som identifieras. För 500 virtuella datorer tar det cirka 1 timme för program inventeringen att visas i Azure Migrate portalen.

### <a name="set-the-scope-of-discovery"></a>Ange identifierings område

Identifiering kan begränsas genom att begränsa åtkomsten till det vCenter-konto som används för identifiering. Du kan ange ett omfång för att vCenter Server Data Center, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda virtuella datorer.

Utför följande procedurer för att ställa in omfånget.

#### <a name="1-create-a-vcenter-user-account"></a>1. skapa ett vCenter-användarkonto
1.  Logga in på vSphere-webbklient som vCenter Server administratör.
1.  Välj **Administration** > **SSO-användare och grupper**och välj sedan fliken **användare** .
1.  Välj ikonen **ny användare** .
1.  Fyll i den information som krävs för att skapa en ny användare och välj sedan **OK**.

#### <a name="2-define-a-new-role-with-required-permission"></a>2. definiera en ny roll med nödvändig behörighet
Den här proceduren krävs för Server migrering utan agent.
1.  Logga in på vSphere-webbklient som vCenter Server administratör.
1.  Bläddra till **Administration** > **roll hanteraren**.
1.  Välj din vCenter Server-instans på den nedrullningsbara menyn.
1.  Välj **skapa roll**.
1.  Ange ett namn för den nya rollen (till exempel <em>Azure_Migrate</em>).
1.  Tilldela [behörigheter](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) till den nyligen definierade rollen.
1.  Välj **OK**.

#### <a name="3-assign-permissions-on-vcenter-objects"></a>3. tilldela behörigheter för vCenter-objekt

Det finns två metoder för att tilldela behörigheter för inventerings objekt i vCenter till vCenter-användarkontot med en roll som tilldelats till det.

För Server utvärdering måste du tillämpa den **skrivskyddade** rollen på vCenter-användarkontot för alla överordnade objekt där de virtuella datorer som ska identifieras finns. Alla överordnade objekt kommer att ingå: värd, mapp med värdar, kluster och mapp för kluster i hierarkin upp till data centret. Dessa behörigheter kommer att spridas till underordnade objekt i hierarkin.

På samma sätt som för Server migrering måste du tillämpa en användardefinierad roll med [behörigheter](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) till vCenter-användarkontot för alla överordnade objekt där de virtuella datorer som ska migreras finns. Den här rollen kan ha namnet <em>Azure _Migrate</em>.

![Tilldela behörigheter](./media/tutorial-assess-vmware/assign-perms.png)

Den alternativa metoden är att tilldela användar kontot och rollen rollen på data center nivå och sprida dem till de underordnade objekten. Ge sedan kontot **Ingen åtkomst** roll för varje objekt (till exempel en virtuell dator) som du inte vill identifiera/migrera. 

Den här alternativa konfigurationen är besvärlig. Det visar oavsiktlig åtkomst kontroll, eftersom varje nytt underordnat objekt automatiskt beviljas åtkomst som ärvs från den överordnade. Vi rekommenderar därför att du använder den första metoden.

> [!NOTE]
> Server utvärderingen kan för närvarande inte identifiera virtuella datorer om vCenter-kontot har åtkomst som beviljats på katalog nivå för vCenter VM. Om du vill begränsa identifieringen av VM-mappar kan du göra det med hjälp av följande procedur. Det garanterar att vCenter-kontot har skrivskyddad åtkomst som tilldelats på en VM-nivå.
>
> 1. Tilldela skrivskyddade behörigheter för alla virtuella datorer i de VM-mappar som du vill använda för att begränsa identifieringen.
> 1. Bevilja skrivskyddad åtkomst till alla överordnade objekt där de virtuella datorerna finns. Alla överordnade objekt (värd, mapp med värdar, kluster, kluster kluster) i hierarkin till data centret tas med. Du behöver inte sprida behörigheterna till alla underordnade objekt.
> 1. Använd autentiseringsuppgifterna för identifiering genom att välja Data Center som **samlings omfång**. Den rollbaserade åtkomst kontrollen som konfigureras garanterar att motsvarande vCenter-användare endast har åtkomst till klientbaserade virtuella datorer.
>
> Observera att mappar med värdar och kluster stöds.

### <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

Efter identifieringen kan du kontrol lera att de virtuella datorerna visas i Azure Portal:

1. Öppna instrument panelen för Azure Migrate.
1. I **Azure Migrate-servrar** > **Azure Migrate: Server utvärdering**väljer du den ikon som visar antalet för **identifierade servrar**.

## <a name="set-up-an-assessment"></a>Konfigurera en utvärdering

Du kan skapa två typer av utvärderingar genom att använda Azure Migrate Server utvärdering:

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar baserade på insamlade prestanda data | **Rekommenderad VM-storlek**: baserat på processor-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)** : baserat på IOPS och data flödet för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek | **Rekommenderad VM-storlek**: baserat på den lokala virtuella dator storleken.<br/><br> **Rekommenderad disk typ**: baserat på den inställning för lagrings typ som du väljer för utvärderingen.

## <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. Gå igenom [metod tipsen](best-practices-assessment.md) för att skapa utvärderingar.
1. På fliken **servrar** i panelen **Azure Migrate: Server bedömning** väljer du **utvärdera**.

   ![Utvärderings knappens placering](./media/tutorial-assess-vmware/assess.png)

1. I **utvärdera servrar**anger du ett namn för utvärderingen.
1. Välj **Visa alla**och granska sedan utvärderings egenskaperna.

   ![Bedömnings egenskaper](./media/tutorial-assess-vmware/view-all.png)

1. I **Välj eller skapa en grupp**väljer du **Skapa ny**och anger ett grupp namn. En grupp samlar in en eller flera virtuella datorer för utvärdering.
1. I **Lägg till datorer i gruppen**väljer du de virtuella datorer som ska läggas till i gruppen.
1. Välj **Skapa utvärdering** för att skapa gruppen och köra utvärderingen.

   ![Utvärdera servrar](./media/tutorial-assess-vmware/assessment-create.png)

1. När utvärderingen har skapats kan du Visa den i **servrar** > **Azure Migrate: Server bedömning** > **utvärderingar**.
1. Välj **Exportera utvärdering** för att ladda ned den som en Excel-fil.

## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskriver:

- **Azure-beredskap**: om virtuella datorer är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: beräknad kostnad för beräkning och lagring för de virtuella datorerna i Azure.
- **Kostnads uppskattning för månatlig lagring**: beräknade kostnader för disk lagring efter migrering.

Så här visar du en utvärdering:

1. I **mål för migrering** > **servrar**väljer du **utvärderingar** i **Azure Migrate: Server utvärdering**.
1. I **utvärderingar**väljer du en utvärdering för att öppna den.

   ![Utvärderings Sammanfattning](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure readiness

1. I **Azure-beredskap**kontrollerar du om de virtuella datorerna är klara för migrering till Azure.
1. Granska VM-statusen:
    - **Redo för Azure**: används när Azure Migrate rekommenderar en VM-storlek och kostnads uppskattning för virtuella datorer i utvärderingen.
    - **Klar med villkor**: visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: visar problem och förslag på åtgärder.
    - **Beredskap okänd**: används när Azure Migrate inte kan utvärdera beredskap på grund av data tillgänglighets problem.

1. Välj en status för **Azure-beredskap** . Du kan visa information om VM-beredskap. Du kan också öka detalj nivån för att se information om virtuella datorer, inklusive beräknings-, lagrings-och nätverks inställningar.

### <a name="review-cost-details"></a>Granska kostnads information

Utvärderings sammanfattningen visar den beräknade beräknings-och lagrings kostnaden för virtuella datorer som körs i Azure. Kostnaderna sammanställs för alla virtuella datorer i den utvärderade gruppen. Du kan öka detalj nivån för att se kostnads information för vissa virtuella datorer.

> [!NOTE]
> Kostnads uppskattningar baseras på storleks rekommendationerna för en dator, dess diskar och dess egenskaper. Uppskattningar används för att köra lokala virtuella datorer som virtuella IaaS-datorer. Azure Migrate Server-utvärderingen beaktar inte PaaS-eller SaaS-kostnader.

De aggregerade lagrings kostnaderna för den utvärderade gruppen delas av olika typer av lagrings diskar. 

### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

Azure Migrate Server Assessment tilldelar en tillförlitlig bedömning till en prestanda baserad utvärdering, från 1 stjärna (lägsta) till 5 stjärnor (högst).

![Säkerhetsomdöme](./media/tutorial-assess-vmware/confidence-rating.png)

Förtroende omdömet hjälper dig att beräkna tillförlitligheten för utvärderingens storleks rekommendationer. Omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen:

**Tillgänglighet för data punkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor

[Lär dig mer om metod tips](best-practices-assessment.md#best-practices-for-confidence-ratings) för Tillförlitlighets klassificering.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapar du en Azure Migrate-apparat. Du har också skapat och granskat en utvärdering.

Om du vill lära dig hur du migrerar virtuella VMware-datorer till Azure med hjälp av Azure Migrate Server-migrering, fortsätter du till den tredje kursen i serien:

> [!div class="nextstepaction"]
> [Migrera virtuella VMware-datorer](./tutorial-migrate-vmware.md)
