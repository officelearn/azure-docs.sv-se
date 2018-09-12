---
title: Styrning i Azure | Microsoft Docs
description: Läs mer om molnbaserad databehandling tjänster som kan skalas upp och ned för att uppfylla behoven i ditt program eller enterprise.
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
ms.openlocfilehash: 6e5b6fac25c8c7f76991a58fbcab363c6fc20f12
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380360"
---
# <a name="governance-in-azure"></a>Styrning i Azure

Azure ger dig många säkerhetsalternativ samt möjligheten att kontrollera dem så att du kan uppfylla de specifika behoven för din organisations distributioner.

Azure-molnet styrning refererar till beslutsprocesser, villkor och principer som ingår i planeringen, arkitektur, förvärv, distribution, åtgärden och hantering av molnbaserad databehandling. Azure-molnet styrning tillhandahåller en integrerad gransknings- och konsult metod för att granska och rådgivning till organisationer på deras användning av Azure-plattformen. 

Om du vill skapa en plan för styrning i Azure-molnet, måste du ta en titt på personer, processer och teknik nu på plats. Sedan kan du skapa ramverk som gör det lättare för IT att stödja konsekvent affärsbehov och ger användarna möjlighet att använda funktioner i Azure.

## <a name="implementation-of-policies-processes-and-standards"></a>Implementering av principer, processer och standarder 

Management har upprättat roller och ansvarsområden som övervakar implementering av säkerhetsprinciper för information och kontinuiteten i Azure. Azure management är ansvarig för att granska säkerhets- och affärskontinuitet metoder inom sina respektive grupper (inklusive tredje part). Det underlättar också efterlevnad med principer, processer och standarder.

### <a name="account-provisioning"></a>Kontoetablering

Definiera Kontohierarki är ett viktigt steg för att använda och strukturera Azure-tjänster inom ett företag. Det är styrningsstrukturen core. Kunder som har ett Enterprise Agreement (EA) kan du dela upp miljö till avdelningar, konton och prenumerationer.

![Kontoetablering](./media/governance-in-azure/security-governance-in-azure-fig1.png)

Om du inte har ett Enterprise-avtal kan du använda [Azure taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) på prenumerationsnivå att definiera hierarkin. En Azure-prenumeration är den grundläggande enheten som innehåller alla resurser. Den definierar också flera begränsningar i Azure, till exempel hur många kärnor och resurser. Prenumerationer kan innehålla [resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), som kan innehålla resurser. [Rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) principer som gäller på dessa tre nivåer.

Alla företag är olika. För icke-enterprise företag kan hierarkin med hjälp av Azure taggar för flexibilitet i hur Azure organiseras. Innan du distribuerar resurser i Azure kan du modellera en hierarki och förstå effekten på fakturering, åtkomst till resurser och komplexitet.

### <a name="subscription-controls"></a>Kontroller för prenumeration

Prenumerationer avgör hur Resursanvändning rapporteras och faktureras. Du kan ställa in prenumerationer för separata fakturering och betalning. Ett Azure-konto kan ha flera prenumerationer. Prenumerationer kan användas för att fastställa Azure resursanvändningen för flera avdelningar i ett företag.

Exempelvis kan ett företag har IT-avdelningen, HR, och marknadsföring avdelningar, och dessa kör olika projekt. Företaget kan basera dess fakturering på varje avdelning användning av Azure-resurser, som virtuella datorer. Företaget kan därefter kontrollera ekonomi för varje avdelning.

Azure-prenumerationer upprätta tre parametrar:

- Unika prenumerations-ID

- Plats för fakturering

- Uppsättningen med tillgängliga resurser

För en individ innehåller de här parametrarna ett Microsoft-konto-ID, kreditkortsnummer och fullständig uppsättning Azure-tjänster. Microsoft tillämpar förbrukning gränser, beroende på vilken prenumerationstyp.

Azure-registrering hierarkier definierar hur tjänster är strukturerade i ett Enterprise-avtal. Enterprise Agreement-portalen ger kunder möjlighet att dela åtkomst till Azure-resurser som är associerade med ett Enterprise-avtal som är baserad på flexibel hierarkier som kan anpassas efter organisationens behov. Hierarkin mönstret måste matcha en organisations hantering och geografiska struktur att kompensera för den associera fakturerings- och åtkomsten.

De tre övergripande hierarki mönster är funktionella, affärsenhet, och geografiska. Avdelningar är en administrativ-konstruktion för kontogrupper. Inom varje avdelning konton kan du tilldela prenumerationer, som skapar silor för fakturering och flera viktiga begränsningar i Azure (till exempel antal virtuella datorer och storage-konton).

![Kontroller för prenumeration](./media/governance-in-azure/security-governance-in-azure-fig2.png)


Följ en hierarki i fyra nivåer för organisationer med ett Enterprise Agreement Azure-prenumerationer:

1. Företagsadministratör för registrering

2. Avdelningsadministratör

3. Kontoägare

4. Tjänstadministratör

Den här hierarkin styr följande:

- Fakturering relation.

- Kontoadministrationen.

- Åtkomst till resurser via RBAC.

- Gränser:

  - Användning och fakturering (priskort baserat på erbjudandet siffror)

  - Begränsningar

  - Virtuellt nätverk

- Bifogad fil till Azure Active Directory (AD Azure). En Azure AD-instans kan associeras med många prenumerationer.

- Kopplingen till ett företagskonto för registrering.

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) möjliggör detaljerad åtkomsthantering av resurser i Azure. Med RBAC kan bevilja du endast mängden åtkomst som användare behöver att utföra sitt arbete. Företag bör fokusera på att ge anställda de exakta behörigheter som de behöver. För många behörigheter exponera ett konto för att angripare. För få behörigheter innebär att anställda inte kan utföra sitt arbete effektivt. 

Istället för att ge alla obegränsad behörighet i din Azure-prenumeration eller resurser kan du tillåta enbart vissa åtgärder. Du kan till exempel använda RBAC för att låta en anställd hantera virtuella datorer i en prenumeration, medan en annan medarbetare hanterar SQL-databaser i samma prenumeration.

Att bevilja åtkomst måste du tilldela roller till användare, grupper eller program för ett visst omfång. Omfattningen för en rolltilldelning kan vara en prenumeration, en resursgrupp eller en enskild resurs. En roll som tilldelats på en överordnad omfattning ger också åtkomst till de underordnade objekten som ingår i detta. En användare med åtkomst till en resursgrupp kan exempelvis hantera alla resurser som den innehåller, som webbplatser, virtuella datorer och undernät. Du kan skapa upp till 2 000 rolltilldelningar inom varje prenumeration.

En roll är en uppsättning behörigheter och RBAC har flera inbyggda roller. Följande inbyggda roller som gäller för alla typer av resurser:

- **Ägare** har fullständig åtkomst till alla resurser, bland annat att delegera åtkomst till andra.

- **Deltagare** kan skapa och hantera alla typer av Azure-resurser, men det går inte att bevilja åtkomst till andra.

- **Läsare** kan visa alla Azure-resurser.

Resten av de inbyggda rollerna i Azure kan hanteringen av specifika Azure-resurser. Exempelvis kan gör rollen virtuell Datordeltagare att användaren att skapa och hantera virtuella datorer. En lista över inbyggda roller och sin verksamhet, se [RBAC inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

RBAC stöd för hanteringsåtgärder för Azure-resurser i Azure-portalen och Azure Resource Manager API: er. I de flesta fall behörighet RBAC datanivå åtgärder för Azure-resurser. Information om hur RBAC utökas till dataåtgärder finns [förstå rolldefinitioner](https://docs.microsoft.com/azure/role-based-access-control/role-definitions).

Om de inbyggda rollerna inte uppfyller dina specifika behov, kan du [skapa en anpassad roll](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Precis som inbyggda roller, kan anpassade roller tilldelas användare, grupper och program på prenumerationen, resursgruppen och resursen omfång. Du kan skapa anpassade roller med hjälp av [Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), och [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

### <a name="resource-management"></a>Resurshantering

Azure har två distributionsmodeller: [klassiska](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) och Azure Resource Manager.

I den klassiska modellen finns varje resurs separat. Det går inte att gruppera relaterade resurser. Du måste manuellt spåra vilka resurser utgör din lösning eller ditt program och Kom ihåg att hantera dem på ett samordnat sätt. Den grundläggande hanteringsenheten är prenumerationen. Det är svårt att bryta ned resurser inom en prenumeration, vilket leder till skapande av stora mängder prenumerationer.

Distributionsmodellen för Resource Manager innehåller konceptet en [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). En resursgrupp är en container för resurser som har en gemensam livscykel. Den kan innehålla alla resurser för lösningen eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation.

Resource Manager-distributionsmodellen ger många fördelar:

- Du kan distribuera, hantera och övervaka alla tjänster för din lösning som en grupp i stället för att hantera tjänsterna separat.

- Du kan upprepade gånger distribuera lösningen under dess livscykel och vara säker på att resurserna distribueras i ett konsekvent tillstånd.

- Du kan använda åtkomstkontroll för alla resurser i resursgruppen. Dessa principer tillämpas automatiskt när nya resurser läggs till i resursgruppen.

- Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.

- Du kan använda JSON (JavaScript Object Notation) till att definiera infrastrukturen för lösningen. JSON-filen är känd som en Resource Manager-mall.

- Du kan definiera beroenden mellan resurserna så att de har distribuerats i rätt ordning.

![Resource Manager](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Rekommendationer om mallar finns i [Metodtips för att skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices).

Azure Resource Manager analyserar beroenden för att säkerställa att resurserna skapas i rätt ordning. Om en resurs bygger på ett värde från en annan resurs (till exempel en virtuell dator behöver ett lagringskonto för diskar), du [ett beroende](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies) i mallen.

Du kan också använda mallen för uppdateringar av infrastrukturen. Du kan till exempel lägga till en resurs till din lösning och lägga till konfigurationsregler för resurser som redan har distribuerats. Om mallen används för att skapa en resurs men resursen redan finns, utför Resource Manager en uppdatering i stället för att skapa en ny tillgång. Resource Manager uppdaterar den befintliga tillgången till samma tillstånd som den skulle ha som ny.

Resource Manager tillhandahåller tillägg för scenarier när du behöver fler åtgärder, till exempel installera programvara som inte ingår i installationsprogrammet.

### <a name="resource-tracking"></a>Resursspårning

När användare i organisationen lägger till resurser i prenumerationen, blir det viktigare att associera resurser med rätt avdelning, kunden och miljö. Du kan koppla metadata till resurser via [taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags). Du kan använda taggar för att ange information om resursen eller ägare. Taggar kan du inte bara sammanställa och grupperar resurser på flera olika sätt, men också använda dessa data för återbetalning.

Använd taggar när du har en komplex samling resursgrupper och resurser och du behöver visualisera dessa tillgångar på ett sätt som passar bäst för dig. Du kan till exempel tagga resurser som har en liknande roll i organisationen eller som tillhör samma avdelning.

Utan taggar kan kan användare i organisationen skapa flera resurser som kan vara svårt att identifiera och hantera längre fram. Du kanske exempelvis vill ta bort alla resurser för ett projekt. Om resurserna inte är taggade för projektet måste du hitta dem manuellt. Taggning kan vara ett bra sätt att minska onödiga kostnader i din prenumeration.

Resurser behöver inte finnas i samma resursgrupp att dela en tagg. Du kan skapa din egen taggtaxonomi för att säkerställa att alla användare i din organisation använder samma taggar i stället för oavsiktligt tillämpa något annorlunda taggar (till exempel ”Avd” i stället för ”avdelning”).

Resursprinciper kan du skapa standardregler för din organisation. Du kan skapa principer för att säkerställa att resurser är taggade med lämpliga värden.

Du kan också visa taggade resurser via Azure Portal. Den [användningsrapporten](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) för din prenumeration innehåller taggnamn och -värden, så du kan dela upp kostnader efter taggar.

Mer information om taggar finns i [principinitiativ](../azure-policy/scripts/billing-tags-policy-init.md).

Följande begränsningar gäller för taggar:

- Varje resurs eller resursgrupp kan innehålla högst 15 taggen nyckel/värde-par. Den här begränsningen gäller endast för taggar som tillämpas direkt på resursgruppen eller resursen. En resursgrupp kan innehålla många resurser som var och en har 15 taggen nyckel/värde-par.

- Taggnamnet är begränsat till 512 tecken.

- Taggvärdet är begränsat till 256 tecken.

- Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.

Om du har fler än 15 värden som du vill koppla till en resurs använder du en JSON-sträng för taggvärdet. JSON-strängen kan innehålla många värden som tillämpas på en enskild tagg-nyckel.

#### <a name="tags-for-billing"></a>Taggar för fakturering

Taggarna gör att du kan gruppera faktureringsinformation. Till exempel om du använder flera virtuella datorer i olika organisationer kan använda taggar arbetsgrupper per kostnadsställe. Du kan också använda taggar för att kategorisera kostnader genom att runtime-miljö, till exempel den fakturering användningen av virtuella datorer som körs i produktionsmiljön.

Du kan hämta information om taggar via den [Azure användning och RateCard APIs](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) eller användningsfil för fil med kommaavgränsade värden (CSV). Du ladda ned användningsfilen från den [Azure-konton portal](https://account.windowsazure.com/) eller [EA-portalen](https://ea.azure.com/).

Läs mer om programmatisk åtkomst till faktureringsinformation [insyn i din Microsoft Azure-resursförbrukning](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview). REST API-åtgärder, se [Azure Billing REST API-referens](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

När du laddar ned användning CSV för tjänster som stöder taggar med debitering visas taggarna i kolumnen taggar.

### <a name="critical-resource-controls"></a>Kritisk resurskontroller

När organisationen lägger till viktiga tjänster i prenumerationen, blir det viktigare att se till att dessa tjänster är tillgängliga för att undvika verksamhetsavbrott i. [Resurslås](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) gör det möjligt att begränsa åtgärder på värdefulla resurser där ändra eller ta bort dem skulle ha en betydande inverkan på ditt program eller molninfrastruktur. Du kan använda lås för en prenumeration, resursgrupp eller resurs. Normalt kan du använda Lås att grundläggande resurser, till exempel virtuella nätverk, gatewayer och storage-konton.

Resurslås stöder för närvarande två värden: **CanNotDelete** och **ReadOnly**. **CanNotDelete** innebär att användare (med lämpliga behörigheter) kan fortfarande läsa eller ändra en resurs men det går inte att ta bort den. **Skrivskyddad** innebär att behöriga användare kan inte ta bort eller ändra en resurs.

Resurslås gäller endast för åtgärder som sker i Hanteringsplanet, som består av åtgärder som skickas till <https://management.azure.com>. Låsen begränsa inte hur resurser utföra egna funktioner. Resursändringar är begränsade, men resursåtgärder är inte begränsade. Till exempel en **ReadOnly** lås på en SQL database gör att du inte tar bort eller ändrar databasen, men den förhindrar inte att du inte skapa, uppdatera eller ta bort data i databasen.

Tillämpa **ReadOnly** kan leda till oväntade resultat eftersom vissa åtgärder som verkar vara läsa åtgärder kräver ytterligare åtgärder. Till exempel placera en **ReadOnly** låset på ett lagringskonto som förhindrar att alla användare lista nycklarna. Åtgärden för att visa en lista över nycklar hanteras via en POST-begäran eftersom de returnerade nycklarna är tillgängliga för skrivåtgärder.

![Kritisk resurskontroller](./media/governance-in-azure/security-governance-in-azure-fig5.png)

För ett annat exempel är att placera en **ReadOnly** lås på en Azure App Service-resurs som förhindrar att Visual Studio Server Explorer visar filer för resursen eftersom den interaktionen kräver skrivbehörighet.

Till skillnad från rollbaserad åtkomstkontroll använder du hanteringslås för att tillämpa en begränsning för alla användare och roller. Läs om hur du anger behörigheter i [hantera åtkomst med RBAC och Azure portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

När du använder ett lås på en överordnad omfattning, ärver alla resurser i detta omfång samma låset. Även resurser som du lägger till senare ärver låset från överordnat. Den mest restriktiva Lås i arvet företräde.

Du måste ha åtkomst till Microsoft.Authorization/ eller Microsoft.Authorization/locks/ åtgärder för att skapa eller ta bort hanteringslås. Av de inbyggda rollerna beviljas endast ägare och administratör för användaråtkomst dessa åtgärder.

### <a name="api-access-to-billing-information"></a>API-åtkomst till faktureringsinformation

Använda Azure Billing API: er att hämta användnings- och data till din önskade analysverktyg. Azure Resource-användning och RateCard APIs kan hjälpa dig att korrekt förutse och hantera dina kostnader. API: er implementeras som en provider för nätverksresurser och en del av familjen av API: er som exponeras av Azure Resource Manager.

#### <a name="resource-usage-api-preview"></a>Resursanvändning API (förhandsversion)

Använd Azure [Resource användning API](https://msdn.microsoft.com/library/azure/mt219003) att hämta uppskattad Azure-förbrukning-data. API: et innehåller:

- **RBAC**: Konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com/) eller via [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration.

- **Timvis eller daglig sammanställning**: anropare kan ange om de vill sina data med Azure-användning i steg om per timme eller dag. Standardvärdet är varje dag.

- **Instans-metadata (inklusive resurstaggar)**: hämta instansnivå information som fullständigt kvalificerade resurs-URI (/subscriptions/ {prenumerations-id} /...), information om resursen och resurstaggar. Dessa metadata kan du deterministiskt och programmässigt allokera användning av taggarna för användningsfall som cross-debitering.

- **Resursmetadata**: resursinformation som mätningsnamn, underkategori, underkategori för mätning, enhet och region ger anroparen en bättre förståelse för vad som förbrukades. Vi arbetar även om du vill justera resursen metadata terminologi för Azure-portalen, Azure-användning CSV, EA fakturering CSV och andra offentliga upplevelser för att korrelera data över upplevelser.

- **Användning för alla erbjuder typer**: användningsdata är tillgänglig för alla erbjuder typer, inklusive betala per användning, MSDN, monetära åtaganden, kredit och EA.

#### <a name="resource-ratecard-api"></a>Resursen RateCard API

Använda Azure Resource RateCard-API: et för att hämta listan över tillgängliga Azure-resurser och uppskattade prisinformationen för varje. API: et innehåller:

- **RBAC**: konfigurera dina åtkomstprinciper på Azure portal eller via Azure PowerShell-cmdletar för att ange vilka användare eller program kan få åtkomst till RateCard-data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till rollen Läsare, ägare eller deltagare för att få åtkomst till användningsdata för en viss Azure-prenumeration.

- **Stöd för betala per användning, MSDN, monetära åtaganden och kredit erbjudanden (men inte EA)**: den här API: et tillhandahåller Azure erbjudandet på servernivå rate information. Anroparen av detta API måste klara i erbjudandeinformation att få information om resursen och priser. EA stöds för närvarande inte eftersom EA erbjudanden har anpassat kostnader per registrering. 

#### <a name="scenarios"></a>Scenarier

Kombinationen av användning och RateCard APIs gör det möjligt att dessa scenarier:

- **Förstå Azure spendera under månaden**: använda en kombination av användning och RateCard APIs du får bättre insikter om ditt moln spendera under månaden. Du kan analysera varje timme och dag användning och avgifter beräkningar.

- **Ställa in aviseringar**: använda användning och RateCard APIs för att få uppskattade molnförbrukning och kostnader och ställa in resurs- eller monetära-baserade aviseringar.

- **Förutsäga faktura**: Get din uppskattade förbrukning och molnet spendera och använda machine learning-algoritmer för att förutsäga fakturan skulle vara i slutet av faktureringsperioden.

- **Utföra en före förbrukningskostnad analysis**: Använd RateCard-API för att förutspå hur mycket din faktura är för din förväntade användning när du flyttar dina arbetsbelastningar till Azure. Om du har befintliga arbetsbelastningar i andra moln eller privata moln du kan också mappa din användning med Azure priserna för att få en bättre uppfattning av Azure-utgifter. Den här beräkningen ger dig möjlighet att pivotera erbjudandet och jämför olika erbjudandetyper utöver användningsbaserad betalning, t.ex. summa i förskott och kredit.

- **Utföra en konsekvensanalys**: du kan fastställa om det är mer kostnadseffektivt att köra arbetsbelastningar i en annan region eller på en annan konfiguration av Azure-resursen. Kostnader för Azure-resurs kan variera beroende på Azure-region du använder. Du kan också bestämma om en annan typ av Azure-erbjudande ger ett bättre pris på en Azure-resurs.

### <a name="networking-controls"></a>Kontroller för nätverk

Åtkomst till resurser kan vara intern (inom företagets nätverk) eller extern (via internet). Det är enkelt för användare i din organisation att oavsiktligt placera resurser på fel plats och öppna dem potentiellt skadliga åtkomst till. Precis som med lokala enheter måste företag lägga till lämpliga kontroller för att säkerställa att Azure-användare fatta rätt beslut.

![Kontroller för nätverk](./media/governance-in-azure/security-governance-in-azure-fig6.png)

För Prenumerationsåtgärder anger du följande kärnresurser grundläggande kontroll över åtkomsten.

#### <a name="network-connectivity"></a>Nätverksanslutning

[Virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) är behållarobjekt för undernät. Även om de inte är absolut nödvändigt, används ofta ett virtuellt nätverk för att ansluta program till interna företagsresurser. Tjänsten Azure virtuellt nätverk kan du ansluta Azure-resurser på ett säkert sätt till varandra med virtuella nätverk.

Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk avgränsning av Azure-molnet för din prenumeration. Du kan också ansluta virtuella nätverk till ditt lokala nätverk.

Följande är funktioner för virtuella Azure-nätverk:

- **Isolering**: virtuella nätverk är isolerade från varandra. Du kan skapa separata virtuella nätverk för utveckling, testning och produktion som använder samma CIDR-Adressblock. Däremot kan du skapa flera virtuella nätverk som använder olika CIDR-Adressblock och ansluta nätverk tillsammans. Du kan ändra ett virtuellt nätverk i flera undernät. Azure tillhandahåller intern namnmatchning för virtuella datorer och Azure Cloud Services-rollinstanser som är anslutna till ett virtuellt nätverk. Du kan också konfigurera ett virtuellt nätverk om du vill använda din egen DNS-servrar, istället för att använda intern namnmatchning för Azure.

- **Internetanslutning**: alla Azure virtual machines och Cloud Services-rollinstanser som är anslutna till ett virtuellt nätverk har åtkomst till internet, som standard. Du kan också aktivera ingående åtkomst till specifika resurser efter behov.

- **Azure-resurs anslutning**: du kan ansluta Azure-resurser, till exempel molntjänster och virtuella datorer, till samma virtuella nätverk. Resurserna som kan ansluta till varandra via privata IP-adresser, även om de finns i olika undernät. Azure tillhandahåller standardroutning mellan undernät, virtuella nätverk och lokala nätverk, så att du inte behöver att konfigurera och hantera vägar.

- **Virtuell nätverksanslutning**: du kan ansluta virtuella nätverk till varandra. Resurser som är anslutna till något virtuellt nätverk kan sedan kommunicera med resurser i andra virtuella nätverket.

- **Lokal anslutning**: du kan ansluta virtuella nätverk till lokala nätverk via privata nätverksanslutningar mellan ditt nätverk och Azure eller via en plats-till-plats virtuellt privat nätverk (VPN)-anslutning via internet.

- **Trafikfiltrering**: du kan filtrera nätverkstrafik (inkommande och utgående) för virtuella datorer och molntjänster efter källans IP-adress och port, målets IP-adress och port och protokoll.

- **Routning**: du kan också åsidosätta standard Azure routning genom att konfigurera dina egna vägar eller genom att använda BGP-vägar via en nätverks-gateway.

#### <a name="network-access-controls"></a>Åtkomstkontroll för nätverk

[Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) är som en brandvägg och ange regler för hur en resurs kan ”prata” över nätverket. De ger kontroll över hur ett undernät (eller en virtuell dator) kan ansluta till internet eller andra undernät i samma virtuella nätverk.

En nätverkssäkerhetsgrupp innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till Azure-nätverk. NSG: er kan associeras med undernät, enskilda virtuella datorer (klassisk) eller enskilda nätverksgränssnitt (NIC) som är kopplade till virtuella datorer (Resource Manager).

När en NSG är associerad med ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. Du kan ytterligare begränsa trafik genom att koppla en NSG till en virtuell dator eller nätverkskort.

## <a name="security-and-compliance-with-organizational-standards"></a>Säkerhet och efterlevnad organisationsstandarden

Alla företag har olika behov och kan dra nytta distinkta fördelarna från molnlösningar. Kunder allt har fortfarande samma grundläggande funderingar om att flytta till molnet. Vad kunderna vill ha från molnleverantörer är:

- **Skydda våra data**: IT-ledare bekräftar att molnet kan ge ökad datasäkerhet och administrationskontroll. Men de är fortfarande berörda att migrera till molnet kommer dem mer sårbar för hackare än sina aktuella interna lösningar.

- **Skydda dina våra data**: molntjänster sekretessutmaningar. Som företag ser att molnet för att spara på infrastrukturkostnader och förbättra deras flexibilitet, tänka de också på att förlora kontroll över sina data ska lagras, vem som åtkomst till den och hur den används.

- **Ge oss kontroll**: även när företag dra nytta av molnet för att distribuera fler innovativa lösningar, de är oroliga att förlora kontrollen över sina data. Senaste utlämnande av myndigheter som har åtkomst till kunddata, både juridiska och utomrättsliga sätt göra vissa CIO: er känner sig tveksamma för att lagra sina data i molnet.

- **Flytta upp transparens**: företag beslutsfattare förstår vikten av säkerhet, sekretess och kontroll. Men de vill även möjligheten att oberoende kontrollera hur deras data lagras, används och säker.

- **Upprätthålla efterlevnaden**: som företag Expandera deras användning av molntekniker, komplexitet och omfattningen av standarder och föreskrifter fortsätter att utvecklas. Företag behöver du veta att deras efterlevnadsstandarder uppfylls.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>Säkerhetskonfiguration för övervakning, loggning och granskning

Azure-prenumeranter kan hantera sina molnmiljöer från flera enheter. Dessa enheter kan innehålla hantering av arbetsstationer, utvecklardatorer och även Privilegierade slutanvändarens enheter som har uppgiftsspecifika behörigheter. 

I vissa fall kan utförs administrativa funktioner via webbaserade konsoler, till exempel Azure portal. I andra fall kan kan det finnas direkta anslutningar till Azure från lokala system över virtuella privata nätverk, Terminal Services, klientprotokoll för program eller (programmässigt) Azure Service Management API (SMAPI). Dessutom kan kan klientslutpunkter vara antingen domänanslutna eller isolerade och ohanterade, t.ex. surfplattor eller smartphones.

Den här variationen kan betydande risker för en molndistribution. Det kan vara svårt att hantera, spåra och granska administrativa åtgärder. Den här variationen kan också innebära säkerhetshot via oreglerad åtkomst till klientslutpunkter som används för att hantera molntjänster. Med hjälp av allmänna eller personliga arbetsstationer för att utveckla och hantera infrastrukturen öppnas oförutsägbara hotvektorer, till exempel webbsurfning (t.ex. vattenhålsattacker) eller mejl (t.ex. social manipulation och nätfiskewebbplatser).

Övervakning, loggning och granskning ger en grund för att spåra och förstå administrativa aktiviteter. Granska alla åtgärder i detalj kanske inte alltid är möjligt på grund av mängden data som genereras. Men utvärdera hur effektiva hanteringsprinciperna är bästa praxis.

Styrning i Azure-säkerhet från Azure Active Directory Domain Services (AD DS) grupprincipobjekt kan hjälpa dig att styra alla administratörens Windows-gränssnitt, till exempel fildelning. Inkludera hanteringsdatorer övervakning, loggning och granskning processer. Spåra alla administratörers och utvecklares åtkomst och användning.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ger en central vy över säkerhetsstatusen för resurserna i prenumerationer. Den innehåller rekommendationer som skyddar mot resurser som har komprometterats. Det kan aktivera mer detaljerad principer – till exempel tillämpa principer på specifika resursgrupper som gör att företag kan du skräddarsy deras efterlevnadsstatus för risken som de adresser.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center tillhandahåller integrerad säkerhet övervaka och hantera principer för alla Azure-prenumerationer, upptäcka hot som kan annars oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhetslösningar. När du har aktiverat [säkerhetsprinciper](https://docs.microsoft.com/azure/security-center/security-center-policies) för en prenumeration analyseras resursernas Security Center analyserar säkerheten för dina resurser för att upptäcka potentiella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt.

Azure Security Center representerar en kombination av bästa praxis analys- och hantering av Grupprincip för alla resurser i en Azure-prenumeration. Det gör att security team och risk införlivande att förhindra, upptäcka och svara på säkerhetshot som den automatiskt samlar in och analyserar säkerhetsdata från dina Azure-resurser, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar.

Dessutom analysteknik Azure Security Center avancerad, inklusive machine learning och beteendeanalys. Den använder global hotinformation från Microsoftprodukter och tjänster i Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) och externa flöden. Du kan använda [security styrning](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) brett på prenumerationsnivån. Eller du kan begränsa den till specifika krav och koppla dem till enskilda resurser via principdefinitionen.

Slutligen Azure Security Center analyserar resurssäkerhetshälsa baserat på de principerna och använder den här informationen för att tillhandahålla informativa instrumentpaneler och avisering för händelser, t.ex identifiering av skadlig kod eller skadliga IP-anslutning försök. Mer information om hur du utför rekommendationerna finns i [utföra säkerhetsrekommendationerna i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Azure Security Center övervakar följande Azure-resurser:

- Virtuella datorer (VM) (inklusive molntjänster)

- Virtuella nätverk

- SQL-databaser

- Partnerlösningar integreras med din Azure-prenumeration, till exempel en brandvägg för webbaserade program på virtuella datorer och på den [App Service Environment](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)

När du först öppnar Security Center är datainsamling aktiverat på alla virtuella datorer i din prenumeration. Vi rekommenderar att du behåller aktiverat datainsamling, men du kan [inaktivera det](https://docs.microsoft.com/azure/security-center/security-center-faq) i Security Center-principen.

### <a name="log-analytics"></a>Log Analytics

Azure Log Analytics programvara utvecklings- och gruppens informationssäkerhet och [styrning programmet](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) stöder affärskraven. Det följer lagar och föreskrifter enligt beskrivningen i [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) och [Microsoft förtroende Center-efterlevnad](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Hur Log Analytics upprättar säkerhetskrav, identifierar säkerhetskontroller och hanterar och övervakar risker beskrivs också det. Teamet granskar årligen, principer, standarder, procedurer och riktlinjer.

Varje teammedlem för utveckling av Log Analytics tar emot formella säkerhetsutbildning. Ett system kan du skydda varje programvaruprojekt under utveckling.

Microsoft har ett team för säkerhet och efterlevnad som övervakar och utvärderar alla tjänster i Microsoft. Information security införlivande utgör teamet och de är inte associerade med de tekniker avdelningar som utvecklar Log Analytics. Security införlivande har sin egen hantering av kedjan. De utför oberoende utvärderingar av produkter och tjänster för att säkerställa säkerhet och efterlevnad.

Huvudfunktionerna i Log Analytics tillhandahåller en uppsättning tjänster som körs i Azure. Varje tjänst tillhandahåller en specifik hanteringsfunktion, och du kan kombinera tjänster för olika hanteringsscenarier.

![Azure-tjänster för hantering](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Dessa tjänster har utformats i molnet. De är helt på Azure, så att de inte inkluderar distribuera och hantera lokala resurser. Konfigurationen är minimal, och du kan vara igång på bara några minuter.

Placera en agent på alla Windows- eller Linux-datorer i ditt datacenter och det ska skicka data till Log Analytics. Det, att de kan analyseras tillsammans med andra data som samlas in från molntjänster eller lokala tjänster. Använda Azure Backup och Azure Site Recovery för att dra nytta av molnet för säkerhetskopiering och hög tillgänglighet för lokala resurser.

![Tjänster på Azure-instrumentpanelen](./media/governance-in-azure/security-governance-in-azure-fig8.png)

Runbooks i molnet kan normalt inte komma åt lokala resurser, men du kan installera en agent på en eller flera datorer som är värd för runbooks i datacentret. När du startar en runbook anger du om du vill att den kan köras i molnet eller på en lokal arbetsprocess.

Olika lösningar är tillgängliga från Microsoft och partner att du kan lägga till din Azure-prenumeration att öka värdet på din investering i Log Analytics. Till exempel Azure erbjuder [hanteringslösningar](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)--färdigförpackade uppsättningar logik som implementerar ett scenario för hantering med hjälp av en eller flera tjänster.

![Galleriet med hanteringslösningar i Azure](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Som partner kan skapa du egna lösningar för dina program och tjänster och tillhandahålla dem till användare via Azure Marketplace eller Quickstart-mallar.

## <a name="performance-alerting-and-monitoring"></a>Prestanda avisering och övervakning

### <a name="alerting"></a>Aviseringar

Aviseringar är en metod för övervakning Azure Resursmått, händelser och loggar. De meddelar dig när ett villkor som du har angett är uppfyllt.

Aviseringar finns tillgängliga och tjänster, inklusive:

- **Azure Application Insights**: aktiverar web aviseringar för testning och mått. Mer information finns i [ställa in aviseringar i Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) och [övervaka tillgänglighet och svarstider på valfri webbplats](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability).

- **Log Analytics**: aktiverar Routning av aktivitet och diagnostikloggar till Log Analytics. Det ger mått, log, och andra aviseringstyper. Mer information finns i [aviseringar i Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts).

- **Azure Monitor**: gör det möjligt för aviseringar baserat på både måttvärden och händelser i aktivitetsloggen. Du kan använda den [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx) att hantera aviseringar. Mer information finns i [använder Azure portal, PowerShell eller kommandoradsgränssnittet för att skapa aviseringar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).

### <a name="monitoring"></a>Övervakning

Problem med prestanda i din molnapp kan påverka din verksamhet. Med flera sammankopplade komponenter och ofta versioner kan försämringar inträffa när som helst. Och om du utvecklar en app kan användarna identifiera vanligtvis problem som du hittade i testet. Du bör bli underrättad om problemen och har verktyg för att diagnostisera och åtgärda dem.

Det finns ett antal verktyg för övervakning av Azure-program och tjänster. Några av sina funktioner överlappar varandra. Detta beror delvis suddiga mellan utveckling och drift av ett program.

Här är de viktigaste verktygen:

- **Azure Monitor** är ett grundläggande verktyg för att övervaka tjänster som körs på Azure. Det ger dig infrastrukturnivå om dataflödet för en tjänst och den omgivande miljön. Om du hanterar alla dina appar i Azure och bestämma om du vill skala upp eller ned resurser, kan du starta Azure Monitor.

- **Application Insights** kan användas för utveckling och som en produktion övervakningslösning. Det fungerar genom att installera ett paket i din app, så att du får en mer interna vy över vad som händer. Datan innehåller svarstiderna för beroenden, undantag, spårningar, felsökning för ögonblicksbilder och körningsprofilerna. Den innehåller verktyg för att analysera den här telemetrin både för att felsöka en app och för att förstå vad användarna gör med den. Du kan avgöra om en topp i svarstiderna är på grund av något i en app eller vissa externa resourcing problem. Om du använder Visual Studio och appen är i fel kan du gå direkt till kodrad problemet så att du kan åtgärda felet.

- **Log Analytics** för den som måste du justera prestanda och planera Underhåll på program som körs i produktionsmiljön. Den samlar in och sammanställer data från många källor med en fördröjning på 10 till 15 minuter. Det ger en heltäckande IT-hanteringslösning för Azure, lokalt och från tredje part molnbaserad infrastruktur (till exempel Amazon Web Services). Den innehåller verktyg för att analysera data från källor, tillåter komplexa frågor över alla loggar och proaktivt kan meddela på angivet villkor. Du kan även samla in anpassade data i dess centrallager och sedan fråga och visualisera data.

- **System Center Operations Manager** är för att hantera och övervaka stora cloud-installationer. Du kanske redan känner till det som ett hanteringsverktyg för lokala Windows Server och Hyper-V-baserad moln, men det kan också integrera med och hantera Azure-appar. Bland annat kan den installera Application Insights på befintliga live-appar. Om en app slutar att fungera om Operations Manager på några sekunder.


## <a name="next-steps"></a>Nästa steg

- [Metodtips för att skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Exempel på implementering av Azure Prenumerationsåtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/)
