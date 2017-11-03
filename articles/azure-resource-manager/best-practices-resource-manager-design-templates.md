---
title: "Utforma Azure-mallar för sammansatta lösningar | Microsoft Docs"
description: "Visar bästa praxis för att utforma Azure Resource Manager-mallar för avancerade scenarier"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ce1141d6-ece7-4976-acea-1db1f775409e
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
ms.openlocfilehash: dcc31f7a8c85a8f7fbd554371a66fb1e348bca17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="design-patterns-for-azure-resource-manager-templates-when-deploying-complex-solutions"></a>Designmönster för Azure Resource Manager-mallar när du distribuerar komplexa lösningar
Med ett flexibelt sätt baserat på Azure Resource Manager-mallar kan distribuera du komplexa topologier snabbt och konsekvent. Du kan anpassa dessa distributioner enkelt som core erbjudanden utvecklas eller för varianter för avvikare scenarier eller kunder.

Det här avsnittet är en del av en större vitbok. Om du vill läsa fullständig dokumentet, hämta [World klassen Azure Resource Manager-mallar överväganden och beprövade metoder](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

Mallar kombinerar fördelarna med underliggande Azure Resource Manager med förmåga och läsbarhet för JavaScript Object Notation (JSON). Mallar kan du:

* Distribuera topologier och deras arbetsbelastning konsekvent.
* Hantera alla dina resurser i ett program tillsammans med resursgrupper.
* Använda rollbaserad åtkomstkontroll (RBAC) för att ge behörighet till användare, grupper och tjänster.
* Använda märkning associationer för att förenkla uppgifter, till exempel fakturering Rollup-beräkningar.

Den här artikeln innehåller information om förbrukningen scenarier, arkitektur och implementering mönster identifieras vid våra design sessioner och verkliga mallen implementeringar med Azure Customer Advisory Team (AzureCAT) kunder. Dessa metoder är är långt från akademisk, beprövade metoder informera utvecklingen av mallar för 12 upp Linux-baserade OSS tekniker, bland annat: Apache Kafka, Apache Spark, Cloudera, Couchbase, Hortonworks HDP, DataStax Enterprise drivs av Apache Cassandra, Elasticsearch, Jenkins, MongoDB, PostgreSQL, Redis och Nagios. 

Den här artikeln delar den här beprövade metoder för att hjälpa dig att skapa world klassen Azure Resource Manager-mallar.  

Vi har hittat flera förbrukning upplevelser för Resource Manager-mall i företag, System dietetisk (SI) s och CSV: er i vårt arbete med kunder. Följande avsnitt innehåller en översikt över vanliga scenarier och mönster för annan kundtyper.

## <a name="enterprises-and-system-integrators"></a>Företag och systemintegrerare.
I stora organisationer ofta visas två konsumenter av Resource Manager-mallar: interna programvaran utvecklingsgrupper och företagets IT. Vi har hittat som scenarier för SIs kartan till scenarier för företag, så gäller samma villkor.

### <a name="internal-software-development-teams"></a>Interna programvaran utvecklingsgrupper
Om din grupp utvecklar programvara för att stödja din verksamhet, tillhandahåller mallar ett enkelt sätt att snabbt distribuera teknik för företag-specifika lösningar. Du kan också använda mallar för att snabbt skapa utbildning miljöer som gör att gruppmedlemmarna att få nödvändiga kunskaper.

Du kan använda mallar som-är eller utöka eller skriva dem för att passa dina behov. Med taggar inom mallar, kan du ange fakturering sammanfattning med olika vyer som team, projekt, individuella och utbildning.

Företag vill ofta programvara utvecklingsgrupper att skapa en mall för konsekvent distribution av en lösning. Mallen underlättar begränsningar så vissa objekt i den miljön som är fasta och inte kan åsidosättas. En bank kan till exempel kräva en mall för att inkludera RBAC så att programmerare inte kan redigera en bank lösning för att skicka data till en personlig storage-konto.

### <a name="corporate-it"></a>Företagets IT
Företagets IT-organisationer använder vanligtvis mallar för att leverera molnet kapaciteten och molnbaserade funktioner.

#### <a name="cloud-capacity"></a>Kapacitet
Det är ett vanligt sätt för företagets IT-grupper ger kapacitet för team med ”t-shirt storlekar”, som är standard erbjudande storlekar, till exempel små, medelstora och stora. Erbjudanden som t-shirt storlek kan blanda olika resurstyper och kvantiteter och ger en nivå av standardisering som gör det möjligt att använda mallar. Mallarna ge kapacitet på ett konsekvent sätt som tillämpar företagsprinciper och använder märkning för att tillhandahålla återbetalning till förbrukar organisationer.

Du kan behöva ange utvecklings-, test- eller produktion miljöer där utvecklingsgrupper programvara kan distribuera lösningar. Miljön har en fördefinierad nätverkstopologi och element som utvecklingsgrupper programvara inte kan ändras, till exempel regler för åtkomst till internet och paket allmänheten. Du kan också ha organisation-specifika rollerna för dessa miljöer med distinkta åtkomstbehörigheter för miljön.

#### <a name="cloud-hosted-capabilities"></a>Molnbaserade funktioner
Du kan använda mallar för att stödja molnbaserade funktioner, inklusive enskilda programvarupaket eller sammansatta erbjudanden som erbjuds i interna rader av företag. Ett exempel på ett erbjudande för sammansatta är analytics as a service – analytics, visualisering och andra tekniker, levereras i en optimerad, anslutna konfiguration på en fördefinierad nätverkstopologi.

Molnbaserade funktioner som påverkas av de överväganden för säkerhet och rollen som upprättas av kapacitet erbjuder på vilket de byggs. Dessa funktioner erbjuds som de är eller som en hanterad tjänst. För rollerna senare, begränsad åtkomst krävs för att aktivera åtkomst till miljön för hanteringsändamål.

## <a name="cloud-service-vendors"></a>Cloud service leverantörer
Kommunicerar med flera CSV: er, identifierat vi flera metoder som du kan använda för att distribuera tjänster för kunder och associerade kraven.

### <a name="csv-hosted-offering"></a>CSV-värdbaserad erbjudande
Om du har ditt erbjudande i din egen Azure-prenumeration två värd sätt är vanliga: distribuera en distinkta distribution för alla kunder eller distribuera skalningsenheter som ligger till grund för en delad infrastruktur som används för alla kunder.

* **Distinkta distributioner för varje kund.** Distinkta distributioner per kund kräver fast topologier för olika kända konfigurationer. Dessa distributioner kan ha storlekar för olika virtuella datorer (VM), varierande antal noder och olika mängder associerad lagring. Taggning av distributioner används för sammanslagning fakturering för varje kund. RBAC kan aktiveras så att kunder tillgång till aspekter av sina molnmiljö.
* **Skalningsenheter i miljöer med delade flera innehavare.** En mall kan representera en skalningsenhet för miljöer med flera innehavare. I det här fallet används samma infrastruktur för att stödja alla kunder. Distributioner representerar en grupp med resurser som levererar en nivå av kapacitet för värdbaserade erbjudandet, till exempel antalet användare och antal transaktioner. Dessa skalenheter ökas eller minskas begäran krävs.

### <a name="csv-offering-injected-into-customer-subscription"></a>CSV-erbjudande injekteras i kundprenumerationen
Du kanske vill distribuera programvaran till prenumerationer som ägs av slutanvändare och kunder. Du kan använda mallar för att distribuera olika distributioner i Azure-konto för en kund.

Dessa distributioner använda RBAC så att du kan uppdatera och hantera distribution inom på kundens konto.

### <a name="azure-marketplace"></a>Azure Marketplace
Du kan utveckla mallar för att leverera olika typer av distributioner som körs i Azure-konto för en kund för att göra sälja dina erbjudanden via en marketplace, till exempel Azure Marketplace. Dessa distinkta distributioner kan beskrivas vanligtvis som en t-shirt storlek (liten, medel, stora), produkt/målgruppen typ (community, developer, enterprise) eller funktionstyp (grundläggande hög tillgänglighet).  I vissa fall kan du ange vissa attribut för att distribuera, till exempel VM eller antalet diskar med dessa typer.

## <a name="oss-projects"></a>OSS projekt
I projekt med öppen källkod, Resource Manager-mallar för att aktivera en grupp som du distribuerar en lösning som snabbt med hjälp av beprövade metoder. Du kan lagra mallar i GitHub-lagringsplatsen så gemenskapen kan ändra dem i framtiden. Användare kan distribuera dessa mallar i sina egna Azure-prenumerationer.

I följande avsnitt visas de saker som du behöver tänka på innan du utformar din lösning.

## <a name="identifying-what-is-outside-and-inside-a-vm"></a>Identifiera vad är utanför och inuti en virtuell dator
När du utformar din mall är det bra att granska kraven vad gäller vad är utanför och på virtuella datorer (VM):

* Utanför: virtuella datorer och andra resurser för din distribution, exempel nätverkstopologi, taggning, refererar till certifikat/hemligheter och rollbaserad åtkomstkontroll. Dessa resurser är en del av din mall.
* I innebär installerad programvara och övergripande tillstånd konfiguration. Andra mekanismer som VM-tillägg eller skript, används helt eller delvis. Dessa mekanismer kan identifieras och utförs av mallen men är inte i den.

Vanliga exempel aktiviteter som du vill göra ”i rutan”-  

* Installera eller ta bort serverroller och funktioner
* Installera och konfigurera programvara på nivån nod eller ett kluster
* Distribuera webbplatser på en webbserver
* Distribuera databasen scheman
* Hantera registret eller andra typer av konfigurationsinställningar
* Hantera filer och kataloger
* Starta, stoppa och hantera processer och tjänster
* Hantera lokala grupper och användarkonton
* Installera och hantera paket (.msi, .exe, yum, etc.)
* Hantera miljövariabler
* Köra interna skript (Windows PowerShell, bash, osv.)

### <a name="desired-state-configuration-dsc"></a>Önskad tillståndskonfiguration DSC)
Tänker det interna tillståndet för dina virtuella datorer efter distributionen, som du vill kontrollera att den här distributionen inte ”driva” från konfigurationen som du har definierat och kontrolleras till källkontroll. Den här metoden gör utvecklarna eller driftspersonal göra inte ad hoc-ändringar i en miljö som inte kontrollerat, testas eller som registrerats i källkontroll. Den här kontrollen är viktig, eftersom de manuella ändringarna inte ingår i källkontroll. De även är inte en del av standarddistributionen och påverkar framtida automatiserad distribution av programvaran.

Önskad tillståndskonfiguration är också viktigt från ett säkerhetsperspektiv utöver din internt anställda. Hackare försöker regelbundet angripa och utnyttja programvarusystem. När lyckas är det vanligt att installera filer och ändra annars avslöjade systemets tillstånd. Med önskad tillståndskonfiguration kan du identifiera går mellan önskade och faktiska tillstånd och Återställ en känd konfiguration.

Det finns resurstillägg för de mest populära mekanismerna för DSC - PowerShell DSC, Chef och Puppet. Var och en av dessa tillägg kan distribuera starttillståndet för den virtuella datorn och också användas för att se till att tillståndet som underhålls.

## <a name="common-template-scopes"></a>Vanliga mallen scope
Vi har sett tre viktiga lösning mallar scope framkommer i vår erfarenhet. Dessa tre scope – kapacitet, kapaciteten och slutpunkt till slutpunkt lösning – beskrivs i följande avsnitt.

### <a name="capacity-scope"></a>Kapacitet omfång
Ett omfång kapacitet ger en uppsättning resurser i en standard-topologi som är förkonfigurerad att är i enlighet med regler och principer. I de flesta vanliga exempel distribuerar en standard utvecklingsmiljö i ett scenario med företagets IT- eller SI.

### <a name="capability-scope"></a>Funktionen omfång
Ett omfång kapaciteten fokuserar på att distribuera och konfigurera en topologi för en viss teknik. Vanliga scenarier inklusive tekniker, till exempel SQL Server, Cassandra, Hadoop.

### <a name="end-to-end-solution-scope"></a>Slutpunkt till slutpunkt lösningen omfattning
En slutpunkt till slutpunkt lösningen omfattning mål utöver en enskild funktion och i stället fokuserar på att leverera en heltäckande lösning som består av flera funktioner.  

En lösning som omfattar mallen ska omfatta visar sig som en uppsättning av en eller flera omfång kapaciteten mallar med lösningen-specifika resurser, logik och tillstånd. Ett exempel på en lösning som omfattar mall är en mall för slutpunkt till slutpunkt data pipeline-lösning. Mallen kan blanda Lösningsspecifika topologi och tillstånd med flera omfång kapaciteten lösningsmallar som Kafka, Storm och Hadoop.

## <a name="choosing-free-form-vs-known-configurations"></a>Att välja Friform kontra kända konfigurationer
Du tror först en mall bör ge konsumenterna största flexibilitet, men många saker påverka valet av om du vill använda Friform konfigurationer kontra kända konfigurationer. Det här avsnittet identifierar viktiga kundens behov och tekniska säkerhetsaspekter som formats metoden delas i det här dokumentet.

### <a name="free-form-configurations"></a>Kostnadsfri-konfigurationer
Yta ljud Friform konfigurationer perfekt. De kan du välja en VM-typ och ange ett godtyckligt antal noder och anslutna diskar för dessa noder, och göra det som parametrar till en mall. Den här metoden är inte idealiskt för vissa scenarier.

I [storlekar för virtuella datorer](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)VM av olika typer och storlekar som kan identifieras och var och en av antalet varaktiga diskar (2, 4, 8, 16 eller 32) som kan bifogas. Varje ansluten disk innehåller 500 IOPS och multiplar av dessa diskar kan vara pooler för en multiplikator som antalet IOPS. Till exempel är 16 diskar poolad för att tillhandahålla 8 000 IOPS. Poolning är klar med konfigurationen i operativsystemet med hjälp av Microsoft Windows lagringsutrymmen eller redundant array of prisvärda diskar (RAID) i Linux.

En konfiguration kan markeringen flera VM-instanser VM av olika typer och storlekar för dessa instanser, olika diskar för den virtuella datorn och en eller flera skript för att konfigurera VM-innehållet.

Det är vanligt att en distribution kan ha flera typer av noder, till exempel master- och datanoder, så den här flexibiliteten ofta har angetts för varje nodtyp.

När du börjar att distribuera kluster alla signifikanta börjar att arbeta med dessa avancerade scenarier. Om du distribuerar ett Hadoop-kluster, till exempel skulle med 8 överordnade noder 200 datanoder och pool 4 anslutna diskar på varje nod och grupperade 16 anslutna diskar per datanod har du 208 virtuella datorer och 3,232 diskar för att hantera.

Storage-konto kommer begränsning begäranden ovan dess identifierade 20 000 transaktioner per sekund begränsa, så du bör titta på lagring konto partitionering och använda beräkningar för att avgöra hur många storage-konton för den här topologin. Beroende på antalet kombinationer som stöds av metoden fritt format, krävs dynamiska beräkningar för att avgöra lämpliga partitionering. Språk för Azure Resource Manager-mallen tillhandahåller för närvarande inte matematiska funktioner, så du måste utföra dessa beräkningar i koden genereras en unik, hårdkodade mall med rätt information.

I företagets IT och SI scenarier någon underhålla mallar och stöd för distribuerade topologier för en eller flera organisationer. Den här ytterligare kostnader – olika konfigurationer och mallar för varje kund, är ett långt önskvärt.

Du kan använda mallarna för att distribuera miljöer i kundens Azure-prenumeration, men både företagets IT-team och CSV: er vanligtvis distribuera dem till sina egna prenumerationer funktionen återbetalning till sina kunder. I dessa scenarier målet är att distribuera kapacitet för flera kunder i en pool av prenumerationer och hålla distributioner tätt fyllts i prenumerationer att minimera prenumeration svällande – det vill säga flera prenumerationer att hantera. Med dynamisk verkligen distributionsstorlekarna kan uppnå den här typen av densitet krävs noggrann planering och ytterligare utveckling för scaffold-teknik för organisationen.

Dessutom kan inte skapa prenumerationer via ett API-anrop men måste göra det manuellt via portalen. När antalet prenumerationer ökar alla resulterande prenumeration svällande kräver mänsklig inblandning – kan automatiseras. Med så mycket variationer i storlek på distributioner skulle du behöva etablera inför ett antal prenumerationer manuellt för att se till att det finns prenumerationer.

Med tanke på sådana faktorer är en helt fritt format konfiguration mindre tilltalande än Min första tanke.

### <a name="known-configurations--the-t-shirt-sizing-approach"></a>Kända konfigurationer – t-shirt storlek metod
Snarare än erbjuder en mall som ger totalt flexibilitet och oräkneliga variationer i vår erfarenhet vanliga mönstret är att ge möjlighet att välja kända konfigurationer – i praktiken standard t-shirt storlekar, till exempel sandbox små, medelstora och stora. Andra exempel på t-shirt storlekar är produkterbjudanden, till exempel community edition eller enterprise edition.  I andra fall kan det vara belastningsspecifikt konfigurationer av en teknik – som kartan minska eller utan sql.

Många företagets IT organisationer, OSS leverantörer och SIs gör sina erbjudanden just nu på detta sätt i lokal virtualiserade miljöer (företag) eller programvara som en tjänst (SaaS)-erbjudanden (CSV: er och OSVs).

Den här metoden ger bra, kända konfigurationer i olika storlekar förkonfigurerade för kunder. Utan kända konfigurationer måste slutkunder fastställa klustret storleken på egen hand, factor plattform resurs begränsningar och beräkna för att identifiera den resulterande partitionering av lagringskonton och andra resurser (på grund av klustrets storlek och resurs begränsningar). Kända konfigurationer möjligt för kunder att enkelt välja rätt t-shirt storlek – det vill säga en viss distribution. Förutom att göra en bättre upplevelse för kunden, ett litet antal kända konfigurationer är enklare att stödja och kan hjälpa dig att leverera en högre säkerhetsnivå för densitet.

En metod för kända konfiguration som fokuserar på t-shirt storlekar kan också ha olika antal noder i en storlek. Exempelvis kanske liten t-shirt mellan 3 och 10 noder.  T-shirt storlek är tänkt att hantera upp till 10 noder och ger klienten möjlighet att välja Friform upp till den maximala storleken som identifieras.  

En t-shirt baserat på arbetsbelastning, kan vara mer Friform till sin natur som antalet noder som kan distribueras, men har distinkta nod arbetsbelastningsstorlek och konfigurationen av programvaran på noden.

T-shirt storlek baserat på produkterbjudanden, t.ex. community- eller Enterprise, kan ha olika resurstyper och maximalt antal noder som kan distribueras, vanligtvis kopplat till licensiering överväganden eller funktionstillgänglighet över olika erbjudanden.

Du kan också anpassa kunder med unika varianter med hjälp av JSON-baserade mallar. När du hanterar avvikare, kan du införliva lämplig planering och överväganden för utveckling, support och kostnadshantering.

Utifrån kunden mallen förbrukning scenarier och krav som anges i början av det här dokumentet kan identifiera vi ett mönster för mallen uppdelning.

## <a name="capacity-and-capability-scoped-solution-templates"></a>Kapacitet och kapaciteten omfång lösningsmallar
Uppdelning ger en modulär metod för mallar som stöder återanvända utökningsbarhet, testning och tooling. Det här avsnittet innehåller information om hur en uppdelning metod kan tillämpas på mallar med en kapacitet omfattning.

I den här metoden en Huvudmall tar emot parametervärden från en mall konsument och länkar till flera typer av mallar och skript nedströms enligt nedan. Parametrar, statiska variabler och genererade variabler används för att ange värden till och från länkade mallar.

![Mallparametrar](./media/best-practices-resource-manager-design-templates/template-parameters.png)

**Parametrar som skickas till en Huvudmall sedan till länkade mallar**

I följande avsnitt fokusera på typerna av mallar och skript som en mall delas i. Avsnitten finns metoder för att skicka statusinformation bland mallarna. Tillsammans med exempel beskrivs varje mall och skript-typer i avbildningen. Kontextuella exempel finns i ”sätta ihop: ett exempel på implementering” senare i det här dokumentet.

### <a name="template-metadata"></a>Mallens metadata
Mallens metadata (metadata.json-fil) innehåller nyckel/värde-par som beskriver en mall i JSON som kan läsas av människor och programvarusystem.

![Mallens metadata](./media/best-practices-resource-manager-design-templates/template-metadata.png)

**Mallens metadata beskrivs i filen metadata.json**

Programvaruagenter kan hämta filen metadata.json och publicera informationen och en länk till mallen i en webbsida eller katalog. Delar är *itemDisplayName*, *beskrivning*, *sammanfattning*, *githubUsername*, och *dateUpdated*.

Nedan visas en exempelfil i sin helhet.

    {
        "itemDisplayName": "PostgreSQL 9.3 on Ubuntu VMs",
        "description": "This template creates a PostgreSQL streaming-replication between a master and one or more slave servers each with 2 striped data disks. The database servers are deployed into a private-only subnet with one publicly accessible jumpbox VM in a DMZ subnet with public IP.",
        "summary": "PostgreSQL stream-replication with multiple slave servers and a publicly accessible jumpbox VM",
        "githubUsername": "arsenvlad",
        "dateUpdated": "2015-04-24"
    }

### <a name="main-template"></a>Huvudmall
Den huvudsakliga mallen tar emot parametrar från en användare använder denna information för att fylla i komplexa objektvariabler och kör länkade mallar.

![Huvudmall](./media/best-practices-resource-manager-design-templates/main-template.png)

**Den huvudsakliga mallen tar emot parametrar från en användare**

En parameter som har angetts är kända konfigurationstyp kallas även t-shirt Storleksparametern på grund av dess standardiserade värden, till exempel små, medel eller stor. Du kan använda den här parametern på flera olika sätt i praktiken. Mer information finns i ”kända configuration resurser mall” senare i det här dokumentet.

Vissa resurser har distribuerats oavsett kända konfiguration som anges av en användare-parameter. Dessa resurser etableras med en enda delad resurs-mall och delas med andra mallar så att den delade resurs mallen körs första.

Vissa resurser har distribuerats eventuellt oavsett angivna kända konfiguration.

### <a name="shared-resources-template"></a>Mall för delade resurser
Den här mallen innehåller resurser som är gemensamma för alla kända konfigurationer. Den innehåller det virtuella nätverket, tillgänglighetsuppsättningar och andra resurser som krävs oavsett mallen kända konfiguration som har distribuerats.

![För Mallresurser](./media/best-practices-resource-manager-design-templates/template-resources.png)

**Mall för delade resurser**

Resursnamn, till exempel det virtuella nätverksnamnet baseras på den huvudsakliga mallen. Du kan ange dem som en variabel i mallen eller tas emot som en parameter från användaren, som krävs av din organisation.

### <a name="optional-resources-template"></a>Valfria resurser mall
Mallen valfria resurser innehåller resurser som distribueras via programmering baserat på värdet för en parameter eller variabel.

![Valfria resurser](./media/best-practices-resource-manager-design-templates/optional-resources.png)

**Valfria resurser mall**

Du kan till exempel använda en mall för valfria resurser för att konfigurera en jumpbox som aktiverar indirekt åtkomst till en distribuerad miljö från Internet. Du skulle använda en parameter eller variabel för att identifiera om jumpbox ska aktiveras och *concat* funktion för att skapa målnamn för mallen som *jumpbox_enabled.json*. Länka mallen använder variabeln resulterande för att installera jumpbox.

Du kan länka mallen valfria resurser från flera platser:

* När det gäller för varje distribution, skapar du en länk med parametern-driven med mallen delade resurser.
* När det gäller att välja kända konfigurationer, till exempel bara installera på stora distributioner – skapa en länk som drivs av parametern eller variabeln-driven från mallen kända konfiguration.

Om en viss resurs är valfritt kan inte styrs av konsumenten mall utan mallprovidern. Du kan till exempel måste uppfylla en viss produkt krav eller produkt-tillägg (common för CSV: er) eller kan tillämpa principer (common för SIs och företagets IT grupper). I dessa fall kan använda du en variabel för att identifiera om resursen ska distribueras.

### <a name="known-configuration-resources-template"></a>Konfigurationen för kända resurser mall
I den huvudsakliga mallen kan en parameter exponeras för att tillåta konsumenten att ange en önskad kända konfiguration för att distribuera mallen. Den här kända konfigurationen använder ofta en t-shirt storlek metod med en uppsättning fast configuration storlekar, till exempel sandbox, small, medium, och stora.

![Konfigurationen för kända resurser](./media/best-practices-resource-manager-design-templates/known-config.png)

**Konfigurationen för kända resurser mall**

Metoden t-shirt storlek är vanligt, men parametrarna kan representera en uppsättning kända konfigurationer. Du kan till exempel ange en uppsättning miljöer för ett företagsprogram som utveckling, testa och produkt. Eller så kan du använda den för en molnbaserad tjänst som representerar olika skalenheter, produktversioner eller produktkonfigurationer, till exempel Community, Developer eller Enterprise.

Precis som med mallen delad resurs skickas variabler till mallen kända konfigurationer från antingen:

* En användare – det vill säga de parametrar som skickas till den huvudsakliga mallen.
* En organisation, det vill säga variablerna i huvudsakliga mallen som representerar interna krav eller principer.

### <a name="member-resources-template"></a>Medlemmen resurser mall
I en konfiguration för kända ingår ofta en eller flera noder medlemstyper. Till exempel med Hadoop har du överordnade noder och datanoder. Om du installerar MongoDB, har du datanoder och ett avgörandet. Om du distribuerar DataStax har datanoder och en virtuell dator med OpsCenter installerad.

![Medlemmar resurser](./media/best-practices-resource-manager-design-templates/member-resources.png)

**Medlemmen resurser mall**

Varje typ av noder kan ha olika storlekar för virtuella datorer, antal anslutna diskar, skript för att installera och konfigurera noder, portkonfigurationer för virtuella datorer, antal instanser och annan information. Så att varje nodtyp hämtar sin egen medlem resurs mallen, som innehåller information om hur du distribuerar och konfigurerar en infrastruktur som skript körs för att distribuera och konfigurera programvara på den virtuella datorn.

För virtuella datorer, vanligtvis är två typer av skript används ofta återanvändbara och anpassade skript.

### <a name="widely-reusable-scripts"></a>Allmänt återanvändbara skript
Allmänt återanvändbara skript kan användas för flera typer av mallar. En av dessa ofta återanvändbara skript bättre exempel ordnar RAID på Linux att poolen diskar och få ett större antal IOPS. Oavsett programvaran installeras på den virtuella datorn, ger det här skriptet återanvändning av beprövade metoder för vanliga scenarier.

![Återanvändbara skript](./media/best-practices-resource-manager-design-templates/reusable-scripts.png)

**Medlemmen resurser mallar kan anropa ofta återanvändbara skript**

### <a name="custom-scripts"></a>Anpassade skript
Mallar anropa ofta en eller flera skript som installerar och konfigurerar programvara på virtuella datorer. Ett vanligt mönster visas med stora topologier där flera instanser av en eller flera medlemstyper har distribuerats. Ett installationsskript startas för varje virtuell dator som kan köras parallellt, följt av ett installationsskript som anropas efter att alla virtuella datorer (eller alla virtuella datorer i en given Medlemstyp) har distribuerats.

![Anpassade skript](./media/best-practices-resource-manager-design-templates/custom-scripts.png)

**Medlemmen resurser mallar kan anropa skript för ett visst syfte, till exempel VM-konfiguration**

## <a name="capability-scoped-solution-template-example---redis"></a>Funktionen omfång lösning mallen exempel – Redis
Om du vill visa hur en implementering fungerar känna till hur ett praktiskt exempel för att skapa en mall som underlättar distributionen och konfigurationen av Redis i standard t-shirt storlekar.  

För att distribuera finns det en uppsättning delade resurser (virtuella nätverk, storage-konto, tillgänglighetsuppsättningar) och en valfri resurs (jumpbox). Det finns flera kända konfigurationer som t-shirt storlekar (liten, medel, stora) men med en nod skriva. Det finns även två specifika ändamål skript (installation, konfiguration).

### <a name="creating-the-template-files"></a>Skapa mallfiler
Du skapar en Main-mall med namnet azuredeploy.json.

Du skapar den delade resurser mallen med namnet delade resources.json

Du skapar en valfri resurs-mall om du vill aktivera distributionen av en jumpbox med namnet jumpbox_enabled.json

Redis använder bara en enskild nodtyp, så du skapar en enda medlem resursmall med namnet nod resources.json.

Med Redis, som du vill installera varje enskild nod och sedan konfigurera klustret.  Du har skript för att hantera installationen och ställa in, redis-kluster-install.sh och redis-kluster-setup.sh.

### <a name="linking-the-templates"></a>Länka mallar
Med hjälp av mallen länka Huvudmall länkar ut till delade resurser-mall som etablerar det virtuella nätverket.

Logik läggs till i den huvudsakliga mallen som ska aktivera konsumenter av mallen att ange om en jumpbox ska distribueras. En *aktiverat* värde för den *EnableJumpbox* parametern anger att kunden vill distribuera en jumpbox. Om det här värdet anges mallen sammanfogar *_enabled* som suffix till ett grundläggande Mallnamn för jumpbox-funktionen.

Den huvudsakliga mallen gäller den *stora* parametern värdet som suffix till ett grundläggande Mallnamn för t-shirt storlekar och använder sedan detta värde i en mall länk ut till *technology_on_os_large.json*.

Topologin liknar den här bilden.

![Redis-mall](./media/best-practices-resource-manager-design-templates/redis-template.png)

**Mallstruktur för ett Redis-mall**

### <a name="configuring-state"></a>Konfigurera tillstånd
Det finns två steg för att konfigurera tillståndet för noder i klustret, både representeras av syfte specifika skript.  ”redis-kluster-install.sh” installerar Redis och ”redis-kluster-setup.sh” ställer in klustret.

### <a name="supporting-different-size-deployments"></a>Stöder olika storlekar distributioner
Inuti variabler, t-shirt storlek mallen anger antalet noder för varje typ av att distribuera för den angivna storleken (*stora*). Distribuerar sedan det antalet VM-instanser som använder resurs slingor, vilket ger ett unikt namn för resurser genom att lägga till ett nodnamn med ett numeriskt sekvensnummer från *copyIndex()*. Det gör de här stegen för både varm och Varm zonen virtuella datorer, som definieras i mallen t-shirt namn

## <a name="decomposition-and-end-to-end-solution-scoped-templates"></a>Lösning för slutpunkt till slutpunkt och uppdelning omfång mallar
En lösningsmall med en omfattning för slutpunkt till slutpunkt lösningen fokuserar på att leverera en slutpunkt till slutpunkt-lösning.  Den här metoden är vanligtvis en sammansättning av flera omfång kapaciteten mallar med ytterligare resurser, logik och tillstånd.

Som i bilden nedan utökas samma modell används för funktionen omfång mallar för mallar med en omfattning för slutpunkt till slutpunkt-lösning.

En mall för delade resurser och valfria resurser mallar har samma funktion som kapaciteten och kapaciteten omfång mallen metoder, men omfattar för slutpunkt till slutpunkt-lösningen.

Heltäckande lösning omfång mallar kan också vanligtvis ha t-shirt storlekar, fungerande konfiguration resurser mallen visar vad som krävs för en viss kända konfiguration av lösningen.

Kända Configuration resurser mallen länkar till kapaciteten för en eller flera omfång lösningsmallar som är relevanta för slutpunkt till slutpunkt-lösningen samt medlem resurs mallar som krävs för slutpunkt till slutpunkt-lösning.

Eftersom lösningen t-shirt storlek är annorlunda än mallen enskilda kapaciteten omfång, används variabler i mallen fungerande konfiguration resurser för att ange lämpliga värden för underordnade kapaciteten omfång lösningsmallar till distribuera t-shirt rätt storlek.

![Slutpunkt till slutpunkt](./media/best-practices-resource-manager-design-templates/end-to-end.png)

**Den modell som används för kapacitet omfång lösning mallar lätt kan utökas för slutpunkt till slutpunkt lösning mallen scope**

## <a name="preparing-templates-for-the-marketplace"></a>Förbereda mallar för Marketplace
Den föregående metoden hanterar lätt scenarier där företag, SIs och CSV: er vill distribuera mallar sig själva eller aktivera sina kunder att distribuera på egen hand.

En annan önskade scenariot distribuerar en mall via marketplace.  Den här metoden uppdelning fungerar på Marketplace, med några mindre ändringar.

Som tidigare nämnts kan mallar användas för att erbjuda olika distributionstyper för försäljning i marketplace. Distinkta distributionstyper kanske t-shirt storlekar (liten, medel, stora), produkt/målgruppen typ (community, developer, enterprise) eller funktionstyp (grundläggande hög tillgänglighet).

Enligt nedan kan befintliga lösning för slutpunkt till slutpunkt eller kapaciteten omfång mallar lätt användas för att visa en lista med kända konfigurationerna i marketplace.

Parametrarna för den huvudsakliga mallen först ändras för att ta bort den ingående parameter med namnet tshirtSize.

Även om de olika distributionstyperna mappas till mallen fungerande konfiguration resurser, behöver de även vanliga resurser och konfiguration finns i mallen för delade resurser och potentiellt dem i valfri resurs mallar.

Om du vill publicera din mall på marketplace upprätta olika kopior av mallen Main som ersätter den tidigare tillgängliga inkommande parametern för tshirtSize till en variabel som är inbäddad i mallen.

![Marketplace](./media/best-practices-resource-manager-design-templates/marketplace.png)

**Anpassning av en lösning omfång mall för marketplace**

## <a name="next-steps"></a>Nästa steg
* Läs om hur du delar tillstånd till och från mallar i [dela tillstånd i Azure Resource Manager-mallar](best-practices-resource-manager-state.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

