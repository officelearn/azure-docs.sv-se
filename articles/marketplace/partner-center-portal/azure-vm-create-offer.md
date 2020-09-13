---
title: Skapa ett erbjudande för virtuella Azure-datorer på Azure Marketplace
description: Lär dig hur du publicerar ett erbjudande för virtuella datorer på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 08/07/2020
ms.openlocfilehash: b9bacb352b3559c33a4a2ef92dc0c3af6ebbc7d6
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90029710"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Skapa ett erbjudande för virtuella Azure-datorer på Azure Marketplace

Den här artikeln beskriver hur du skapar och publicerar ett erbjudande för virtuella Azure-datorer på [Azure Marketplace](https://azuremarketplace.microsoft.com/). Den behandlar både Windows-baserade och Linux-baserade virtuella datorer som innehåller ett operativ system, en virtuell hård disk (VHD) och upp till 16 data diskar. 

Innan du börjar [skapar du ett kommersiellt marknads plats konto i Partner Center](create-account.md). Se till att ditt konto har registrerats i programmet för kommersiella marknads platser.

## <a name="introduction"></a>Introduktion

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Fördelarna med att publicera på Azure Marketplace

När du publicerar dina erbjudanden på Azure Marketplace kan du:

- Marknadsför ditt företag med hjälp av Microsofts varumärke.
- Når över 100 000 000 Microsoft 365-och Dynamics 365-användare och fler än 200 000-organisationer.
- Få högkvalitativa leads från dessa marknads platser.
- Få dina tjänster upphöjda av Microsoft fält Sales-och inköps teamen för köp.

### <a name="before-you-begin"></a>Innan du börjar

Om du inte har gjort det ännu kan du läsa [publicerings guiden för den virtuella datorn](../marketplace-virtual-machines.md) och den virtuella Azure-datorns material:

- Snabb starts guider
  - [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure snabb starts mallar](https://github.com/azure/azure-quickstart-templates)
- Självstudier
  - [Virtuella Linux-datorer](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [Virtuella Windows-datorer](../../virtual-machines/windows/tutorial-manage-vm.md)
- Exempel
  - [Azure CLI-exempel för virtuella Linux-datorer](../../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell för virtuella Linux-datorer](../../virtual-machines/linux/powershell-samples.md)
  - [Azure CLI-exempel för virtuella Windows-datorer](../../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell för virtuella Windows-datorer](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>Grunderna i teknisk kunskap

Processen för att utforma, skapa och testa erbjudanden tar tid och kräver expertis i både Azure-plattformen och de tekniker som används för att skapa ditt erbjudande.

Ditt tekniska team bör ha en grundläggande förståelse och erfarenhet av följande Microsoft-tekniker:

- [Azure-tjänster](https://azure.microsoft.com/services/)
- [Design och arkitektur för Azure-program](https://azure.microsoft.com/solutions/architecture/)
- [Azure-Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. I det vänstra fönstret väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På sidan **Översikt** väljer du **nytt erbjudande**för  >  **Azure virtuell dator**.

    ![Skärm bild som visar meny alternativ för det vänstra fönstret och knappen "nytt erbjudande".](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> När ditt erbjudande har publicerats visas alla ändringar som du gör i Partner Center på Azure Marketplace först när du har publicerat om erbjudandet. Se till att alltid publicera om ett erbjudande när du har gjort ändringar i det.

## <a name="new-offer"></a>Nytt erbjudande

Ange ett **erbjudande-ID**. Detta är en unik identifierare för varje erbjudande i ditt konto.

- Detta ID är synligt för kunder i webb adressen för Azure Marketplace-erbjudandet och i Azure PowerShell och Azure CLI, om tillämpligt.
- Använd bara gemena bokstäver och siffror. ID: t kan innehålla bindestreck och under streck, men inte blank steg och är begränsat till 50 tecken. Om du till exempel anger **test-erbjudande-1**, är webb adressen för erbjudandet `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Erbjudande-ID: t kan inte ändras när du har valt **skapa**.

Ange ett **erbjudande alias**. Erbjudandets alias är det namn som används för erbjudandet i Partner Center.

- Det här namnet används inte på Azure Marketplace. Det skiljer sig från namnet på erbjudandet och andra värden som visas för kunderna.

Välj **skapa** för att generera erbjudandet och fortsätt.

## <a name="offer-setup"></a>Erbjudande konfiguration

### <a name="test-drive"></a>Test Drive

En *testen het* är ett bra sätt att presentera ditt erbjudande för potentiella kunder. Det ger dem möjlighet att "testa innan du köper", vilket kan hjälpa till att öka dina konverteringar och generera mycket kvalificerade leads. Mer information finns i [Vad är en test-enhet?](../what-is-test-drive.md).

Om du vill aktivera en testenhet under en fast tids period markerar du kryss rutan **Aktivera en testenhet** . Avmarkera kryss rutan om du vill ta bort test enheten från erbjudandet.

Ytterligare resurser för test enheter:

- [Rekommenderade tekniska metoder](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Metodtips för marknadsföring](../what-is-test-drive.md)
- [Hämta Test Drive-översikten](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF-fil (se till att blockering av popup-fönster är inaktiverat).

### <a name="customer-leads"></a>Kund ledare

När du publicerar erbjudandet till den kommersiella marknads platsen med partner Center ansluter du det till ditt CRM-system (Customer Relations hip Management). På så sätt kan du ta emot kund kontakt information så snart någon uttrycker intresse för eller använder produkten. Att ansluta till en CRM krävs om du vill aktivera en testen het (se föregående avsnitt). Annars är det valfritt att ansluta till en CRM.

1. Välj ett leadmål där du vill att vi ska skicka kundleads. Partner Center stöder följande CRM-system:
    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) för kund engagemang
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Om ditt CRM-system inte visas i listan kan du använda [Azure Table Storage](commercial-marketplace-lead-management-instructions-azure-table.md) eller en [https-slutpunkt](commercial-marketplace-lead-management-instructions-https.md) för att lagra kundens lead-data. Exportera sedan data till CRM-systemet.

1. Anslut ditt erbjudande till lead-målet när du publicerar i Partner Center.
1. Bekräfta att anslutningen till lead-målet har kon figurer ATS korrekt. När du har publicerat den i Partner Center, verifierar Microsoft anslutningen och skickar ett test lead. När du för hands Grans kar erbjudandet innan det går live kan du också testa din lead-anslutning genom att försöka distribuera erbjudandet själv i för hands versionen.
1. Se till att anslutningen till mål platsen förblir uppdaterad så att du inte förlorar några leads.

1. Välj **Spara utkast** innan du fortsätter.

## <a name="properties"></a>Egenskaper

På sidan **Egenskaper** definierar du de kategorier som används för att gruppera ditt erbjudande på Azure Marketplace, din program version och de juridiska kontrakt som stöder ditt erbjudande.

### <a name="category"></a>Kategori

Välj kategorier och under Kategorier för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Se till att du beskriver hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. Välj:

- Minst en och upp till två kategorier, inklusive en primär och en sekundär kategori (valfritt).
- Upp till två under Kategorier för varje primär och/eller sekundär kategori. Om ingen under kategori gäller ditt erbjudande väljer du **ej tillämpligt**.

Se den fullständiga listan över kategorier och under Kategorier i [erbjudande lista med bästa praxis](../gtm-offer-listing-best-practices.md). Virtual Machine-erbjudanden visas alltid under **beräknings** kategorin på Azure Marketplace.

### <a name="legal"></a>Juridisk information

Du måste ange villkor för erbjudandet till dina kunder. Du kan välja mellan två alternativ:

- **Använd egna villkor**

   Om du vill ange egna anpassade villkor anger du upp till 10 000 tecken text i rutan **allmänna** villkor. Om du behöver en längre Beskrivning anger du en enskild webb adress för dina allmänna villkor. Den kommer att visas för kunder som en aktiv länk.

   Dina kunder måste acceptera dessa villkor innan de kan testa ditt erbjudande.

- **Använd standard kontraktet för Microsofts kommersiella marknads plats**

   För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft ett standard kontrakt för den kommersiella marknaden. När du erbjuder program varan under standard kontraktet behöver kunderna bara läsa och godkänna det en gång, och du behöver inte skapa anpassade allmänna villkor.

   Använd standard kontraktet genom att markera kryss rutan **Använd standard avtalet för Microsofts kommersiella Marketplace** och välj sedan Godkänn i popup-fönstret. Välj sedan **acceptera** (du kan behöva rulla upp för att visa det).

   ![Skärm bild som visar det juridiska fönstret i Partner Center med kryss rutan Använd standard kontraktet för Microsofts kommersiella marknads platser.](media/use-standard-contract.png)

  > [!NOTE]
  > När du har publicerat ett erbjudande med hjälp av standard kontraktet för den kommersiella marknads platsen kan du inte använda dina egna anpassade villkor. Du kan erbjuda en lösning enligt antingen standard avtalet eller dina egna villkor.

  Mer information finns i [standard kontrakt för Microsoft Commercial Marketplace](../standard-contract.md). Ladda ned PDF-filen med [standard kontrakt](https://go.microsoft.com/fwlink/?linkid=2041178) (se till att blockering av popup-fönster är inaktiverat).

  **Standard kontrakts ändringar**

  Med standard kontrakts ändringar kan du välja standard avtals villkoren för enkelhetens skull och skapa villkoren för din produkt eller verksamhet. Kunderna behöver bara granska ändringarna i kontraktet om de redan har granskat och godkänt Microsofts standard kontrakt. Det finns två typer av ändringar:

  * **Universella ändringar**: dessa ändringar tillämpas universellt på standard kontraktet för alla kunder. De visas för varje kund av erbjudandet i inköps flödet. Kunderna måste godkänna villkoren i standard kontraktet och ändringarna innan de kan använda ditt erbjudande. Du kan ange en enda universell ändring per erbjudande. Du kan ange ett obegränsat antal tecken i den här rutan. Dessa villkor visas för kunder i AppSource, Azure Marketplace och/eller Azure Portal under identifierings-och inköps flödet.

  * **Anpassade ändringar**: dessa särskilda ändringar av standard kontraktet är riktade till specifika kunder via Azures klient-ID. Du kan välja den klient som du vill använda som mål. Endast kunder från klient organisationen visas med villkoren för anpassad ändring i erbjudandets inköps flöde. Kunderna måste godkänna villkoren i standard kontraktet och ändringarna innan de kan använda ditt erbjudande.

    1. Börja med att välja **Lägg till anpassade ändrings villkor (max 10)**. Du kan ange upp till tio anpassade ändrings villkor per erbjudande. Gör följande:

       a. Ange dina egna ändrings villkor i rutan **anpassad ändrings term** . Du kan ange ett obegränsat antal tecken. Endast kunder från klient organisations-ID: n som du anger för de här anpassade villkoren visas i erbjudandet inköps flöde i Azure Portal.

       b. Kunna Ange **klient-ID: n**. Varje anpassad ändring kan riktas mot upp till 20 klient-ID: n. Om du lägger till en anpassad ändring måste du ange minst ett klient-ID som identifierar din kund i Azure. Din kund kan hitta detta åt dig i Azure genom att välja **Azure Active Directory**  >  **Egenskaper**. Värdet för katalog-ID är klient-ID (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också hitta organisationens klient-ID för din kund genom att använda deras domän namns webb adress i [Vad är mitt Microsoft Azure och Microsoft 365 klient-ID?](https://www.whatismytenantid.com/).

       c. Valfritt Ange en egen **Beskrivning** för klient-ID: t som hjälper dig att identifiera den kund som du är mål för med ändringen.

        > [!NOTE]
        > Dessa två typer av ändringar är kopplade till varandra. Kunder som är riktade till anpassade ändringar kommer också att få de allmänna ändringarna i standard kontraktet under köpet.

    1. Välj **Spara utkast** innan du fortsätter.

## <a name="offer-listing"></a>Erbjudande lista

På sidan **erbjudande lista** definierar du erbjudande information, till exempel erbjudande namn, beskrivning, länkar och kontakter.

> [!NOTE]
> Ditt erbjudande som visar innehåll, till exempel beskrivning, dokument, skärm bilder och användnings villkor, behöver inte vara på engelska, så länge som erbjudande beskrivningen börjar med frasen, "det här programmet är endast tillgängligt i \<non-English language> ". Du kan också ange en URL för att länka till en plats som erbjuder innehåll på ett annat språk än det som används i innehålls listan för erbjudandet.

### <a name="marketplace-details"></a>Information om Marketplace

#### <a name="name"></a>Name

Namnet som du anger här visas för kunder som titel på din erbjudande lista. Det här fältet fylls i automatiskt med det namn som du angav i rutan för **erbjudet alias** när du skapade erbjudandet. Du kan ändra det här namnet senare. Namnet:

- Kan vara ett varumärke. Du kan inkludera varumärkes-och Copyright-symboler.
- Får innehålla högst 50 tecken.
- Det går inte att inkludera emojis.

#### <a name="search-results-summary"></a>Sammanfattning av Sök Resultat

Ange en kort beskrivning av ditt erbjudande, som visas i Sök resultaten för Azure Marketplace. Det kan innehålla upp till 100 tecken.

#### <a name="long-summary"></a>Lång Sammanfattning

Ange en längre Beskrivning av ditt erbjudande, som visas i Sök resultaten för Azure Marketplace. Det kan innehålla upp till 256 tecken.

#### <a name="description"></a>Beskrivning

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Länk till sekretess policy

Ange webb adressen (URL) för din organisations sekretess policy. Se till att ditt erbjudande uppfyller sekretess lagar och föreskrifter. Du måste också publicera en giltig sekretess policy på din webbplats.

### <a name="useful-links"></a>Användbara länkar

Ge kompletterande online-dokument om ditt erbjudande. Om du vill lägga till en länk väljer du **Lägg till en länk**och fyller sedan i följande fält:

- **Namn**: kunder kommer att se namnet på informations sidan.
- **Länk (URL)**: Ange en länk som kunder kan använda för att visa ditt online-dokument.

### <a name="customer-support-links"></a>Kund support länkar

Ange support webbplatsen där kunderna kan kontakta support teamet.

- Azure Global support webbplats
- Azure Government support webbplats

### <a name="partner-support-contact"></a>Support kontakt för partner

Ange kontakt information för Microsoft-partner som ska användas när kunderna öppnar ett support ärende. Den här informationen visas inte på Azure Marketplace.

- Name
- E-post
- Telefon

### <a name="engineering-contact"></a>Teknisk kontakt

Ange kontakt information för Microsoft som ska användas när det finns problem med erbjudandet, inklusive problem med certifieringen. Den här informationen visas inte på Azure Marketplace.

- Name
- E-post
- Telefon

### <a name="azure-marketplace-media"></a>Azure Marketplace-Media

Ange logo typer och avbildningar som ska användas med ditt erbjudande. Alla bilder måste vara i PNG-format. Suddiga bilder gör att ditt bidrag avvisas.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Om du har problem med att ladda upp filer kontrollerar du att det lokala nätverket inte blockerar https://upload.xboxlive.com tjänsten som används av Partner Center.

#### <a name="azure-marketplace-logos"></a>Logo typer för Azure Marketplace

Ange en PNG-fil för logo typen med **stor** storlek. Partner Center använder detta för att skapa en **liten** och en **medie** logo typ. Du kan också ersätta dem med olika avbildningar senare.

- **Stor** (från 216 x 216 till 350 x 350 BPT, krävs)
- **Medel** (90 x 90 BPT, valfritt)
- **Liten** (48 x 48 BPT, valfritt)

Dessa Logo typer används på olika platser i listan:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Skärmbilder

Lägg till upp till fem skärm bilder som visar hur ditt erbjudande fungerar. Varje skärm bild måste vara 1280 &times; 720 bild punkter i storlek och i PNG-format. Varje skärm bild måste innehålla en beskrivning.

#### <a name="videos"></a>Videoklipp

Lägg till upp till fem videor som demonstrerar ditt erbjudande. Videor bör finnas på en extern video tjänst. Ange varje video namn, webb adress och en miniatyr bild av videon med 1280 &times; 720 bild punkter.

Mer information om resurser för Marketplace finns i [metod tips för Marketplace-erbjudanden](../gtm-offer-listing-best-practices.md).

Välj **Spara utkast** innan du fortsätter.

## <a name="preview"></a>Förhandsgranskning

Välj fliken **förhands granskning** och välj sedan en begränsad **förhands gransknings grupp** för att verifiera ditt erbjudande innan du publicerar det Live till den bredare webbplatsen för kommersiella marknads platser.

> [!IMPORTANT]
> När du har kontrollerat ditt erbjudande i **förhands gransknings** fönstret väljer du **Go Live** för att publicera ditt erbjudande för den publika publike Marketplace.

Din förhands gransknings mål identifieras av ID: n för Azure Subscription ID, tillsammans med en valfri beskrivning av varje. Inget av dessa fält kan ses av kunder. Du hittar ditt prenumerations-ID för Azure på sidan **prenumerationer** i Azure Portal.

Lägg till minst ett Azure-prenumerations-ID, antingen individuellt (upp till 10 ID) eller genom att ladda upp en CSV-fil (upp till 100 ID). Genom att lägga till dessa prenumerations-ID: n definierar du vem som kan förhandsgranska ditt erbjudande innan det publiceras Live. Om ditt erbjudande redan är Live kan du fortfarande definiera en förhands gransknings grupp för testning av erbjudanden eller uppdateringar av erbjudandet.

> [!NOTE]
> En förhands gransknings mål skiljer sig från en privat publik. En förhands gransknings grupp kan komma åt ditt erbjudande _innan_ det publiceras Live på Azure Marketplace. Förhands gransknings publiken kan se och validera alla planer, inklusive de som endast är tillgängliga för en privat publik när ditt erbjudande har publicerats fullständigt till Azure Marketplace. En privat mål grupp (som definieras i fönstret planera **priser och tillgänglighet** ) har exklusiv åtkomst till en viss plan.

Välj **Spara utkast** innan du fortsätter till nästa avsnitt.

## <a name="plan-overview"></a>Plan översikt

Du kan ange olika plan alternativ inom samma erbjudande i Partner Center. Ett erbjudande kräver minst en plan (tidigare kallat SKU), som kan variera av försäljande mål, Azure-region,-funktioner eller VM-avbildningar.

Du kan skapa upp till 100-planer för varje erbjudande: upp till 45 av dessa kan vara privata. Lär dig mer om privata planer i [privata erbjudanden på Microsofts kommersiella marknads platser](../private-offers.md).

När du har skapat dina planer väljer du fliken **plan översikt** för att visa:

- Planera namn
- Licens modeller
- Mål grupp (offentlig eller privat)
- Aktuell publicerings status
- Tillgängliga åtgärder

De åtgärder som är tillgängliga i fönstret **plan översikt** varierar beroende på planens aktuella status.

- Om plan status är ett utkast väljer du **ta bort utkast**.
- Om plan status är publicerad Live väljer du **stoppa Sälj plan** eller **Synkronisera privat publik**.

### <a name="create-a-new-plan"></a>Skapa en ny plan

Välj **Skapa ny plan** längst upp. Dialog rutan **nytt plan** visas.

Skapa ett unikt plan-ID för varje plan i det här erbjudandet i rutan **plan-ID** . Detta ID visas för kunder i produktens webb adress. Använd bara gemena bokstäver och siffror, bindestreck eller under streck och högst 50 tecken.

> [!NOTE]
> Det går inte att ändra plan-ID när du har valt **skapa**.

I rutan **plan namn** anger du ett namn för den här planen. Kunderna ser det här namnet när de bestämmer vilken plan som ska väljas i erbjudandet. Skapa ett unikt namn som tydligt pekar på skillnaderna mellan planerna. Du kan till exempel ange **Windows Server** med " *betala per*användning"-, *BYOL*-, *Advanced*-och *Enterprise* -planer.

Välj **Skapa**.

### <a name="plan-setup"></a>Planera installationen

Ange den avancerade konfigurationen för typen av plan, ange om den återanvänder en teknisk konfiguration från ett annat abonnemang och identifiera de Azure-regioner där planen ska vara tillgänglig. Dina val här avgör vilka fält som visas i andra fönster för samma plan.

#### <a name="reuse-a-technical-configuration"></a>Återanvänd en teknisk konfiguration

Om du har mer än en plan av samma typ och paketen är identiska mellan dem, kan du välja **den här prenumerationen Återanvänd teknisk konfiguration från ett annat abonnemang**. Med det här alternativet kan du välja något av de andra planerna av samma typ för det här erbjudandet och du kan återanvända den tekniska konfigurationen.

> [!NOTE]
> När du återanvänder den tekniska konfigurationen från ett annat abonnemang försvinner fliken **teknisk konfiguration** från den här planen. Den tekniska konfigurations informationen från den andra planen, inklusive eventuella uppdateringar som du gör i framtiden, kommer även att användas för den här planen. Det går inte att ändra den här inställningen när planen har publicerats.

#### <a name="azure-regions"></a>Azure-regioner

Din plan måste göras tillgänglig i minst en Azure-region.

Välj det **globala Azure** -alternativet för att göra ditt abonnemang tillgängligt för kunder i alla globala Azure-regioner med marknads plats integrering. Mer information finns i [stöd för geografisk tillgänglighet och valuta](../marketplace-geo-availability-currencies.md).

Välj alternativet **Azure Government** för att göra planen tillgängligt i [Azure Government](../../azure-government/documentation-government-welcome.md) region. Den här regionen tillhandahåller kontrollerad åtkomst för kunder från amerikanska federala, statliga, lokala eller stambaserad entiteter, samt för partner som är berättigade att betjäna dem. Du, som utgivare, är ansvariga för alla kontroller, säkerhets åtgärder och bästa praxis. Azure Government använder fysiskt isolerade Data Center och nätverk (som finns i USA).

Innan du publicerar till [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md)ska du testa och validera planen i miljön, eftersom vissa slut punkter kan skilja sig åt. Ställ in och testa planen genom att begära ett utvärderings konto på sidan [Microsoft Azure Government utvärderings version](https://azure.microsoft.com/global-infrastructure/government/request/) .

> [!NOTE]
> När planen har publicerats och är tillgänglig i en speciell Azure-region kan du inte ta bort den regionen.

#### <a name="azure-government-certifications"></a>Azure Government certifieringar

Det här alternativet visas bara om du har valt **Azure Government** som Azure-region i föregående avsnitt.

Azure Government Services hanterar data som omfattas av vissa myndighets bestämmelser och krav. Till exempel FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att få kännedom om dina certifieringar för dessa program kan du ge upp till 100 länkar som beskriver dem. De kan antingen vara länkar till din lista över programmet direkt eller länkar till beskrivningar av dina efterlevnad med dem på dina egna webbplatser. Dessa länkar är bara synliga för Azure Government kunder.

Välj **Spara utkast** innan du fortsätter.

### <a name="plan-listing"></a>Plan lista

I det här avsnittet ska du konfigurera registrerings information för planen. I det här fönstret visas detaljerad information som kan skilja sig från andra planer i samma erbjudande.

#### <a name="plan-name"></a>Plan namn

Det här fältet fylls automatiskt med det namn som du gav din plan när du skapade det. Det här namnet visas på Azure Marketplace som rubrik för den här planen. Den är begränsad till 100 tecken.

#### <a name="plan-summary"></a>Plan Sammanfattning

Ge en kort sammanfattning av din plan, inte erbjudandet. Den här sammanfattningen är begränsad till 100 tecken.

#### <a name="plan-description"></a>Beskrivning av plan

Beskriv vad som gör den här program varu planen unik och Beskriv eventuella skillnader mellan planer i erbjudandet. Beskriv endast planen, inte erbjudandet. Plan beskrivningen kan innehålla upp till 2 000 tecken.

Välj **Spara utkast** innan du fortsätter.

### <a name="pricing-and-availability"></a>Priser och tillgänglighet

I det här fönstret konfigurerar du:

- Marknader där denna plan är tillgänglig. Varje plan måste vara tillgänglig på minst en [marknad](../marketplace-geo-availability-currencies.md).
- Priset per timme.
- Om planen ska vara synlig för alla eller bara för vissa kunder (en privat mål grupp).

#### <a name="markets"></a>Marknaden

Varje plan måste vara tillgänglig på minst en marknad. Markera kryss rutan för varje marknads plats där den här planen ska vara tillgänglig för köp. (Användare på dessa marknader kan fortfarande distribuera erbjudandet till alla Azure-regioner som valts i avsnittet ["planera konfiguration"](#plan-setup) .) Knappen **skatt remitterad** visar länder/regioner där Microsoft betalar och använder moms för din räkning. Publicering till Kina är begränsad till planer som antingen är *kostnads fria* eller har *en egen licens* (BYOL).

Om du redan har angett priser för din plan i valutan US dollar (USD) och lägger till en annan marknads plats, beräknas priset för den nya marknaden enligt aktuella växelkurser. Granska alltid priset för varje marknad innan du publicerar. Granska din prissättning genom att välja **export priser (xlsx)** när du har sparat ändringarna.

När du tar bort en marknad kommer kunder från den marknaden som använder aktiva distributioner inte att kunna skapa nya distributioner eller skala upp sina befintliga distributioner. Befintliga distributioner påverkas inte.

#### <a name="pricing"></a>Prissättning

För **licens modellen**väljer du Använd **månatlig månatlig fakturerings plan** för att konfigurera prissättningen för den här planen eller Välj **Bring Your Own License** så att kunderna kan använda den här planen med sin befintliga licens.

Använd en av följande tre alternativ för pris poster för en användnings-baserad månatlig fakturerings plan:

- **Per kärna**: ange priser per kärna i USD. Microsoft beräknar priset per kärn storlek och omvandlar det till lokala valutor med hjälp av den aktuella växelkursen.
- **Per kärn storlek**: ange priser per kärn storlek i USD. Microsoft beräknar priset och konverterar det till lokala valutor med hjälp av den aktuella växelkursen.
- **Per marknad och kärn storlek**: Ange prissättning för varje kärn storlek för alla marknader. Du kan importera priserna från ett kalkyl blad.

> [!NOTE]
> Spara pris ändringar så att du kan exportera pris information. När du har publicerat ett pris för en marknad i planen kan du inte ändra det senare. För att se till att priserna är rätt innan du publicerar dem, exportera pris kalkyl bladet och granska priserna på varje marknad.

#### <a name="free-trial"></a>Kostnadsfri utvärdering

Du kan erbjuda en månads *kostnads fri utvärderings version* av en månad eller tre månader till dina kunder.

#### <a name="visibility"></a>Visibility (Sikt)

Du kan utforma varje plan som ska vara synlig för alla eller endast till en förvaltad mål grupp. Tilldela medlemskap i den här begränsade mål gruppen med hjälp av ID för Azure-prenumeration.

**Offentlig**: din prenumeration kan ses av alla.

**Privat publik**: gör ditt plan bara synligt för en förvaltad mål grupp. När den har publicerats som en privat plan kan du uppdatera mål gruppen eller ändra den till offentlig. När du har gjort en plan offentlig måste den vara offentlig. Det går inte att ändra tillbaka till en privat plan.

> [!NOTE]
> En privat eller begränsad mål grupp skiljer sig från den förhands visnings mål som du definierade i **förhands gransknings** fönstret. En förhands gransknings grupp kan komma åt ditt erbjudande _innan_ det publiceras Live till Azure Marketplace. Även om valet för den privata publiken bara gäller för en speciell plan, kan förhands gransknings publiken Visa alla privata och offentliga planer i validerings syfte.

**Begränsad mål grupp (Azure-prenumerations-ID)**: tilldela den mål grupp som ska ha åtkomst till den privata planen genom att använda Azures prenumerations-ID. Du kan också ta med en beskrivning av varje Azure-prenumerations-ID som du har tilldelat. Lägg till upp till 10 prenumerations-ID: n manuellt eller upp till 20 000-ID om du importerar ett CSV-kalkylblad. ID: n för Azure-prenumerationen visas som GUID och alla bokstäver måste vara gemena.

>[!Note]
>Privata erbjudanden stöds inte med Azure-prenumerationer som upprättats via en åter försäljare av Cloud Solution Provider-programmet (CSP).

#### <a name="hide-a-plan"></a>Dölj en plan

Om den virtuella datorn är avsedd att användas endast indirekt när den refereras till via en annan lösnings mall eller ett hanterat program, markerar du den här kryss rutan för att publicera den virtuella datorn, men döljer den från kunder som kan söka efter eller bläddra efter den direkt.

> [!NOTE]
> Dolda planer stöder inte för hands versions länkar.

Välj **Spara utkast** innan du fortsätter.

### <a name="technical-configuration"></a>Teknisk konfiguration

Ange de bilder och andra tekniska egenskaper som är associerade med den här planen. Mer information finns i [skapa en teknisk Azure VM-tillgång](create-azure-container-technical-assets.md).

> [!NOTE]
> Fliken **teknisk konfiguration** visas inte om du har konfigurerat den här planen för att återanvända paket från en annan plan på fliken **Planera konfiguration** .

#### <a name="operating-system"></a>Operativsystem

Gör följande i fönstret **operativ system** :

- För **operativ system familj**väljer du operativ systemet **Windows** eller **Linux** .
- För **version** eller **leverantör**väljer du Windows-versionen eller Linux-leverantören.
- För **eget**operativ system namn anger du ett eget operativ system namn. Det här namnet är synligt för kunderna.

#### <a name="recommended-vm-sizes"></a>Rekommenderade VM-storlekar

Välj upp till sex rekommenderade storlekar för virtuella datorer som ska visas på Azure Marketplace.

#### <a name="open-ports"></a>Öppna portar

Öppna offentliga eller privata portar på en distribuerad virtuell dator.

#### <a name="storage-option-for-deployment"></a>Lagrings alternativ för distribution

För **disk distributions alternativ**väljer du den typ av disk distribution som dina kunder kan använda för den virtuella datorn. Microsoft rekommenderar att du endast begränsar distributionen till **hanterad disk distribution** .

#### <a name="properties"></a>Egenskaper

För **stöd för accelererat nätverk**väljer du om den virtuella datorn har stöd för [accelererat nätverk](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>VM-avbildningar

Ange en disk version och URL: en för signaturen för delad åtkomst (SAS) för de virtuella dator avbildningarna. Lägg till upp till 16 data diskar för varje VM-avbildning. Ange endast en ny avbildnings version per plan i ett angivet överförings objekt. När en avbildning har publicerats kan du inte redigera den, men du kan ta bort den. Om du tar bort en version förhindrar det att både nya och befintliga användare distribuerar en ny instans av den borttagna versionen.

- **Skiv version**: den version av avbildningen som du tillhandahåller.
- **SAS-URI**: platsen i ditt Azure Storage-konto där du har lagrat operativ systemet på den virtuella hård disken. Information om hur du hämtar en SAS-URI finns i [Hämta signatur-URI för delad åtkomst för din virtuella dator avbildning](get-sas-uri.md).
- Data disk avbildningar är också URI: er för delad åtkomst för virtuella hård diskar som lagras i deras Azure Storage-konton.
- Lägg endast till en bild per överföring i en plan.

Oavsett vilket operativ system du använder lägger du endast till det minsta antalet data diskar som krävs för lösningen. Under distributionen kan kunderna inte ta bort diskar som ingår i en avbildning, men de kan alltid lägga till diskar under eller efter distributionen.

Välj **Spara utkast** innan du fortsätter och återgå till **plan översikt**.

## <a name="resell-through-csps"></a>Att sälja via CSP

Utöka räckvidden för ditt erbjudande genom att göra det tillgängligt för partner i program varan [Cloud Solution Provider (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) . Alla BYOL-planer () är automatiskt inloggade i programmet. Du kan också välja att välja icke-BYOL planer.

Välj **Spara utkast** innan du fortsätter.

## <a name="test-drive"></a>Test Drive

Konfigurera en demonstration, eller *Testkör*, som gör att kunderna kan testa ditt erbjudande under en bestämd tids period innan de köper det. För att skapa en demonstrations miljö för dina kunder, se [test av enhets erbjudanden på den kommersiella Marketplace](test-drive.md).

Om du vill aktivera en testen het markerar du kryss rutan **Aktivera en testenhet** i fönstret för **erbjudande inställningar** . Avmarkera kryss rutan om du vill ta bort test enheten från erbjudandet.

Ytterligare resurser för test enheter:

- [Metodtips för marknadsföring](../what-is-test-drive.md)
- [Rekommenderade tekniska metoder](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Översikt över test enhet](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF-fil (kontrol lera att blockering av popup-fönster är inaktive rad)

Välj **Spara utkast** innan du fortsätter.

## <a name="review-and-publish"></a>Granska och publicera

När du har slutfört alla nödvändiga avsnitt i erbjudandet kan du skicka det till granskning och publicering.

I det övre högra hörnet väljer du **Granska och publicera**.

I det här fönstret kan du:

- Visa slut för ande status för varje avsnitt i erbjudandet:

  - **Inte startat**: avsnittet har inte startats och måste slutföras.
  - **Ofullständig**: avsnittet innehåller fel som måste åtgärdas eller kräver att du anger mer information. Information om hur du uppdaterar ofullständig information finns i den här artikelns tidigare avsnitt.
  - **Slutför**: avsnittet är klart och det finns inga fel. Alla avsnitt i erbjudandet måste slutföras innan du kan skicka in erbjudandet.
- Lämna **kommentarer** till certifierings teamet för att se till att programmet testas korrekt. Inkludera test instruktioner och eventuella kompletterande anteckningar som kan hjälpa teamet att förstå ditt program.

Om du vill skicka publicerings erbjudandet väljer du **Granska och publicera**.

Microsoft skickar ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Publicera ditt erbjudande till allmänheten (eller, om det är ett privat erbjudande, publicera det till en privat mål grupp), gå till Partner Center, gå till ditt erbjudandes **översikts** sida och välj **Go-Live**. Status för ditt erbjudande visas längst upp på sidan när publicering pågår.

### <a name="errors-and-review-feedback"></a>Fel och granska feedback

Det **manuella validerings** steget i publicerings processen representerar en omfattande granskning av ditt erbjudande och dess tillhör ande tekniska till gångar. Om den här processen upptäcker fel med erbjudandet får du en certifierings rapport som innehåller information om problemen. Gör bara de ändringar som krävs och publicera erbjudandet igen.

## <a name="offer-overview"></a>Erbjudande översikt

Sidan **erbjudande översikt** visar en visuell representation av stegen, både slutförda och pågående, som krävs för att publicera ditt erbjudande och hur lång tid varje steg tar att slutföra.

Den här sidan innehåller också länkar som hjälper dig att arbeta med erbjudandet, beroende på dess status:

- Om erbjudandet är ett utkast: [ta bort utkast erbjudande](update-existing-offer.md#delete-a-draft-offer)
- Om erbjudandet är Live: [sluta sälja erbjudandet](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Om erbjudandet är i för hands version: [Go-Live](publishing-status.md#publisher-approval)
- Om du inte har slutfört utloggningen av utgivare: [Avbryt publicering](update-existing-offer.md#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace-exempel

Här är ett exempel på hur information om erbjudandet visas på Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="Visar hur det här erbjudandet visas på Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Stor logo typ
2. Pris
3. Kategorier
4. Villkor
5. Sekretess policy adress (länk)
6. Erbjudandets namn
7. Beskrivning
8. Användbara länkar
9. Skärm bilder/videor

<br>Här är ett exempel på hur information om erbjudandet visas i Sök resultaten för Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="Visar hur det här erbjudandet visas i Sök resultaten för Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Liten logo typ
2. Erbjudandets namn
3. Sammanfattning av Sök Resultat
4. Utvärdering

<br>Här är ett exempel på Azure Marketplace-plan information:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="Visar information om Azure Marketplace-planer.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Planera namn och sammanfattning
2. Rekommendera VM-storlekar
3. Planera priser

<br>Här är ett exempel på hur information om erbjudandet visas i Azure Portal:

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="Visar hur det här erbjudandet visas i Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Name
2. Beskrivning
3. Användbara länkar
4. Skärm bilder/videor

<br>Här är ett exempel på hur information om erbjudandet visas i Azure Portal Sök Resultat:

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="Visar hur det här erbjudandet visas i Azure Portal Sök resultat.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Liten logo typ
2. Erbjudandets namn
3. Sammanfattning av Sök Resultat

<br>Här är ett exempel på Azure Portal Plans information:

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="Visar Azure Portal Plans information.":::

#### <a name="call-out-descriptions"></a>Beskrivningar av anrop

1. Plan namn
2. Beskrivning av plan

## <a name="next-step"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](update-existing-offer.md)
