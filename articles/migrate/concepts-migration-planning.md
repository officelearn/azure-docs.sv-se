---
title: Bygg migrerings plan med Azure Migrate | Microsoft Docs
description: Ger vägledning om hur du skapar migrerings plan med Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: ce6f0ff97b1dfa8757b05b13dff653a1d34f4531
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92315296"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Skapa migreringsplan med Azure Migrate

Den här artikeln innehåller en snabb guide som hjälper dig att bygga din migrerings plan till Azure med [Azure Migrate](migrate-services-overview.md).Om du har andra frågor kontrollerar du följande resurser:

- [Allmänna frågor](resources-faq.md) om Azure Migrate
- Frågor om [Azure Migrate-enheten](common-questions-appliance.md)
- Frågor om [Server migrering](common-questions-server-migration.md)
- Få svar på frågor i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum)

## <a name="define-the-goals-of-cloud-migration"></a>Definiera målen för molnbaserad migrering

Innan du skapar en migreringstabell är det viktigt att förstå och utvärdera [motivationen](/azure/cloud-adoption-framework/strategy/motivations) att gå över till molnet som kan hjälpa till att producera fler lyckade affärs resultat. Som [moln implementerings ramverk för Azure](/azure/cloud-adoption-framework) förklarar det att det finns olika utlösare och olika metoder för migrering som passar din verksamhet:  

**Kritiska affärs händelser** | **Migrerings resultat**
--- | ---
Avsluta data Center | Kostnadsbesparingar
Sammanslagning, förvärv eller Divestiture | Minskning av leverantör eller teknisk komplexitet
Minskning av kapital kostnader | Optimering av interna åtgärder
Slut på support för verksamhets kritiska tekniker | Öka flexibiliteten i verksamheten
Svar på ändringar i regelefterlevnad | Förberedelser för nya tekniska funktioner
Nya krav för data suveränitet | Skalning för att uppfylla kraven på marknaden
Nedsättning av avbrott och förbättringar av IT-stabiliteten | Skalning för att uppfylla geografiska krav

Ditt motivation i migreringen kan också hjälpa dig att avspegla de strategiska målen och de resultat som du vill uppnå genom att migrera till Azure. Nästa steg är att identifiera och planera en sökväg för migrering till Azure som är anpassat för dina arbets belastningar. Azure Migrate: Server bedömnings verktyget hjälper dig att utvärdera lokala arbets belastningar och ger vägledning och verktyg som hjälper dig att migrera.

## <a name="understand-your-digital-estate"></a>Förstå din digitala egendom

Börja med att förstå din lokala infrastruktur, program och beroenden för att identifiera arbets belastningar som du vill migrera till Azure och få optimerade kostnads projektioner. Verktyget Server utvärdering hjälper dig att besvara följande frågor:

### <a name="what-workloads-are-in-use"></a>Vilka arbets belastningar används?

Använd den lätta Azure Migrate-enheten för att utföra en agent lös identifiering av dina lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar. Kontinuerlig identifiering samlar in dator konfiguration och prestanda-metadata och kan även användas för att hämta inventeringen av installerade program och roller/funktioner som körs på dina lokala datorer. Azure Migrates apparaten samlar in:

- Information om metadata för datorer, diskar och nätverkskort

- Installerade program inklusive program och roller/funktioner  

- Prestanda data inklusive processor-och minnes användning, disk-IOPS och data flöde

Exportera sedan program inventerings listan för att ta reda på alla SQL Server instanser som körs på dina arbets belastningar och Använd verktyget Azure Migrate: databas utvärdering för att förstå deras beredskap.

 ![Program inventering på Portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Export av program inventering](./media/concepts-migration-planning/application-inventory-export.png)

Tillsammans med identifierings data från Server bedömnings verktyget, använder du dina befintliga CMDB-data för att bygga vår vy över din server och din databas egendom och förstå Server distribution över affär senheter, program ägare, geografiska områden osv., som kan i sin tur hjälpa till att prioritera de arbets belastningar som ska migreras.

### <a name="what-dependencies-exist-between-workloads"></a>Vilka beroenden finns mellan arbets belastningar?

När du har identifierat dina servrar använder du en agent lös beroende mappning för att visualisera och identifiera beroenden mellan servrar och optimerings strategier för att flytta beroende servrar till Azure. Visualiseringen hjälper till att förstå om vissa datorer används eller om de kan tas ur bruk i stället för att migreras.  Se till att du analyserar beroenden för att se till att inget är kvar bakom och Undvik oväntade avbrott under migreringen. När du har utfört program inventering och beroende mappning kan du skapa grupper med hög exakthet och börja utvärdera dina servrar.

 ![Beroendemappning](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>Är de optimerade och rätt storlek?

Eftersom Azure ger flexibilitet att ändra storlek på moln kapaciteten med tiden, är migrering en möjlighet att optimera de processor-och minnes resurser som allokeras till dina servrar. Skapa en utvärdering på den grupp som har identifierats innan du förstår arbets Belastningens prestanda historik, vilket kommer att vara mycket viktigt i rightsd VM SKU: er och disk rekommendationer på Azure.

## <a name="assess-your-readiness-for-migration"></a>Utvärdera din beredskap för migrering

### <a name="readiness-and-suitability-analysis-for-azure"></a>Beredskap och lämplighet analys för Azure
Exportera utvärderings rapporten för Azure VM och filtrera efter följande beredskaps kategorier för att förstå VM-beredskap för Azure:

- **Redo för Azure**: du kan migrera de här datorerna som de är till Azure utan några ändringar  

- **Villkorligt redo för Azure**: du kan migrera de här datorerna till Azure, men du behöver mindre ändringar på dessa servrar enligt den reparations vägledning som finns i utvärderingen

- **Inte redo för Azure**: du kan inte migrera de här datorerna till Azure som de är och behöver åtgärda problemen enligt reparations vägledningen före migreringen

- **Beredskap okänd**: Azure Migrate kan inte fastställa datorns beredskap på grund av otillräckliga metadata

Med hjälp av databas utvärderingen kan du utvärdera beredskap för att migrera SQL Server datafastighet till Azure SQL Database eller Azure SQL-hanterade instanser. Du kan se status procent för migrations beredskap för var och en av SQL Server-instanserna. För var och en av instanserna kan du också se det rekommenderade målet i Azure, potentiella migreringsåtgärder, bryta antalet ändringar, beredskap för Azure SQL DB/Azure SQL VM och kompatibilitetsnivå. Du kan gå djupare och lär dig hur du kan ta reda på hur migrations spärrarna och rekommendationerna fungerar.

 ![Databas utvärderingar](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Storleks rekommendationer

När datorn har marker ATS som klar för Azure, gör Server utvärderingen det möjligt att identifiera den virtuella Azure-datorn och disk-SKU: n för dina virtuella datorer. Du kan välja att Visa storleks rekommendationen baserat på prestanda historiken (så att du optimerar resurser när du migrerar) eller utifrån den lokala konfigurationen utan att beakta prestanda historiken. För-databaser kan du se rekommendationer kring databas-SKU, pris nivå och beräknings nivå i din databas utvärdering.  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>Beräknings bedömningar för att få en uppskattad kostnad för att köra arbets belastningarna i Azure

Med det *prestandabaserade* alternativet för storleks ändring i utvärderingarna kan du optimera arbets belastningar för Azure. Förutom att göra det finns det några andra vägar som gör det enklare att spara pengar:

- **Reserverade instanser**: med reserverade instanser kan du avsevärt minska kostnaderna jämfört med priset enligt principen betala per användning med 1-eller tre års villkor för Windows och Linux (VM: ar)

- **Azure Hybrid-förmåner**: du kan ta med lokala Windows Server-licenser med Software Assurance till Azure och kombinera dem med alternativ för reserverade instanser

- **Enterprise-avtal erbjudande (EA)**: Enterprise-avtal erbjuder inbyggda besparingar som gäller din prenumeration

- **Erbjudanden**: det finns flera Azure-erbjudanden, till exempel Dev/Test – betala per användning och Enterprise dev/test som tillhandahåller lägre priser för utveckling eller testning av virtuella datorer

- **VM-drift tid**: du kan ange varaktigheten i dagar per månad och timmar per dag när de virtuella Azure-datorerna ska köras för att minska kostnaderna (gäller inte för RI)

- **Mål region**: du kan skapa flera utvärderingar i olika regioner för att jämföra om migrering till en viss region i en geografi kan vara mer kostnads effektivt

- **Prestandabaserade rekommendationer**: bästa praxis är att försöka använda de rekommendationer som hjälper dig att spara moln kostnader

### <a name="visualize-data"></a>Visualisera data

Du kan se server utvärderings rapporten med Azure readiness och kostnad för månatlig distribution i portalen samt exportera utvärderingen för att lägga till fler visualiseringar på identifierings-och utvärderings data för att göra migrerings planen bättre. Du kan skapa flera utvärderingar för olika kombinationer av egenskaper och välja den uppsättning egenskaper som passar bäst för ditt företag.  

 ![Översikt över utvärderingar](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>Utvärdera luckor och potentiella blockerare

När du bestämmer vilka program och underliggande infrastruktur som ska migreras kan du identifiera nedtidnas begränsningar för dessa program och leta efter eventuella drift beroenden mellan dina program och den underliggande infrastrukturen. Den här analysen kan hjälpa dig att planera migreringar som uppfyller ditt mål för återställnings tid (RTO) och garanterar minimal data förlust. Innan du migrerar rekommenderar vi att du granskar och minimerar kompatibilitetsproblem eller funktioner som inte stöds och som kan blockera migrering av servrar och SQL-databaser med hjälp av Server utvärderings rapporten och Azure Migrate: rekommendationer för databas bedömning.

### <a name="first-workloads-to-target-and-approach"></a>De första arbets belastningarna till målet och metoden

Nu när du har all viktig information för att fatta ditt beslut om migrering bör du prioritera vilka program och arbets belastningar som ska migreras först. Utveckla en metod för att använda "tillämpa och lär" för att migrera dina avsedda program på ett systematiskt och kontrollerbar sätt så att du kan stryka bort eventuella fel i den här strategin innan du påbörjar en fullständig migrering. Du kan också använda strategiska faktorer, till exempel komplexitet och tid för migrering, angelägenhets grad, produktion/inte produktions miljö, efterlevnad och säkerhets krav, program kunskaper osv. för att prioritera vilka program grupper som ska migreras.

Några rekommenderade strategier för migrering är:

- **Prioritera din snabb WINS**: du kan använda utvärderings rapporterna för att identifiera låg hängande frukt, inklusive servrar och databaser som är helt klara och som kräver minimal ansträngning för att migrera till Azure:
    - Azure-klar: exportera utvärderings rapporten och filtrera alla datorer som är klara för Azure. Det kan vara din första grupp av datorer som du kan lyfta och byta med Azure Migrate: Migreringsverktyg för Server.
    - Support för OS-slut: exportera utvärderings rapporten och filtrera alla datorer som kör operativ systemen Windows Server 2008 och Windows Server 2008 R2. Dessa SKU: er har slut på support och bara Azure ger dig kostnads fria tre års säkerhets uppdateringar när du migrerar dem till Azure. När du kombinerar, Azure Hybrid-förmån och använder reserverade instanser kan det vara mycket högre att spara.
    - SQL Server migrering: Använd rekommendationer för databas bedömning för att migrera databaserna som är klara för Azure SQL-databaser med hjälp av Azure Migrate: databas migrering och databaserna som är klara för virtuella Azure SQL-datorer med hjälp av Azure Migrate: Server-migrering.
    - Support för program vara: exportera program varu inventeringen och filtrera alla program/tillägg som kan nå slutet av supporten. Du bör prioritera dessa program.
    - Överallokerade virtuella datorer: exportera utvärderings rapporten och filtrera ut datorer med låg processor användning (%) och minnes användning (%).  Du kan använda den här möjligheten för att migrera till en beställd virtuell dator i Azure och spara det du har betalat för underutnyttjade resurser.
    - Kapacitets begränsningar: exportera utvärderings rapporten och filtrera ut datorer med hög processor användning (%) och minnes användning (%).  Du kan förhindra att de överbelastade virtuella datorerna bryter ned och ökar prestanda genom att migrera dem till Azure och använda funktionen för automatisk skalning för att möta efter frågan. Du kan också titta på utvärderings rapporten för att förstå dina lagrings begränsningar genom att analysera diskens IOPS och data flöde och hitta den rekommenderade disk typen som passar dina behov bäst.

- **Starta små och gå sedan till stor**: börja med att flytta program och arbets belastningar som är minimala och mindre komplexa, för att skapa förtroende i din migrerings strategi. Du kan också överlappa dina rekommendationer för Azure Migrate bedömning med organisationens CMDB-lagringsplats för att hitta och migrera arbets belastningar för utveckling/testning av miljö i dina pilot-migreringar. De lär sig från dessa piloter kan användas för att migrera produktions arbets belastningar.  

- **Följ kraven för din lagstiftning eller bransch**: Azure underhåller den största portföljen för regelefterlevnad i branschen, med avseende på bredd och djup för erbjudanden. Använd detta som en affärs möjlighet för att prioritera dina migreringar till Azure och följa dina nationella, regionala och branschspecifika standarder och lagar. Detta gäller särskilt för organisationer som hanterar affärs kritiska eller innehåller känslig information eller som är i kraftigt reglerade branscher, där standarderna och reglerna Abound och i vissa fall kan ändras ofta, vilket gör det svårt att fortsätta med.  

## <a name="finalize-the-migration-planandprepare-formigration"></a>Slutför migrations planen och Förbered för migrering

Innan du Slutför migreringen bör du se till att dessa överväganden inte spelar hinder för migreringen:

- Utvärdera begränsningar av nätverks bandbredd och latens, vilket kan orsaka oförutsedda fördröjningar och störa replikeringens hastighet för migrering.

- Buffert i tid för att utföra testning av prestanda och användar godkännande på migrerade program eller köra eventuella appar efter migrering, till exempel uppdatering av databas anslutnings strängar och webb Server konfiguration, utföra start punkt och rensning osv.

- Granska de rekommenderade Azure-behörigheterna och dina Server-/databas åtkomst roller och behörighets modell som krävs för migreringen.

- Förbered din organisation och säkerställ personal anpassningen till den digitala omvandlingen. En solid Training Foundation är viktig för lyckad organisations ändring. Ta en titt på den kostnads fria utbildningen som finns på [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), inklusive kurser om Azure fundament ALS, lösnings arkitektur och säkerhet. Uppmuntra ditt team att utforska [Azure-certifiering](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)   också.  

- Få support för din implementering om det behövs. Många organisationer väljer utanför hjälpen för att stödja sin migrering av molnet. För att snabbt och säkert flytta till Azure med personligt anpassad hjälp bör du överväga en [Azure expert-hanterad tjänst leverantör](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   eller [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  

Skapa en effektiv molnbaserad plan för molnet som innehåller detaljerad information om listan/grupperna med program som du vill migrera, dina program-och databas tillgänglighets-och drift stopps begränsningar och önskade migrerings mil stolpar. Din migrerings plan bör också ta hänsyn till hur lång tid data kopian tar och innehåller en considerate-buffert för testning och start punkt-aktiviteter efter migreringen. Testet efter migrering måste omfatta användnings fall för funktioner, integrering, säkerhet och prestanda för att säkerställa att de migrerade programmen fungerar som förväntat och att alla databas objekt och data relationer har överförts till molnet.  

Använd den här analysen för att bygga en översikt över migreringen och deklarera en underhålls period för att migrera dina program och databaser med minimal till noll stillestånds tid och begränsa den potentiella driften/verksamhets påverkan under migreringen.  

Vi rekommenderar att du alltid testar och fortsätter att använda Azure Migrates funktioner för att *testa migreringen* innan du påbörjar storskalig migrering till Azure. Den här verkliga informationen hjälper dig att uppskatta den faktiska tiden och göra nödvändiga ändringar i migrerings planen. Med testmigreringen får du också möjlighet att identifiera eventuella problem med migreringen och åtgärda dem innan den faktiska migreringen äger rum.  

När du är redo att migrera använder du Azure Migrate Migreringsverktyg för *Server* och Azure Migrate *data migration service* för en sömlös och integrerad migrering med slut punkt till slut punkts spårning. Migreringsverktyg för Server stöder migrering av virtuella datorer och servrar som finns lokalt i kunders data Center eller något annat privat eller offentligt moln, inklusive AWS, GCP, osv. Azure Database Migration Service är en fullständigt hanterad tjänst som är utformad för att möjliggöra sömlös migrering från flera databas källor till Azure-dataplattformar med minimal stillestånds tid.  

> [!NOTE]
> För virtuella VMware-datorer använder Server utvärderingen det operativ system som har angetts för den virtuella datorn i vCenter Server för att hantera gäst operativ system analys. För virtuella Linux-datorer som körs på VMware kan för närvarande inte identifiera den exakta kernel-versionen av gäst operativ systemet.

## <a name="next-steps"></a>Nästa steg

- Undersök [resan för migrering i molnet](/azure/architecture/cloud-adoption/getting-started/migrate)   i Azure Cloud adoption Framework.
- [Kom igång](https://youtu.be/wFfq3YPxYHE) med Azure Migrate.
- Skapa en utvärdering för virtuella [VMware-datorer](./tutorial-assess-vmware-azure-vm.md) eller [virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md).