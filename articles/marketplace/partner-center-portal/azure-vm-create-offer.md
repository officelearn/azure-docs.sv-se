---
title: Skapa ett erbjudande om en virtuell Azure-dator – Azure Marketplace
description: Lär dig hur du skapar ett erbjudande om virtuella datorer på den kommersiella marknaden.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d598f741c5add58a89aa2b7aa01802a7e35f9a19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869065"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Skapa ett erbjudande om virtuell azure-dator

> [!IMPORTANT]
> Vi flyttar hanteringen av dina Azure VM-erbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats fortsätter du att följa instruktionerna i [Skapa erbjudande om virtuell dator](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) i Cloud Partner Portal för att hantera dina erbjudanden.

I den här artikeln beskrivs hur du skapar och publicerar ett erbjudande om en virtuell Azure-dator på [Azure Marketplace](https://azuremarketplace.microsoft.com/). Den behandlar både Windows- och Linux-baserade virtuella datorer som innehåller ett operativsystem, virtuell hårddisk (VHD) och upp till 16 datadiskar.

## <a name="introduction"></a>Introduktion

### <a name="publishing-benefits"></a>Publiceringsförmåner

Publicering till Azure Marketplace har följande fördelar:

- Marknadsför ditt företag med varumärket Microsoft
- Nå över 100 miljoner Office 365- och Dynamics 365-användare och mer än 200 000 organisationer via Azure Marketplace
- Få högkvalitativa leads från dessa marknadsplatser
- Få dina tjänster marknadsförda av Microsoft-fält- och telefonförsäljningsteamen

### <a name="before-you-begin"></a>Innan du börjar

Om du inte har gjort det ännu läser du [den virtuella datorns erbjudandeförlagsguide](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) och det här Azure-material med virtuella datorer:

- Snabbstartsguider
  - [Snabbstartsmallar för Azure](https://azure.microsoft.com/resources/templates/)
  - [Snabbstartsmallar för GitHub Azure](https://github.com/azure/azure-quickstart-templates)
- Självstudier
  - [Virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Exempel
  - [Azure CLI-exempel för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Virtuella Azure PowerShell för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Azure CLI-exempel för virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Virtuella Azure PowerShell för Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Grunderna i teknisk kunskap

Designa, bygga och testa dessa tillgångar tar tid och kräver teknisk kunskap om både Azure-plattformen och de tekniker som används för att skapa erbjudandet.

Ditt ingenjörsteam bör förstå följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure Services](https://azure.microsoft.com/services/)
- Så här [utformar och arkitekt azure-program utformas](https://azure.microsoft.com/solutions/architecture/)
- Kunskap om [virtuella Azure-datorer,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)och Azure [Networking](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Skapa ett erbjudande om virtuell azure-dator

Innan du kan skapa ett azure-erbjudande för virtuella datorer måste du ha ett kommersiellt marketplace-konto i Partner Center. Om du inte har skapat ett ännu läser du [Skapa ett kommersiellt marketplace-konto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

1. Logga in i [Partnercenter](https://partner.microsoft.com/dashboard/home)och välj sedan **Instrumentpanel**på den övre menyn .
2. I fältet till vänster väljer du **Kommersiell marknadsplats**och sedan **Översikt**.
3. På sidan **Översikt** väljer du **+ Nytt erbjudande**och sedan Azure Virtual **Machine**. Dialogrutan **Nytt erbjudande** visas.

    ![Illustrerar översiktssidan i Partnercenter med knappen Nytt erbjudande och Azure Virtual Machine-erbjudandet valt.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Det här ID:t är synligt för kunder i webbadressen för marketplace-erbjudandet och i Azure PowerShell och Azure CLI, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Den kan innehålla bindestreck och understreck, men inga blanksteg och är begränsad till 50 tecken. Om du till exempel anger **test-erbjudande-1** här blir `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`erbjudandeadressen .
- Erbjudande-ID:t kan inte ändras när du har valt **Skapa**.

Ange ett **offeralias**. Det här är namnet som används för erbjudandet i Partner Center. Det här namnet används inte på marknadsplatsen och skiljer sig från erbjudandenamnet och andra värden som visas för kunderna.

Välj **Skapa** för att generera erbjudandet och fortsätta.

## <a name="offer-setup"></a>Inställningar för erbjudande

### <a name="test-drive"></a>Provkörning

Konfigurera en demonstration (provkörning) som gör att kunderna kan prova ditt erbjudande innan de köper det. Om du vill skapa en demonstrationsmiljö som gör att kunderna kan prova ditt erbjudande under en viss tidsperiod läser du [Testa drive ditt erbjudande på den kommersiella marknaden.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

Om du vill aktivera en provkörning markerar du kryssrutan **Aktivera en provkörning.** Avmarkera den här kryssrutan om du vill ta bort provkörningen från erbjudandet.

Ytterligare provkörningsresurser:

- [Tekniska bästa metoder](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Metodtips för marknadsföring](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Översikt över testenheter](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (se till att popup-blockeraren är avstängd).

### <a name="lead-management"></a>Leadhantering

När du publicerar ditt erbjudande på den kommersiella marknadsplatsen med Partner Center ansluter du det till crm-systemet (Customer Relationship Management). På så sätt kan du få information om kundkontakter så snart någon uttrycker intresse för eller använder din produkt. Det krävs anslutning till en CRM om du aktiverar **Test Drive** (se föregående avsnitt), annars är det valfritt.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Partner Center stöder följande CRM-system:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) för kundengagemang
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Om ditt CRM-system inte visas ovan använder du [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) eller [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) för att lagra leaddata för kunder. Exportera sedan data till crm-systemet.

2. Anslut ditt erbjudande till leaddestinationen när du publicerar i Partner Center.
3. Kontrollera att anslutningen till lead-målet är korrekt konfigurerad. När du har publicerat den i Partner Center validerar vi anslutningen och skickar en testledare till dig. Medan du förhandsgranskar erbjudandet innan det visas kan du också testa din leadanslutning genom att försöka distribuera erbjudandet själv i förhandsversionen.
4. Se till att anslutningen till leaddestinationen förblir uppdaterad så att du inte förlorar några leads.

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera de kategorier och branscher som används för att gruppera ditt erbjudande på marknadsplatsen, din appversion och de juridiska kontrakt som stöder ditt erbjudande.

### <a name="categories"></a>Kategorier

Välj minst en och högst fem kategorier. Dessa kategorier används för att placera ditt erbjudande i lämpliga marknadsplatssökområden. Förklara i erbjudandebeskrivningen hur ditt erbjudande stöder dessa kategorier. Erbjudanden för virtuella datorer visas under kategorin **Beräkna** på Azure Marketplace.

### <a name="legal"></a>Juridisk information

Du måste ange villkor för erbjudandet. Du kan välja mellan två alternativ:

- Använd dina egna villkor
- Använda standardkontraktet för Microsofts kommersiella marknadsplats

#### <a name="use-your-own-terms-and-conditions"></a>Använd dina egna villkor

Om du vill ange egna anpassade villkor anger du upp till 10 000 texttecken i rutan **Regler och villkor.** Om du behöver en längre beskrivning anger du en enda webbadress som pekar på var dina villkor finns. Den visas för kunderna som en aktiv länk.

Kunderna måste acceptera dessa villkor innan de kan prova ditt erbjudande.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Använda standardkontraktet för Microsofts kommersiella marknadsplats

För att förenkla upphandlingsprocessen för kunder och minska den juridiska komplexiteten för programvaruleverantörer erbjuder Microsoft ett standardavtal för den kommersiella marknadsplatsen. När du erbjuder din programvara enligt standardavtalet behöver kunderna bara läsa och acceptera den en gång, och du behöver inte skapa anpassade villkor.

Använd standardkontraktet genom att markera kryssrutan **Använd standardkontraktet för Microsofts kommersiella marknadsplats** och sedan **Acceptera** i popup-fönstret (du kan behöva rulla uppåt för att se det).

![Illustrerar översiktssidan i Partnercenter med knappen Nytt erbjudande och Erbjudandet om konsulttjänster valt.](media/use-standard-contract.png)

> [!NOTE]
> När du har publicerat ett erbjudande med standardkontraktet för kommersiell marknadsplats kan du inte använda dina egna anpassade villkor. Antingen erbjuda din lösning enligt standardavtalet **eller** enligt dina egna villkor.

Mer information om standardkontraktet finns i Standardkontrakt för [Microsofts kommersiella marknadsplats](https://docs.microsoft.com/azure/marketplace/standard-contract). Du kan ladda ner [standardkontraktet](https://go.microsoft.com/fwlink/?linkid=2041178) som pdf (se till att popup-blockeraren är avstängd).

##### <a name="standard-contract-amendments"></a>Standardändring av kontrakt

Med standardavtalsändringar kan du välja standardavtalsvillkor för enkelhet och skapa villkoren för din produkt eller ditt företag. Kunder behöver bara granska ändringarna i avtalet om de redan har granskat och accepterat Microsofts standardavtal. Det finns två typer av ändringsförslag: universella och anpassade.

**Universella ändringar** – Dessa tillämpas allmänt på standardavtalet för alla kunder. De visas för varje kund av erbjudandet i inköpsflödet. Kunderna måste acceptera villkoren i standardavtalet och de ändringar som gäller innan de kan använda ditt erbjudande. Du kan tillhandahålla en enda universell ändring per erbjudande. Du kan ange ett obegränsat antal tecken i den här rutan. Dessa villkor visas för kunder i AppSource, Azure Marketplace och/eller Azure-portalen under identifierings- och inköpsflödet.

**Anpassade ändringar** – Det här är särskilda ändringar av standardkontraktet som är riktade till specifika kunder via Azure-klient-ID:er. Du kan välja den klient som du vill rikta in dig på. Endast kunder från klienten kommer att presenteras med de anpassade ändringsvillkoren i erbjudandets inköpsflöde. Kunderna måste acceptera villkoren i standardavtalet och de ändringar som gäller innan de kan använda ditt erbjudande.

Börja med att välja **Lägg till anpassade ändringsvillkor (max 10)**. Du kan ange upp till tio anpassade ändringsvillkor per erbjudande.

- **Anpassade ändringsvillkor** – Ange dina egna ändringsvillkor i rutan för anpassade ändringsvillkor. Du kan ange ett obegränsat antal tecken. Endast kunder från klient-ID:na som du anger för dessa anpassade villkor ser dessa i erbjudandets inköpsflöde i Azure-portalen.
- **Klient-ID** (krävs) – Varje anpassad ändring kan riktas till upp till 20 klient-ID:er. Om du lägger till en anpassad ändring måste du ange minst ett klient-ID som identifierar din kund i Azure. din kund kan hitta åt dig i Azure under och sedan Egenskaper. Katalog-ID-värdet är klient-ID (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också hitta organisationens klient-ID för din kund med hjälp av deras domännamnswebbadress på [Vad är mitt Microsoft Azure- och Office 365-klient-ID?](https://www.whatismytenantid.com/).
- **Beskrivning** (valfritt) – Ange en vänlig beskrivning av klient-ID som hjälper dig att identifiera kunden du riktar in dig på med ändringen.

> [!NOTE]
> Dessa två typer av ändringsförslag stapla ovanpå varandra. Kunder som är inriktade på anpassade ändringar kommer också att få den universella ändringen av standardavtalet vid köp.

Välj **Spara utkast** innan du fortsätter.

## <a name="offer-listing"></a>Lista över erbjudande

På den här sidan kan du definiera erbjudandeinformation som erbjudandenamn, beskrivning, länkar och kontakter.

> [!NOTE]
> Erbjudandelista innehåll (såsom beskrivning, dokument, skärmdumpar och användarvillkor) är inte skyldig att vara på engelska, så länge erbjudandet beskrivningen börjar med frasen,"Denna ansökan är endast tillgänglig på [icke-engelska språket]." Du kan också ange en _användbar länkadress_ för att erbjuda innehåll på ett annat språk än det som används i innehåll i erbjudandelistan.

### <a name="marketplace-details"></a>Information om Marketplace

#### <a name="name"></a>Namn

Namnet du anger här visas för kunderna som titeln på din erbjudandebeskrivning. Det här fältet är förfyllt med den text som du angav i rutan **Erbjudandealias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan varumärkesskyddas (och du kan inkludera varumärkes- och upphovsrättssymboler)
- Kan inte vara mer än 50 tecken lång
- Det går inte att inkludera emojis.

#### <a name="search-results-summary"></a>Sammanfattning av sökresultat

En kort beskrivning av ditt erbjudande. Detta kan vara upp till 100 tecken långt och används i marketplace-sökresultat.

#### <a name="long-summary"></a>Lång sammanfattning

Ge en längre beskrivning av ditt erbjudande. Detta kan vara upp till 256 tecken lång och används i marketplace sökresultat.

#### <a name="description"></a>Beskrivning

Ge en detaljerad beskrivning av ditt erbjudande, upp till 3000 tecken. Detta visas för kunder i den kommersiella marknadsplatslistningsöversikten.

Inkludera ett eller flera av följande i beskrivningen:

- Värdet och de viktigaste fördelarna med ditt erbjudande
- Kategori- eller branschorganisationer, eller båda
- Köpmöjligheter i appen
- Alla nödvändiga upplysningar

Här är några tips för att skriva din beskrivning:

- Beskriv tydligt värdet proposition av ditt erbjudande i de första meningarna i din beskrivning. Inkludera följande:
  - Beskrivning av ditt erbjudande.
  - Den typ av användare som drar nytta av ditt erbjudande.
  - Kundens behov eller problem som erbjudandet adresserar.
- Kom ihåg att de första meningarna kan visas i sökmotorresultat.
- Lita inte på funktioner och funktioner för att sälja ditt erbjudande. Fokusera istället på det värde som ditt erbjudande ger.
- Använd branschspecifika eller förmånsbaserade ord.

Om du vill göra erbjudandebeskrivningen mer engagerande använder du RTF-redigeraren för att formatera beskrivningen. Med RTF-redigeraren kan du lägga till tal, punkter, fetstil, kursiv stil och indrag för att göra beskrivningen mer läsbar.

![Illustrerar översiktssidan i Partnercenter med knappen Nytt erbjudande och Erbjudandet om konsulttjänster valt.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Länk till sekretesspolicy

Ange webbadressen (URL:en) i organisationens sekretesspolicy. Se till att ditt erbjudande följer sekretesslagar och sekretessregler. Du måste också publicera en giltig sekretesspolicy på din webbplats.

### <a name="useful-links"></a>Användbara länkar

Tillhandahålla kompletterande onlinedokument om ditt erbjudande. Om du vill lägga till en länk väljer du **+ Lägg till en länk** och fyller sedan i följande fält:

- **Namn** – Kunderna ser namnet på informationssidan.
- **Länk (URL)** – Ange en länk så att kunderna kan visa ditt onlinedokument.

### <a name="customer-support-links"></a>Länkar till kundsupport

Ange supportwebbplatsen där kunderna kan nå ditt supportteam.

- Azure Global supportwebbplats
- Supportwebbplats för Azure Government

### <a name="partner-support-contact"></a>Kontakt med partnersupport

Ange kontaktinformation som Microsoft-partner kan använda när dina kunder öppnar en supportbiljett. Detta kommer inte att listas på marknaden.

- Namn
- E-post
- Telefon

### <a name="engineering-contact"></a>Teknisk kontakt

Ange kontaktinformation som Microsoft kan använda när det finns problem med ditt erbjudande, inklusive problem med certifieringen. Detta kommer inte att listas på marknaden.

- Namn
- E-post
- Telefon

### <a name="marketplace-media"></a>Marknadsplatsmedia

Ange logotyper och bilder som du kan använda med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder kommer att göra att ditt bidrag avvisas.

>[!Note]
>Om du har problem med att ladda upp filer https://upload.xboxlive.com kontrollerar du att det lokala nätverket inte blockerar tjänsten som används av Partner Center.

#### <a name="marketplace-logos"></a>Marketplace-logotyper

Ange PNG-filer av erbjudandets logotyp i följande fyra pixelstorlekar:

- **Liten** (48 x 48)
- **Medel** (90 x 90)
- **Stor** (216 x 216)
- **Bred** (255 x 115)

Alla fyra logotyper krävs och används på olika platser på marknadsplatsen notering.

#### <a name="screenshots"></a>Skärmbilder

Lägg till upp till fem skärmbilder som visar hur erbjudandet fungerar. Varje skärmbild måste vara 1280 x 720 pixlar i storlek och i PNG-format. Du måste också lägga till en bildtext för att beskriva din skärmbild.

#### <a name="videos"></a>Videoklipp

Lägg till upp till fem videor som visar ditt erbjudande. Dessa bör finnas på en extern videotjänst. Ange videons namn, webbadress och en PNG-miniatyrbild av videon med en storlek på 1 280 x 720 pixlar.

Ytterligare resurser för marknadsplatslistning finns i [Metodtips för marknadsplatserbjudanden.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Välj **Spara utkast** innan du fortsätter.

## <a name="preview"></a>Förhandsversion

På fliken Förhandsgranska väljer du en begränsad målgrupp för **att** validera ditt erbjudande innan du publicerar det live för den bredare marknadsplatsens målgrupp.

> [!IMPORTANT]
> När du har kontrollerat ditt erbjudande i förhandsversion väljer du **Gå live** för att publicera ditt erbjudande till den offentliga målgruppen på den kommersiella marknadsplatsen.

Din förhandsgranskningsmålgrupp identifieras av Azure-prenumerations-ID-GUID: er, tillsammans med en valfri beskrivning för varje. Inget av dessa fält kan ses av kunder. Du hittar ditt Azure-prenumerations-ID på sidan **Prenumerationer** i Azure-portalen.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100). Genom att lägga till dessa prenumerations-ID:er definierar du vem som kan förhandsgranska erbjudandet innan det publiceras live. Om ditt erbjudande redan är live kan du fortfarande definiera en målgrupp för förhandsgranskning för testning av ändringar eller uppdateringar av ditt erbjudande.

> [!NOTE]
> En förhandsgranskningspublik skiljer sig från en privat målgrupp. En förhandsgranskningspublik kan komma åt ditt erbjudande _innan_ det publiceras live på marknadsplatserna. De kan se och validera alla planer, inklusive de som endast kommer att vara tillgängliga för en privat publik efter att ditt erbjudande har publicerats helt på marknadsplatsen. En privat målgrupp (definierad i fliken **Prissättning och tillgänglighet)** har exklusiv åtkomst till en viss plan.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, Planera översikt.

## <a name="plan-overview"></a>Översikt över plan

Du kan ange olika planalternativ i samma erbjudande i Partner Center. Dessa planer kallades tidigare SKU:er. Ett erbjudande kräver minst en plan, som kan skilja sig åt när det gäller intäktsgenereringsmålgrupp, Azure-regioner, funktioner eller VM-avbildningar.

När du har skapat dina planer visas på fliken **Planera översikt:**

- Planera namn
- Licensmodeller
- Målgrupp (offentlig eller privat)
- Aktuell publiceringsstatus
- Tillgängliga åtgärder

Vilka åtgärder som är tillgängliga i översikten Planera varierar beroende på planens aktuella status. De omfattar:

- **Ta bort utkast** – Om planens status är ett utkast
- **Sluta sälja plan** eller **Synkronisera privat målgrupp** – Om planens status publiceras live

### <a name="create-new-plan"></a>Skapa ny plan

Välj **+ Skapa ny plan** högst upp. Dialogrutan **Ny plan** visas.

Skapa ett unikt plan-ID för varje plan i det här erbjudandet i rutan **Plan-ID.** Detta ID kommer att vara synligt för kunder i produktens webbadress. Använd bara gemener och siffror, streck eller understreck och högst 50 tecken.

> [!NOTE]
> Det går inte att ändra plan-ID:t när du har valt **Skapa**.

Ange ett namn för den här planen i rutan **Plannamn.** Kunderna ser det här namnet när de bestämmer vilken plan som ska väljas inom ditt erbjudande. Skapa ett unikt namn som tydligt pekar på skillnaderna i varje plan. Du kan till exempel använda **Windows Server** med abonnemang betala **per användning**, **BYOL**, **Avancerat**och **Företag**.

Välj **Skapa**.

### <a name="plan-setup"></a>Planera inställningar

Ange konfiguration på hög nivå för typen av plan, om den återanvänder teknisk konfiguration från en annan plan och där Azure-regioner planen ska vara tillgänglig. Dina val här avgör vilka fält som visas på andra flikar för samma plan.

#### <a name="reuse-technical-configuration"></a>Återanvänd teknisk konfiguration

Om du har mer än en plan av samma typ och paketen är identiska mellan dem, kan du välja **den här planen återanvänder teknisk konfiguration från en annan plan**. Med det här alternativet kan du välja en av de andra abonnemangen av samma typ för det här erbjudandet och återanvända dess tekniska konfiguration.

> [!NOTE]
> När du återanvänder den tekniska konfigurationen från ett annat plan försvinner hela fliken **Teknisk konfiguration** från den här planen. Den tekniska konfigurationsinformationen från den andra planen, inklusive eventuella uppdateringar du gör i framtiden, kommer också att användas för den här planen. Den här inställningen kan inte ändras när den här planen har publicerats.

#### <a name="azure-regions"></a>Azure-regioner

Din plan måste göras tillgänglig i minst en Azure-region.

Välj alternativet **Azure Global** för att göra din plan tillgänglig för kunder i alla offentliga Azure-regioner som har kommersiell marketplace-integrering. Mer information finns i [Geografisk tillgänglighet och valutastöd](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Välj alternativet **Azure Government** för att göra din plan tillgänglig i [Azure Government-regionen.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) Den här regionen ger kontrollerad åtkomst för kunder från amerikanska federala, statliga, lokala eller stam-enheter, samt partner som är berättigade att betjäna dem. Du som utgivare är ansvarig för alla efterlevnadskontroller, säkerhetsåtgärder och bästa praxis. Azure Government använder fysiskt isolerade datacenter och nätverk (endast i USA).

Innan du publicerar till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), testa och validera din plan i miljön eftersom vissa slutpunkter kan skilja sig åt. Om du vill konfigurera och testa din plan begär du ett utvärderingskonto från [testperioden](https://azure.microsoft.com/global-infrastructure/government/request/)för Microsoft Azure Government .

> [!NOTE]
> När ditt abonnemang har publicerats och är tillgängligt i en viss Azure-region kan du inte ta bort den regionen.

#### <a name="azure-government-certifications"></a>Azure Government-certifieringar

Det här alternativet visas bara om du väljer **Azure Government** under **Azure-regioner**.

Azure Government-tjänster hanterar data som omfattas av vissa myndighetsregler och krav. FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att öka medvetenheten om dina certifieringar för dessa program kan du tillhandahålla upp till 100 länkar som beskriver dem. Dessa kan vara antingen länkar till dina uppgifter på programmet direkt eller länkar till beskrivningar av din efterlevnad av dem på dina egna webbplatser. Dessa länkar som endast är synliga för Azure Government-kunder.

Välj **Spara utkast** innan du fortsätter.

### <a name="plan-listing"></a>Planera listning

Här konfigurerar du detaljinformation för planen. På den här fliken visas specifik information som kan skilja sig åt mellan olika planer i samma erbjudande.

#### <a name="plan-name"></a>Plannamn

Detta är förfyllt med namnet du gav din plan när du skapade den. Det här namnet visas på marknadsplatsen som titeln på den här planen och är begränsad till 100 tecken.

#### <a name="plan-summary"></a>Sammanfattning av planen

Ge en kort sammanfattning av din plan (inte erbjudandet). Den här sammanfattningen är begränsad till 100 tecken.

#### <a name="plan-description"></a>Beskrivning av planen

Beskriv vad som gör denna programvaruplan unik, samt skillnader mellan planer inom ditt erbjudande. Beskriv inte erbjudandet, bara planen. Planbeskrivningen kan innehålla upp till 2 000 tecken.

Välj **Spara utkast** innan du fortsätter.

### <a name="pricing-and-availability"></a>Priser och tillgänglighet

På den här fliken konfigurerar du följande:

- Marknader kommer denna plan att finnas tillgänglig i
- Priset per timme
- Om planen ska vara synlig för alla eller bara för specifika kunder (en privat målgrupp)

#### <a name="markets"></a>Marknader

Varje plan måste finnas tillgänglig på minst en marknad. Markera kryssrutan för varje marknadsplats där den här planen ska vara tillgänglig för köp (användare på dessa marknader kan fortfarande distribuera erbjudandet till alla Azure-regioner som valts i **[Planinställningar](#plan-setup)**). Knappen **Momsbesänd** visar länder där Microsoft skickar moms och använder moms för din räkning. Publicering till Kina är begränsad till planer som antingen är **gratis** eller **ta med egen licens** (BYOL).

Om du redan har angett priser för din plan i USA-dollar (USD) och lägger till en annan marknadsplats, beräknas priset för den nya marknaden enligt aktuella valutakurser. Granska alltid priset för varje marknad innan publicering. Granska priser med hjälp av länken **Exportpriser (xlsx)** när du har sparat ändringarna.

När du tar bort en marknad kan kunder från den marknaden med aktiva distributioner inte skapa nya distributioner eller skala upp sina befintliga distributioner. Befintliga distributioner påverkas inte.

#### <a name="pricing"></a>Prissättning

**Licensmodell** – Välj **Användningsbaserad månadsbekturerad plan** för att konfigurera priser för det här abonnemanget eller **Ta med din egen licens** så att kunderna kan använda det här abonnemanget med sin befintliga licens.

Använd ett av följande tre pristransaktionsalternativ för en abonnemang för användningsbaserad månadsfäst:

- **Per kärna** – Ange priset per kärna i USA-dollar (USD). Vi beräknar prissättningen per kärnstorlek och konverterar till lokala valutor med den aktuella valutakursen.
- **Per kärnstorlek** – Ange priser per kärnstorlek i USD. Vi konverterar priserna till lokala valutor med den aktuella växelkursen.
- **Per marknad och kärnstorlek** – Ange priser för varje kärnstorlek för alla marknader. Du kan importera priser från ett kalkylblad.

> [!NOTE]
> Spara prisändringar för att aktivera export av prisdata. När ett pris för en marknad i din plan har publicerats kan det inte ändras senare. Se till att dessa priser är precis innan publicering genom att exportera priskalkylbladet och granska priset på varje marknad.

#### <a name="free-trial"></a>Kostnadsfri utvärderingsversion

Du kan erbjuda en kostnadsfri provperiod på en månad eller tre månader till dina kunder.

#### <a name="visibility"></a>Synlighet

Du kan utforma varje plan så att den är synlig för alla eller för endast en förvald målgrupp. Tilldela medlemskap i den här begränsade målgruppen med hjälp av Azure-prenumerations-ID:er.

**Offentlig** – Din plan kan ses av alla.

**Privat målgrupp** – Gör din plan bara synlig för en förvald målgrupp. När den har publicerats som en privat plan kan du uppdatera målgruppen eller ändra den till offentlig. När du har gjort en plan offentlig måste den förbli offentlig. Du kan inte ändra tillbaka den till privat.

**Begränsad målgrupp (Azure-prenumerations-ID)** – Tilldela målgruppen som har åtkomst till den här privata planen med hjälp av Azure-prenumerations-ID:er. Inkludera också en beskrivning av varje Azure-prenumerations-ID som du har tilldelat. Lägg till upp till 10 prenumerations-ID manuellt eller 20 000 om du importerar ett CSV-kalkylblad. Azure-prenumerations-ID:er representeras som GUID och bokstäver måste vara gemener.

> [!NOTE]
> En privat eller begränsad målgrupp skiljer sig från den målgrupp du har definierat på fliken **Förhandsgranska.** En förhandsgranskningspublik kan komma åt ditt erbjudande _innan_ det publiceras live på marknadsplatsen. Även om valet av privat målgrupp endast gäller för en viss plan kan förhandsgranskningspubliken visa alla planer (privata eller inte) för valideringsändamål.

#### <a name="hide-plan"></a>Dölj plan

Om din virtuella dator är avsedd att användas endast indirekt när den refereras via en annan lösningsmall eller hanterat program väljer du den här rutan för att publicera den virtuella datorn men döljer den från kunder som söker och surfar efter den direkt.

> [!NOTE]
> Dolda planer stöder inte förhandsgranskningslänkar.

Välj **Spara utkast** innan du fortsätter.

### <a name="technical-configuration"></a>Teknisk konfiguration

Ange bilder och andra tekniska egenskaper som är associerade med den här planen. Mer information finns i [Skapa en teknisk tillgång för Azure VM](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Den här fliken visas inte om du har konfigurerat den här planen för att återanvända paket från ett annat abonnemang på fliken **Planera inställningar.**

#### <a name="operating-system"></a>Operativsystem

- **Operativsystem familj** - Välj från **Windows** eller **Linux** operativsystem
- **Release** eller **leverantör** – Välj Windows-versionen eller Linux-leverantören
- **OS eget namn** – Välj ett eget operativsystemnamn. Det här namnet är synligt för kunder

#### <a name="recommended-vm-sizes"></a>Rekommenderade VM-storlekar

Välj upp till sex rekommenderade storlekar för virtuella datorer som ska visas på Azure Marketplace.

#### <a name="open-ports"></a>Öppna portar

Öppna offentliga eller privata portar på en distribuerad virtuell dator.

#### <a name="storage-option-for-deployment"></a>Lagringsalternativ för distribution

**Alternativet Diskdistribution** – Välj vilken typ av diskdistribution användarna kan använda när de använder den virtuella datorn. Microsoft rekommenderar att du begränsar distributionen till Endast hanterad diskdistribution.

#### <a name="properties"></a>Egenskaper

**Stöd för accelererade nätverk** – Välj om den virtuella datorn stöder [accelererade nätverk.](https://go.microsoft.com/fwlink/?linkid=2124513)

#### <a name="vm-images"></a>VM-avbildningar

Ange en diskversion och SAS-URI för avbildningar för den virtuella datorn. Lägg till upp till 16 datadiskar för varje vm-avbildning. Ange endast en ny bildversion per plan i en viss inlämning. När en bild har publicerats kan du inte redigera den, men du kan ta bort den. Om du tar bort en version hindras både nya och befintliga användare från att distribuera en ny instans av den borttagna versionen.

- **Skivversionen** är den version av bilden du tillhandahåller.
- **SAS URI** är den plats i Azure Storage där du har lagrat operativsystemet VHD.
- Datadiskavbildningar är också VHD SAS-URI:er som lagras i deras Azure-lagring.
- Lägg bara till en bild per inlämning i en plan.

Oavsett vilket operativsystem du använder lägger du bara till det minsta antal datadiskar som behövs av lösningen. Kunder kan inte ta bort diskar som ingår i en avbildning vid tidpunkten för distributionen, men de kan alltid lägga till diskar under eller efter distributionen.

Välj **Spara utkast** innan du fortsätter och återgå till **översikten Planera**.

## <a name="resell-through-csps"></a>Sälja vidare via CSP:er

Utöka räckvidden för ditt erbjudande genom att göra det tillgängligt för partner i [CSP-programmet (Cloud Solution Providers).](https://azure.microsoft.com/offers/ms-azr-0145p/) Alla Ta med din egen licens (BYOL) planer automatiskt valt i; du kan välja att välja i din icke-BYOL planer.

Välj **Spara utkast** innan du fortsätter.

## <a name="test-drive"></a>Provkörning

Konfigurera en demonstration (provkörning) som gör att kunderna kan prova ditt erbjudande innan de köper det. Om du vill skapa en demonstrationsmiljö som gör att kunderna kan prova ditt erbjudande under en viss tidsperiod läser du [Testa drive ditt erbjudande på den kommersiella marknaden.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

Om du vill aktivera en provkörning markerar du kryssrutan Aktivera en provkörning på fliken [Erbjudandeinställning.](#test-drive) Avmarkera den här kryssrutan om du vill ta bort provkörningen från erbjudandet.

Ytterligare provkörningsresurser:

- Tekniska bästa metoder
- Metodtips för marknadsföring
- Översikt över testenheter (PDF; kontrollera att popup-blockeraren är avstängd).

Välj **Spara utkast** innan du fortsätter.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla nödvändiga avsnitt i erbjudandet kan du skicka in det för att granska och publicera.

I det övre högra hörnet på portalen väljer du **Granska och publicera**.

På den här sidan kan du:

- Se slutförandestatusen för varje avsnitt i erbjudandet.
  - **Startade inte** – Avsnittet har inte startats och måste slutföras.
  - **Ofullständig** – Avsnittet har fel som måste åtgärdas eller kräver att du anger mer information. Se avsnitten tidigare i det här dokumentet för vägledning om hur du uppdaterar det här avsnittet.
  - **Slutför** – Avsnittet är klart och det finns inga fel. Alla delar av erbjudandet måste vara fullständiga innan du kan skicka erbjudandet.
- **Anteckningar för certifiering** – Ge testinstruktioner till certifieringsteamet för att säkerställa att din app testas korrekt. Ange eventuella kompletterande anteckningar som kan vara till hjälp för att förstå din app.

Om du vill skicka erbjudandet för publicering väljer du **Granska och publicerar**.

Vi skickar ett e-postmeddelande till dig när en förhandsversion av erbjudandet finns tillgänglig för granskning och godkännande. Om du vill publicera ditt erbjudande för allmänheten (eller om ett privat erbjudande, till en privat målgrupp), går till Partnercenter, letar reda på sidan **Översikt** för erbjudandet och väljer **Gå live**. Statusen för erbjudandet visas högst upp på sidan när publicering pågår.

### <a name="errors-and-review-feedback"></a>Fel och granska feedback

Manuellt **valideringssteg** i publiceringsprocessen representerar en omfattande genomgång av ditt erbjudande och dess associerade tekniska tillgångar. Om den här processen upptäcker fel med ditt erbjudande får du en certifieringsrapport som beskriver problemen. Helt enkelt göra de nödvändiga korrigeringarna och publicera ditt erbjudande igen.

## <a name="offer-overview"></a>Översikt över erbjudandet

**Översiktssidan Erbjudande** visar en visuell representation av de steg som krävs för att publicera erbjudandet (både slutförda och pågående) och hur lång tid varje steg ska ta att slutföra.

Den här sidan innehåller länkar för att utföra åtgärder på det här erbjudandet baserat på det val du gör. Ett exempel:

- Om erbjudandet är ett utkast - [Ta bort utkast erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Om erbjudandet är live - [Sluta sälja erbjudandet](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Om erbjudandet är i förhandsgranskning - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Om du inte har slutfört utgivnings signering - [Avbryt publicering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Exempel på Marketplace

De här exemplen visar hur erbjudandet visas på Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Azure Marketplace erbjuder information

![Azure Marketplace erbjuder informationsdeempel](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Sökresultat för Azure Marketplace

![Azure Marketplace-sökinformationsskärmsexempel](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Information om Azure Marketplace-abonnemang

![Azure Marketplace-planinformationsskärmsexempel](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Information om Azure-portalen erbjuder

![Azure portal erbjuder information skärmexempel](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Sökresultat för Azure Portal

![Exempel på sökresultat på Azure Portal](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Information om Azure-portalplan

![Exempel på information om Azure Portal-planinformation](media/avm-create6.png)

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande på den kommersiella marknaden](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
