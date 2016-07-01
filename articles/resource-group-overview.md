<properties
   pageTitle="Översikt över Azure Resource Manager | Microsoft Azure"
   description="Beskriver hur du använder Azure Resource Manager för distribution, hantering och åtkomstkontroll av resurser i Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="tomfitz"/>

# Översikt över Azure Resource Manager

Infrastrukturen för ditt program består normalt av många komponenter – kanske en virtuell dator, ett lagringskonto och ett virtuellt nätverk eller en webbapp, en databas, en databasserver och tjänster från tredje part. Du ser inte de här komponenterna som separata entiteterna, utan som relaterade delar av samma enhet som är beroende av varandra. Du vill distribuera, hantera och övervaka dem som en grupp. Med Azure Resource Manager kan du arbeta med resurserna i en lösning som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd. Du använder en mall för distributionen. Mallen kan användas i olika miljöer, till exempel för testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper dig att hantera dina resurser efter distributionen. 

## Fördelarna med att använda Resource Manager

Resource Manager har flera fördelar:

- Du kan distribuera, hantera och övervaka alla resurser i en lösning som en grupp i stället för att hantera resurserna separat.
- Du kan distribuera lösningen flera gånger genom utvecklingslivscykeln och vara säker på att dina resurser distribueras på ett enhetligt sätt.
- Du kan använda deklarativa mallar för att definiera distributionen.
- Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.
- Du kan använda åtkomstkontroll för alla tjänster i resursgruppen eftersom rollbaserad åtkomstkontroll (RBAC) är inbyggt i hanteringsplattformen.
- Du kan lägga till taggar för resurser och organisera dem logiskt i din prenumeration.
- Du kan tydliggöra faktureringen för din organisation genom att visa den sammanlagda kostnaden för hela gruppen eller för en grupp med resurser som delar samma tagg.  

Resource Manager erbjuder ett nytt sätt att distribuera och hantera lösningar. Om du använde den tidigare distributionsmodellen och vill veta mer om ändringarna läser du [Förstå Resource Manager-distribution och klassisk distribution](resource-manager-deployment-model.md).

## Riktlinjer

Följande rekommendationer hjälper dig att dra full nytta av Resource Manager när du arbetar med dina lösningar.

1. Definiera och distribuera infrastrukturen med den deklarativa syntaxen i Resource Manager-mallarna i stället för med tvingande kommandon.
2. Definiera alla distributions- och konfigurationssteg i mallen. Inga manuella steg ska behövas för att konfigurera lösningen.
3. Kör tvingande kommandon för att hantera resurser, exempelvis för att starta eller stoppa en app eller dator.
4. Ordna resurser med samma livscykel i en resursgrupp. Använd taggar för all annan resursorganisation.

## Resursgrupper

En resursgrupp är en behållare som innehåller relaterade resurser för ett program. Resursgruppen kan innehålla alla resurser för ett program eller bara de resurser som är logiskt grupperade. Du kan bestämma hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation.

Det finns några viktiga faktorer att tänka på när du definierar en resursgrupp:

1. Alla resurser i gruppen måste ha samma livscykel. Du distribuerar, uppdaterar och tar bort dem tillsammans. Om en resurs, till exempel en databasserver, behöver finnas i en annan distributionscykel ska den höra till en annan resursgrupp.
2. En enskild resurs kan bara finnas i en resursgrupp.
3. Du kan lägga till eller ta bort en resurs i en resursgrupp när som helst.
4. Du kan flytta en resurs från en resursgrupp till en annan grupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](resource-group-move-resources.md).
4. En resursgrupp kan innehålla resurser som finns i olika regioner.
5. En resursgrupp kan användas för att definiera omfattningen av åtkomstkontrollen för administrativa åtgärder.
6. En resurs kan länkas till en resurs i en annan resursgrupp om de båda resurserna behöver interagera med varandra men inte har samma livscykel (till exempel flera appar som ansluter till en databas). Mer information finns i [Länka resurser i Azure Resource Manager](resource-group-link-resources.md).

## Resursproviders

En resursprovider är en tjänst som tillhandahåller de resurser som du kan distribuera och hantera via Resource Manager. Varje resursprovider ger tillgång till åtgärder via ett REST-API som du kan använda för att arbeta med resurserna. Om du till exempel vill distribuera ett Azure-nyckelvalv för att lagra nycklar och hemligheter arbetar du med resursprovidern **Microsoft.KeyVault**. Den här resursprovidern erbjuder resurstypen **valv** som används för att skapa nyckelvalvet, och resurstypen **valv/hemligheter** som används för att skapa en hemlighet i nyckelvalvet. Du kan lära dig mer om en resursprovider genom att titta på åtgärderna i providerns REST-API, t.ex. [åtgärderna i REST-API:et för Azure Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Innan du kan distribuera och hantera infrastrukturen måste du känna till en del information om resursprovidern, t.ex. vilka resurstyper den erbjuder, versionsnumren för åtgärderna i REST-API:et, vilka åtgärder som stöds och vilket schema du ska använda när du anger värdena för den resurstyp som du ska skapa. Mer information om vilka resursproviders som stöds finns i [providers, regioner, API-versioner och scheman för Resource Manager](resource-manager-supported-services.md).

## Malldistribution

Med Resource Manager kan du skapa en enkel mall (i JSON-format) som definierar programmets distribution och konfiguration. Den här mallen kallas för en Resource Manager-mall och tillhandahåller en deklarativ metod för att definiera distributioner. Genom att använda en mall kan du distribuera ditt program flera gånger under applivscykeln och vara säker på att dina resurser distribueras konsekvent.

I mallen definierar du infrastrukturen för appen, hur infrastrukturen ska konfigureras och hur appkoden ska publiceras till infrastrukturen. Du behöver inte bekymra dig om distributionsordningen eftersom Azure Resource Manager analyserar alla beroenden och ser till att resurserna skapas i rätt ordning. Mer information finns i [Definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md).

När du skapar en lösning från Marketplace innehåller den automatiskt en distributionsmall. Du behöver inte skapa mallen från scratch. I stället kan du börja med mallen för din lösning och anpassa den efter dina specifika behov. Du kan hämta en mall för en befintlig resursgrupp antingen genom att exportera resursgruppens aktuella tillstånd till en mall eller genom att visa mallen som användes för en viss distribution. Att granska den exporterade mallen är ett bra sätt att lära sig mer om mallsyntaxen. Mer information om hur du arbetar med exporterade mallar finns i [Exportera en Azure Resource Manager-mall från befintliga resurser](resource-manager-export-template.md).

Du behöver inte definiera hela infrastrukturen i samma mall. Ofta är det praktiskt att dela in dina distributionskrav i en uppsättning riktade mallar för specifika ändamål. Du kan enkelt återanvända dessa mallar för olika lösningar. Om du vill distribuera en viss lösning skapar du en huvudmall som länkar alla nödvändiga mallar. Mer information finns i [Använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).

Du kan också använda mallen för uppdateringar av infrastrukturen. Du kan till exempel lägga till en ny resurs till din app och lägga till konfigurationsregler för resurser som redan har distribuerats. Om mallen används för att skapa en ny resurs men resursen redan finns utför Azure Resource Manager en uppdatering i stället för att skapa en ny tillgång. Azure Resource Manager uppdaterar den befintliga tillgången till samma tillstånd som den skulle ha som ny. Du kan också ange att Resource Manager ska ta bort resurser som inte har angetts i mallen. Information om skillnaderna mellan distributionsalternativen finns i [Distribuera ett program med en Azure Resource Manager-mall](resource-group-template-deploy.md). 

Du kan ange parametrar i mallen som ger möjlighet till anpassning och flexibilitet i distributionen. Du kan till exempel ange parametervärden som anpassar distributionen för en testmiljö. Genom att ange parametrarna kan du använda samma mall för distribution till alla appens miljöer.

Resource Manager tillhandahåller tillägg för scenarier då du behöver ytterligare åtgärder, t.ex. för att installera viss programvara som inte ingår i installationsprogrammet. Om du redan använder en konfigurationshanteringstjänst, t.ex. DSC, Chef eller Puppet, kan du fortsätta arbeta med den tjänsten genom att använda tillägg.

Slutligen blir mallen en del av källkoden för din app. Du kan checka in den i databasen för din källkod och uppdatera den i takt med att appen utvecklas. Du kan redigera mallen från Visual Studio.

Mer information om hur du definierar mallen finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).

Stegvisa instruktioner om hur du skapar en mall finns i [Genomgång av Resource Manager-mallar](resource-manager-template-walkthrough.md).

Information om hur du distribuerar lösningen till olika miljöer finns i [Utvecklings- och testmiljöer i Microsoft Azure](solution-dev-test-environments.md). 

## Taggar

Resource Manager innehåller en taggningsfunktion som du kan använda för att kategorisera resurser utifrån dina hanterings- eller faktureringskrav. Det kan vara bra att använda taggar om du har en komplex samling resursgrupper och resurser och behöver visualisera dessa tillgångar på ett sätt som passar dig. Du kan till exempel tagga resurser som har en liknande roll i organisationen eller som tillhör samma avdelning. Utan taggar kan användare i din organisation skapa flera resurser som kan vara svåra att identifiera och hantera längre fram. Till exempel kanske du vill ta bort alla resurser för ett visst projekt, men om resurserna inte har taggats för projektet måste du hittar dem manuellt. Taggning kan vara ett bra sätt att minska onödiga kostnader i din prenumeration. 

Resurser behöver inte finnas i samma resursgrupp för att dela en tagg. Du kan skapa din egen taggtaxonomi för att försäkra dig om att alla användare i organisationen använder samma taggar och att de inte av misstag använder varianter (till exempel ”avd” i stället för ”avdelning”).

Mer information om taggar finns i [Ordna dina Azure-resurser med hjälp av taggar](resource-group-using-tags.md). Du kan skapa en [anpassad princip](#manage-resources-with-customized-policies) som kräver att taggar läggs till för resurser under distributionen.

## Åtkomstkontroll

Med Resource Manager kan du kontrollera vem som har åtkomst till specifika åtgärder för din organisation. Verktyget integrerar OAuth och rollbaserad åtkomstkontroll (RBAC) i hanteringsplattformen och tillämpar åtkomstkontrollen på alla tjänster i resursgruppen. Du kan lägga till användare till fördefinierade plattforms- och resursspecifika roller och tillämpa rollerna på en prenumeration, resursgrupp eller resurs för att begränsa åtkomsten. Du kan till exempel använda den fördefinierade rollen SQL DB-deltagare som tillåter att användare hanterar databaser, men inte databasservrar eller säkerhetsprinciper. Du lägger till användare i organisationen som behöver den här typen av åtkomst till rollen SQL DB-deltagare och tillämpar rollen på prenumerationen, resursgruppen eller resursen.

Resource Manager loggar automatiskt användaråtgärder för granskning. Information om hur du arbetar med granskningsloggarna finns i [Granskningsåtgärder med Resource Manager](resource-group-audit.md).

Mer information om rollbaserad åtkomstkontroll finns i [Rollbaserad åtkomst i Azure](./active-directory/role-based-access-control-configure.md). Avsnittet [RBAC: Inbyggda roller](./active-directory/role-based-access-built-in-roles.md) innehåller en lista över inbyggda roller och tillåtna åtgärder. Exempel på inbyggda roller är allmänna roller som Ägare, Läsare och Deltagare, samt tjänstspecifika roller som Virtual Machine-deltagare, Virtual Network-deltagare och SQL-säkerhetsansvarig (för att bara nämna några av de tillgängliga rollerna).

Du kan också låsa viktiga resurser explicit för att förhindra att användare tar bort eller ändrar dem. Mer information finns i [Låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

Rekommendationer och bästa praxis finns i [Säkerhetsaspekter för Azure Resource Manager](best-practices-resource-manager-security.md)

## Hantera resurser med anpassade principer

Med Resource Manager kan du hantera resurser genom att skapa anpassade principer. Du kan skapa principer för vitt skilda scenarier, t.ex. för att tillämpa namngivningskonventioner på resurser, begränsa vilka typer och instanser av resurser som kan distribueras, begränsa vilka regioner som kan vara värdar för en viss typ av resurs eller för att ordna faktureringen efter avdelning genom att kräva ett taggvärde för resurser. Du skapar principer för att minska kostnaderna och upprätthålla konsekvensen i din prenumeration. Mer information finns i [Hantera resurser och kontrollera åtkomsten med hjälp av principer](resource-manager-policy.md).

## Enhetligt hanteringslager

Resource Manager tillhandahåller helt kompatibla åtgärder via Azure PowerShell, Azure CLI för Mac, Linux och Windows, Azure Portal eller REST-API:et. Du kan använda det gränssnitt som passar dig bäst och du kan snabbt och enkelt växla mellan gränssnitten. På portalen visas även meddelanden för åtgärder som vidtas utanför portalen.

Information om PowerShell finns i [Använda Azure PowerShell med Resource Manager](powershell-azure-resource-manager.md) och [Cmdlets för Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn757692.aspx).

Information om Azure CLI finns i [Använda Azure CLI för Mac, Linux och Windows med Azure Resource Manager](xplat-cli-azure-resource-manager.md).

Information om REST-API:et finns i [Referens för REST-API:et för Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx). Om du vill visa REST-åtgärder för dina distribuerade resurser går du till [Visa och ändra resurser med hjälp av Resursutforskaren i Azure](resource-manager-resource-explorer.md).

Information om hur du använder portalen finns i [Hantera Azure-resurser med hjälp av Azure Portal](./azure-portal/resource-group-portal.md).

Azure Resource Manager stöder resursdelning för korsande ursprung (CORS, Cross-Origin Resource Sharing). Med CORS kan du anropa REST-API:et för Resource Manager eller ett REST-API för en Azure-tjänst från en webbapp som finns i en annan domän. Utan stödet för CORS skulle webbläsaren hindra en app i en domän från att komma åt resurser i en annan domän. Resource Manager aktiverar CORS för alla förfrågningar med giltiga autentiseringsuppgifter.

## Nästa steg

- En enkel introduktion till mallar finns i [Exportera en Azure Resource Manager-mall från befintliga resurser](resource-manager-export-template.md).
- En mer omfattande genomgång över hur du skapar en mall finns i [Genomgång av Resource Manager-mallar](resource-manager-template-walkthrough.md).
- Information om de funktioner som du kan använda i en mall finns i [Mallfunktioner](resource-group-template-functions.md)
- Information om hur du använder Visual Studio med Resource Manager finns i [Skapa och distribuera Azure-resursgrupper via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

Här är en videodemonstration av den här översikten:

[AZURE.VIDEO azure-resource-manager-overview]



<!--HONumber=Jun16_HO2-->


