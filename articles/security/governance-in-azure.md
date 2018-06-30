---
title: Styrning i Azure | Microsoft Docs
description: Mer information om molnbaserade databehandling tjänster som kan skalas upp och ned för att uppfylla behoven i programmet eller enterprise.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 9c6509f25be7fe520a427e17ca1206e10f296fea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110770"
---
# <a name="governance-in-azure"></a>Styrning i Azure

Azure ger dig många säkerhetsalternativ och möjlighet att styra dem så att du kan uppfylla de specifika behoven för din organisation distributioner.

Azure-molnet styrning refererar till beslutsprocesser, villkor och principer som ingår i planeringen, arkitektur, förvärv, distribution, åtgärden och hantering av molntjänster. Azure-molnet styrning ger en integrerad granskning och rådgivning metod för att granska och ge råd organisationer på deras användning av Azure-plattformen. 

Om du vill skapa en plan för Azure-molnet styrning, måste du ta en titt på personer, processer och tekniker nu på plats. Du kan sedan skapa ramverk som gör det lättare för IT att stödja konsekvent affärsbehov och ger användarna möjlighet att använda funktioner i Azure.

## <a name="implementation-of-policies-processes-and-standards"></a>Implementering av principer, processer och standarder 

Management har upprättat roller och ansvarsområden som övervakar implementering av information säkerhetsprinciper och fungerar kontinuitet i Azure. Azure management är ansvarig för att granska säkerhet och kontinuiteten praxis inom sina respektive grupper (inklusive tredje part). Gör det också lättare efterlevnad med principer, processer och standarder.

### <a name="account-provisioning"></a>Kontoetablering

Definiera Kontohierarki är ett viktigt steg för att använda och struktur Azure-tjänster inom ett företag. Det är styrningsstrukturen kärnor. Kunder som har ett Enterprise-avtal (EA) kan du dela upp miljö till avdelningar, konton och prenumerationer.

![Kontoetablering](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Om du inte har ett Enterprise-avtal kan du använda [Azure taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) på prenumerationsnivån att definiera hierarkin. En Azure-prenumeration är den grundläggande enheten som innehåller alla resurser. Den definierar också flera begränsningar i Azure, till exempel antalet kärnor och resurser. Prenumerationer kan innehålla [resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), som kan innehålla resurser. [Rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview) principer som tillämpas på dessa tre nivåer.

Alla företag har olika. För icke-enterprise företag tillåter hierarki med hjälp av Azure taggar flexibilitet i hur Azure organiseras. Innan du distribuerar resurser i Azure bör du utforma en hierarki och förstå effekten på fakturering, åtkomst till företagsresurser och komplexitet.

### <a name="subscription-controls"></a>Prenumerationen kontroller

Prenumerationer avgör hur resursförbrukning rapporteras och debiteras. Du kan konfigurera prenumerationer för separata fakturering och betalning. En Azure-konto kan ha flera prenumerationer. Prenumerationer kan användas för att fastställa Azure resursanvändningen för flera avdelningar i ett företag.

Till exempel ett företag har IT-avdelningen, HR, och marknadsföring avdelningar, och dessa kör olika projekt. Företaget kan basera dess fakturering på varje avdelning användning av Azure-resurser, t.ex. virtuella datorer. Företaget kan sedan kontrollera ekonomi för varje avdelning.

Azure-prenumerationer upprätta tre parametrar:

- Unikt prenumerations-ID

- Plats för fakturering

- Uppsättning av tillgängliga resurser

För en enskild innehåller parametrarna en Microsoft-konto-ID, kreditkortsnummer och hela sviten för Azure-tjänster. Microsoft tillämpar förbrukning gränser, beroende på vilken prenumerationstyp.

Registrering av Azure hierarkier definiera hur tjänster är strukturerade inom ett Enterprise-avtal. Enterprise-avtal portal ger kunder möjlighet att dela åtkomst till Azure-resurser som är associerade med ett Enterprise-avtal baserat på flexibla hierarkier som kan anpassas efter organisationens behov. Hierarkin mönstret måste matcha en organisations hantering och geografiska struktur för den associera fakturerings- och åtkomsten.

De tre övergripande hierarki mönster är funktionell, affärsenhet och geografiska. Avdelningar är en administrativ konstruktion för kontot grupperingar. Inom varje avdelning kan tilldelas konton prenumerationer som skapar silor för fakturering och flera viktiga begränsningar i Azure (till exempel antal virtuella datorer och storage-konton).

![Prenumerationen kontroller](./media/governance-in-azure/security-governance-in-azure-fig2.png)


För organisationer med ett Enterprise-avtal så en hierarki i fyra nivåer i Azure-prenumerationer:

1. registreringen företagsadministratör

2. avdelning administratör

3. kontoägaren

4. Tjänstadministratör

Den här hierarkin styr följande:

- Fakturering relation.

- Administrationen.

- Åtkomst till resurser via RBAC.

- Gränser:

  - Användnings- och fakturering (priskort baserat på erbjudande siffror)

  - Begränsningar

  - Virtuellt nätverk

- Bifogad fil till Azure Active Directory (AD Azure). En Azure AD-instans kan vara associerat med många prenumerationer.

- Kopplingen med ett konto för registrering av företaget.

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) möjliggör detaljerad åtkomsthantering av resurser i Azure. Med RBAC kan bevilja du endast åtkomstnivå som användare måste utföra sitt arbete. Företag bör fokusera på att ge anställda behörigheterna exakt som de behöver. För många behörigheter exponerar ett konto för attacker. För få behörigheter innebär att anställda kan få arbetet gjort effektivt. 

Obegränsad istället för att ge alla behörigheter i din Azure-prenumeration eller resurser, kan du tillåta endast vissa åtgärder. Du kan till exempel använda RBAC så att en medarbetare som hanterar virtuella datorer i en prenumeration medan en annan medarbetare hanterar SQL-databaser i samma prenumeration.

Om du vill bevilja åtkomst, tilldela roller till användare, grupper eller program för ett visst område. Omfånget för en rolltilldelning kan vara en prenumeration, resursgrupp eller en enskild resurs. En roll som tilldelats en överordnad omfattning även ger åtkomst till underordnade som finns i den. En användare med åtkomst till en resursgrupp kan exempelvis hantera alla resurser som den innehåller som webbplatser, virtuella datorer och undernät. Du kan skapa upp till 2 000 rolltilldelningar inom varje prenumeration.

En roll är en samling behörigheter och RBAC har flera inbyggda roller. Följande inbyggda roller gäller för alla typer av resurser:

- **Ägare** har fullständig åtkomst till alla resurser, bland annat att delegera åtkomst till andra.

- **Deltagare** kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra.

- **Läsaren** kan visa alla Azure-resurser.

Resten av de inbyggda rollerna i Azure kan hanteringen av specifika Azure-resurser. Till exempel tillåter virtuella deltagarrollen användaren att skapa och hantera virtuella datorer. En lista över inbyggda roller och verksamheten finns [inbyggda RBAC-roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

RBAC stöder hanteringsåtgärder Azure-resurser i Azure-portalen och Azure Resource Manager API: er. I de flesta fall kan inte RBAC godkänna datanivå åtgärder för Azure-resurser. Information om hur RBAC utökas till dataåtgärder finns [förstå rolldefinitioner](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-definitions).

Om inbyggda roller inte uppfyller dina specifika behov, kan du [skapa en anpassad roll](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Precis som inbyggda roller kan anpassade roller tilldelas användare, grupper och program på prenumerationen, resursgruppen och resursen omfång. Du kan skapa anpassade roller med hjälp av [Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), och [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Resurshantering

Azure tillhandahåller två distributionsmodeller: [klassiska](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) och Azure Resource Manager.

I den klassiska modellen finns varje resurs oberoende av varandra. Det går inte att gruppera relaterade resurser. Du måste manuellt spåra vilka resurser som utgör din lösning eller ditt program och Kom ihåg att hantera dem på ett samordnat sätt. Den grundläggande hanteringsenheten är prenumerationen. Det är svårt att dela upp resurser inom en prenumeration, vilket leder till att skapa många prenumerationer.

Resource Manager-distributionsmodellen omfattar begreppet en [resursgruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). En resursgrupp är en behållare för resurser som har en gemensam livscykel. Det kan innefatta alla resurser för lösningen eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation.

Resource Manager-distributionsmodellen ger många fördelar:

- Du kan distribuera, hantera och övervaka alla tjänster för din lösning som en grupp i stället för att hantera tjänsterna separat.

- Du kan upprepade gånger distribuera lösningen under hela dess livscykel och vara säker på att dina resurser distribueras i ett konsekvent tillstånd.

- Du kan använda åtkomstkontroll för alla resurser i resursgruppen. Dessa principer tillämpas automatiskt när nya resurser läggs till i resursgruppen.

- Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.

- Du kan använda JSON (JavaScript Object Notation) till att definiera infrastrukturen för lösningen. JSON-filen är känd som en Resource Manager-mall.

- Du kan definiera beroenden mellan resurser så att de har distribuerats i rätt ordning.

![Resource Manager](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Rekommendationer om mallar finns i [Metodtips för att skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analyserar alla beroenden för att säkerställa att resurserna skapas i rätt ordning. Om en resurs som förlitar sig på ett värde från en annan resurs (till exempel en virtuell dator behöver ett lagringskonto för diskar), du [ange ett beroende](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) i mallen.

Du kan också använda mallen för uppdateringar av infrastrukturen. Du kan till exempel lägga till en resurs till din lösning och lägga till konfigurationsregler för resurser som redan har distribuerats. Om mallen används för att skapa en resurs, men att resursen finns redan, utför Resource Manager en uppdatering i stället för att skapa en ny tillgång. Resource Manager uppdaterar den befintliga tillgången till samma tillstånd som den skulle ha som ny.

Resource Manager tillhandahåller tillägg för scenarier när du behöver fler åtgärder, till exempel installera programvara som inte ingår i installationsprogrammet.

### <a name="resource-tracking"></a>Resursen spårning

Som användare i din organisation kan du lägga till resurser i prenumerationen, blir det viktigare vill associera resurser med rätt avdelning, kund och miljö. Du kan koppla metadata till resurser via [taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Du kan använda taggar för att ange information om resursen eller ägare. Taggar kan du inte bara sammanställa och gruppera resurser på flera sätt, men också använda dessa data för återbetalning.

Använd taggar när du har en komplex samling resursgrupper och resurser och du behöver visualisera dessa tillgångar på ett sätt som är bäst för dig. Du kan till exempel tagga resurser som har en liknande roll i organisationen eller som tillhör samma avdelning.

Utan taggar kan kan användare i din organisation skapa flera resurser som kan vara svårt att identifiera och hantera senare. Du kanske vill ta bort alla resurser för ett projekt. Om resurserna inte har taggats för projektet måste du hittar dem manuellt. Taggning kan vara ett bra sätt att minska onödiga kostnader i din prenumeration.

Resurser behöver inte finnas i samma resursgrupp att dela en tagg. Du kan skapa din egen taggtaxonomi för att säkerställa att alla användare i din organisation använder samma taggar i stället för oavsiktligt använder något annorlunda taggar (till exempel ”Avd” i stället för ”avdelning”).

Principer för företagsresurser kan du skapa standardregler för din organisation. Du kan skapa principer för att säkerställa att resurserna är märkta med lämpliga värden.

Du kan också visa taggade resurser via Azure Portal. Den [användningsrapporten](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) för din prenumeration omfattar taggnamn och värden, så du kan dela ut kostnaderna genom taggar.

Mer information om taggar finns [fakturering taggar princip initiativ](../azure-policy/scripts/billing-tags-policy-init.md).

Följande begränsningar gäller för taggar:

- Varje resurs eller en resursgrupp kan innehålla högst 15 taggen nyckel/värde-par. Den här begränsningen gäller endast för taggar som tillämpas direkt på resursgruppen eller resursen. En resursgrupp kan innehålla många resurser som har 15 taggen nyckel/värde-par.

- Taggnamnet är begränsat till 512 tecken.

- Taggvärdet är begränsat till 256 tecken.

- Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.

Om du har fler än 15 värden som du vill koppla till en resurs använder du en JSON-sträng för taggvärdet. JSON-strängen kan innehålla flera värden som tillämpas på en enskild tagg nyckel.

#### <a name="tags-for-billing"></a>Taggar för fakturering

Taggar kan du gruppera dina faktureringsinformation. Till exempel om du använder flera virtuella datorer i olika organisationer använda taggar om du vill gruppera användning av kostnadsställe. Du kan också använda taggar för att kategorisera kostnader av körningsmiljön, till exempel fakturering användning för virtuella datorer som körs i produktionsmiljön.

Du kan hämta information om taggar via den [Azure Resursanvändning och RateCard APIs](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) eller filen användning fil med kommaavgränsade värden (CSV). Du hämta filen från användning av [konton i Azure portal](https://account.windowsazure.com/) eller [EA portal](https://ea.azure.com/).

Mer information om programmatisk åtkomst till faktureringsinformationen finns [få insikter om dina Microsoft Azure-resursförbrukning](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). REST-API: et finns [Azure Billing REST API-referens](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

När du hämtar användning CSV för tjänster som stöder taggar med fakturering visas taggarna i kolumnen taggar.

### <a name="critical-resource-controls"></a>Kritisk resurskontroller

När organisationen lägger till kärntjänsterna prenumerationen, blir den mer viktigt att se till att tjänsterna är tillgängliga för att undvika avbrott i verksamheten. [Resurslås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) att du kan begränsa värdefulla resurser där ändra eller ta bort dem skulle ha en betydande inverkan på ditt program eller molninfrastruktur. Du kan använda lås för en prenumeration, resursgrupp eller resurs. Normalt kan du använda Lås i grundläggande resurser, till exempel virtuella nätverk, gatewayenheter och storage-konton.

Resurslås för närvarande stöder två värden: **CanNotDelete** och **ReadOnly**. **CanNotDelete** innebär att användare (med lämpliga behörigheter) kan fortfarande läsa eller ändra en resurs men inte ta bort den. **ReadOnly** innebär att behöriga användare kan inte ta bort eller ändra en resurs.

Resurslås gäller endast för åtgärder som sker i management-plan, som består av åtgärder som skickas till <https://management.azure.com>. Lås begränsa inte hur resurser utföra sina egna funktioner. Resursändringar är begränsad, men resursåtgärder är inte begränsade. Till exempel en **ReadOnly** lås på en SQL-databas hindrar dig från att ta bort eller ändra databasen, men det hindrar dig inte från att skapa, uppdatera eller ta bort data i databasen.

Tillämpa **ReadOnly** kan leda till oväntade resultat eftersom vissa åtgärder som ser ut som att läsa ytterligare åtgärder krävs för åtgärderna. Till exempel placera en **ReadOnly** lås på ett lagringskonto som förhindrar att alla användare lista nycklarna. Åtgärden för att visa en lista över nycklar hanteras via en POST-begäran eftersom returnerade nycklar är tillgängliga för skrivåtgärder.

![Kritisk resurskontroller](./media/governance-in-azure/security-governance-in-azure-fig5.png)

För ett annat exempel är att placera en **ReadOnly** lås på en resurs i Azure App Service som förhindrar att Visual Studio Server Explorer visar filer för resursen eftersom den interaktionen kräver skrivåtkomst.

Till skillnad från rollbaserad åtkomstkontroll använder du management Lås för att tillämpa en begränsning för alla användare och roller. Läs om hur du anger behörigheter i [hantera åtkomst med hjälp av RBAC och Azure portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

När du använder ett lås på en överordnad omfattning, ärver alla resurser i omfattningen samma låset. Även resurser som du senare lägger till ärver låset från överordnat. Det mest restriktiva låset i ärvda företräde.

Du måste ha åtkomst till Microsoft.Authorization/ eller Microsoft.Authorization/locks/ åtgärder för att skapa eller ta bort management Lås. I de inbyggda rollerna beviljas endast ägare och administratör för användaråtkomst dessa åtgärder.

### <a name="api-access-to-billing-information"></a>API-åtkomst till faktureringsinformationen

Använd Azure fakturering API: er som hämtar användnings- och data till din önskade verktyg för dataanalys. Användning av Azure och RateCard APIs kan hjälpa dig korrekt förutsäga och hantera dina kostnader. API: erna implementeras som en resursprovidern och en del av API: er som exponeras av Azure Resource Manager.

#### <a name="resource-usage-api-preview"></a>Resursanvändning API (förhandsgranskning)

Använd Azure [resurs användning API](https://msdn.microsoft.com/library/azure/mt219003) hämtar dina data uppskattade Azure förbrukningen. Detta API innehåller:

- **RBAC**: Konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com/) eller via [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration.

- **Timvis eller daglig sammanställning**: anropare kan ange om de vill sina Azure användningsdata i steg om timvis eller dagligen. Standardvärdet är varje dag.

- **Instansen metadata (inklusive resurstaggar)**: hämta instansen detaljnivåer som fullständiga resurs-URI (/subscriptions/ {prenumerations-id} /..), grupp resursinformation och resurstaggar. Dessa metadata hjälper dig att deterministiskt och genom programmering allokera användning av taggarna för användningsområden som mellan debitering.

- **Resursmetadata**: resursinformation som mätaren namn, mätaren kategori, mätaren underkategori, enhet och region ge anroparen en bättre förståelse för vad förbrukades. Vi arbetar också för att justera resurs metadata terminologi i Azure-portalen, Azure användning CSV, EA fakturering CSV och andra offentliga upplevelser, som hjälper dig att korrelera data över upplevelser.

- **Användning för alla erbjuder typer**: användningsdata är tillgängliga för alla erbjuder typer, inklusive betala per användning, MSDN, Summa, kredit och EA.

#### <a name="resource-ratecard-api"></a>Resursen RateCard API

Använd Azure Resource RateCard API för att hämta listan över tillgängliga Azure-resurser och uppskattade prisinformation för varje. Detta API innehåller:

- **RBAC**: Konfigurera principer för åtkomst på Azure-portalen eller via Azure PowerShell-cmdletar för att ange vilka användare eller program kan få åtkomst till RateCard data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen rollen Läsare, ägare eller deltagare för att få åtkomst till användningsdata för en viss Azure-prenumeration.

- **Stöd för betala per användning, MSDN, Summa och kredit erbjudanden (men inte EA)**: Detta API ger Azure erbjudande nivå hastighet information. Anroparen av denna API måste klara i erbjudandet information för att hämta resursinformation och priser. EA stöds inte för närvarande eftersom EA erbjudanden har anpassat kostnader per registrering. 

#### <a name="scenarios"></a>Scenarier

En kombination av användnings- och RateCard APIs gör det möjligt att dessa scenarier:

- **Förstå Azure utgifter under månaden**: använda en kombination av användningen och RateCard APIs få bättre insikter om ditt moln utgifter under månaden. Du kan analysera varje timme och dagliga användnings- och kostnad uppskattningar.

- **Ställa in aviseringar**: använda användnings- och RateCard APIs för att hämta uppskattade molnet förbrukning och kostnader och konfigurera resurs- eller monetära baserade aviseringar.

- **Förutsäga faktura**: Get uppskattade användnings- och molnet tillbringar och tillämpa maskininlärningsalgoritmer för att förutsäga växeln skulle vara i slutet av faktureringsperioden.

- **Utföra en före förbrukning kostnad analysis**: Använd RateCard API för att förutse hur mycket din faktura är för din förväntade användning när du flyttar dina arbetsbelastningar till Azure. Om du har befintliga arbetsbelastningar i andra moln eller privata moln, kan du även mappa förbrukningen i Azure tillbringar priser för att få en bättre uppfattning av Azure. Denna uppskattning ger dig möjlighet att pivotera på erbjudandet och jämför olika erbjudandetyper utöver betala per användning som summa och kredit.

- **Utföra en konsekvensanalys**: du kan fastställa om det är mer kostnadseffektivt att köra arbetsbelastningar i en annan region eller på en annan konfiguration av Azure-resurs. Azure resurskostnader kan variera beroende på Azure-regionen du använder. Du kan också bestämma om en annan typ av Azure-erbjudande ger en bättre hastighet på Azure-resurs.

### <a name="networking-controls"></a>Kontroller för nätverk

Åtkomst till resurser kan vara antingen interna (inom företagets nätverk) eller externa (via internet). Det är enkelt för användare i din organisation att oavsiktligt placera resurser i fel plats och öppna dem potentiellt skadliga åtkomst. Företag måste lägga till lämpliga kontroller för att säkerställa att Azure användare fatta rätt beslut som lokala enheter.

![Kontroller för nätverk](./media/governance-in-azure/security-governance-in-azure-fig6.png)

För prenumerationen styrningen ger följande kärnresurser grundläggande kontroll av åtkomst.

#### <a name="network-connectivity"></a>Nätverksanslutning

[Virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) är behållarobjekt för undernät. Även om det inte är absolut nödvändigt, används ofta ett virtuellt nätverk för att ansluta program till interna företagsresurser. Tjänsten Azure Virtual Network kan du ansluta Azure-resurser på ett säkert sätt till varandra med virtuella nätverk.

Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk isolering av Azure-molnet dedikerad till din prenumeration. Du kan också ansluta virtuella nätverk till ditt lokala nätverk.

Följande är funktioner för virtuella Azure-nätverk:

- **Isolering**: virtuella nätverk är isolerade från varandra. Du kan skapa separata virtuella nätverk för utveckling, testning och produktion som använder samma CIDR-Adressblock. Däremot kan du skapa flera virtuella nätverk som använder olika CIDR-Adressblock och ansluta nätverk tillsammans. Du kan dela ett virtuellt nätverk i flera undernät. Azure erbjuder intern namnmatchning för virtuella datorer och Azure Cloud Services rollinstanser som är anslutna till ett virtuellt nätverk. Du kan också konfigurera ett virtuellt nätverk för att använda dina egna DNS-servrar i stället för intern namnmatchning för Azure.

- **Internetanslutning**: alla Azure-datorer och molntjänster rollinstanser som är anslutna till ett virtuellt nätverk har åtkomst till internet, som standard. Du kan också aktivera inkommande åtkomst till specifika resurser efter behov.

- **Azure-resurs anslutningen**: Azure-resurser, till exempel molntjänster och virtuella datorer kan anslutas till samma virtuella nätverk. Resurserna kan ansluta till varandra via privata IP-adresser, även om de inte finns i olika undernät. Azure tillhandahåller standardroutning mellan undernät, virtuella nätverk och lokala nätverk, så du behöver att konfigurera och hantera vägar.

- **Virtuell nätverksanslutning**: du kan ansluta virtuella nätverk till varandra. Resurser som är anslutna till något virtuellt nätverk kan sedan kommunicera med en resurs på andra virtuella nätverket.

- **Lokal anslutning**: du kan ansluta virtuella nätverk till lokala nätverk via privata nätverksanslutningar mellan ditt nätverk och Azure, eller en plats-till-plats virtuellt privat nätverk (VPN)-anslutning via internet.

- **Trafikfiltrering**: du kan filtrera nätverkstrafik (inkommande och utgående) för virtuella datorer och molntjänster genom källans IP-adress och port, mål-IP-adress och port och protokoll.

- **Routning**: du kan om du vill åsidosätta standardvärdet för Azure routning genom att konfigurera egna vägar eller genom att använda BGP-vägar via en nätverksgateway.

#### <a name="network-access-controls"></a>Åtkomstkontroller för nätverk

[Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG: er) är som en brandvägg och ange regler för hur en resurs kan ”prata” över nätverket. De ger kontroll över hur ett undernät (eller en virtuell dator) kan ansluta till internet eller andra undernät i samma virtuella nätverk.

En nätverkssäkerhetsgrupp innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk. NSG: er kan associeras med undernät, enskilda virtuella datorer (klassiskt) eller enskilda nätverksgränssnitt (NIC) kopplad till virtuella datorer (Resource Manager).

När en NSG är associerad med ett undernät, tillämpas reglerna till alla resurser som är anslutna till undernätet. Du kan begränsa trafik genom att koppla en NSG till en VM eller nätverkskort.

## <a name="security-and-compliance-with-organizational-standards"></a>Säkerhet och kompatibilitet med organisationens normer

Alla företag har olika behov och kan dra nytta av distinkta fördelar från molnlösningar. Kunder för alla typer har fortfarande samma grundläggande frågor om hur du flyttar till molnet. Kunder vill från molntjänstleverantörer är:

- **Skydda våra data**: IT-företag som bekräftar att molnet kan ge ökad säkerhet och administrativ kontroll. Men de är fortfarande berörda att migrera till molnet lämnar dem mer sårbara för hackare än deras aktuella interna lösningar.

- **Hålla våra data privat**: molntjänster Generera en unik sekretess utmaningar. Som företag ser till att spara på kostnader med infrastruktur och förbättra deras flexibilitet molnet, behöver de också förlora kontroll över där deras data lagras, vem som åtkomst till den och hur den används.

- **Ge oss kontrollen**: även om företag dra nytta av molnet för att distribuera flera innovativa lösningar, de är orolig för att förlora kontroll över sina data. De senaste upplysningarna hos myndigheter som har åtkomst till kundens data på både juridiska och utomrättsliga sätt göra vissa IT-chefer försiktig med att lagra sina data i molnet.

- **Befordra genomskinlighet**: företagets beslutsfattare förstår vikten av säkerhet, sekretess och kontroll. Men de vill också möjlighet att oberoende kontrollera hur data lagras, ofta och säkra.

- **Upprätthålla överensstämmelse**: företag Expandera användningen av molntekniker, komplexitet och omfattningen av standarder och föreskrifter fortsätta att utvecklas. Företag måste du veta att deras efterlevnadsstandarder uppfylls.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Säkerhetskonfiguration för övervakning, loggning och granskning

Azure-prenumeranter kan hantera sina molnmiljöer från flera enheter. Dessa enheter kan innehålla hantering av arbetsstationer, utvecklardatorer och även Privilegierade slutanvändarens enheter som har uppgiftsspecifika behörigheter. 

I vissa fall kan utförs administrativa funktioner via webbaserade konsoler som Azure-portalen. I andra fall kan finnas det direkta anslutningar till Azure från lokala system över VPN-anslutningar, Terminal Services, klientprotokoll för program eller (programmässigt) Azure Service Management API (SMAPI). Dessutom kan klientslutpunkter vara antingen domänanslutna eller isolerade och ohanterade, som surfplattor eller smartphones-domän.

Den här variationen innebära betydande risk för en molndistribution. Det kan vara svårt att hantera, spåra och granska administrativa åtgärder. Den här variationen kan också introducera säkerhetshot via oreglerad åtkomst till klientslutpunkter som används för att hantera molntjänster. Med hjälp av allmänna eller personliga arbetsstationer för att utveckla och hantera infrastrukturen öppnas oförutsägbara hotvektorer, till exempel webbsurfning (t.ex. vattenhålsattacker) eller mejl (t.ex. social manipulation och nätfiskewebbplatser).

Övervakning, loggning och granskning ger en grund för att spåra och förstå administrativa aktiviteter. Granska alla åtgärder i detalj kanske inte alltid möjligt på grund av mängden data som genereras. Men hanteringsprinciperna principer för hantering av bästa praxis.

Azure-säkerhet styrning från Azure Active Directory Domain Services (AD DS) grupprincipobjekt hjälper dig att styra alla administratörens Windows-gränssnitt, till exempel fildelning. Inkludera hanteringsdatorer i övervakning, loggning och granskning processer. Spåra alla administratörers och utvecklares åtkomst och användning.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) innehåller en central för säkerhetsstatusen för resurserna i prenumerationer. Det ger rekommendationer som kan förhindra angripna resurser. Det kan aktivera mer detaljerad principer--exempelvis tillämpa principer på specifika resursgrupper som gör att företag kan anpassa sina hållningsdata för risk som de adressering.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center innehåller integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer, och upptäcka hot som annars kanske skulle förbli oupptäckta, fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar. När du har aktiverat [säkerhetsprinciper](https://docs.microsoft.com/azure/security-center/security-center-policies) för en prenumeration resurser, Security Center analyserar säkerheten för dina resurser för att identifiera potentiella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt.

Azure Security Center representerar en kombination av bästa praxis analys- och principhantering för alla resurser i en Azure-prenumeration. Aktiverar säkerhet team och risker antal för att förebygga, upptäcka och svara på säkerhetshot som automatiskt samlar in och analyserar säkerhetsdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar.

Dessutom analysteknik Azure Security Center avancerad, inklusive machine learning och beteendeanalys. Den använder globala hotinformation från Microsoftprodukter och tjänster, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa flöden. Du kan använda [säkerhet styrning](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) brett på prenumerationsnivån. Eller så kan du begränsa den till specifika krav och koppla dem till enskilda resurser via principdefinitionen.

Slutligen Azure Security Center analyserar resurssäkerhetshälsa baserat på de principerna och använder informationen för att ange insiktsfulla instrumentpaneler och avisering för händelser, t.ex identifiering av skadlig kod eller skadliga IP-anslutning försök. Mer information om hur du utför rekommendationerna finns [utföra säkerhetsrekommendationerna i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Azure Security Center övervakar följande Azure-resurser:

- Virtuella datorer (VM) (inklusive molntjänster)

- Virtuella nätverk

- SQL-databaser

- Partnerlösningar som är integrerad med din Azure-prenumeration, till exempel en brandvägg för webbaserade program på virtuella datorer och på den [Apptjänstmiljö](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

Första gången du öppnar Security Center, är insamling av data aktiverat på alla virtuella datorer i din prenumeration. Vi rekommenderar att du behåller insamling av data aktiverad, men du kan [inaktivera det](https://docs.microsoft.com/azure/security-center/security-center-faq) i Security Center-princip.

### <a name="log-analytics"></a>Log Analytics

Azure logganalys programvara utvecklings- och gruppens informationssäkerhet och [styrning programmet](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) stöder affärskraven. Det följer lagar och förordningar enligt beskrivningen i [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) och [Microsoft förtroende Center kompatibilitet](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Hur logganalys upprättar säkerhetskrav, identifierar säkerhetskontroller och hanterar och övervakar risker beskrivs också det. Teamet granskar årligen, principer, standarder, procedurer och riktlinjer.

Varje medlem i gruppen logganalys utveckling får formella säkerhetsutbildning. Ett system för version skyddar varje programvara projekt under utveckling.

Microsoft har ett team för säkerhet och efterlevnad som övervakar och utvärderar alla tjänster i Microsoft. Information security polis utgör teamet och de är inte associerad med engineering avdelningar som utvecklar logganalys. Säkerhet polis har sina egna management-kedjan. De genomföra oberoende bedömning av produkter och tjänster för att garantera säkerhet och efterlevnad.

Huvudfunktionerna i logganalys tillhandahålls av en uppsättning tjänster som körs i Azure. Varje tjänst tillhandahåller en specifik hanteringsfunktion, och du kan kombinera tjänster för olika hanteringsscenarier.

![Azure-tjänster för hantering](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Dessa tjänster har utformats i molnet. De är helt finns i Azure, så att de inte inkluderar distribuera och hantera lokala resurser. Konfigurationen är minimal och du kan vara igång på bara några minuter.

Placera en agent på alla Windows- eller Linux-datorer i ditt datacenter och den skickar data till logganalys. Det, kan analyseras tillsammans med andra data som samlas in från molnet eller lokala tjänster. Använda Azure Backup och Azure Site Recovery för att dra nytta av molnet för säkerhetskopiering och hög tillgänglighet för lokala resurser.

![Tjänster på instrumentpanelen för Azure](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Runbooks i molnet normalt inte kan komma åt lokala resurser, men du kan installera en agent på en eller flera datorer som är värd för runbooks i ditt datacenter. När du startar en runbook kan ange du om du vill att den körs i molnet eller på en lokal worker.

Olika lösningar är tillgängliga från Microsoft och att du kan lägga till din Azure-prenumeration att öka värdet på din investering i logganalys-partner. Till exempel Azure erbjuder [hanteringslösningar](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)--färdigförpackade uppsättningar av logik som implementerar ett hanteringsscenario med hjälp av en eller flera tjänster.

![Galleriet med hanteringslösningar i Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Som partner kan skapa du egna lösningar för att stödja dina program och tjänster och ge dem till användare via Azure Marketplace eller Quickstart mallar.

## <a name="performance-alerting-and-monitoring"></a>Prestandaaviseringar och övervakning

### <a name="alerting"></a>Aviseringar

Aviseringar är en metod för övervakning mätvärden i Azure-resurs, händelser eller loggar. De meddela dig när en som du har angett är uppfyllt.

Aviseringar är tillgänglig över tjänster, inklusive:

- **Azure Application Insights**: aktiverar web test och mått aviseringar. Mer information finns i [Ställ in aviseringar på Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) och [övervaka tillgänglighet och svarstider för alla webbplatser](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Logga Analytics**: aktiverar Routning av aktivitet och diagnostiska loggar till logganalys. Det gör mått, logg och andra aviseringstyper. Mer information finns i [aviseringar i logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Azure-Monitor**: gör varningar baserat på både måttvärden och aktivitet logghändelser. Du kan använda den [REST-API för Azure-Monitor](https://msdn.microsoft.com/library/dn931943.aspx) att hantera aviseringar. Mer information finns i [använder Azure-portalen, kommandoradsgränssnittet eller PowerShell för att skapa aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Övervakning

Prestandaproblem i din molnapp kan påverka din verksamhet. Med flera sammankopplade komponenter och ofta versioner kan degradations inträffa när som helst. Och om du utvecklar en app användarna identifiera vanligtvis problem som du inte kan hitta vid testning. Du bör känna till de här problemen omedelbart och har verktyg för att diagnostisera och åtgärda dem.

Det finns ett antal verktyg för övervakning av Azure-program och tjänster. Några av sina funktioner överlappar varandra. Detta beror delvis på oskärpa mellan utveckling och drift av ett program.

Här är de viktigaste verktygen:

- **Azure-Monitor** är ett grundläggande verktyg för att övervaka tjänster som körs på Azure. Den ger dig infrastrukturnivå data om genomflödet av en tjänst och omgivningen. Om du hanterar dina appar i Azure och bestämmer dig för att skala upp eller ned resurser, hjälper dig att starta Azure-Monitor.

- **Application Insights** kan användas för utveckling och som en produktion övervakningslösning. Det fungerar genom att installera ett paket i din app så att den ger dig en mer interna vy över vad som händer. Data innehåller svarstider för beroenden, undantag spårning, felsökning ögonblicksbilder och körning av profiler. Den innehåller verktyg för analys av den här telemetri både för att felsöka en app och som hjälper dig att förstå vad användarna gör med den. Du kan avgöra om är en topp i antal svarstider på grund av något i en app eller vissa externa resourcing problemet. Om du använder Visual Studio och appen är fel kan du gå direkt till problemet kodrad så att du kan åtgärda den.

- **Logga Analytics** för den som behöver Finjustera prestanda och planera Underhåll på program som körs i produktion. Den samlar in och samlar in data från flera källor med en fördröjning på 10 – 15 minuter. Det ger en heltäckande lösning för IT för Azure, lokalt och från tredje part molnbaserad infrastruktur (till exempel Amazon Web Services). Den innehåller verktyg för att analysera data från källor, tillåter komplexa frågor över alla loggar och proaktivt kan meddela på angivna villkor. Du kan även samla in anpassade data på den centrala lagringsplatsen, och sedan fråga och visualisera data.

- **System Center Operations Manager** är för att hantera och övervaka stora molnet installationer. Du kanske redan känner till den som ett hanteringsverktyg för lokala Windows Server och Hyper-V baserat moln, men den kan även integreras med och hantera Azure-appar. Bland annat installera den Application Insights på befintliga live appar. Om en app kraschar om Operations Manager i sekunder.


## <a name="next-steps"></a>Nästa steg

- [Metodtips för att skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Exempel på att implementera styrning av Azure-prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/)
