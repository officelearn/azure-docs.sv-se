---
title: Styrning i Azure | Microsoft Docs
description: "Läs mer om molnbaserad databearbetning tjänster som omfattar ett brett urval av compute-instanser och tjänster som kan skalas upp och ned automatiskt så att den passar ditt program-eller enterprise."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 03e86448a1b0a13addab789bf2aea62e5d84149b
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="governance-in-azure"></a>Styrning i Azure

Vi vet att säkerhet är ett jobb i molnet och det är viktigt att du hitta korrekt och rimlig information om säkerheten i Azure. Ett av de bästa skälen att använda Azure för dina program och tjänster är att dra nytta av dess mängd säkerhetsverktyg och funktioner. Dessa verktyg och funktioner för att göra det möjligt att skapa säkra lösningar för säkra Azure-plattformen.

För att hjälpa dig att bättre förstå matrisen för styrning kontroller som implementerats i Microsoft Azure från både kund- och Microsoft operations perspektiv, den här artikeln ”styrning i Azure” skrivs att tillhandahålla en omfattande titt på styrningen funktioner som är tillgängliga med Microsoft Azure.

## <a name="azure-platform"></a>Azure-plattformen

Azure är en offentlig molntjänstplattform som har stöd för ett brett urval av operativsystem, programmeringsspråk, ramverk, verktyg, databaser och enheter. Det kan köras Linux behållare med Dockers integration. skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; build-servrar för iOS, Android och Windows enheter. Offentliga Azure-molnet och support att samma tekniker miljontals utvecklare och IT-proffs redan förlitar sig på och litar på.

När du bygger på, eller migrera IT tillgångar, en offentlig molntjänstleverantör du förlita dig på den organisationens förmåga att skydda dina program och data med tjänster och kontrollerna som ger för att hantera säkerheten för din molnbaserade tillgångar.

Azures infrastruktur från funktionen är utformad för att program som värd för miljontals kunder samtidigt och ger en säker grund som företag kan uppfylla sina säkerhetskrav. Dessutom ger Azure dig många säkerhetsalternativ och möjlighet att styra dem så att du kan anpassa säkerhet för att uppfylla de specifika behoven för din organisation distributioner.

Det här dokumentet hjälper dig att förstå hur Azure styrning funktioner kan hjälpa dig att uppfylla kraven.

## <a name="abstract"></a>Abstrakt

Microsoft Azure cloud styrning ger en integrerad granskning och rådgivning metod för att granska och ge råd organisationer på deras användning av Azure-plattformen. Microsoft Azure cloud styrning refererar till beslutsprocesser, villkor och principer som ingår i planeringen, arkitektur, förvärv, distribution, åtgärden och hantering av ett moln datoranvändning.

Om du vill skapa en plan för Microsoft Azure cloud styrning, måste du ta en titt på personer, processer och tekniker för närvarande på plats och skapa ramverk som gör det lättare för IT att stödja affärsbehov och ger användarna konsekvent möjlighet att använda kraftfulla funktioner i Microsoft Azure.

Det här dokumentet beskriver hur du kan uppnå en förhöjd styrning av din IT-resurser i Microsoft Azure. Det här dokumentet hjälper dig att förstå de funktioner för säkerhet och styrning inbyggd i Microsoft Azure.

Följande är main styrning problem som beskrivs i det här dokumentet:

- Genomförande av principer, processer och procedurer enligt organisationens mål.

- Säkerhet och kontinuerlig organisation krav uppfylls

- Övervakning och aviseringar

## <a name="implementation-of-policies-processes-and-procedures"></a>Implementering av principer, processer och procedurer 

Management har upprättat roller och ansvarsområden som övervakar genomförandet av information säkerhetsprincip och operativa kontinuitet i Azure. Microsoft Azure management är ansvarig för att granska säkerhets- och kontinuiteten rutiner inom sina respektive grupper (inklusive tredje part) och gör det lättare att efterlevnad med principer, processer och standarder.

Här följer de faktorer som utvecklats:

- Kontoetablering

- Prenumerationen kontroller

- Roll baserad åtkomstkontroll

- Resurshantering

- Resursen spårning

- Kritisk resurskontroll

- Åtkomst till faktureringsinformation API

- Kontroller för nätverk

## <a name="account-provisioning"></a>Kontoetablering

Definiera Kontohierarki är ett viktigt steg för att använda och struktur Azure services inom ett företag och är styrningsstrukturen kärnor. Vid kunder med enterprise-avtal kan kunder ytterligare dela upp miljö till avdelningar, konton och slutligen prenumerationer.

![Kontoetablering](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Om du inte har ett enterprise-avtal, bör du använda [Azure taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) på prenumerationsnivån att definiera hierarki. En Azure-prenumeration är den grundläggande enheten där alla resurser som ingår. Den definierar också flera begränsningar i Azure, till exempel antal kärnor, resurser osv. Prenumerationer kan innehålla [resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), som kan innehålla resurser. [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) principer som tillämpas på dessa tre nivåer.

Alla företag har olika och hierarkin med hjälp av Azure taggar vid företagskunder tillåter stor flexibilitet i hur Azure ordnas inom företaget. Innan du distribuerar resurserna i Microsoft Azure, bör du modellen hierarkin och förstå effekten på fakturering, åtkomst till företagsresurser och komplexitet.

## <a name="subscription-controls"></a>Prenumerationen kontroller

Prenumerationen styr hur resursförbrukning rapporteras och debiteras. Prenumerationer kan konfigureras för separata fakturering och betalning. Vi kan ha flera prenumerationer som nämnts tidigare i en Azure-konto. Prenumerationer kan användas för att fastställa Azure resursanvändningen för flera avdelningar i ett företag.

Om exempelvis ett företag har IT-avdelningen, HR och marknadsföring avdelningar och dessa har olika projekt som körs. Baserat på användning av Azure-resurser som virtuella datorer av varje avdelning, kan de faktureras därefter. Vi kan styra ekonomi för varje avdelning av detta.

Azure-prenumerationer upprätta tre parametrar:

- ett unikt prenumerations-ID

- en fakturering plats

- Uppsättning av tillgängliga resurser

För en individ, som omfattar en Microsoft-konto-ID, kreditkortsnummer och hela sviten för Azure-tjänster – även om Microsoft tillämpar förbrukning gränser, beroende på vilken prenumerationstyp.

Registrering av Azure hierarkier definiera hur tjänster är strukturerade inom ett Enterprise-avtal. Enterprise Portal möjligheten för kunder att dela åtkomst till Azure-resurser som är associerade med ett Enterprise-avtal baserat på flexibla hierarkier anpassningsbara till en organisations unika behov. Hierarkin mönstret måste matcha en organisations hantering och geografiska struktur så att den associera fakturerings- och åtkomsten kan redovisas korrekt.

De tre övergripande mönster är funktionell, affärsenhet och geografiska, med avdelningar som en administrativ konstruktion för kontot grupperingar. Inom varje avdelning kan tilldelas konton prenumerationer som skapar silor för fakturering och flera viktiga begränsningar i Azure (t.ex. antalet virtuella datorer, lagringskonton osv.).

![Prenumerationen kontroller](./media/governance-in-azure/security-governance-in-azure-fig2.png)


För organisationer med ett Enterprise-avtal så en hierarki i fyra nivåer i Azure-prenumerationer:

- registreringen företagsadministratör

- avdelning administratör

- kontoägaren

- Tjänstadministratör

Den här hierarkin styr följande:

- Fakturering relation

- Administrationen

- Rollen åtkomstkontroll (RBAC) till artefakter

- Gränser-gränser

- Gränser

  - Användnings- och fakturering (priskort baserat på erbjudande siffror)

  - Begränsningar

  - Virtual Network

- Ansluten till 1 AAD (1 AAD associeras med många prenumerationer)

- Som är associerade med ett konto för enterprise-registrering

## <a name="role-based-access-controls"></a>Rollbaserad åtkomstkontroll

När Azure släpptes ursprungligen, åtkomstkontroller för att en prenumeration har grundläggande: administratör eller medadministratör. Åtkomst till en prenumeration i klassiskt modell underförstådda åtkomst till alla resurser i portalen. Den här bristen på finmaskig kontroll ledde till den ökande mängden av prenumerationer att ge en rimlig åtkomstkontroll för en Azure-registrering.

![Rollbaserad åtkomstkontroll](./media/governance-in-azure/security-governance-in-azure-fig3.png)

Den här spridning av prenumerationer behövs inte längre. Du kan tilldela användare till standardroller (till exempel ”reader” och ”författare” vanliga roller) med rollbaserad åtkomstkontroll. Du kan också definiera anpassade roller.

[Azure rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) Aktivera detaljerad åtkomsthantering för Azure. Med RBAC kan du bevilja exakt den åtkomstnivå som användarna behöver för att kunna utföra sitt arbete. Säkerhet indatavärdena företag bör tänka på att ge anställda behörigheterna exakt som de behöver. För många behörigheter exponerar ett konto för attacker. För få behörigheter innebär att anställda kan få arbetet gjort effektivt. Azure rollbaserad åtkomstkontroll (RBAC) kan du lösa det här problemet genom att erbjuda detaljerad åtkomsthantering för Azure. RBAC hjälper dig att särskilja uppgifter i din grupp och ge bara mängden åtkomst till användare som de behöver för att utföra sitt arbete. Obegränsad istället för att ge alla behörigheter i din Azure-prenumeration eller resurser, kan du tillåta endast vissa åtgärder.

Till exempel använda RBAC så att en medarbetare som hanterar virtuella datorer i en prenumeration medan en annan kan hantera SQL-databaser inom samma prenumeration.

Azure RBAC har tre grundläggande roller som gäller för alla typer av resurser:

- **Ägare** har fullständig åtkomst till alla resurser som bland annat att delegera åtkomst till andra.

- **Deltagare** kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra.

- **Läsaren** kan visa befintliga Azure-resurser.

Resten av rollerna i Azure RBAC kan hanteringen av specifika Azure-resurser. Till exempel tillåter virtuella deltagarrollen användaren att skapa och hantera virtuella datorer. Det ger dem åtkomst till virtuella nätverk eller undernät som den virtuella datorn ansluter till.

[Inbyggda RBAC-roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) listar rollerna som är tillgängliga i Azure. Det anger åtgärder och scope som varje inbyggd roll som ger användare.

Bevilja åtkomst genom att tilldela rollen RBAC till användare, grupper och program för ett visst område. Omfånget för en rolltilldelning kan vara en prenumeration, resursgrupp eller en enskild resurs. En roll som tilldelats en överordnad omfattning även ger åtkomst till underordnade som finns i den.

En användare med åtkomst till en resursgrupp kan exempelvis hantera alla resurser som den innehåller som webbplatser, virtuella datorer och undernät.

Azure RBAC stöder endast hanteringsåtgärder Azure-resurser i Azure-portalen och Azure Resource Manager API: er. Det går inte att tillåta alla data på åtgärder för Azure-resurser. Du kan till exempel auktorisera någon att hantera Storage-konton, men inte till blobbar eller tabeller i ett Lagringskonto kan du inte. På samma sätt kan kan en SQL-databas hanteras, men inte tabellerna i den.

Mer information om hur RBAC kan hjälpa dig att hantera åtkomsten finns i [Vad är rollbaserad åtkomstkontroll?](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)

Du kan också [skapa en anpassad roll](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) i rollbaserad åtkomstkontroll (RBAC) om ingen av de inbyggda rollerna uppfyller dina specifika åtkomst måste. Anpassade roller kan skapas med [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell), [Azure-kommandoradsgränssnittet (CLI)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli), och [REST API](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest). Precis som inbyggda roller kan anpassade roller tilldelas användare, grupper och program på prenumerationen, resursgruppen och resursen omfattningar.

Du kan bevilja upp till 2 000 rolltilldelningar inom varje prenumeration.

## <a name="resource-management"></a>Resurshantering

Azure som ursprungligen den klassiska distributionsmodellen. I den här modellen fanns varje resurs självständigt. Det gick inte att gruppera relaterade resurser. I stället var du tvungen att manuellt spåra vilka resurser som består av din lösning eller ditt program och Kom ihåg att hantera dem på ett samordnat sätt.

Om du vill distribuera en lösning var du tvungen att skapa varje resurs individuellt via Azure-portalen eller skapa ett skript som distribuerats alla resurser i rätt ordning. Om du vill ta bort en lösning var du tvungen att ta bort varje resurs individuellt. Du kan inte enkelt tillämpa och uppdatera principer för åtkomstkontroll för relaterade resurser. Slutligen kan du inte använda taggar till resurser för att märka dem med villkor som hjälper dig övervaka dina resurser och hantera fakturering.

I 2014 introducerade Azure Resource Manager som läggs till konceptet för en resursgrupp. En resursgrupp är en behållare för resurser som delar en gemensam livscykel. Resource Manager-distributionsmodellen ger många fördelar:

- Du kan distribuera, hantera och övervaka alla tjänster för din lösning som en grupp i stället för att hantera dessa tjänster individuellt.

- Du kan upprepade gånger distribuera lösningen under hela dess livscykel och vara säker på att dina resurser distribueras i ett konsekvent tillstånd.

- Du kan använda åtkomstkontroll för alla resurser i resursgruppen och dessa principer tillämpas automatiskt när nya resurser läggs till i resursgruppen.

- Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.

- Du kan använda JavaScript Object Notation (JSON) för att definiera infrastrukturen för lösningen. JSON-filen kallas en Resource Manager-mall.

- Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.

![Resurshantering](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Resource Manager kan du placera resurser i meningsfulla grupper för hantering av fakturerings- eller fysisk tillhörighet. Som nämnts tidigare har Azure två distributionsmodeller. I den tidigare [modell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model), den grundläggande hanteringsenheten var prenumerationen. Det var svårt att dela upp resurser inom en prenumeration, vilket ledde till att skapa många prenumerationer. Vi såg introduktionen av resursgrupper med Resource Manager-modellen.

En resursgrupp är en behållare som innehåller relaterade resurser för en Azure-lösning. [Resursgruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) kan innehålla alla resurser för lösningen, eller bara de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation.

Rekommendationer om mallar finns i [Metodtips för att skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analyserar beroenden för att säkerställa att resurserna skapas i rätt ordning. Om en resurs bygger på ett värde från en annan resurs (till exempel en virtuell dator som behöver ett lagringskonto för diskar) anger du ett beroende.

>[!Note]
>Mer information finns i [Definiera beroenden i Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies).

Du kan också använda mallen för uppdateringar av infrastrukturen. Du kan till exempel lägga till en resurs till din lösning och lägga till konfigurationsregler för resurser som redan har distribuerats. Om mallen används för att skapa en resurs men resursen redan finns utför Azure Resource Manager en uppdatering i stället för att skapa en ny tillgång. Azure Resource Manager uppdaterar den befintliga tillgången till samma tillstånd som den skulle ha som ny.

Resource Manager tillhandahåller tillägg för scenarier när du behöver ytterligare åtgärder som att installera programvara som inte ingår i installationsprogrammet.

## <a name="resource-tracking"></a>Resursen spårning

Som användare i din organisation kan du lägga till resurser i prenumerationen, blir allt viktigare att koppla resurser till rätt avdelning, kund och miljö. Du kan koppla metadata till resurser via taggar. Du använder [taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) att ge information om resursen eller ägare. Taggar kan du inte bara sammanställa och gruppera resurser på flera sätt, men använda dessa data för återbetalning.

Använd taggar om du har en komplex samling resursgrupper och resurser och behöver visualisera dessa tillgångar på ett sätt som passar dig. Du kan till exempel tagga resurser som har en liknande roll i organisationen eller som tillhör samma avdelning.

Utan taggar kan användare i din organisation skapa flera resurser som kan vara svåra att identifiera och hantera längre fram. Exempelvis kanske du vill ta bort alla resurser för ett projekt. Om resurserna inte har taggats för projektet måste du hittar dem manuellt. Taggning kan vara ett bra sätt att minska onödiga kostnader i din prenumeration.

Resurser behöver inte finnas i samma resursgrupp för att dela en tagg. Du kan skapa din egen taggtaxonomi för att försäkra dig om att alla användare i organisationen använder samma taggar och att de inte av misstag använder varianter (till exempel ”avd” i stället för ”avdelning”).

Principer för företagsresurser kan du skapa standardregler för din organisation. Du kan skapa principer som kontrollera resurser som är märkta med lämpliga värden.

> [!Note]
> Mer information finns i [fakturering taggar princip initiativ](../azure-policy/scripts/billing-tags-policy-init.md).

Du kan också visa taggade resurser via Azure Portal.

[Användningsrapporten](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) för din prenumeration innehåller taggnamn och -värden, vilket betyder att du kan dela upp kostnader efter taggar.

> [!Note]
> Mer information om taggar finns [fakturering taggar princip initiativ](../azure-policy/scripts/billing-tags-policy-init.md).

Följande begränsningar gäller för taggar:

- Varje resurs eller en resursgrupp kan innehålla högst 15 taggen nyckel/värde-par. Den här begränsningen gäller bara för taggar tillämpas direkt på den resursgrupp eller resurs. En resursgrupp kan innehålla många resurser som har 15 taggen nyckel/värde-par.

- Taggnamnet är begränsat till 512 tecken.

- Taggvärdet är begränsat till 256 tecken.

- Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.

Om du har fler än 15 värden som du vill koppla till en resurs använder du en JSON-sträng för taggvärdet. JSON-strängen kan innehålla flera värden som tillämpas på en enskild tagg nyckel.

### <a name="tags-and-billing"></a>Taggar och fakturering

Taggar kan du gruppera dina faktureringsinformation. Till exempel om du kör flera virtuella datorer i olika organisationer använda taggar om du vill gruppera användning av kostnadsställe. Du kan också använda taggar för att kategorisera kostnader av körningsmiljön; till exempel den fakturering användningen av virtuella datorer som körs i produktionsmiljön.

Du kan hämta information om taggar via den [Azure Resursanvändning och RateCard APIs](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) eller filen användning fil med kommaavgränsade värden (CSV). Du hämta filen från användning av [konton i Azure portal](https://account.windowsazure.com/) eller [EA portal](https://ea.azure.com/).

>[!Note]
> Mer information om programmatisk åtkomst till faktureringsinformationen finns [få insikter om dina Microsoft Azure-resursförbrukning](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). REST-API: et finns [Azure Billing REST API-referens](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

När du hämtar användning CSV för tjänster som stöder taggar med fakturering visas taggarna i kolumnen taggar.

## <a name="critical-resource-controls"></a>Kritisk resurskontroller

När organisationen lägger till kärntjänsterna prenumerationen, blir det ytterst viktigt att se till att tjänsterna är tillgängliga för att undvika avbrott i verksamheten. [Resurslås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) att du kan begränsa värdefulla resurser där ändra eller ta bort dem skulle ha en betydande inverkan på ditt program eller molninfrastruktur. Du kan använda lås för en prenumeration, resursgrupp eller resurs. Normalt kan du använda Lås i grundläggande resurser, till exempel virtuella nätverk, gatewayenheter och storage-konton.

Resurslås för närvarande stöder två värden: CanNotDelete och ReadOnly. CanNotDelete innebär att användare (med lämpliga behörigheter) kan fortfarande läsa eller ändra en resurs men ta bort inte den. ReadOnly innebär att behöriga användare inte kan ta bort eller ändra en resurs.

Hanteraren för filserverresurser Lås gäller endast för åtgärder som sker i management-plan, som består av åtgärder som skickas till <https://management.azure.com>. Lås begränsar inte hur resurser utföra sina egna funktioner. Resursändringar är begränsad, men resursåtgärder är inte begränsade. Till exempel en ReadOnly-lås på en SQL-databas hindrar dig från att ta bort eller ändra databasen, men det förhindrar inte du att skapa, uppdatera eller ta bort data i databasen.

Tillämpa **ReadOnly** kan leda till oväntade resultat eftersom vissa åtgärder som ser ut som att läsa ytterligare åtgärder krävs för åtgärderna. Till exempel placera en **ReadOnly** lås på ett lagringskonto som förhindrar att alla användare lista nycklarna. Listan med nycklar operationen hanteras via en POST-begäran eftersom returnerade nycklar är tillgängliga för skrivåtgärder.

![Kritisk resurskontroller](./media/governance-in-azure/security-governance-in-azure-fig5.png)

För ett annat exempel är förhindrar att placera en ReadOnly-lås på en App Service-resurs Visual Studio Server Explorer visar filer för resursen eftersom den interaktionen kräver skrivåtkomst.

Till skillnad från rollbaserad åtkomstkontroll använder du management Lås för att tillämpa en begränsning för alla användare och roller. Läs om hur du anger behörigheter för användare och roller i [Azure rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

När du använder ett lås på en överordnad omfattning, ärver alla resurser i omfattningen samma låset. Även resurser som du senare lägger till ärver låset från överordnat. Det mest restriktiva låset i ärvda företräde.

För att skapa eller ta bort management lås, måste du ha tillgång till Microsoft.Authorization/ _eller Microsoft.Authorization/locks/_ åtgärder. I de inbyggda rollerna, endast **ägare** och **administratör för användaråtkomst** beviljas dessa åtgärder.

## <a name="api-access-to-billing-information"></a>API-åtkomst till faktureringsinformationen

Använd Azure fakturering API: er som hämtar användnings- och data till din önskade verktyg för dataanalys. Användning av Azure och RateCard APIs kan hjälpa dig korrekt förutsäga och hantera dina kostnader. API: erna implementeras som en Resource Provider och en del av API: er som exponeras av Azure Resource Manager.

### <a name="azure-resource-usage-api-preview"></a>Azure Resursanvändning API (förhandsgranskning)

Använd Azure [resurs användning API](https://msdn.microsoft.com/library/azure/mt219003) hämtar dina data uppskattade Azure förbrukningen. Detta API innehåller:

- **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com/) eller via [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till den Prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration.

- **Varje timme och dag** – anropare kan ange om de vill ha sina Azure användningsdata i varje timme tidsgrupper eller dagligen tidsgrupper. Standardvärdet är varje dag.

- **Instansen metadata (inklusive resurstaggar)** – hämta instansen detaljnivåer som fullständiga resurs-uri (/subscriptions/ {prenumerations-id} /..), grupp resursinformation och resurstaggar. Dessa metadata hjälper dig att deterministiskt och genom programmering allokera användning av taggarna för användningsfall som mellan debitering.

- **Resursmetadata** -resursinformation som mätaren namn, mätaren kategori, mätaren underkategori, enhet och region ge anroparen en bättre förståelse för vad förbrukades. Vi arbetar också för att justera resurs metadata terminologi i Azure-portalen, Azure användning CSV, EA fakturering CSV och andra offentliga upplevelser, så att du kan korrelera data över upplevelser.

- **Användning för alla erbjuder typer** – användningsdata är tillgängliga för alla erbjuder typer som betala per användning, MSDN, Summa, kredit och EA.

**Azure-resurs RateCard API (förhandsgranskning)**

Använd Azure Resource RateCard API för att hämta listan över tillgängliga Azure-resurser och uppskattade prisinformation för varje. Detta API innehåller:

- **Azure rollbaserad åtkomstkontroll** – konfigurera principer för åtkomst på Azure-portalen eller via Azure PowerShell-cmdletar för att ange vilka användare eller program kan få åtkomst till RateCard data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration.

- **Stöd för betala per användning, MSDN, Summa och kredit erbjuder (EA stöds inte)** -detta API ger Azure erbjudande nivå hastighet information. Anroparen av denna API måste klara i erbjudandet information för att hämta resursinformation och priser. Vi kan för närvarande inte att ange EA priser eftersom EA erbjudanden har anpassat kostnader per registrering. Här följer några scenarier som möjliggörs med en kombination av användningen och RateCard APIs:

- **Azure utgifter under månaden** - använda en kombination av användningen och RateCard APIs få bättre insikter om ditt moln utgifter under månaden. Du kan analysera per timme och dag buckets användnings- och kostnad uppskattningar.

- **Ställa in aviseringar** – använda användningen och RateCard APIs för att hämta uppskattade molnet förbrukning och kostnader och konfigurera resurs- eller monetära baserade aviseringar.

- **Förutsäga faktura** – Get uppskattade användnings- och molnet tillbringar och tillämpa maskininlärningsalgoritmer för att förutsäga växeln skulle vara i slutet av faktureringsperioden.

- **Före förbrukning kostnad analysis** – Använd RateCard API för att förutse hur mycket din faktura är för din förväntade användning när du flyttar dina arbetsbelastningar till Azure. Om du har befintliga arbetsbelastningar i andra moln eller privata moln, kan du även mappa förbrukningen i Azure tillbringar priser för att få en bättre uppfattning av Azure. Denna uppskattning ger dig möjlighet att pivotera på erbjudandet och jämför olika erbjudandetyper utöver betala per användning, t.ex. summa och kredit. API: et även ger dig möjlighet att se kostnaden skillnader efter region och kan du göra en vad händer om kostnadsanalys som hjälper dig att fatta distributionsbeslut.

- **Konsekvensanalys** -du kan fastställa om det är mer kostnadseffektivt att köra arbetsbelastningar i en annan region, eller på en annan konfiguration av Azure-resurs. Azure resurskostnader kan variera beroende på Azure-regionen du använder.

- Du kan också bestämma om en annan typ av Azure-erbjudande ger en bättre hastighet på Azure-resurs.

## <a name="networking-controls"></a>Kontroller för nätverk

Åtkomst till resurser kan vara antingen interna (inom företagets nätverk) eller externa (via internet). Det är enkelt för användare i din organisation att oavsiktligt placera resurser i fel plats och öppna dem potentiellt skadliga åtkomst. Precis som med lokalt / enheter, företag måste lägga till lämpliga kontroller för att säkerställa att rätt beslut på Azure-användare.

![Kontroller för nätverk](./media/governance-in-azure/security-governance-in-azure-fig6.png)

Vi kan identifiera kärnresurser som ger grundläggande kontroll av åtkomst för prenumeration styrning. Kärnresurserna består av:

### <a name="network-connectivity"></a>Nätverksanslutning

[Virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) är behållarobjekt för undernät. Även om det inte är absolut nödvändigt, används ofta när du ansluter program till interna företagsresurser. Tjänsten Azure Virtual Network kan du ansluta Azure-resurser på ett säkert sätt till varandra med virtuella nätverk (Vnet).

Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk isolering av Azure-molnet dedikerad till din prenumeration. Du kan också ansluta Vnet till ditt lokala nätverk.

Följande är funktioner för virtuella Azure-nätverk:

- **Isolering**: Vnet isoleras från varandra. Du kan skapa separata Vnet för utveckling, testning och produktion som använder samma CIDR-Adressblock. Däremot kan du skapa flera virtuella nätverk som använder olika CIDR-Adressblock och ansluta nätverk tillsammans. Du kan dela ett VNet i flera undernät. Azure erbjuder intern namnmatchning för virtuella datorer och molntjänster rollinstanser ansluten till ett virtuellt nätverk. Du kan också konfigurera ett virtuellt nätverk för att använda dina egna DNS-servrar i stället för intern namnmatchning för Azure.

- **Internetanslutning**: alla Azure virtuella datorer (VM) och molntjänster rollinstanser ansluten till ett virtuellt nätverk har åtkomst till Internet, som standard. Du kan också aktivera inkommande åtkomst till specifika resurser efter behov.

- **Azure-resurs anslutningen**: Azure-resurser som molntjänster och virtuella datorer kan anslutas till samma virtuella nätverk. Resurserna kan ansluta till varandra med privata IP-adresser, även om de finns i olika undernät. Azure tillhandahåller standardroutning mellan undernät, virtuella nätverk och lokala nätverk, så du behöver att konfigurera och hantera vägar.

- **VNet-anslutningen**: Vnet kan anslutas till varandra, aktivera resurser som är anslutna till alla virtuella nätverk för att kommunicera med alla resurser för andra virtuella nätverk.

- **Lokal anslutning**: Vnet kan anslutas till lokalt nätverk via privata nätverksanslutningar mellan ditt nätverk och Azure, eller en plats-till-plats VPN-anslutning via Internet.

- **Trafikfiltrering**: molntjänster och Virtuella nätverkstrafik för rollen instanser inkommande och utgående kan filtreras efter källans IP-adress och port, mål-IP-adress och port och protokoll.

- **Routning**: du kan du åsidosätta Azures standard routning genom att konfigurera egna vägar eller använda BGP-vägar via en nätverksgateway.

## <a name="network-access-controls"></a>Åtkomstkontroller för nätverk

[Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) fungerar som en brandvägg och ange regler för hur en resurs kan ”prata” över nätverket. De ger detaljerad kontroll över hur / om ett undernät (eller en virtuell dator) kan ansluta till Internet eller andra undernät i samma virtuella nätverk.

En nätverkssäkerhetsgrupp (NSG) innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk (VNet). Nätverkssäkerhetsgrupper kan kopplas till undernät, enskilda virtuella datorer (klassisk) eller enskilda nätverkskort (NIC) som är anslutna till virtuella datorer (Resource Manager).

När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. Trafiken kan begränsas ytterligare genom att en nätverkssäkerhetsgrupp associeras med en virtuell dator eller ett nätverkskort.

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>Säkerhet och kontinuerlig kompatibilitet med organisationens normer

Alla företag har olika behov och alla företag kan dra nytta av distinkta fördelar från molnlösningar. Kunder för alla typer har fortfarande samma grundläggande frågor om hur du flyttar till molnet. De vill behålla kontrollen över data och de vill att data ska hållas säker och privat, alla samtidigt genomskinlighet och efterlevnad.

Kunder vill från molntjänstleverantörer är:

- **Skydda våra data** medan bekräftades att molnet kan ge ökad säkerhet för data och administrativ kontroll, IT-företag fortfarande gäller att migrera till molnet lämnar dem mer sårbara för hackare än sin nuvarande interna lösningar.

- **Skydda våra data** privata molntjänster Generera en unik sekretess utmaningar för företag. Som företag ser till att spara på kostnader med infrastruktur och förbättra deras flexibilitet molnet, behöver de också förlora kontroll över där deras data lagras, vem som åtkomst till den och hur den används.

- **Ge oss kontrollen** även om de dra nytta av molnet för att distribuera flera innovativa lösningar företag är mycket orolig förlora kontroll över sina data. De senaste upplysningarna hos myndigheter som har åtkomst till kundens data på både juridiska och extra juridiska sätt göra vissa IT-chefer försiktig med att lagra sina data i molnet.

- **Befordra genomskinlighet** säkerhet, sekretess och kontroll är viktigt att företagets beslutsfattare, de också vill kunna oberoende kontrollera hur data lagras, ofta och säkra.

- **Upprätthålla överensstämmelse** som företag Expandera användningen av molntekniker, komplexitet och omfattningen av standarder och föreskrifter fortsätter att utvecklas. Företag måste du veta att deras efterlevnadsstandarder uppfylls och att efterlevnad kommer att utvecklas som föreskrifter ändring över tid.

## <a name="security-configuration-monitoring-and-alerting"></a>Konfiguration, övervakning och avisering

Azure-prenumeranter kan hantera sina molnmiljöer från flera enheter, inklusive hantering av arbetsstationer, utvecklardatorer och även privilegierade slutanvändarens enheter som har uppgiftsspecifika behörigheter. I vissa fall kan utförs administrativa funktioner via webbaserade konsoler som Azure-portalen. I andra fall kan det finnas direkta anslutningar till Azure från lokala system över virtuella privata nätverk (VPN), Terminal Services, klientprotokoll för program eller (programmässigt) Azure Service Management API (SMAPI). Dessutom kan klientslutpunkter vara antingen domänanslutna eller isolerade och ohanterade, till exempel surfplattor eller smartphones.

Även om de många funktionerna för åtkomst och hantering tillhandahåller en omfattande uppsättning alternativ, kan den här variationen medföra betydande risker för en molndistribution. Det kan vara svårt att hantera, spåra och granska administrativa åtgärder. Den här variationen kan också innebära säkerhetshot via oreglerad åtkomst till klientslutpunkter som används för att hantera molntjänster. Med hjälp av allmänna eller personliga arbetsstationer för att utveckla och hantera infrastrukturen öppnas oförutsägbara hotvektorer, till exempel webbsurfning (t.ex. vattenhålsattacker) eller mejl (t.ex. social manipulation och nätfiskewebbplatser).

Övervakning, loggning och granskning ger en grund för att spåra och förstå administrativa aktiviteter, men det inte alltid möjligt att granska alla åtgärder i detalj på grund av mängden data som genereras. Men det är alltid bra att utvärdera hur effektiva hanteringsprinciperna är.

Azure-säkerhet styrning från AD DS-grupprincipobjekt för att styra alla administratörens Windows-gränssnitt, till exempel fildelning. Inkludera hanteringsdatorer processerna för granskning, övervakning och loggning. Spåra alla administratörers och utvecklares åtkomst och användning.

### <a name="azure-security-center"></a>Azure security center

Den [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) innehåller en central för säkerhetsstatusen för resurserna i prenumerationerna och ger rekommendationer som kan förhindra angripna resurser. Det kan aktivera mer detaljerad principer (till exempel tillämpa principer till specifika resursgrupper som gör att företag kan anpassa sina hållningsdata för risk de adressering).

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center innehåller integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer, och upptäcka hot som annars kanske skulle förbli oupptäckta, fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar. När du har aktiverat [säkerhetsprinciper](https://docs.microsoft.com/azure/security-center/security-center-policies) för en prenumeration resurser, Security Center analyserar säkerheten för dina resurser för att identifiera potentiella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt.

Azure Security Center representerar en kombination av bästa praxis analys- och principhantering för alla resurser i en Azure-prenumeration. Det här kraftfulla och enkla att använda verktyget kan säkerhet team och risker antal för att förebygga, upptäcka och svara på säkerhetshot som automatiskt samlar in och analyserar säkerhetsdata från resurserna i Azure, nätverket och partnerlösningar som skadlig program och brandväggar.

Dessutom analysteknik Azure Security Center avancerad, inklusive machine learning och beteendeanalys samtidigt utnyttja globala hotinformation från Microsoftprodukter och tjänster, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa flöden används. [Säkerhet styrning](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) kan tillämpas brett på prenumerationsnivån eller minskar antalet specifika, detaljerade krav som tillämpas på enskilda resurser via principdefinition.

Slutligen Azure Security Center analyserar resurssäkerhetshälsa baserat på de principerna och använder dem för att ange insiktsfulla instrumentpaneler och avisering för händelser, t.ex identifiering av skadlig kod eller skadliga IP-anslutning försök.

>[!Note]
> Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Security Center samlar in data från dina virtuella datorer för att utvärdera deras säkerhetstillstånd, ange säkerhetsrekommendationer och varna dig om hot. Första gången du öppnar Security Center, är insamling av data aktiverat på alla virtuella datorer i din prenumeration. Insamling av data rekommenderas, men du kan välja bort av [inaktivera datainsamling](https://docs.microsoft.com/azure/security-center/security-center-faq) i Security Center-princip.

Slutligen är Azure Security Center en öppen plattform som gör Microsoft-partner och oberoende programvaruleverantörer kan skapa program som ansluts till Azure Security Center för att förbättra dess funktioner.

Azure Security Center övervakar följande Azure-resurser:

- Virtuella datorer (VM) (inklusive Cloud Services)

- Azure Virtual Networks

- Azure SQL-tjänsten

- Partnerlösningar som är integrerad med din Azure-prenumeration, till exempel en brandvägg för webbaserade program på virtuella datorer och på [Apptjänstmiljö](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme).

### <a name="operations-management-suite"></a>Operations Management Suite

OMS programvara utvecklings- och gruppens informationssäkerhet och [styrning programmet](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) stöder affärskraven och följer lagar och förordningar enligt beskrivningen i [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) och [Microsoft Trust Center kompatibilitet](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Hur OMS upprätta säkerhetskrav, identifierar säkerhetsåtgärder, hanterar och övervakar risker beskrivs också det. Årligen, vi granska principer, standarder och procedurer som riktlinjer.

Varje medlem i gruppen OMS utveckling får formella säkerhetsutbildning. Internt, använder vi ett system för version för programutveckling. Varje projekt program är skyddat av systemet för versionskontroll.

Microsoft har ett team för säkerhet och efterlevnad som övervakar och utvärderar alla tjänster i Microsoft. Information security polis utgör teamet och de är inte kopplade till de tekniska avdelningar som utvecklar OMS. De har sina egna management kedjan och utföra oberoende bedömning av produkter och tjänster för att garantera säkerhet och efterlevnad.

Operations Management Suite (även kallat OMS) är en samling hanteringstjänster som redan från början har utformats för molnet. I stället för att distribuera och hantera lokala resurser finns helt OMS-komponenter i Azure. Konfigurationen är minimal, och du kommer igång på bara några minuter.

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Bara för att OMS-tjänsterna körs i molnet betyder det inte att de inte kan hantera din lokala miljö på ett effektivt sätt.

Placera en agent på alla Windows eller Linux-dator i datacentret och den skickar data till logganalys där den kan analyseras tillsammans med andra data som samlas in från molnet eller lokala tjänster. Använda Azure Backup och Azure Site Recovery för att utnyttja molnet för säkerhetskopiering och hög tillgänglighet för på lokala resurser.

Runbook-flöden i molnet kan normalt inte komma åt dina lokala resurser, men du kan installera en agent på en eller flera datorer som fungerar som värd för runbook-flöden i ditt datacenter. När du startar en runbook anger du om du vill att den ska köras i molnet eller på en lokal arbetsprocess.

Huvudfunktionerna i OMS tillhandahålls genom en uppsättning tjänster som körs i Azure. Varje tjänst tillhandahåller en specifik hanteringsfunktion, och du kan kombinera tjänster för olika hanteringsscenarier.

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Azure-åtgärden manager överskrider dess funktioner genom att tillhandahålla lösningar för hantering. [Hanteringslösningar](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) är färdigförpackade uppsättningar av logik som implementerar ett hanteringsscenario som utnyttjar en eller flera OMS-tjänster.

![Hantera Azure-åtgärden](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Olika lösningar är tillgängliga från Microsoft och partners. Du kan enkelt lägga till dem i din Azure-prenumeration för att öka värdet av din OMS-investering.

Som partner kan skapa du egna lösningar för att stödja dina program och tjänster och ge dem till användare via Azure Marketplace eller snabb Start mallar.

## <a name="performance-alerting-and-monitoring"></a>Prestandaaviseringar och övervakning

### <a name="alerting"></a>Aviseringar

Aviseringar är en metod för att övervaka Azure-resurs mått, händelser eller loggar och att meddelas när du anger ett villkor uppfylls.

**Aviseringar i olika Azure-tjänster**

Aviseringar är tillgängliga för olika tjänster, inklusive:

- Application Insights: Gör det möjligt för webbtestet och mått aviseringar.

>[!Note]
> Se [Ställ in aviseringar på Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) och [övervaka tillgänglighet och svarstider för alla webbplatser](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- Logganalys (Operations Management Suite): Gör routning av aktivitet och diagnostiska loggar till logganalys. Operations Management Suite kan mått, logg och andra aviseringstyper.

>[!Note]
> Mer information finns i aviseringar i [logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- Azure övervaka: Gör det möjligt för varningar baserat på både måttvärden och aktivitet logghändelser. Du kan använda den [REST-API för Azure-Monitor](https://msdn.microsoft.com/library/dn931943.aspx) att hantera aviseringar.

>[!Note]
> Mer information finns i [använder Azure-portalen, kommandoradsgränssnittet eller PowerShell för att skapa aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Övervakning

Prestandaproblem i din molnapp kan påverka din verksamhet. Med flera sammankopplade komponenter och ofta versioner kan degradations inträffa när som helst. Och om du utvecklar en app användarna identifiera vanligtvis problem som du inte kan hitta vid testning. Du bör känna till de här problemen omedelbart och har verktyg för att diagnostisera och lösa problem. Microsoft Azure har en uppsättning verktyg för att identifiera problemen.

**Hur övervakar jag min Azure-molnappar?**

Det finns ett antal verktyg för övervakning av Azure-program och tjänster. Några av sina funktioner överlappar varandra. Detta är delvis historiska skäl och delvis på grund av oskärpa mellan utveckling och drift av ett program.

Här är de viktigaste verktygen:

- **Azure-Monitor** är grundläggande verktyg för att övervaka tjänster som körs på Azure. Den ger dig infrastrukturnivå data om genomflödet av en tjänst och omgivningen. Om du hanterar dina appar i Azure, ger bestämmer dig för att skala upp eller ned resurser, sedan Azure-Monitor dig vad du använder för att starta.

- **Application Insights** kan användas för utveckling och som en produktion övervakningslösning. Det fungerar genom att installera ett paket i din app och så får du en mer interna vy över vad som händer. Data innehåller svarstider för beroenden, undantag spårning, felsökning ögonblicksbilder, körning av profiler. Den innehåller kraftfulla smarta verktyg för analys av den här telemetri både för att felsöka en app och som hjälper dig att förstå vad användarna gör med den. Du kan avgöra om är en topp i antal svarstider på grund av något i en app eller vissa externa resourcing problemet. Om du använder Visual Studio och appen är fel, kan du tas rätt att problemet raderna i koden så att du kan åtgärda den.

- **Logga Analytics** för den som behöver Finjustera prestanda och planera Underhåll på program som körs i produktion. Den är baserad i Azure. Den samlar in och samlar in data från flera källor, men med en fördröjning på 10 – 15 minuter. Det ger en heltäckande lösning för IT för Azure, lokalt och från tredje part molnbaserad infrastruktur (till exempel Amazon Web Services). Det ger bättre verktyg för att analysera data från flera källor, tillåter komplexa frågor över alla loggar och proaktivt kan meddela på angivna villkor. Du kan även samla in anpassade data till den centrala databasen så kan fråga och visualisera den.

- **System Center Operations Manager (SCOM)** är för att hantera och övervaka stora molnet installationer. Du kanske redan känner till den som ett hanteringsverktyg för lokal Windows Sever och baserat Hyper-V-moln, men den kan även integreras med och hantera Azure-appar. Bland annat installera den Application Insights på befintliga live appar. Om en app kraschar, om det i sekunder.


## <a name="next-steps"></a>Nästa steg

- [Bästa praxis för att skapa mallar för Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

- [Exempel på att implementera Azure-prenumeration styrning](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-examples).

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/).
