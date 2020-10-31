---
title: Skapa ett erbjudande för Azure-program – Microsoft Commercial Marketplace
description: Lär dig mer om steg och överväganden för att skapa ett nytt Azure-programerbjudande på den kommersiella Marketplace-portalen i Partner Center. Du kan visa eller sälja ditt Azure Application-erbjudande på Azure Marketplace eller via program varan för Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: AarathiN
ms.author: aarathin
ms.date: 07/14/2020
ms.openlocfilehash: 9ae770a21e93a0c8ab3827e91f15e163d7a875b4
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130212"
---
# <a name="create-an-azure-application-offer"></a>Skapa ett erbjudande för Azure-program

Den här artikeln beskriver steg och överväganden för att skapa ett nytt Azure-programerbjudande på den kommersiella Marketplace. Du bör känna till dessa begrepp innan du skapar ett nytt erbjudande för Azure-program.

Innan du kan publicera ett nytt Azure-program kan du [skapa ett kommersiellt marknads plats konto i Partner Center](create-account.md) och se till att ditt konto har registrerats i det kommersiella Marketplace-programmet.

## <a name="before-you-begin"></a>Innan du börjar

Design, utveckling och testning av Azure-programerbjudanden kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet. Teknik teamet bör ha kunskap om följande Microsoft-tekniker:

* Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/).
* Hur du [utformar och arkitekterar Azure-program](https://azure.microsoft.com/solutions/architecture/).
* Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking#networking).
* Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Arbeta med [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Teknisk dokumentation och resurser

Granska följande resurser när du förbereder ditt erbjudande för Azure-program för den kommersiella marknads platsen.

* [Förstå Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md)

* Snabbstarter:

    * [Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/)
    * [Best Practices guide för Azure-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [Publicera programdefinition](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    * [Distribuera tjänstkatalogapp](../../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)

* Självstudier:

    * [Skapa definitionsfiler](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    * [Publicera marknadsplatsprogram]()

* Stickprov

    * [Azure CLI](../../azure-resource-manager/managed-applications/cli-samples.md)
    * [Azure PowerShell](../../azure-resource-manager/managed-applications/powershell-samples.md)
    * [Hanterade programlösningar](../../azure-resource-manager/managed-applications/sample-projects.md)

[Mallarna för video skapande lösningar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) ger en omfattande introduktion till Azures program erbjudande typ:

* Vilka erbjudande typer är tillgängliga
* Det krävs tekniska till gångar
* Så här redigerar du en Azure Resource Manager mall
* Utveckla och testa användar gränssnittet för appen
* Så här publicerar du app-erbjudandet
* Programmets gransknings process

### <a name="suggested-tools"></a>Rekommenderade verktyg

Välj en eller båda av följande skript miljöer för att hantera ditt Azure-program:

* [Azure PowerShell](/powershell/azure/)
* [Azure CLI](/cli/azure)

Vi rekommenderar att du lägger till följande verktyg i utvecklings miljön:

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/) med följande fil namns tillägg:
    * Tillägg: [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Tillägg: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Tillägg: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Du kan granska de tillgängliga verktygen på [Azure utvecklarverktyg](https://azure.microsoft.com/tools/) -sidan. Även om du använder Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="types-of-azure-application-plans"></a>Typer av program planer i Azure

Det finns två typer av Azure-programplaner: Solution-mallar och hanterade program.

* **Lösnings mal len** är ett av de största sätten att publicera en lösning på Marketplace. Använd den här plan typen när lösningen kräver ytterligare distributions-och konfigurations automatisering utöver en enskild virtuell dator (VM). Med en lösnings mall kan du automatisera att tillhandahålla mer än en resurs, inklusive virtuella datorer, nätverk och lagrings resurser för att tillhandahålla komplexa IaaS-lösningar.  Mer information om hur du skapar Solution-mallar finns i [Azure Resource Manager](../../azure-resource-manager/management/overview.md).

* Det **hanterade programmet** liknar Solution-mallar med en viktig skillnad. I ett hanterat program distribueras resurserna till en resursgrupp som hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen. Som utgivare anger du kostnaden för fortlöpande support för lösningen. Använd hanterade program för att enkelt skapa och leverera fullständigt hanterade, nyckel färdiga program till dina kunder.  Mer information om fördelarna och typerna av hanterade program finns i [Översikt över Azure Managed Applications](../../azure-resource-manager/managed-applications/overview.md).

## <a name="technical-requirements"></a>Tekniska krav

Alla Azure-program inkluderar minst två filer i rotmappen för ett `.zip` Arkiv:

* En mall för en Resource Manager-mall med namnet [mainTemplate.jspå](../../azure-resource-manager/management/overview.md).  Den här mallen definierar de resurser som ska distribueras till kundens Azure-prenumeration. Exempel på Resource Manager-mallar finns i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/) eller motsvarande [GitHub: Azure Resource Manager snabb starts mallar](https://github.com/azure/azure-quickstart-templates) lagrings platsen.

* En definition av användar gränssnittet för att skapa Azure-program med namnet [createUiDefinition.jspå](../../azure-resource-manager/managed-applications/create-uidefinition-overview.md).  I användargränssnittet anger du element som ger konsumenterna möjlighet att ange parametervärden.

Alla nya Azure-programerbjudanden måste innehålla en [Azure-partner kund användnings-GUID](../azure-partner-customer-usage-attribution.md). 

Mer information om publicerings krav för varje program plan finns i [lösnings mal len publicerings krav](../marketplace-solution-templates.md) och [hanterings program erbjudande publicerings krav](../marketplace-managed-apps.md).

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

>[!NOTE]
>När ett erbjudande har publicerats visas inte redigeringarna som du gör i Partner Center i onlinebutiker förrän du publicerar om erbjudandet. Se till att alltid publicera om ditt erbjudande när du har gjort ändringar.

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).

1. På den vänstra menyn väljer du **kommersiell Marketplace** -  >  **Översikt** .

1. På sidan Översikt väljer du **+ nytt erbjudande**  >  **Azure Application** .

    ![Visar menyn till vänster-navigering.](./media/new-offer-azure-app.png)

1. På sidan **nytt erbjudande** anger du ett **erbjudande-ID** . Detta är en unik identifierare för varje erbjudande i ditt konto.

     * Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och Azure Resource Manager mallar, om tillämpligt.
     * Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudande-1** , är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
     * Erbjudande-ID: t kan inte ändras när du har valt **skapa** .

1. Ange ett **erbjudande alias** . Detta är det namn som används för erbjudandet i Partner Center.

     * Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.
     * Det går inte att ändra namnet på erbjudandet när du har valt **skapa** .

1. Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-setup"></a>Erbjudande konfiguration

På sidan **erbjudande konfiguration** kan du konfigurera en test-enhet och lead-hantering för ditt erbjudande.

### <a name="test-drive"></a>Test enhet

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och skapandet av mycket kvalificerade leads. [Läs mer om test enheter](../what-is-test-drive.md).

Om du vill aktivera en testenhet under en fast tids period markerar du kryss rutan **Aktivera en testenhet** . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet. Konfigurera testen hets miljö i avsnittet [test enhet teknisk konfiguration](#test-drive-technical-configuration) senare i det här avsnittet.

Mer information finns i [Testa ditt erbjudande på den kommersiella marknads platsen](../what-is-test-drive.md). Du kan också läsa om [metod tips för testprogram](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) och hämta [översikten över test enheter PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (se till att blockeringen av popup-fönster är avstängd)

>[!Note]
>Eftersom alla Azure-program implementeras med hjälp av en Azure Resource Manager-mall är den enda typen av test enhet som är tillgänglig för en Azure Application en [Azure Resource Manager baserad test enhet](../azure-resource-manager-test-drive.md).

### <a name="customer-leads"></a>Kund ledare

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Mer information finns i [Översikt över hantering av leads](./commercial-marketplace-get-customer-leads.md).

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På sidan **Egenskaper** definierar du de kategorier som används för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska avtal som stöder ditt erbjudande.

### <a name="category"></a>Kategori

Välj kategorier och under Kategorier för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Se till att du beskriver hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om ingen under kategori gäller ditt erbjudande väljer du **ej tillämpligt** .

Se den fullständiga listan över kategorier och under Kategorier i [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md).

### <a name="legal"></a>Juridisk information

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Erbjudande lista

På den här sidan kan du hantera kopian och avbildningarna för ditt erbjudande på ditt kommersiella Marketplace.

### <a name="marketplace-details"></a>Information om Marketplace

> [!NOTE]
> Erbjudande för List innehåll (till exempel beskrivning, dokument, skärm bilder och användnings villkor) behöver inte vara på engelska, förutsatt att beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det är också acceptabelt att tillhandahålla en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

Här är ett exempel på hur information om erbjudandet visas på Azure Marketplace (alla noterade priser är till exempel endast avsedda att avspegla faktiska kostnader):

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="Visar hur det här erbjudandet visas på Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Logotyp
2. Kategorier
3. Support adress (länk)
4. Villkor för användning
5. Sekretess policy adress (länk)
6. Erbjudandets namn
7. Sammanfattning
8. Beskrivning
9. Skärm bilder/videor

<br>Här är ett exempel på hur information om erbjudandet visas i Azure Portal:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Visar hur det här erbjudandet visas på Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Rubrik
2. Beskrivning
3. Användbara länkar
4. Skärmbilder

#### <a name="name"></a>Namn

Det namn som du anger här visas för kunder som rubrik på din erbjudande lista. Det här fältet fylls i automatiskt med texten du angav för **erbjud alias** när du skapade erbjudandet, men du kan ändra det här värdet. Det här namnet kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler). Namnet får innehålla högst 50 tecken och får inte innehålla några emojis.

#### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

Ange en kort beskrivning av erbjudandet, upp till 100 tecken. Beskrivningen kan användas i Sök resultat.

#### <a name="long-summary"></a>Lång Sammanfattning

Ange en längre Beskrivning av erbjudandet, upp till 256 tecken. Beskrivningen kan användas i Sök resultat.

#### <a name="description"></a>Beskrivning

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Sök efter nyckelord

Du kan också ange upp till tre Sök nyckelord för att hjälpa kunderna att hitta ditt erbjudande i Marketplace. För bästa resultat bör du också använda dessa nyckelord i beskrivningen.

#### <a name="privacy-policy-link"></a>Länk till sekretess policy

Ange URL: en till din organisations sekretess policy. Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy.

### <a name="useful-links"></a>Användbara länkar

Lägg till länkar till valfria kompletterande online-dokument om din lösning genom att välja **+ Lägg till en länk** .

### <a name="contact-information"></a>Kontaktinformation

Ange namn, e-postadress och telefonnummer för kontakt med **support kontakt** , **tekniker kontakt** och **CSP-program** . Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner. Vissa kontakter kan kräva ytterligare information.

### <a name="marketplace-media"></a>Marketplace-Media

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder gör att ditt bidrag avvisas.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar https://upload.xboxlive.com tjänsten som används av Partner Center.

#### <a name="store-logos"></a>Butiks logo typer

Ange en PNG-fil för logo typen med **stor** storlek. Partner Center använder detta för att skapa en **liten** och en **medie** logo typ. Du kan också ersätta dem med olika avbildningar senare.

- **Stor** (från 216 x 216 till 350 x 350 BPT, krävs)
- **Medel** (90 x 90 BPT, valfritt)
- **Liten** (48 x 48 BPT, valfritt)

Dessa Logo typer används på olika platser i listan:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Skärmbilder

Lägg till upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Varje skärm bild måste vara 1280 x 720 bild punkter i storlek och i PNG-format. Varje skärm bild måste innehålla en beskrivning.

#### <a name="videos"></a>Videoklipp

Lägg till upp till fem videor som demonstrerar ditt erbjudande. De ska vara värd för en extern video tjänst. Ange varje video namn, webb adress och en miniatyr bild av videon med 1280 x 720 pixlar.

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

- [Metod tips för Marketplace-erbjudanden](../gtm-offer-listing-best-practices.md)

Välj **Spara utkast** innan du fortsätter.

## <a name="preview-audience"></a>Förhandsgranska mål grupp

På fliken förhands granskning väljer du en begränsad **förhands visning** för att verifiera ditt erbjudande innan du publicerar den Live till den bredare Marketplace-publiken.

> [!IMPORTANT]
> När du har kontrollerat ditt erbjudande i för hands version väljer du **Go Live** för att publicera ditt erbjudande till den publika publike Marketplace.

Din förhands gransknings mål identifieras av ID: n för Azure Subscription ID, tillsammans med en valfri beskrivning av varje. Inget av dessa fält kan ses av kunder. Du hittar ditt prenumerations-ID för Azure på sidan **prenumerationer** i Azure Portal.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100). Genom att lägga till dessa prenumerations-ID: n definierar du vem som kan förhandsgranska ditt erbjudande innan det publiceras Live. Om ditt erbjudande redan är Live kan du fortfarande definiera en förhands gransknings grupp för testning av erbjudande ändringar eller uppdateringar av erbjudandet.

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat publik. En förhands gransknings grupp kan komma åt ditt erbjudande *innan* det publiceras Live på marknads platser. De kan se och validera alla planer, inklusive de som endast är tillgängliga för en privat mål grupp när ditt erbjudande har publicerats fullständigt till Marketplace. En privat publik (som definieras på fliken planera **priser och tillgänglighet** ) har exklusiv åtkomst till en viss plan.

Välj **Spara utkast** innan du fortsätter.

## <a name="technical-configuration"></a>Teknisk konfiguration

Fyll bara i det här avsnittet om erbjudandet innehåller ett hanterat program som genererar mätnings händelser med hjälp av API: et för avläsning av program vara i Marketplace. Ange **Azure Active Directory klient-ID** och **Azure Active Directory program-ID** som din tjänst ska använda när du avger Mät händelser.

Välj **Spara utkast** innan du fortsätter.

## <a name="technical-configuration-offer-level"></a>Teknisk konfiguration (erbjudande nivå)

>[!Note]
>Teknisk information om erbjudande nivå är valfritt.  Du behöver bara konfigurera den här informationen om du publicerar ett hanterat program med avgiftsbelagd fakturering och har en tjänst som ska autentiseras med en Azure AD-säkerhetstoken. Mer information finns i [autentiserings strategier](./marketplace-metering-service-authentication.md) för olika autentiseringsalternativ.

Den tekniska konfigurationen definierar information (klient-ID och app-ID) som används för att identifiera din tjänst, vilket genererar Mät händelser för ett hanterat program med hjälp av [API: er för avläsning av tjänst](./marketplace-metering-service-apis.md).  Ange den identitet som din tjänst ska använda när du avger Mät händelser.

* **Azure AD-klient-ID** (krävs): i Azure Portal måste du [skapa en Azure Active Directory-app (AD)](../../active-directory/develop/howto-create-service-principal-portal.md) så att vi kan verifiera anslutningen mellan våra två tjänster bakom en autentiserad kommunikation. Du hittar [klient-ID: t](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) genom att gå till Azure Active Directory och välja **Egenskaper** och leta efter **katalog-ID** -numret i listan (till exempel 50c464d3-4930-494c-963c-1e951d15360e).
* **ID för Azure AD-App** (krävs): du behöver också ditt [program-ID](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) och en autentiseringsnyckel. Om du vill hämta dessa värden går du till Azure Active Directory och väljer **Appregistreringar** och letar sedan efter det **program-ID-** nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Om du vill hitta autentiseringsnyckel går du till **Inställningar** och väljer **nycklar** . Du måste ange en beskrivning och varaktighet och kommer sedan att tillhandahålla ett Number-värde.

>[!Note]
>Azure-program-ID: t associeras med ditt utgivar-ID och kan bara användas på nytt i det här utgivar kontot.

>[!Note]
>Den här konfigurationen krävs om du vill använda [batch användnings händelse](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)).  Om du vill skicka [användnings händelse](marketplace-metering-service-apis.md#metered-billing-single-usage-event)kan du också använda [instansens metadatatjänst](../../active-directory/managed-identities-azure-resources/overview.md) för att hämta [token för JSON-webbtoken (JWT)](pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

## <a name="plan-overview"></a>Plan översikt

På den här fliken kan du ange olika plan alternativ inom samma erbjudande. Dessa planer (tidigare kallade SKU: er) kan variera beroende på typ av plan (lösnings mal len vs. hanterat program), monetarisering eller mål grupp. Konfigurera minst en plan för att lista ditt erbjudande i Marketplace.

Du kan skapa upp till 100-planer för varje erbjudande: upp till 45 av dessa kan vara privata. Lär dig mer om privata planer i [privata erbjudanden på Microsofts kommersiella marknads platser](../private-offers.md).

När du har skapat dina prenumerations namn, ID, plan typ, tillgänglighet (offentlig eller privat), aktuell publicerings status och alla tillgängliga åtgärder på den här fliken.

Vilka **åtgärder** som är tillgängliga i **plan översikten** varierar beroende på planens aktuella status och kan vara:

* Om plan status är **Draft** – ta bort utkast.
* Om plan status är **Live** – stoppa Sälj plan eller synkronisera privat publik.

### <a name="create-new-plan"></a>Skapa ny plan

**_Plan-ID_* _ – skapa ett unikt plan-ID för varje plan i det här erbjudandet. Detta ID visas för kunder i produkt-URL: en.  Använd endast gemener, alfanumeriska tecken, bindestreck eller under streck. Högst 50 tecken tillåts för det här plan-ID: t. Detta ID kan inte ändras efter att du valt skapa.

_*_Plan namn_*_ – kunder ser det här namnet när du bestämmer vilken plan du vill välja i erbjudandet. Skapa ett unikt erbjudande namn för varje plan i det här erbjudandet. Plan namnet används för att särskilja program varu planer som kan vara en del av samma erbjudande (till exempel erbjudande namn: Windows Server; abonnemang: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Planera installationen

På den här fliken kan du ange en hög nivå konfiguration för typen av plan, om den återanvänder paket från ett annat abonnemang och vilka moln planen ska vara tillgänglig i. Dina svar på den här fliken kommer att påverka vilka fält som visas på andra flikar för samma plan.

#### <a name="plan-type"></a>Plantyp
Välj typ av plan för ditt erbjudande. En _ *lösnings mal len* * plan hanteras helt av kunden. En **hanterad program** plan gör det möjligt för utgivare att hantera programmet för kundens räkning. Mer information finns i [typer av Azure-programplaner](#types-of-azure-application-plans).

#### <a name="re-use-technical-configuration"></a>Använd teknisk konfiguration igen

Om du har mer än en plan av samma typ och paketen är identiska mellan dem kan du välja **det här schemat Återanvänd paket från ett annat abonnemang** .  När du väljer det här alternativet kommer du att kunna välja något av de andra planerna av samma typ för det här erbjudandet för att återanvända paket från.

>[!Note]
>När du återanvänder paket från ett annat abonnemang försvinner fliken teknisk konfiguration från den här planen. Den tekniska konfigurations informationen från den andra planen, inklusive eventuella uppdateringar som du gör i framtiden, kommer även att användas för den här planen.<br><br>Den här inställningen kan inte ändras när den här planen har publicerats.

#### <a name="azure-regions-cloud"></a>Azure-regioner (moln)

Din plan måste göras tillgänglig i minst en Azure-region.

Välj det **globala Azure** -alternativet för att göra ditt abonnemang tillgängligt för kunder i alla globala Azure-regioner med marknads plats integrering. Mer information finns i [stöd för geografisk tillgänglighet och valuta](../marketplace-geo-availability-currencies.md).

Välj alternativet **Azure Government** för att göra planen tillgängligt i [Azure Government](../../azure-government/documentation-government-welcome.md) region. Den här regionen ger till gång till kontrollerad åtkomst för kunder från amerikanska federala, statliga, lokala eller stambaserad entiteter, samt partner som är berättigade att betjäna dem. Du, som utgivare, är ansvariga för alla kontroller, säkerhets åtgärder och bästa praxis. Azure Government använder fysiskt isolerade Data Center och nätverk (som finns i USA).

Innan du publicerar till [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md)ska du testa och validera planen i miljön eftersom vissa slut punkter kan skilja sig. Ställ in och testa planen genom att begära ett utvärderings konto från [Microsoft Azure Government utvärderings version](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>När planen har publicerats och är tillgänglig i en speciell Azure-region kan du inte ta bort den regionen.

#### <a name="azure-government-certifications"></a>Azure Government certifieringar

Det här alternativet visas bara om du har valt **Azure Government** .

Azure Government Services hanterar data som omfattas av vissa myndighets bestämmelser och krav. Till exempel FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att få kännedom om dina certifieringar för dessa program kan du ge upp till 100 länkar som beskriver dem. De kan antingen vara länkar till din lista över programmet direkt eller länkar till beskrivningar av dina efterlevnad med dem på dina egna webbplatser. Dessa länkar är bara synliga för Azure Government kunder.

Välj **Spara utkast** innan du fortsätter.

### <a name="plan-listing"></a>Plan lista

Det är här du konfigurerar registrerings information för planen. På den här fliken visas detaljerad information som kan skilja sig mellan olika planer i samma erbjudande.

#### <a name="plan-name"></a>Plan namn

Detta är förifyllt med det namn som du gav din plan när du skapade det. Det här namnet visas i Marketplace som rubrik för den här planen och är begränsat till 100 tecken.

#### <a name="plan-summary"></a>Plan Sammanfattning

Ange en kort sammanfattning av planen (inte erbjudandet). Den här sammanfattningen är begränsad till 100 tecken.

#### <a name="plan-description"></a>Beskrivning av plan

Beskriv vad som gör den här program varu planen unik, samt skillnader mellan planer i erbjudandet. Beskriv inte erbjudandet, bara planen. Plan beskrivningen kan innehålla upp till 2 000 tecken.

Välj **Spara utkast** innan du fortsätter.

### <a name="availability-solution-template-plans-only"></a>Tillgänglighet (endast lösnings mal len planerar)

Du kan göra planen synlig för alla, endast för vissa kunder (en privat mål grupp) och om du vill att planen ska vara dold för användning av annan lösnings mall eller endast hanterade program.

#### <a name="plan-visibility"></a>Plan synlighet

Du kan konfigurera varje plan så att den blir synlig för alla eller till en speciell grupp som du väljer. Du kan tilldela medlemskap i den här begränsade mål gruppen med hjälp av Azures prenumerations-ID.

Välj **det här är en privat plan** för att göra din plan privat och endast synlig för den begränsade gruppen som du väljer. När du har publicerat som privat plan kan du uppdatera mål gruppen eller välja att göra planen tillgänglig för alla. När en plan publiceras som synlig för alla måste den förbli synlig för alla; den kan inte konfigureras om som privat plan.

Om du gör planen privat anger du ett **ID för Azure-prenumeration** och dess beskrivning. Varje är en mål grupp som kommer att ha åtkomst till den här privata planen. Åtkomst tilldelas med hjälp av Azures prenumerations-ID med alternativet att inkludera en beskrivning av varje tilldelad Azure-prenumeration. Lägg till upp till 10 kunders prenumerations-ID individuellt eller 20 000 genom att importera en CSV-fil. ID: n för Azure-prenumerationen visas som GUID och bokstäver måste vara gemena.

>[!Note]
>En privat eller begränsad mål grupp skiljer sig från den förhands visnings mål som du definierade på fliken **förhands granskning** . En förhands gransknings grupp kan komma åt ditt erbjudande _innan_ det publiceras Live på Marketplace. Även om valet för privat mål bara gäller för en speciell plan, kan förhands gransknings gruppen Visa alla planer (privata eller inte) för validerings syfte.

#### <a name="hide-plan"></a>Dölj plan

Om din lösnings mall är avsedd att distribueras endast indirekt när den refereras till, även om en annan lösning eller ett hanterat program, markerar du den här kryss rutan om du vill publicera din lösnings mall men dölja den från kunder som söker efter den direkt.

Välj **Spara utkast** innan du fortsätter.

### <a name="pricing-and-availability-managed-application-plans-only"></a>Priser och tillgänglighet (endast hanterade program planer)

Använd den för att konfigurera de **marknader** som den här planen kommer att vara tillgänglig i, **priset** per månad för hantering av lösningen och **prenumerationens synlighet** om bara specifika kunder bör se den (en privat mål grupp).

Välj **Spara utkast** innan du fortsätter.

#### <a name="markets"></a>Marknaden

Varje plan måste vara tillgänglig på minst en marknad. Markera kryss rutan för alla marknads platser där du vill att den här planen ska vara tillgänglig. En sökruta och knapp för att välja "skatte mottagare", där Microsoft betalar försäljnings-och användnings skatt för din räkning, ingår i hjälpen.

Om du redan har angett priser för din plan i USA dollar (USD) och lagt till en annan marknads plats kommer priset för den nya marknaden att beräknas enligt de aktuella växelkurserna. Granska alltid priset för varje marknad innan du publicerar. Priserna kan granskas med hjälp av länken "export priser (xlsx)" när du har sparat ändringarna.

#### <a name="pricing"></a>Prissättning

Ange priset per månad för den här planen.  Detta pris är utöver alla Azure-infrastrukturer eller program kostnader som du betalar per användning av de resurser som distribueras av den här lösningen.

Utöver priset per månad kan du också ange priser för förbrukning av enheter som inte är standard med hjälp av [avgiftsbelagd fakturering](./azure-app-metered-billing.md).  Du kan ställa in priset per månad på noll och debitera exklusivt genom att fakturera faktureringen om du vill.

Priser som angetts i USD (USD = USA dollar) konverteras till den lokala valutan för alla valda marknader med de aktuella växelkurserna när de sparas. Validera dessa priser innan du publicerar genom att exportera pris kalkyl bladet och granska priset på varje marknad. Om du vill ange anpassade priser på en enskild marknad ändrar du och importerar pris kalkyl bladet.

>[!Note]
>Du måste först spara dina pris ändringar för att möjliggöra export av pris information.

Granska dina priser noggrant innan du publicerar eftersom det finns vissa begränsningar för vad som kan ändras när en plan har publicerats.  

>[!Note]
>När ett pris för en marknad i planen har publicerats kan det inte ändras senare.

#### <a name="plan-visibility"></a>Plan synlighet

Du kan konfigurera varje plan så att den blir synlig för alla eller till en speciell grupp som du väljer. Du kan tilldela medlemskap i den här begränsade mål gruppen med hjälp av Azures prenumerations-ID.

Välj **det här är en privat plan** för att göra din plan privat och endast synlig för den begränsade gruppen som du väljer. När du har publicerat som privat plan kan du uppdatera mål gruppen eller välja att göra planen tillgänglig för alla. När en plan publiceras som synlig för alla måste den förbli synlig för alla; den kan inte konfigureras om som privat plan.

>[!Note]
>En privat eller begränsad mål grupp skiljer sig från den förhands visnings mål som du definierade på fliken **förhands granskning** . En förhands gransknings grupp kan komma åt ditt erbjudande _innan_ det publiceras Live på Marketplace. Även om valet för privat mål bara gäller för en speciell plan, kan förhands gransknings gruppen Visa alla planer (privata eller inte) för validerings syfte.

Om du gör planen privat anger du ett **ID för Azure-prenumeration** och dess beskrivning. Varje är en mål grupp som kommer att ha åtkomst till den här privata planen. Åtkomst tilldelas med hjälp av Azures prenumerations-ID med alternativet att inkludera en beskrivning av varje tilldelad Azure-prenumeration. Lägg till upp till 10 kunders prenumerations-ID individuellt eller 20 000 genom att importera en CSV-fil. ID: n för Azure-prenumerationen visas som GUID och bokstäver måste vara gemena.

>[!Note]
>Privata erbjudanden stöds inte med Azure-prenumerationer som upprättats via en åter försäljare av Cloud Solution Provider-programmet (CSP).

### <a name="technical-configuration"></a>Teknisk konfiguration

På den här fliken kan du ladda upp distributions paketet som gör det möjligt för kunder att distribuera planen.

>[!Note]
>Den här fliken visas inte om du har konfigurerat den här planen för att återanvända paket från en annan plan på fliken **Planera konfiguration** .

#### <a name="package-details"></a>Paket information

På den här fliken kan du redigera utkast versionen av din tekniska konfiguration.

**Version** – tilldela den aktuella versionen av den tekniska konfigurationen.  Öka den här versionen varje gången du publicerar en ändring på den här sidan. Versionen måste vara i formatet `{integer}.{integer}.{integer}` .

**Paketfil** (. zip) – det här paketet innehåller alla mallfiler som behövs för den här planen, samt ytterligare resurser, paketerade som en `.zip` fil.

Alla paket för Azure-programplanen måste innehålla de här två filerna i rotmappen för ett `.zip` Arkiv:

* En mall för en Resource Manager-mall med namnet [mainTemplate.jspå](../../azure-resource-manager/management/overview.md). Den här mallen automatiserar distributionen av resurser till Azure-prenumerationen för kunder.  Exempel på Resource Manager-mallar finns i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/) eller motsvarande [GitHub: Azure Resource Manager snabb starts mallar](https://github.com/azure/azure-quickstart-templates) lagrings platsen.
* En definition av användar gränssnittet för att skapa Azure-program med namnet [createUiDefinition.jspå](../../azure-resource-manager/managed-applications/create-uidefinition-overview.md).

Högsta antal fil storlekar som stöds är:

* Upp till 1 GB i total komprimerad `.zip` Arkiv storlek
* Upp till 1 GB för varje enskild okomprimerad fil i `.zip` arkivet  

Alla nya erbjudanden för Azure-program måste också innehålla ett GUID för [Azure-partner kund användnings behörighet](../azure-partner-customer-usage-attribution.md) .

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar tjänsten som https://upload.xboxlive.com används av Partner Center.

Hanterade program planer kräver ytterligare information på den här fliken.

#### <a name="previously-published-packages"></a>Tidigare publicerade paket

Under fliken **tidigare publicerade paket** kan du Visa alla publicerade versioner av den tekniska konfigurationen.

#### <a name="enable-just-in-time-jit-access"></a>Aktivera JIT-åtkomst (just-in-Time)

Välj det här alternativet för att aktivera JIT-åtkomst (just-in-Time) för den här planen.  Med JIT-åtkomst kan du begära utökad åtkomst till ett hanterat programs resurser för fel sökning eller underhåll. Du har alltid skrivskyddad åtkomst till resurserna, men under en viss tids period kan du ha större åtkomst.  Mer information finns i [Aktivera och begär just-in-Time-åtkomst för Azure Managed Applications](../../azure-resource-manager/managed-applications/request-just-in-time-access.md).  Om du vill kräva att konsumenter av ditt hanterade program ger ditt konto permanent åtkomst, lämnar du alternativet omarkerat.

>[!Note]
>Se till att uppdatera din `createUiDefinition.json` fil för att stödja den här funktionen.  

#### <a name="deployment-mode"></a>Distributions läge

Välj om du vill konfigurera **slutfört** eller **stegvis distributions läge** när du distribuerar den här planen: 

* I **komplett läge** leder en omdistribution av programmet av kunden till att resurser i den hanterade resurs gruppen tas bort om resurserna inte har definierats i `mainTemplate.json` . 
* I **stegvist läge** lämnar en omdistribution av programmet befintliga resurser oförändrade.

Läs mer om distributions lägen i [Azure Resource Manager distributions lägen](../../azure-resource-manager/templates/deployment-modes.md).

#### <a name="notification-endpoint-url"></a>URL för meddelande slut punkt

Ange en HTTPS webhook-slutpunkt för att ta emot aviseringar om alla CRUD-åtgärder på hanterade program instanser av den här plan versionen.

#### <a name="customize-allowed-customer-actions"></a>Anpassa tillåtna kund åtgärder

Välj det här alternativet om du vill ange vilka åtgärder som kunder kan utföra på de hanterade resurserna utöver de " `*/read` "-åtgärder som är tillgängliga som standard.

Lista de ytterligare åtgärder som du vill att kunden ska utföra här, avgränsade med semikolon.  Mer information finns i [förstå neka tilldelningar för Azure-resurser](../../role-based-access-control/deny-assignments.md). För tillgängliga åtgärder, se [Azure Resource Provider-åtgärder](../../role-based-access-control/resource-provider-operations.md). Om du till exempel vill tillåta att användare startar om virtuella datorer kan du lägga till dem `Microsoft.Compute/virtualMachines/restart/action` i de tillåtna åtgärderna.

#### <a name="global-azure--azure-government-cloud"></a>Globalt Azure/Azure Government-moln

Ange vem som ska ha hanterings åtkomst till det här hanterade programmet i varje moln som stöds. Användare, grupper eller program som du vill ska beviljas behörighet till den hanterade resurs gruppen identifieras med hjälp av Azure Active Directory-identiteter (AD).

**Azure Active Directory klient-ID** – Azure AD-klient-ID: t (även kallat katalog-ID) som innehåller identiteterna för de användare, grupper eller program som du vill ge behörighet till. Du hittar ditt Azure AD-klient-ID på Azure Portal i [Egenskaper för Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

**Auktorisering** – lägg till Azure Active Directory objekt-ID för den användare, grupp eller det program som du vill bevilja behörighet till den hanterade resurs gruppen. Identifiera användaren med sitt huvud-ID, som finns på [bladet Azure Active Directory användare på Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

För varje huvud konto väljer du en av de inbyggda Azure AD-rollerna i listan (ägare eller deltagare). Rollen du väljer beskriver de behörigheter som huvud kontot kommer att ha på resurserna i kund prenumerationen. Mer information finns i [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md). Mer information om rollbaserad åtkomst kontroll i Azure (Azure RBAC) finns i [Vad är Azure RBAC](../../role-based-access-control/overview.md).

>[!Note]
>Även om du kan lägga till upp till 100-auktoriseringar per moln är det oftast enklare att skapa en Active Directory användar grupp och ange dess ID i "huvud-ID". På så sätt kan du lägga till fler användare i hanterings gruppen när planen har distribuerats och minska behovet av att uppdatera planen bara för att lägga till fler auktoriseringar.

#### <a name="policy-settings"></a>Principinställningar

Använd [Azure-principer](../../governance/policy/overview.md) för det hanterade programmet för att ange krav för kompatibilitet för den distribuerade lösningen. Du kan läsa om principdefinitioner och parametervärdenas format i [Azure Policy-exempel](../../governance/policy/samples/index.md). Du kan konfigurera högst fem principer och bara en instans av varje princip alternativ. Vissa principer kräver ytterligare parametrar. Standard-SKU: n krävs för gransknings principer. Princip namnet är begränsat till 50 tecken.

Välj **Spara utkast** innan du fortsätter.

## <a name="co-sell-with-microsoft"></a>Säljsamarbeta med Microsoft

Att tillhandahålla information på fliken Co-försäljning är helt valfritt för att publicera ditt erbjudande. Det krävs för att uppnå en färdig status för alla färdiga och IP-adresser för samtidig försäljning. Den information du anger kommer att användas av Microsoft Sales Teams för att lära dig mer om din lösning när du utvärderar den efter kund behov. Den är inte tillgänglig direkt för kunderna.

Mer information om den här fliken finns [i Co-Sälj alternativet i Partner Center](commercial-marketplace-co-sell.md).

## <a name="resell-through-csps"></a>Att sälja via CSP

Utöka räckvidden för ditt erbjudande genom att göra det tillgängligt för partner i CSP-programmet ( [Cloud solution providers](https://azure.microsoft.com/offers/ms-azr-0145p/) ). Detta gör det möjligt för åter försäljare att sälja ditt erbjudande till sina kunder och skapa sammansamlings lösningar.

Välj **Spara utkast** innan du fortsätter.

## <a name="test-drive"></a>Test enhet

Konfigurera en demonstration (test enhet) som låter kunderna testa ditt erbjudande innan du köper det. Om du vill skapa en demonstrations miljö som gör det möjligt för kunder att testa ditt erbjudande under en viss tids period, se [testköra ditt erbjudande på den kommersiella Marketplace](../what-is-test-drive.md).

Om du vill aktivera en testen het markerar du kryss rutan **Aktivera en testenhet** på fliken [erbjudande konfiguration](#test-drive) . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

### <a name="test-drive-technical-configuration"></a>Teknisk konfiguration för provkörning

- **ID för Azure AD-App** (krävs): ange ditt Azure Active Directory (AD) [program-ID](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Appregistreringar** och letar sedan efter det **program-ID-** nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Information om distributions prenumeration

Om du vill tillåta att test enheten distribueras åt dig skapar du och tillhandahåller en separat, unik Azure-prenumeration (krävs inte för Power BI test enheter).

* **ID för Azure-prenumeration** (krävs för Azure Resource Manager-och logi Kap par) – ange prenumerations-ID för att bevilja åtkomst till dina Azure-Kontotjänster för rapportering och fakturering av resursanvändning. Vi rekommenderar att du [skapar en separat Azure-prenumeration](../../cost-management-billing/manage/create-subscription.md) som ska användas för test enheter om du inte redan har en. Du hittar ditt prenumerations-ID för Azure genom att logga in på [Azure Portal](https://portal.azure.com/) och navigera till fliken **prenumerationer** på den vänstra menyn. Om du väljer fliken visas ditt prenumerations-ID (till exempel "a83645ac-1234-5AB6-6789-1h234g764ghty").
* **Azure AD-klient-ID** (krävs) – ange ditt Azure Active Directory (AD) [klient organisations-ID](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Egenskaper** och letar efter **katalog-ID** -nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också leta upp organisationens klient-ID med din domän namns-URL på:  [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .
* **Namn på Azure AD-klient** (krävs för dynamisk 365) – ange ditt Azure Active Directory (AD) namn. Du hittar det här namnet genom att logga in på [Azure Portal](https://portal.azure.com/)i det övre högra hörnet ditt klient namn visas under ditt konto namn.
* **ID för Azure AD-App** (krävs) – ange ditt Azure Active Directory (AD) [program-ID](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på vänster-navigerings menyn, väljer **Appregistreringar** och letar sedan efter det **program-ID-** nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure Active Directory program klient hemlighet** (krävs) – Ange din Azure AD-program [klient hemlighet](../../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)). Du hittar det här värdet genom att logga in på [Azure Portal](https://portal.azure.com/). Välj fliken **Azure Active Directory** på menyn till vänster-navigerings, Välj **Appregistreringar** och välj sedan appen Test Drive. Välj sedan **certifikat och hemligheter** , Välj **ny klient hemlighet** , ange en beskrivning, Välj **aldrig** under **upphör ande** och välj sedan **Lägg till** . Var noga med att kopiera värdet innan du lämnar den här sidan.)

Välj **Spara utkast** innan du fortsätter.

### <a name="marketplace-listing-optional"></a>Marketplace-lista (valfritt)

Beskriv test enhets upplevelsen.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Beskrivning** (krävs) – Beskriv din test enhet, vad som visas, mål för användaren att experimentera med, vilka funktioner som ska utforskas och all relevant information som hjälper användaren att avgöra om ditt erbjudande ska erhållas. Upp till 3 000 tecken text kan anges i det här fältet. 
* **Åtkomst information** (krävs för Azure Resource Manager och logik test enheter) – förklara vad en kund behöver veta för att få åtkomst till och använda den här test enheten. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör känna till för att få åtkomst till funktioner i hela test enheten. Upp till 10 000 tecken text kan anges i det här fältet.
* **Användar handbok** (krävs) – en djupgående genom gång av test enhets upplevelsen. Användar handboken bör bestå av exakt vad du vill att kunden ska få från att använda test enheten och fungera som referens för alla frågor som de kan ha. Filen måste vara i PDF-format och ha namnet (högst 255 tecken) efter överföringen.
* **Videor: Lägg till videor** (valfritt) – videor kan laddas upp till YouTube eller Vimeo och refereras till med en länk och miniatyr bild (533 x 324 bild punkter) så att en kund kan visa en genom gång av information för att hjälpa dem att bättre förstå test enheten, inklusive hur du kan använda funktionerna i ditt erbjudande och förstå scenarier som fokuserar på deras fördelar.
  * **Namn** (obligatoriskt)
  * **Adress** (YouTube eller Vimeo, krävs)
  * **Miniatyr** bild (bild filen måste vara i PNG-format och 533 x 324 pixlar).

Välj **Spara utkast** innan du fortsätter.

## <a name="publish"></a>Publicera

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **Granska och publicera** i det övre högra hörnet i portalen.

Granska slut för ande status för varje avsnitt i erbjudandet.
    - *Inte startad* – innebär att avsnittet inte har vidrör ATS och måste slutföras.
    - *Ofullständig* – innebär att avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
    - *Complete* – innebär att avsnittet har slutförts, att alla nödvändiga data har angetts och att det inte finns några fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.

Om detta är första gången du publicerar det här erbjudandet kan du tillhandahålla test instruktioner till certifierings teamet för att se till att appen testas korrekt, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.

Välj **Skicka** för att skicka in ditt erbjudande om publicering. Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas.

Gå tillbaka till Partner Center och välj **Go-Live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande till den privata mål gruppen).

### <a name="errors-and-review-feedback"></a>Fel och granska feedback

Det **manuella validerings** steget i publicerings processen representerar en omfattande granskning av ditt erbjudande och dess tillhör ande tekniska till gångar (särskilt Azure Resource Manager mal len), problem visas vanligt vis som pull-begäranden (PR). En förklaring av hur du kan visa och svara på dessa pull finns i [hantera granskning av feedback](./azure-apps-review-feedback.md).

Om du har fel i ett eller flera publicerings steg, korrigerar du dem innan du publicerar erbjudandet igen.

## <a name="next-steps"></a>Nästa steg

* [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)