---
title: "Bästa praxis för företag som flyttar till Azure | Microsoft Docs"
description: "Beskriver en kodskelett företag kan använda för att säkerställa en säker och hanterbar miljö."
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: 8692f37e-4d33-4100-b472-a8da37ce628f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: rodend;karlku;tomfitz
ms.openlocfilehash: 3a19f2cf7566f38f80639d7c966638a3ec900cf4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Azure enterprise kodskelett - normativ prenumeration styrning
Företag vidtar allt det offentliga molnet dess rörlighet och flexibilitet. De använder Molnets styrkor för att generera intäkter eller optimera resurser för företaget. Microsoft Azure tillhandahåller många olika tjänster att företag kan sätta ihop som byggstenar för att adressera en mängd olika arbetsbelastningar och program. 

Men om du vet var du ska börja är ofta svårt. När du har bestämt att använda Azure uppstå några frågor ofta:

* ”Hur jag uppfylla våra juridiska krav för data suveränitet i vissa länder”?
* ”Hur jag säkerställa att någon inte oavsiktligt ändrar kritiska system”?
* ”Hur vet jag vad varje resurs stöder så jag kan kontot för den och faktura tillbaka korrekt”?

Potentiella för en tom prenumerationen med ingen guard spår är avskräckande. Den här tomt utrymme kan hindra övergången till Azure.

Den här artikeln ger en startpunkt för tekniker att behovet av styrning och balansera med behovet av flexibilitet. Den introducerar konceptet för en enterprise-kodskelett som hjälper organisationer i implementera och hantera sina Azure-prenumerationer. 

## <a name="need-for-governance"></a>Behovet av att styrning
När du flyttar till Azure måste du åtgärda i avsnittet för styrning tidigt för att säkerställa en framgångsrik användning av molnet inom företaget. Tid och bureaucracy för att skapa ett omfattande styrning system du tyvärr innebär vissa affärsgrupper gå direkt till leverantörer utan att inbegripa företagets IT. Den här metoden kan lämna företaget öppen säkerhetsproblem om resurserna som inte hanteras korrekt. Egenskaperna hos offentliga cloud - flexibilitet, flexibilitet och priser för förbrukningsbaserad - är viktigt att affärsgrupper som behöver snabbt uppfyller behoven hos kunder (interna och externa). Men företagets IT måste se till att skyddas effektivt data och datorer.

I verkligheten används scaffold-teknik för att skapa grunden för strukturen. Kodskelett hjälper allmänna mönster och ger fästpunkter för permanent system som ska monteras. En enterprise-kodskelett är samma: en uppsättning flexibla kontroller och funktioner i Azure som tillhandahåller strukturen i miljön och ankare för tjänster som bygger på offentliga moln. Det ger builders (IT och affärsgrupper) en grund för att skapa och koppla nya tjänster.

Kodskelett baseras på praxis som vi har samlats in från många Användarsegmentet med klienter med olika storlekar. Dessa klienter mellan små organisationer utveckling av lösningar i molnet Fortune 500 företag och oberoende programvaruleverantörer som migrerar och utveckling av lösningar i molnet. Enterprise-kodskelett är ”specialbyggt” ska vara flexibel stöder både traditionell IT-arbetsbelastningar och flexibel arbetsbelastningar; utvecklare som skapar programvara som en-tjänst (SaaS)-program utifrån t.ex, Azure-funktioner.

Enterprise-kodskelett är avsett att utgöra grunden för varje ny prenumeration i Azure. Det gör att administratörer kan kontrollera arbetsbelastningar uppfyller minsta styrningskraven i en organisation utan förhindrar att affärsgrupper och utvecklare snabbt uppfylla sina egna mål.

> [!IMPORTANT]
> Styrning är avgörande för Azure ska lyckas. Den här artikeln riktar sig till en enterprise-kodskelett teknisk implementering, men endast vidrör på bredare processen och relationer mellan komponenter. Princip för styrning flödar från överkanten ned och bestäms av vad företaget som vill uppnå. Naturligtvis finns skapandet av en modell för styrning för Azure innehåller representanter från IT-avdelningen, men viktigare ska den vara starkt representation från business grupp utfyllnadstecken och säkerhet och riskhantering. I slutet är en enterprise-kodskelett om att minimera risk för företag för att underlätta uppdrag och mål för en organisation.
> 
> 

Följande bild beskrivs komponenterna i kodskelett. Grunden är beroende av en fast plan för avdelningar, konton och prenumerationer. Pelare består av Resource Manager principer och starka namngivning standarder. Resten av kodskelett hämtas från grundläggande funktioner i Azure och funktioner att aktivera en säker och hanterbar miljö.

![kodskelett komponenter](./media/resource-manager-subscription-governance/components.png)

> [!NOTE]
> Azure har växt snabbt efter lanseringen 2008. Detta krävs Microsoft teknikerna snappa göras av deras metod för att hantera och distribuera tjänster. Azure Resource Manager-modellen introducerades i 2014 och ersätter den klassiska distributionsmodellen. Hanteraren för filserverresurser kan organisationer enkelt distribuera, ordna och styra Azure-resurser. Resource Manager innehåller parallellisering när du skapar resurser för snabbare distribution av lösningar för komplexa, beroende av varandra. Den omfattar även detaljerad åtkomstkontroll och möjligheten att taggen resurser med metadata. Microsoft rekommenderar att du skapar alla resurser via Resource Manager-modellen. Enterprise-kodskelett är explicit utformad för Resource Manager-modellen.
> 
> 

## <a name="define-your-hierarchy"></a>Definiera din hierarki
Grunden för kodskelett är Azure Enterprise-registrering (och Enterprise Portal). Enterprise-registrering definierar formen och användning av Azure-tjänster inom ett företag och är styrningsstrukturen kärnor. I enterprise-avtal kan kunder att ytterligare dela upp miljö till avdelningar, konton och slutligen prenumerationer. En Azure-prenumeration är den grundläggande enheten där alla resurser som ingår. Den definierar också flera begränsningar i Azure, till exempel antal kärnor, resurser osv.

![Hierarki](./media/resource-manager-subscription-governance/agreement.png)

Alla företag har olika och hierarkin i föregående bild tillåter stor flexibilitet i hur Azure ordnas inom företaget. Innan du implementerar de riktlinjer som finns i det här dokumentet, bör du modellera hierarkin och förstå effekten på fakturering, åtkomst till företagsresurser och komplexitet.

De tre vanliga mönster för Azure-registreringar är:

* Den **funktionella** mönster
  
    ![funktionella](./media/resource-manager-subscription-governance/functional.png)
* Den **affärsenhet** mönster 
  
    ![verksamheten](./media/resource-manager-subscription-governance/business.png)
* Den **geografiska** mönster
  
    ![geografisk](./media/resource-manager-subscription-governance/geographic.png)

Du kan använda kodskelett på prenumerationsnivån att utöka styrningen krav i företaget i prenumerationen.

## <a name="naming-standards"></a>Namnge standarder
Den första hörnsten i kodskelett är naming standarder. Väl utformad namngivning standarder kan du identifiera resurser i portalen på en faktura och i skript. Troligen redan namngivningen standarder för lokal infrastruktur. När du lägger till Azure i din miljö, ska du utöka dessa namngivning standarder till Azure-resurser. Namnge standard underlätta mer effektiv hantering av miljön på alla nivåer.

> [!TIP]
> För namngivningsregler:
> * Granska och vidta om möjligt den [Patterns and Practices vägledning](../guidance/guidance-naming-conventions.md). Den här vägledningen hjälper dig att bestämma på ett meningsfullt namngivningsstandarden.
> * Använd camelCasing för namnen på de resurser (till exempel myResourceGroup och vnetNetworkName). Obs: Finns det vissa resurser, till exempel lagringskonton, där det enda alternativet är att använda gemener (och inga andra specialtecken).
> * Överväg att använda Azure Resource Manager-principer (beskrivs i nästa avsnitt) att genomdriva namngivning standarder.
> 
> Med hjälp av föregående tips när du implementerar en konsekvent namngivningskonvention.

## <a name="policies-and-auditing"></a>Principer och granskning
Andra hörnsten i kodskelett innebär att du skapar [Azure Resource Manager principer](resource-manager-policy.md) och [granskning aktivitetsloggen](resource-group-audit.md). Principer för Resource Manager ger dig möjlighet att hantera risker i Azure. Du kan definiera principer som kontrollera data suveränitet genom att begränsa, framtvinga eller granskning av vissa åtgärder. 

* Principen är en standard **Tillåt** system. Du styr åtgärder genom att definiera och tilldelning av principer till resurser som neka eller granska åtgärder på resurser.
* Principer beskrivs av principdefinitioner i en princip definition language (if-then villkor).
* Du skapar principer med JSON (Javascript Object Notation) formaterade filer. När du har definierat en princip du tilldela det till ett visst scope: prenumerationen, resursgruppen eller resursen.

Principer har flera åtgärder som gör att en detaljerad metod för dina scenarier. Åtgärder är:

* **Neka**: blockerar resursbegäran
* **Granska**: gör att begäran men lägger till en rad i aktivitetsloggen (som kan användas för att ge aviseringar eller att utlösa runbooks)
* **Lägg till**: lägger till angivna information till resursen. Till exempel om det inte taggen ”CostCenter” på en resurs lägger du till taggen med ett standardvärde.

### <a name="common-uses-of-resource-manager-policies"></a>Vanliga användningsområden för Resource Manager-principer
Principer för Azure Resource Manager är ett kraftfullt verktyg i Azure toolkit. De kan du undvika oväntade kostnader, att identifiera ett kostnadsställe för resurser via taggning och se till att restriktioner uppfylls. När principer kombineras med de inbyggda funktionerna för granskning, kan du fashion komplexa och flexibla lösningar. Principer kan företag som tillhandahåller kontroller för arbetsbelastningar ”traditionell IT” och ”Agile” arbetsbelastningar; som, utveckla kundprogram. De vanligaste mönster som vi finns i principer är:

* **GEO-kompatibilitetsdata/suveränitet** -Azure tillhandahåller regioner över hela världen. Företag vill ofta styra där resurserna skapas (om du vill se till att data suveränitet eller bara för att se till att resurserna skapas nära slutet konsumenter av resurser).
* **Kostnadshanteringen** -Azure-prenumeration kan innehålla resurser av många typer och skala. Företag vill ofta så att prenumerationer som standard inte använder onödigt stora resurser, vilket kan kosta hundratals kronor i månaden eller mer.
* **Standard styrning via taggar som krävs** -kräver taggar är en av de vanligaste och hög önskade funktionerna. Använda principer för Azure Resource Manager kan företag Se till att en resurs är taggade på rätt sätt. De vanligaste taggarna är: avdelning, resurs-ägare och miljö typ (till exempel - produktion, test och utveckling)

**Exempel**

”Traditionell IT” prenumerationen för line-of-business-program

* Framtvinga avdelning och ägare taggarna på alla resurser
* Begränsa skapa en resurs för Nordamerika-regionen
* Begränsa möjligheten att skapa virtuella datorer G-serien och HDInsight-kluster

”Flexibel” miljö för en affärsenhet skapar molnprogram

* Att skapa resurser för att uppfylla kraven för suveränitet bara i en viss region.
* Tillämpa miljötaggen på alla resurser. Om en resurs har skapats utan en tagg, bifoga den **miljö: Okänt** taggen till resursen.
* Granska när resurserna skapas utanför Nordamerika men förhindrar inte.
* Granska när hög kostnad resurser skapas.

> [!TIP]
> De vanligaste användningen av Resource Manager principer mellan olika organisationer är att kontrollera *där* resurser kan skapas och *vad* typer av resurser kan skapas. Förutom att tillhandahålla kontroller på *där* och *vad*, många företag använda principer för att se till att resurserna har rätt metadata till för användning. Vi rekommenderar att tillämpa principer på prenumerationsnivån för:
> 
> * GEO-kompatibilitetsdata/suveränitet
> * Kostnadshantering
> * Taggar som krävs (bestämd av företagets behov, till exempel BillTo Programägaren)
> 
> Du kan använda ytterligare principer på lägre nivåer i omfånget.
> 
> 

### <a name="audit---what-happened"></a>Gransknings - vad hände?
Om du vill visa hur din miljö fungerar, måste du granska användaraktivitet. De flesta typer av resurser i Azure skapa diagnostikloggar som du kan analysera via ett verktyg i loggen eller i Azure Operations Management Suite. Du kan samla in aktivitetsloggar i flera prenumerationer att tillhandahålla en avdelningsnivå eller enterprise-vyn. Granskningsposter är både ett viktigt diagnostiska verktyg och en avgörande mekanism för att utlösa händelser i Azure-miljön.

Aktivitetsloggar från Resource Manager distributioner gör det möjligt att fastställa den **operations** som tog plats och vem som utförde dem. Aktivitetsloggar kan samlas in och sammanställs med verktyg som logganalys.

## <a name="resource-tags"></a>Resurstaggar
Som användare i din organisation kan du lägga till resurser i prenumerationen, blir allt viktigare att koppla resurser till rätt avdelning, kund och miljö. Du kan koppla metadata till resurser via [taggar](resource-group-using-tags.md). Du kan använda taggar för att ange information om resursen eller ägare. Taggar kan du inte bara sammanställa och gruppera resurser på olika sätt, men använda dessa data för återbetalning. Du kan tagga resurser med upp till 15 nyckel: värde-par. 

Resurstaggarna är flexibla och ska kopplas till mest resurser. Exempel på vanliga resurstaggar är:

* BillTo
* Avdelning (eller affärsenhet)
* Miljö (produktion, fas, utveckling)
* Nivån (Webbnivå, program nivå)
* Programmet ägare
* Projektnamn

![tags](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Fler exempel på taggar finns [rekommenderas namnkonventionerna för Azure-resurser](../guidance/guidance-naming-conventions.md).

> [!TIP]
> Överväg att göra en princip som kräver märkning för:
> 
> * Resursgrupper
> * Lagring
> * Virtuella datorer
> * Tjänsten miljöer/web programservrar
> 
> Den här märkning strategin identifierar alla prenumerationer vilka metadata som behövs för företag, ekonomi, säkerhet, riskhantering och övergripande hanteringen av miljön. 

## <a name="resource-group"></a>Resursgrupp
Resource Manager kan du placera resurser i meningsfulla grupper för hantering av fakturerings- eller fysisk tillhörighet. Som nämnts tidigare har Azure två distributionsmodeller. I den tidigare klassiska modellen var den grundläggande enheten för hantering av prenumerationen. Det var svårt att dela upp resurser inom en prenumeration, vilket ledde till att skapa många prenumerationer. Vi såg introduktionen av resursgrupper med Resource Manager-modellen. Resursgrupper är behållare för resurser som har en gemensam livscykel eller dela ett attribut, till exempel ”alla SQL-servrar” eller ”A”.

Resursgrupper kan inte ingå i varandra och resurser kan bara tillhöra en resursgrupp. Du kan använda vissa åtgärder på alla resurser i en resursgrupp. Till exempel försvinner tar bort en resursgrupp alla resurser i resursgruppen. Normalt placera du en hela programmet eller relaterade system i samma resursgrupp. Till exempel skulle en tre nivåprogram kallas Contoso webbprogrammet innehålla webbservern, programservern och SQLServer i samma resursgrupp.

> [!TIP]
> Hur du ska organisera dina resursgrupper kan skilja sig från ”traditionell IT” arbetsbelastningar till ”flexibel IT” arbetsbelastningar:
> 
> * ”Traditionell IT” arbetsbelastningar är oftast grupperade efter objekt inom samma livscykel, till exempel ett program. Gruppering av program kan enskilda programhantering.
> * ”Flexibel IT” arbetsbelastningar tenderar att fokusera på extern kund-riktade molnprogram. Resursgrupper bör återspegla lager för distributionen (till exempel Webbnivå, App-nivå) och hantering.
> 
> Förstå din arbetsbelastning hjälper dig att utveckla en strategi för gruppen.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll
Du ber förmodligen dig själv ”som ska ha åtkomst till resurser”? och ”hur kan jag styra åtkomst”? Att tillåta eller inte tillåta åtkomst till Azure-portalen och kontrollera åtkomst till resurser i portalen är avgörande. 

När Azure släpptes ursprungligen, åtkomstkontroller för att en prenumeration har grundläggande: administratör eller medadministratör. Åtkomst till en prenumeration i klassiskt modell underförstådda åtkomst till alla resurser i portalen. Den här bristen på finmaskig kontroll ledde till den ökande mängden av prenumerationer att ge en rimlig åtkomstkontroll för en Azure-registrering.

Den här spridning av prenumerationer behövs inte längre. Du kan tilldela användare till standardroller (till exempel ”reader” och ”författare” vanliga roller) med rollbaserad åtkomstkontroll. Du kan också definiera anpassade roller.

> [!TIP]
> Implementera rollbaserad åtkomstkontroll:
> * Anslut din företagsidentitet store (vanligtvis Active Directory) till Azure Active Directory med AD Connect-verktyget.
> * Kontrollera den Admin/Medadministratör för en prenumeration med hjälp av en hanterad identitet. **Inte** Co-Admin-administratör tilldela en ny prenumeration ägare. Använd i stället RBAC-roller för att tillhandahålla **ägare** rättigheter till en grupp eller en person.
> * Lägga till Azure användare i en grupp (exempelvis X Programägarna) i Active Directory. Använd gruppen synkroniserade ge gruppmedlemmar tillräcklig behörighet för att hantera den resursgrupp som innehåller programmet.
> * Följ principen att bevilja den **minsta privilegium** krävs för att den förväntade arbete. Exempel:
>   * Distributionsgruppen: En grupp som kan bara distribuera resurser.
>   * Hantering av virtuell dator: En grupp som kan starta virtuella datorer (för åtgärder)
> 
> De här tipsen hjälpa dig att hantera åtkomst i din prenumeration.

## <a name="azure-resource-locks"></a>Azure-resurslås
När organisationen lägger till kärntjänsterna prenumerationen, blir det ytterst viktigt att se till att tjänsterna är tillgängliga för att undvika avbrott i verksamheten. [Resurslås](resource-group-lock-resources.md) att du kan begränsa värdefulla resurser där ändra eller ta bort dem skulle ha en betydande inverkan på ditt program eller molninfrastruktur. Du kan använda lås för en prenumeration, resursgrupp eller resurs. Normalt kan du använda Lås i grundläggande resurser, till exempel virtuella nätverk, gatewayenheter och storage-konton. 

Resurslås för närvarande stöder två värden: CanNotDelete och ReadOnly. CanNotDelete innebär att användare (med lämpliga behörigheter) kan fortfarande läsa eller ändra en resurs men ta bort inte den. ReadOnly innebär att behöriga användare inte kan ta bort eller ändra en resurs.

För att skapa eller ta bort management lås, måste du ha åtkomst till `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` åtgärder.
I de inbyggda rollerna beviljas endast ägare och administratör för användaråtkomst dessa åtgärder.

> [!TIP]
> Nätverksalternativ för kärnor bör skyddas med lås. Oavsiktlig borttagning av en gateway för plats-till-plats VPN skulle vara katastrofal till en Azure-prenumeration. Azure tillåter inte att ta bort ett virtuellt nätverk som används, men att använda fler begränsningar är en bra försiktighetsåtgärd. 
> 
> * Virtuellt nätverk: CanNotDelete
> * Nätverkssäkerhetsgruppen: CanNotDelete
> * Principer: CanNotDelete
> 
> Principer är också viktigt att upprätthålla lämpliga kontroller. Vi rekommenderar att du installerar en **CanNotDelete** Lås till principer som används.

## <a name="core-networking-resources"></a>Core nätverksresurser
Åtkomst till resurser kan vara antingen interna (inom företagets nätverk) eller externa (via internet). Det är enkelt för användare i din organisation att oavsiktligt placera resurser i fel plats och öppna dem potentiellt skadliga åtkomst. Företag måste lägga till lämpliga kontroller för att säkerställa att Azure användare fatta rätt beslut som lokala enheter. Vi kan identifiera kärnresurser som ger grundläggande kontroll av åtkomst för prenumeration styrning. Kärnresurserna består av:

* **Virtuella nätverk** är behållarobjekt för undernät. Även om det inte är absolut nödvändigt, används ofta när du ansluter program till interna företagsresurser.
* **Nätverkssäkerhetsgrupper** liknar en brandvägg och ange regler för hur en resurs kan ”prata” över nätverket. De ger detaljerad kontroll över hur / om ett undernät (eller en virtuell dator) kan ansluta till Internet eller andra undernät i samma virtuella nätverk.

![Kärnnätverket](./media/resource-manager-subscription-governance/core-network.png)

> [!TIP]
> För nätverk:
> * Skapa virtuella nätverk som är dedikerad för arbetsbelastningar för externa och interna arbetsbelastningar. Den här metoden minskar risken för att oavsiktligt placera virtuella datorer som är avsedda för internt arbetsbelastningar i ett externt Internetriktade utrymme.
> * Konfigurera nätverkssäkerhetsgrupper för att begränsa åtkomsten. Blockera åtkomst till internet från interna virtuella nätverk som minimum och blockera åtkomst till företagets nätverk från externa virtuella nätverk.
> 
> De här tipsen hjälper dig att implementera säker nätverksresurser.

### <a name="automation"></a>Automation
Hantera resurser individuellt är både tidskrävande och potentiellt känsliga för vissa åtgärder. Azure tillhandahåller olika automatiseringsfunktionerna inklusive Azure Automation, Logic Apps och Azure Functions. [Azure Automation](../automation/automation-intro.md) gör att administratörer kan skapa och definiera runbooks för att hantera vanliga uppgifter vid hantering av resurser. Du kan skapa runbooks med hjälp av en redigerare för PowerShell eller grafiska redigerare. Du kan skapa komplexa arbetsflöden i flera steg. Azure Automation används ofta för att hantera vanliga aktiviteter som att stänga av oanvända resurser eller skapar resurser som svar på en utlösare utan mänsklig inblandning.

> [!TIP]
> För automatisering:
> * Skapa ett Azure Automation-konto och granska den tillgängliga runbooks (både grafiskt och kommandot rad) som är tillgängliga i den [Runbook-galleriet](../automation/automation-runbook-gallery.md).
> * Importera och anpassa viktiga runbooks för eget bruk.
> 
> Ett vanligt scenario är möjligheten för Start/avstängning virtuella datorer på ett schema. Det finns exempel runbooks som är tillgängliga i galleriet som hanterar det här scenariot och lär dig att expandera den.
> 
> 

## <a name="azure-security-center"></a>Azure Security Center
Kanske har ett av de största blockeringar till molnet införande problem över säkerheten. Risk IT-chefer och säkerhet avdelningar måste du se till att resurser i Azure är säker. 

Den [Azure Security Center](../security-center/security-center-intro.md) innehåller en central för säkerhetsstatusen för resurserna i prenumerationerna och ger rekommendationer som kan förhindra angripna resurser. Det kan aktivera mer detaljerad principer (till exempel tillämpa principer till specifika resursgrupper som gör att företag kan anpassa sina hållningsdata för risk de adressering). Slutligen är Azure Security Center en öppen plattform som gör Microsoft-partner och oberoende programvaruleverantörer kan skapa program som ansluts till Azure Security Center för att förbättra dess funktioner. 

> [!TIP]
> Azure Security Center aktiveras som standard i varje prenumeration. Dock måste du aktivera insamling av data från virtuella datorer så att Azure Security Center att installera dess agenten och börja samla in data.
> 
> ![Datainsamling](./media/resource-manager-subscription-governance/data-collection.png)
> 
> 

## <a name="next-steps"></a>Nästa steg
* Nu när du har lärt dig om prenumerationen styrning, är det dags att se dessa rekommendationer i praktiken. Se [exempel på att implementera Azure-prenumeration styrning](resource-manager-subscription-examples.md).

