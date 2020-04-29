---
title: Skapa ett erbjudande för virtuell Azure-dator – Azure Marketplace
description: Lär dig hur du skapar ett erbjudande för virtuella datorer på den kommersiella marknads platsen.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d598f741c5add58a89aa2b7aa01802a7e35f9a19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869065"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Skapa ett erbjudande för virtuell Azure-dator

> [!IMPORTANT]
> Vi flyttar hanteringen av dina virtuella Azure-erbjudanden från Cloud Partner Portal till Partner Center. Fortsätt att följa anvisningarna i [Skapa virtuell dator](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) i Cloud Partner Portal för att hantera dina erbjudanden innan du migrerar dina erbjudanden.

Den här artikeln beskriver hur du skapar och publicerar ett erbjudande för virtuella Azure-datorer på [Azure Marketplace](https://azuremarketplace.microsoft.com/). Den behandlar både Windows-och Linux-baserade virtuella datorer som innehåller ett operativ system, en virtuell hård disk (VHD) och upp till 16 data diskar.

## <a name="introduction"></a>Introduktion

### <a name="publishing-benefits"></a>Publicera förmåner

Publicering till Azure Marketplace har följande fördelar:

- Marknadsför ditt företag med Microsoft varumärke
- Kontakta över 100 000 000 Office 365-och Dynamics 365-användare och fler än 200 000 organisationer via Azure Marketplace
- Få högkvalitativa leads från dessa marknads platser
- Få dina tjänster upphöjda av Microsoft-fälten och inköps teamet för köp

### <a name="before-you-begin"></a>Innan du börjar

Om du inte har gjort det ännu kan du läsa [publicerings guiden för den virtuella datorn](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) och den virtuella Azure-datorns material:

- Snabb starts guider
  - [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure snabb starts mallar](https://github.com/azure/azure-quickstart-templates)
- Självstudier
  - [Virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Exempel
  - [Azure CLI-exempel för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell för virtuella Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Azure CLI-exempel för virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell för virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Grunderna i teknisk kunskap

Att utforma, skapa och testa dessa till gångar tar tid och kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet.

Teknik teamet bör förstå följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/)
- Så här [utformar och skapar du Azure-program](https://azure.microsoft.com/solutions/architecture/)
- Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Skapa ett erbjudande för virtuell Azure-dator

Innan du kan skapa ett erbjudande för virtuella Azure-datorer måste du ha ett kommersiellt marknads plats konto i Partner Center. Om du inte har skapat en ännu, se [skapa ett kommersiellt marknads plats konto i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home)och välj **instrument panel**på den översta menyn.
2. I det vänstra navigerings fältet väljer du **kommersiell Marketplace**och sedan **Översikt**.
3. På sidan **Översikt** väljer du **+ nytt erbjudande**, sedan **virtuell Azure-dator**. Dialog rutan **nytt erbjudande** visas.

    ![Visar översikts sidan i Partner Center med den nya erbjudande knappen och erbjudandet för virtuella Azure-datorer valda.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Marketplace-erbjudandet och i Azure PowerShell och Azure CLI, om tillämpligt.
- Använd bara gemena bokstäver och siffror. Det kan innehålla bindestreck och under streck, men inte blank steg, och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudandet-1** här, är `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`webb adressen för erbjudandet.
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Detta är det namn som används för erbjudandet i Partner Center. Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-setup"></a>Erbjudande konfiguration

### <a name="test-drive"></a>Test enhet

Konfigurera en demonstration (test enhet) som låter kunderna testa ditt erbjudande innan du köper det. Om du vill skapa en demonstrations miljö som gör det möjligt för kunder att testa ditt erbjudande under en viss tids period, se [testköra ditt erbjudande på den kommersiella Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Om du vill aktivera en testen het markerar du kryss rutan **Aktivera en testenhet** . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

Ytterligare resurser för test enheter:

- [Rekommenderade tekniska metoder](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Metodtips för marknadsföring](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Översikt över test enheter](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (se till att blockering av popup-fönster är inaktiverat).

### <a name="lead-management"></a>Leadhantering

När du publicerar erbjudandet till den kommersiella Marketplace med partner Center ansluter du det till ditt kund Relations hanterings system (CRM). På så sätt kan du ta emot kund kontakt information så snart någon uttrycker intresse för eller använder produkten. Det krävs en anslutning till en CRM om du ska aktivera **test enheten** (se föregående avsnitt), annars är den valfri.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Partner Center stöder följande CRM-system:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) för kund engagemang
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Om ditt CRM-system inte finns med i listan ovan använder du [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) eller [https-slutpunkt](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) för att lagra kundens lead-data. Exportera sedan data till CRM-systemet.

2. Anslut ditt erbjudande till lead-målet när du publicerar i Partner Center.
3. Bekräfta att anslutningen till lead-målet har kon figurer ATS korrekt. När du har publicerat den i Partner Center kommer vi att validera anslutningen och skicka ett test lead. När du för hands Grans kar erbjudandet innan det blir Live kan du också testa din lead-anslutning genom att försöka distribuera erbjudandet själv i för hands versionen.
4. Se till att anslutningen till mål målet förblir uppdaterad så att du inte förlorar några leads.

Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På den här sidan kan du definiera kategorier och branscher som används för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska kontrakt som stöder ditt erbjudande.

### <a name="categories"></a>Kategorier

Välj minst en och högst fem kategorier. Dessa kategorier används för att placera ditt erbjudande i lämpliga sökområden för Marketplace. I erbjudande beskrivningen förklarar du hur ditt erbjudande stöder dessa kategorier. Erbjudanden för virtuella datorer visas under **beräknings** kategorin i Azure Marketplace.

### <a name="legal"></a>Juridisk information

Du måste ange villkor för erbjudandet. Du kan välja mellan två alternativ:

- Använd egna villkor
- Använd standard kontraktet för Microsofts kommersiella marknads plats

#### <a name="use-your-own-terms-and-conditions"></a>Använd egna villkor

Om du vill ange egna anpassade villkor anger du upp till 10 000 tecken text i rutan **allmänna** villkor. Om du behöver en längre Beskrivning anger du en enskild webb adress som pekar på var du hittar dina villkor. Den kommer att visas för kunder som en aktiv länk.

Kunderna måste acceptera dessa villkor innan de kan testa ditt erbjudande.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Använd standard kontraktet för Microsofts kommersiella marknads plats

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft ett standard kontrakt för den kommersiella marknaden. När du erbjuder program varan under standard kontraktet behöver kunderna bara läsa och godkänna den en gång, och du behöver inte skapa anpassade allmänna villkor.

Använd standard kontraktet genom att markera kryss rutan **Använd standard kontraktet för Microsofts kommersiella Marketplace** och **Godkänn** sedan i popup-fönstret (du kan behöva rulla upp för att se det).

![Visar översikts sidan i Partner Center med den nya erbjudande knappen och konsult tjänst erbjudandet valt.](media/use-standard-contract.png)

> [!NOTE]
> När du har publicerat ett erbjudande med standard kontraktet för kommersiell marknads plats kan du inte använda dina egna anpassade villkor. Erbjud antingen lösningen enligt standard kontraktet **eller** enligt dina egna villkor.

Mer information om standard kontraktet finns i standard-kontrakt för Microsoft [Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract). Du kan ladda ned [standard kontraktet](https://go.microsoft.com/fwlink/?linkid=2041178) som en PDF (se till att blockering av popup-fönster är inaktiverat).

##### <a name="standard-contract-amendments"></a>Standard kontrakts ändringar

Med standard kontrakts ändringar kan du välja standard avtals villkoren för enkelhetens skull och skapa villkoren för din produkt eller verksamhet. Kunderna behöver bara granska ändringarna i kontraktet om de redan har granskat och godkänt Microsofts standard kontrakt. Det finns två typer av ändringar som är tillgängliga: universell och anpassad.

**Universella ändringar** – dessa används universellt till standard kontraktet för alla kunder. De visas för varje kund av erbjudandet i inköps flödet. Kunderna måste godkänna villkoren i standard kontraktet och ändringarna innan de kan använda ditt erbjudande. Du kan ange en enda universell ändring per erbjudande. Du kan ange ett obegränsat antal tecken i den här rutan. Dessa villkor visas för kunder i AppSource, Azure Marketplace och/eller Azure Portal under identifierings-och inköps flödet.

**Anpassade ändringar** – dessa är särskilda ändringar i standard kontraktet som är riktade till specifika kunder via Azure klient-ID: n. Du kan välja den klient som du vill använda som mål. Endast kunder från klient organisationen visas med de anpassade ändrings villkoren i erbjudandets inköps flöde. Kunderna måste godkänna villkoren i standard kontraktet och ändringarna innan de kan använda ditt erbjudande.

Börja med att välja **Lägg till anpassade ändrings villkor (max 10)**. Du kan ange upp till tio anpassade ändrings villkor per erbjudande.

- **Anpassade ändrings villkor** – ange dina egna ändrings villkor i rutan anpassad ändrings villkor. Du kan ange ett obegränsat antal tecken. Endast kunder från klient-ID: n som du anger för de här anpassade villkoren visas i erbjudandet inköps flöde i Azure Portal.
- **Klient-ID** (obligatoriskt) – varje anpassad ändring kan riktas mot upp till 20 klient-ID: n. Om du lägger till en anpassad ändring måste du ange minst ett klient-ID som identifierar din kund i Azure. Kunden kan hitta dig i Azure under och sedan Egenskaper. Värdet för katalog-ID är klient-ID (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också hitta organisationens klient-ID för din kund genom att använda deras domän namns webb adress i [Vad är mitt Microsoft Azure-och Office 365-klient-ID?](https://www.whatismytenantid.com/).
- **Beskrivning** (valfritt) – ange en egen beskrivning för klient-ID: t som hjälper dig att identifiera kunden som du är mål för med ändringen.

> [!NOTE]
> Dessa två typer av ändringar staplas ovanpå varandra. Kunder som är riktade mot anpassade ändringar får också den universella ändringen av standard kontraktet under köpet.

Välj **Spara utkast** innan du fortsätter.

## <a name="offer-listing"></a>Erbjudande lista

På den här sidan kan du definiera erbjudande information, till exempel namn, beskrivning, länkar och kontakter för erbjudandet.

> [!NOTE]
> Erbjudande för List innehåll (till exempel beskrivning, dokument, skärm bilder och användnings villkor) behöver inte vara på engelska, förutsatt att beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Du kan också tillhandahålla en _användbar länk webb adress_ för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

### <a name="marketplace-details"></a>Information om Marketplace

#### <a name="name"></a>Name

Det namn som du anger här visas för kunder som titel på din erbjudande lista. Det här fältet fylls i automatiskt med den text som du angav i rutan för **erbjuden alias** när du skapade erbjudandet. Du kan ändra det här namnet senare.

Namnet:

- Kan vara ett varumärke (och du kan inkludera varumärkes-och Copyright-symboler)
- Får innehålla högst 50 tecken
- Det går inte att inkludera emojis.

#### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

En kort beskrivning av erbjudandet. Detta kan vara upp till 100 tecken långt och används i Sök resultaten för Marketplace.

#### <a name="long-summary"></a>Lång Sammanfattning

Ange en längre Beskrivning av erbjudandet. Detta kan vara upp till 256 tecken långt och används i Sök resultaten för Marketplace.

#### <a name="description"></a>Beskrivning

Ange en detaljerad beskrivning av erbjudandet, upp till 3 000 tecken. Detta visas för kunder i Översikt över den kommersiella Marketplace-listan.

Inkludera ett eller flera av följande i din beskrivning:

- Värdet och viktiga fördelar med ditt erbjudande
- Kategori-eller bransch kopplingar, eller båda
- Köp möjligheter i appen
- Eventuella upplysningar som krävs

Här följer några tips om hur du skriver beskrivningen:

- Beskriv tydligt värdet på erbjudandet i de första meningarna i beskrivningen. Ta med följande objekt:
  - Beskrivning av erbjudandet.
  - Den typ av användare som har nytta av ditt erbjudande.
  - Kund behov eller problem som erbjuder erbjudandet.
- Kom ihåg att de första meningarna kan visas i Sök motorns resultat.
- Förlita dig inte på funktioner och funktioner för att sälja erbjudandet. I stället fokuserar du på det värde som erbjudandet erbjuder.
- Använd branschspecifika eller förmåns baserade ord.

Om du vill göra din erbjudande Beskrivning mer engagerande använder du RTF-redigeraren för att formatera beskrivningen. Med RTF-redigeraren kan du lägga till siffror, punkter, fet, kursiv stil och indrag för att göra beskrivningen mer lättläst.

![Visar översikts sidan i Partner Center med den nya erbjudande knappen och konsult tjänst erbjudandet valt.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Länk till sekretess policy

Ange webb adressen (URL) till din organisations sekretess policy. Se till att ditt erbjudande uppfyller sekretess lagar och föreskrifter. Du måste också publicera en giltig sekretess policy på din webbplats.

### <a name="useful-links"></a>Användbara länkar

Ge kompletterande online-dokument om ditt erbjudande. Om du vill lägga till en länk väljer du **+ Lägg till en länk** och fyller sedan i följande fält:

- **Namn** – kunder ser namnet på informations sidan.
- **Länk (URL)** – ange en länk till kunder för att visa ditt online-dokument.

### <a name="customer-support-links"></a>Kund support länkar

Ange support webbplatsen där kunderna kan kontakta support teamet.

- Azure Global support webbplats
- Azure Government support webbplats

### <a name="partner-support-contact"></a>Support kontakt för partner

Ange kontakt information för Microsoft-partner som ska användas när kunderna öppnar ett support ärende. Detta visas inte i Marketplace.

- Name
- E-post
- Telefon

### <a name="engineering-contact"></a>Teknisk kontakt

Ange kontakt information för Microsoft som ska användas när det finns problem med erbjudandet, inklusive problem med certifieringen. Detta visas inte i Marketplace.

- Name
- E-post
- Telefon

### <a name="marketplace-media"></a>Marketplace-Media

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder gör att ditt bidrag avvisas.

>[!Note]
>Om du har problem med att ladda upp filer kontrollerar du att ditt lokala nätverk inte blockerar https://upload.xboxlive.com tjänsten som används av Partner Center.

#### <a name="marketplace-logos"></a>Marketplace-logotyper

Ange PNG-filer för ditt erbjudandes logo typ i följande fyra pixel storlekar:

- **Liten** (48 x 48)
- **Medel** (90 x 90)
- **Stor** (216 x 216)
- **Bred** (255 x 115)

Alla fyra logo typer krävs och används på olika platser i Marketplace-listan.

#### <a name="screenshots"></a>Skärmbilder

Lägg till upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Varje skärm bild måste vara 1280 x 720 bild punkter i storlek och i PNG-format. Du måste också lägga till en beskrivning som beskriver skärm bilden.

#### <a name="videos"></a>Videoklipp

Lägg till upp till fem videor som demonstrerar ditt erbjudande. De ska vara värd för en extern video tjänst. Ange varje video namn, webb adress och en miniatyr bild av videon med 1280 x 720 pixlar i storlek.

Mer information om resurser för Marketplace finns i [metod tips för Marketplace-erbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

Välj **Spara utkast** innan du fortsätter.

## <a name="preview"></a>Förhandsversion

På fliken förhands granskning väljer du en begränsad **förhands visning** för att verifiera ditt erbjudande innan du publicerar den Live till den bredare Marketplace-publiken.

> [!IMPORTANT]
> När du har kontrollerat ditt erbjudande i för hands version väljer du **Go Live** för att publicera ditt erbjudande till den publika publike Marketplace.

Din förhands gransknings mål identifieras av ID: n för Azure Subscription ID, tillsammans med en valfri beskrivning av varje. Inget av dessa fält kan ses av kunder. Du hittar ditt prenumerations-ID för Azure på sidan **prenumerationer** i Azure Portal.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10) eller genom att ladda upp en CSV-fil (upp till 100). Genom att lägga till dessa prenumerations-ID: n definierar du vem som kan förhandsgranska ditt erbjudande innan det publiceras Live. Om ditt erbjudande redan är Live kan du fortfarande definiera en förhands gransknings grupp för testning av erbjudande ändringar eller uppdateringar av erbjudandet.

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat publik. En förhands gransknings grupp kan komma åt ditt erbjudande _innan_ det publiceras Live på marknads platser. De kan se och validera alla planer, inklusive de som endast är tillgängliga för en privat mål grupp när ditt erbjudande har publicerats fullständigt till Marketplace. En privat publik (som definieras på fliken planera **priser och tillgänglighet** ) har exklusiv åtkomst till en viss plan.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt, plan översikt.

## <a name="plan-overview"></a>Plan översikt

Du kan ange olika plan alternativ inom samma erbjudande i Partner Center. Dessa planer kallas tidigare för SKU: er. Ett erbjudande kräver minst en plan, som kan skilja sig åt för inbetalnings mål, Azure-regioner,-funktioner eller VM-avbildningar.

När du har skapat dina planer visas fliken **plan översikt** :

- Planera namn
- Licens modeller
- Mål grupp (offentlig eller privat)
- Aktuell publicerings status
- Tillgängliga åtgärder

Vilka åtgärder som är tillgängliga i plan översikten varierar beroende på planens aktuella status. De omfattar:

- **Ta bort utkast** – om planens status är ett utkast
- **Stoppa Sälj plan** eller **Synkronisera privat publik** – om plan statusen publiceras Live

### <a name="create-new-plan"></a>Skapa ny plan

Välj **+ Skapa ny plan** längst upp. Dialog rutan **nytt plan** visas.

Skapa ett unikt plan-ID för varje plan i det här erbjudandet i rutan **plan-ID** . Detta ID visas för kunder i produktens webb adress. Använd bara gemena bokstäver och siffror, bindestreck eller under streck och högst 50 tecken.

> [!NOTE]
> Det går inte att ändra plan-ID när du har valt **skapa**.

I rutan **plan namn** anger du ett namn för den här planen. Kunderna ser det här namnet när du bestämmer vilken plan du vill välja i erbjudandet. Skapa ett unikt namn som tydligt pekar ut skillnaderna i varje plan. Du kan till exempel använda **Windows Server** med abonnemang **betala per**användning, **BYOL**, **Advanced**och **Enterprise**.

Välj **Skapa**.

### <a name="plan-setup"></a>Planera installationen

Ange den avancerade konfigurationen för typen av plan, oavsett om den återanvänder teknisk konfiguration från ett annat avtal och i vilka Azure-regioner som planen ska vara tillgänglig. Dina val här avgör vilka fält som visas på andra flikar för samma plan.

#### <a name="reuse-technical-configuration"></a>Återanvänd teknisk konfiguration

Om du har mer än en plan av samma typ och paketen är identiska mellan dem, kan du välja **den här prenumerationen Återanvänd teknisk konfiguration från ett annat abonnemang**. Med det här alternativet kan du välja något av de andra planerna av samma typ för det här erbjudandet och återanvända den tekniska konfigurationen.

> [!NOTE]
> När du återanvänder den tekniska konfigurationen från ett annat abonnemang försvinner fliken **teknisk konfiguration** från den här planen. Den tekniska konfigurations informationen från den andra planen, inklusive eventuella uppdateringar som du gör i framtiden, kommer även att användas för den här planen. Den här inställningen kan inte ändras efter att planen har publicerats.

#### <a name="azure-regions"></a>Azure-regioner

Din plan måste göras tillgänglig i minst en Azure-region.

Välj det **globala Azure** -alternativet för att göra ditt abonnemang tillgängligt för kunder i alla offentliga Azure-regioner som har kommersiell Marketplace-integration. Mer information finns i [stöd för geografisk tillgänglighet och valuta](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Välj alternativet **Azure Government** för att göra planen tillgängligt i [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) region. Den här regionen ger till gång till kontrollerad åtkomst för kunder från amerikanska federala, statliga, lokala eller stambaserad entiteter, samt partner som är berättigade att betjäna dem. Du, som utgivare, är ansvariga för alla kontroller, säkerhets åtgärder och bästa praxis. Azure Government använder fysiskt isolerade Data Center och nätverk (som finns i USA).

Innan du publicerar till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)ska du testa och validera planen i miljön eftersom vissa slut punkter kan skilja sig. Ställ in och testa planen genom att begära ett utvärderings konto från [Microsoft Azure Government utvärderings version](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> När planen har publicerats och är tillgänglig i en speciell Azure-region kan du inte ta bort den regionen.

#### <a name="azure-government-certifications"></a>Azure Government certifieringar

Det här alternativet visas bara om du väljer **Azure Government** under **Azure-regioner**.

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

### <a name="pricing-and-availability"></a>Priser och tillgänglighet

På den här fliken konfigurerar du följande:

- Marknader den här planen kommer att finnas tillgänglig i
- Priset per timme
- Om planen ska vara synlig för alla eller bara för vissa kunder (en privat mål grupp)

#### <a name="markets"></a>Marknaden

Varje plan måste vara tillgänglig på minst en marknad. Markera kryss rutan för varje marknads plats där den här planen ska vara tillgänglig för köp (användare på dessa marknader kan fortfarande distribuera erbjudandet till alla Azure-regioner som valts i **[Planera installationen](#plan-setup)**). Knappen **skatt mottagare** visar länder där Microsoft betalar och använder moms för din räkning. Publicering till Kina är begränsad till planer som antingen är **kostnads fria** eller **Bring Your Own License** (BYOL).

Om du redan har angett priser för din plan i USA dollar (USD) och lagt till en annan marknads plats kommer priset för den nya marknaden att beräknas enligt aktuella växelkurser. Granska alltid priset för varje marknad innan du publicerar. Granska priserna med hjälp av länken **export priser (xlsx)** när du har sparat ändringarna.

När du tar bort en marknad kommer kunder från den marknaden som använder aktiva distributioner inte att kunna skapa nya distributioner eller skala upp sina befintliga distributioner. Befintliga distributioner kommer inte att påverkas.

#### <a name="pricing"></a>Prissättning

**Licens modell** – Välj **användning-baserad månatlig fakturerings plan** för att konfigurera prissättningen för den här planen eller **Bring Your Own License** så att kunderna kan använda den här planen med sin befintliga licens.

Använd en av följande tre alternativ för pris poster för en användnings-baserad månatlig fakturerings plan:

- **Per kärna** – ange priset per kärna i USA dollar (USD). Vi beräknar priserna per kärn storlek och omvandlar till lokala valutor med den aktuella växelkursen.
- **Per kärn storlek** – ange priser per kärn storlek i USD. Vi omvandlar priserna till lokala valutor med den aktuella växelkursen.
- **Per marknad och kärn storlek** – ange priser för varje kärn storlek för alla marknader. Du kan importera priser från ett kalkyl blad.

> [!NOTE]
> Spara pris ändringar så att du kan exportera pris information. När du har publicerat ett pris för en marknad i planen kan du inte ändra det senare. Se till att dessa priser är rätt före publicering genom att exportera pris kalkyl bladet och granska priset på varje marknad.

#### <a name="free-trial"></a>Kostnadsfri utvärderingsversion

Du kan erbjuda en månads kostnads fri utvärderings version av en månad eller tre månader till dina kunder.

#### <a name="visibility"></a>Synlighet

Du kan utforma varje plan så att den blir synlig för alla eller till endast en förvaltad mål grupp. Tilldela medlemskap i den här begränsade mål gruppen med hjälp av Azures prenumerations-ID.

**Offentligt** – din prenumeration kan ses av alla.

**Privat publik** – gör ditt abonnemang bara synligt för en förvaltad mål grupp. När den har publicerats som en privat plan kan du uppdatera mål gruppen eller ändra den till offentlig. När du har gjort en plan offentlig måste den vara offentlig. Du kan inte ändra tillbaka till privat.

**Begränsad mål grupp (Azure-prenumerations-ID)** – tilldela den mål grupp som ska ha åtkomst till den privata planen med ID: n för Azure-prenumeration. Du kan också ta med en beskrivning av varje Azure-prenumerations-ID som du har tilldelat. Lägg till upp till 10 prenumerations-ID: n manuellt eller 20 000 om du importerar ett CSV-kalkylblad. ID: n för Azure-prenumerationen visas som GUID och bokstäver måste vara gemena.

> [!NOTE]
> En privat eller begränsad mål grupp skiljer sig från den förhands visnings mål som du definierade på fliken **förhands granskning** . En förhands gransknings grupp kan komma åt ditt erbjudande _innan_ det publiceras Live på Marketplace. Även om valet för privat mål bara gäller för en speciell plan, kan förhands gransknings gruppen Visa alla planer (privata eller inte) för validerings syfte.

#### <a name="hide-plan"></a>Dölj plan

Om den virtuella datorn är avsedd att användas endast indirekt vid referenser via en annan lösnings mall eller ett hanterat program, markerar du den här kryss rutan om du vill publicera den virtuella datorn men dölja den från kunder som söker efter den direkt.

> [!NOTE]
> Dolda planer stöder inte för hands versions länkar.

Välj **Spara utkast** innan du fortsätter.

### <a name="technical-configuration"></a>Teknisk konfiguration

Ange de bilder och andra tekniska egenskaper som är associerade med den här planen. Mer information finns i [skapa en teknisk till gång för Azure VM](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Den här fliken visas inte om du har konfigurerat den här planen för att återanvända paket från en annan plan på fliken **Planera konfiguration** .

#### <a name="operating-system"></a>Operativsystem

- **Operativ system familj** – Välj från **Windows** eller **Linux** -operativsystemet
- **Version** eller **leverantör** – Välj Windows-versionen eller Linux-leverantören
- **Eget namn på** operativ system – Välj ett eget operativ system namn. Det här namnet är synligt för kunder

#### <a name="recommended-vm-sizes"></a>Rekommenderade VM-storlekar

Välj upp till sex rekommenderade storlekar för virtuella datorer som ska visas på Azure Marketplace.

#### <a name="open-ports"></a>Öppna portar

Öppna offentliga eller privata portar på en distribuerad virtuell dator.

#### <a name="storage-option-for-deployment"></a>Lagrings alternativ för distribution

**Disk distributions alternativ** – Välj vilken typ av disk distribution som användarna kan använda när de använder den virtuella datorn. Microsoft rekommenderar att du endast begränsar distributionen till hanterad disk distribution.

#### <a name="properties"></a>Egenskaper

**Stöd för accelererat nätverk** – Välj om din virtuella dator har stöd för [accelererat nätverk](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>VM-avbildningar

Ange en disk version och SAS-URI: n för avbildningar av virtuella datorer. Lägg till upp till 16 data diskar för varje VM-avbildning. Ange endast en ny avbildnings version per plan i en viss överföring. När en avbildning har publicerats kan du inte redigera den, men du kan ta bort den. Om du tar bort en version så förhindras både nya och befintliga användare från att distribuera en ny instans av den borttagna versionen.

- **Skiv versionen** är versionen av den avbildning som du tillhandahåller.
- **SAS-URI** är platsen i Azure Storage där du har lagrat operativ systemet på den virtuella hård disken.
- Data disk avbildningar är också URI: er för virtuella hård diskar lagrade i Azure Storage.
- Lägg endast till en bild per överföring i en plan.

Oavsett vilket operativ system du använder lägger du endast till det minsta antalet data diskar som behövs för lösningen. Kunder kan inte ta bort diskar som ingår i en avbildning vid tidpunkten för distributionen, men de kan alltid lägga till diskar under eller efter distributionen.

Välj **Spara utkast** innan du fortsätter och återgår till **plan översikt**.

## <a name="resell-through-csps"></a>Att sälja via CSP

Utöka räckvidden för ditt erbjudande genom att göra det tillgängligt för partner i CSP-programmet ( [Cloud solution providers](https://azure.microsoft.com/offers/ms-azr-0145p/) ). Alla BYOL-planer (ta med din egen licens) markeras automatiskt. Du kan välja att välja dina icke-BYOL planer.

Välj **Spara utkast** innan du fortsätter.

## <a name="test-drive"></a>Test enhet

Konfigurera en demonstration (test enhet) som låter kunderna testa ditt erbjudande innan du köper det. Om du vill skapa en demonstrations miljö som gör det möjligt för kunder att testa ditt erbjudande under en viss tids period, se [testköra ditt erbjudande på den kommersiella Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Om du vill aktivera en testen het markerar du kryss rutan Aktivera en testenhet på fliken [erbjudande konfiguration](#test-drive) . Avmarkera den här kryss rutan om du vill ta bort test enheten från erbjudandet.

Ytterligare resurser för test enheter:

- Rekommenderade tekniska metoder
- Metodtips för marknadsföring
- Översikt över test enheter (PDF, se till att blockering av popup-fönster är inaktiverat).

Välj **Spara utkast** innan du fortsätter.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla obligatoriska avsnitt i erbjudandet kan du skicka det till granskning och publicering.

I det övre högra hörnet av portalen väljer du **Granska och publicera**.

På den här sidan kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
  - **Inte startat** – avsnittet har inte startats och måste slutföras.
  - **Ofullständig** – avsnittet innehåller fel som måste åtgärdas eller kräver att du anger mer information. Information om hur du uppdaterar det här avsnittet finns i avsnitten tidigare i det här dokumentet.
  - **Slutförd** – avsnittet är klart och det finns inga fel. Alla avsnitt i erbjudandet måste slutföras innan du kan skicka in erbjudandet.
- **Anmärkningar om certifiering** – ge certifierings teamet till certifierings teamet för att se till att appen testas korrekt. Ge eventuella kompletterande anteckningar som hjälper dig att förstå din app.

Om du vill skicka publicerings erbjudandet väljer du **Granska och publicera**.

Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Om du vill publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande till en privat mål grupp) går du till Partner Center, letar upp ditt erbjudandes **översikts** sida och väljer **Go-Live**. Status för ditt erbjudande visas överst på sidan när publicering pågår.

### <a name="errors-and-review-feedback"></a>Fel och granska feedback

Det **manuella validerings** steget i publicerings processen representerar en omfattande granskning av ditt erbjudande och dess tillhör ande tekniska till gångar. Om den här processen avvisar fel med erbjudandet får du en certifierings rapport som innehåller information om problemen. Gör bara de ändringar som krävs och publicera erbjudandet igen.

## <a name="offer-overview"></a>Erbjudande översikt

På sidan **erbjudande översikt** visas en visuell representation av de steg som krävs för att publicera det här erbjudandet (både slutfört och pågående) och hur lång tid varje steg ska ta att slutföra.

Den här sidan innehåller länkar för att utföra åtgärder på det här erbjudandet baserat på det val du gör. Ett exempel:

- Om erbjudandet är ett utkast – [ta bort utkast erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Om erbjudandet är Live- [sluta att sälja erbjudandet](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Om erbjudandet är i för hands version- [Go-Live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Om du inte har slutfört utloggningen av Publisher- [avbryta publicering](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace-exempel

I de här exemplen visas hur erbjudandet visas i Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Information om Azure Marketplace-erbjudanden

![Exempel på Azure Marketplace-erbjudande information](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Sök Resultat för Azure Marketplace

![Exempel på skärmen Sök information för Azure Marketplace](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Information om Azure Marketplace-prenumeration

![Exempel på Azure Marketplace plan information Screen](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Information om Azure Portal erbjudande

![Exempel på skärmen information om Azure Portal erbjudande](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure Portal Sök Resultat

![Exempel på skärmen för att Azure Portal Sök Resultat](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Information om Azure Portals plan

![Exempel på skärmen information om Azure Portal plan](media/avm-create6.png)

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
