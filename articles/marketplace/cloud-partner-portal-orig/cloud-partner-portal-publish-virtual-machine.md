---
title: Erbjudande för virtuell dator av författaren | Microsoft Docs
description: Publicera en virtuell dator på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 9a2512c488ac368205da11d79de479a888735729
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448711"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Publicera en virtuell dator på Azure Marketplace

Den här artikeln innehåller de steg som du vill publicera ett erbjudande för virtuell dator på Azure Marketplace.

## <a name="prerequisites"></a>Förutsättningar

Följande tekniska och icke-tekniska krav som gäller för att publicera en virtuell dator på Azure Marketplace

### <a name="technical"></a>Teknik

-   [Tekniska krav för att skapa en avbildning av virtuell dator för Azure Marketplace](../cloud-partner-portal/virtual-machine/cpp-create-technical-assets.md)
-   [Skapa och ladda upp en VHD för Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
-   [Skapa och testa en Linux VM från en avbildning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)
-   [Skapa och ladda upp en VHD för Windows ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)
-   [Skapa och testa en virtuell Windows-dator från en avbildning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)
-   [Så här felsöker du vanliga problem som uppstod vid skapande av virtuell Hårddisk](../cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues.md)
-   [Säkerhetsrekommendationer för Azure Marketplace-avbildningar](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>Icke-teknisk (affärskrav)

 -   Ditt företag (eller dess filial) finns i ett köpland som stöds av Azure Marketplace
-   Produkten måste ha licens på ett sätt som är kompatibel med faktureringsmodellerna som stöds av Azure Marketplace
-   Du är ansvarig för att göra teknisk support tillgänglig för kunder på ett kommersiellt rimligt sätt. Det här stödet kan erbjudas utan kostnad, betalning eller via support.
-   Du är ansvarig för att licensiera ditt program och eventuella beroenden för programvara från tredje part.
-   Du kan ange innehåll som uppfyller villkoren för ditt erbjudande ska visas på Azure Marketplace och i Azure-portalen.
-   Du samtycker till villkoren i Deltagandepolicyer för Azure Marketplace och avtalet för utgivare.
-   Du samtycker till att följa den [användningsvillkor](https://azure.microsoft.com/support/legal/website-terms-of-use/) , [Microsoft Privacy Statement](https://www.microsoft.com/privacystatement/default.aspx), och [Microsoft Azure Certified-programavtalet](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="before-you-begin"></a>Innan du börjar

Du kan börja redigera erbjudandet lösningsmallen när du har uppfyllt alla krav. Innan du börjar måste du granska följande erbjudande och SKU-information.

**Erbjudande**

Ett program för Azure-erbjudande motsvarar en klass av produkten tjänsterbjudande från en utgivare. Om du har en ny typ av lösning/program som du vill ska vara tillgängliga i Azure Marketplace är ett nytt erbjudande den bästa metoden. Ett erbjudande är en samling SKU: er. Varje erbjudande visas som en egen enhet på Azure Marketplace.

**SKU**

En SKU är den minsta köpbara enheten i ett erbjudande. Under samma produkt klass (erbjudandet), kan du skilja mellan olika funktioner som stöds i SKU: er. Till exempel erbjudandet är hanterade eller ohanterade och olika faktureringsmodellerna stöds.

Lägg till flera SKU: er i följande scenarier:
- Du vill kunna använda olika faktureringsmodellerna, till exempel Bring Your Own License (BYOL) eller betala (PAYG).
- Varje SKU stöder en mängd olika funktionsuppsättningar och varje funktionsuppsättningen prissätts på olika sätt.

En SKU visas den under det överordnade erbjudandet på Azure Marketplace och visas som en egen köpbara entitet i Azure-portalen.

## <a name="to-create-a-new-offer"></a>Skapa ett nytt erbjudande

1.  Logga in på den [partnerportalen i molnet](http://cloudpartner.azure.com/).

2.  På vänstra navigeringsfältet väljer **+ nytt erbjudande**, och välj sedan **virtuella datorer**.

    ![Nytt erbjudande för virtuella datorer](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  Under **nytt erbjudande**väljer **redigeraren**.

![Nytt erbjudande redigeraren](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  Under **redigeraren**, anger du informationen i följande vyer:
    - Erbjudandeinställningar
    - SKU:er
    - Marketplace
    - Stöd för varje vy innehåller en uppsättning fält för att fylla. Obligatoriskt fält är markerade med en röd asterisk (\*)

## <a name="to-configure-offer-settings"></a>Konfigurera inställningar för erbjudande

1. Konfigurera följande **erbjuder identitet** fält i erbjuder inställningar.

    **Erbjudande-ID**

     En unik identifierare för erbjudandet i en utgivarprofil för. Detta ID visas i produkten URL: er, Azure Resource Manager-mallar och fakturering rapporter. Du kan endast använda gemena alfanumeriska tecken eller bindestreck (-). ID: T får inte sluta med ett bindestreck och kan ha högst 50 tecken. Exempel: om en utgivare **contoso** publicerar ett erbjudande med erbjudande-ID **exempel vm**, visas den i Azure marketplace som **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview**  
    >[!Note]
    >Det här fältet är låsta när ett erbjudande lanseras.

    **Utgivar-ID**

    En listruta för publisher-profilen. Välj den profil som du vill publicera erbjudandet under. 
    >[!Note]
    >Det här fältet är låsta när ett erbjudande lanseras.

    **Namn**

    Visningsnamn för ditt erbjudande. Det här namnet visas i Azure Marketplace och i Azure-portalen. Det får innehålla högst 50 tecken. Använd följande riktlinjer för erbjudandenamn:
    -  Ange ett igenkännligt varumärke för produkten. 
    - Omfattar inte företagets namn här såvida detta inte hur erbjudandet släpps.
    - Om du marknadsföring erbjudandet på din egen webbplats, kontrollera att namnet är identiskt med namnet på din webbplats.

2. Välj **spara** Slutför erbjuder inställningar.

## <a name="to-create-a-sku"></a>Att skapa en SKU

1. Välj **SKU: er**. 
2. Välj **Lägg till en SKU** att ange en SKU-ID. SKU-ID: T är en unik identifierare för SKU: N i ett erbjudande. Detta ID visas i produkten URL: er, Azure Resource Manager-mallar och fakturering rapporter. SKU-ID:
    - Kan bara ha högst 50 tecken.
    - Endast bestå av alfanumeriska gemener eller bindestreck (-).
    - ID:t får inte avslutas med ett bindestreck.

    ![Lägg till en SKU](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>Konfigurera SKU-information

När du lägger till en SKU, visas den i listan över SKU: er i vyn SKU: er. Välj SKU-namnet för att se SKU-information. Du kan använda den detaljerade vyn för att lägga till information i följande fält.

### <a name="hide-this-sku"></a>Dölj den här SKU: N

Använd den här inställningen för att hantera SKU visas. Om ”dölja den här SKU” stängs av SKU: N är synlig i [Azure Marketplace](https://azuremarketplace.microsoft.com) och i [Azure-portalen](https://portal.azure.com/) till kunder. Du kanske vill dölja SKU: N om du bara vill att den tillgänglig via lösningsmallar och inte för köp individuellt.

### <a name="cloud-availability"></a>Tillgänglighet på moln

Det här fältet kan du definiera tillgängligheten för din SKU i olika Azure-moln.

**Offentlig Azure**

Denna SKU kan distribueras till kunder i alla offentliga Azure-regioner som har Marketplace-integration.

**Azure Government Cloud**

Denna SKU kan distribueras i Azure Government-molnet. Innan publicering till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), rekommenderar vi utgivare testa och validera sin lösning fungerar som förväntat. Till samt test, [begär ett utvärderingskonto](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). 

>[!Note]
>Microsoft Azure Government är ett myndighetscommunitymoln med kontrollerad åtkomst för kunder från den amerikanska federala myndigheter, tillstånd, lokal eller stambaserad, och partner berättigade att hantera dessa enheter.

### <a name="countryregion-availability"></a>Tillgänglighet för land/Region

Det här fältet identifierar de regioner där dina SKU: N ska vara tillgängligt för köp. Du måste du noga överväga där du tillgängliggöra dina SKU: er. Vissa länder klassificeras som ”Microsoft skatt efterges land”.

-   I ”Microsoft skatt efterges land”, Microsoft samlar in skatter från kunder och betalar (inkomna ärenden) skatt till myndigheterna.

-   Partner är ansvarig för att samla in skatter kunder och betala in skatt till myndigheterna i andra länder. Du måste ha möjlighet att beräkna och betalar in i dem om du vill sälja i dessa länder.

![Välj land/Region tillgänglighet](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>Prissättning

Två prismodellerna stöds för närvarande.

#### <a name="bring-your-own-license-byol"></a>Bring-Your-Own-License (BYOL)

Du hanterar licensieringen av den programvara som körs på den virtuella datorn. Microsoft debiterar bara infrastrukturkostnaderna.

#### <a name="usage-based-monthly-billed-sku"></a>Användning baserat per månad faktureras SKU

Kunder debiteras på basis av per timme baserat på priserna som anges av utgivare på VM-storlekar. Om den **timme** modellen används blir det totala priset summering av kostnad för programvara som debiteras utgivaren och infrastrukturkostnader debiteras av Microsoft. Den totala kostnaden ska visas för kunden som ett pris per timme och månatliga när de överväger köpet. Fakturering kommer i det här fallet att per månad.

Det finns ytterligare inställningar som krävs av dig i användningsbaserad-modellen.

**Kostnadsfri utvärderingsversion**

Du kan ange om du vill ge en kostnadsfri utvärderingsversion för dina kunder.
Här debiteras kunden inte för kostnad för programvara för de första 30/90 dagarna (beroende på markeringen) när du har distribuerat den virtuella datorn. När den kostnadsfria utvärderingsperioden har gått ut debiteras de på basis av per timme baserat på priserna som anges av utgivare i modellen per timme.

**Per kärna priser**

Du kan ange priser per kärna för din SKU. För det här alternativet behöver du bara ange en baspris för en enda kärna och vi automatiskt-beräkning priserna för resten av kärnorna. Ange priserna i USD i portalen och vi kommer automatisk – beräkna priser för andra regioner. Du kan kontrollera priserna i andra regioner med hjälp av **exportera priser för Data**

![Per kärna priser](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**Diskret priser**

Du kan ange prisinformationen för varje uppsättning kärnor separat om du skulle vilja pris varje kärna separat.

![Diskret priser](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**Priser för export-Import**

Du har möjlighet att exportera de priser som har konfigurerats via portalen för att göra ändringar via excel-gränssnittet. Det här alternativet kan du kontrollera priser per region och priser i lokal valuta.
När du klickar på **Export-priser** hämtas en Excel-fil med prisinformation förifylld. Du kommer att kunna redigera detaljerna i kalkylbladet och sedan använda **Import-priser** att importera de ändringar som gjorts.
Importerade priserna visas i portalen samt.

I den här prissättningen excel visas priserna för de olika regionerna i lokal valuta. En valutakurs som vi använder uppdateras dagligen.

![Priser med valutakurs exempel export-import](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   Priserna kan inte ändras när ett erbjudande lanseras. Du kan dock fortfarande att kunna lägga till eller ta bort regioner som stöds.
>-   Detta är priset debiteras för användaren förutom [Azure\'s priser för virtuella datorer](https://aka.ms/vmpricingdetails).
>-   Priserna anges för alla regioner i lokal valuta, med hjälp av de tillgängliga valuta priserna vid tidpunkten för att ange priser.
>-   För att ställa in eller visa priset för varje region individuellt, exportera prissättning kalkylbladet och importera med anpassad prissättning.

## <a name="vm-images"></a>VM-avbildningar

Nästa avsnitt för att slutföra kommer att avsnittet avbildningar av Virtuella datorer. Du måste ha den virtuella Hårddisken som du vill publicera klar innan du fortsätter till det här avsnittet. Här följer några länkar som hjälper dig att skapa en virtuell Hårddisk:

-   [Tekniska krav för att skapa en avbildning av virtuell dator för Azure Marketplace](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md)
-   [Skapa och ladda upp en VHD för Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
-   [Skapa och testa en Linux VM från en avbildning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)
-   [Skapa och ladda upp en virtuell Windows-Hårddisk ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)
-   [Skapa och testa en virtuell Windows-dator från en avbildning](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)
-   [Så här felsöker du vanliga problem som uppstod vid skapande av virtuell Hårddisk](../cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues.md)

När du har virtuella Hårddisken är redo kan börja du fylla i det här avsnittet.  Här är viss information för vissa fält.

### <a name="recommended-vm-sizes"></a>Rekommenderade storlekar för Virtuella datorer

Välj upp till sex rekommenderade virtuella datorstorlekar. Dessa rekommendationer visas för kunder i Azure Marketplace och i bladet prisnivå i Azure portal när de beslutar sig att köpa och distribuera din avbildning. *De här storlekarna är bara rekommendationer. Kunden kan välja vilken storlek som kan hanterar diskarna som specificerats i din avbildning.*  Följande skärmdump visar de rekommendera storlekar som en kund kommer att se i Azure-portalen.


![Rekommenderade storlekar för Virtuella datorer](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>Öppna portar

Ange vilka portar som du vill ha gjort öppen och tillgänglig. Dessa portar är öppna under den här distributionen av virtuella datorer.

### <a name="adding-vm-images"></a>Att lägga till avbildningar av Virtuella datorer

Nästa steg är att lägga till en VM-avbildning för din SKU. Du kan lägga till upp till åtta versioner per SKU. Endast det högsta disk versionsnumret för en viss SKU visas på Azure Marketplace. Andra kommer att vara synliga via API: er.

Under **Disk version**väljer **+ ny version**. Det här alternativet visar följande fält som du behöver för att fylla i.

#### <a name="vm-image-version"></a>Versionsnumret för VM-avbildningen

Versionsnumret för VM-avbildningen måste följa den [sémantickou verzi](http://semver.org/) format. Versionerna bör vara i formatet X.Y.Z, där X, Y och Z är heltal. Bilder i olika SKU: er kan ha olika versioner av högre och den lägre. Versioner i en SKU bör endast vara inkrementella ändringar, som ökar Uppdateringsversion (Z från X.Y.Z).

#### <a name="os-vhd-url"></a>OS-VHD-URL

Ange den [signatur för delad åtkomst URI](../cloud-partner-portal/virtual-machine/cpp-get-sas-uri.md) skapade för det virtuella Hårddisken för operativsystemet.

Om det finns datadiskar kopplade till denna SKU, kan du lägga till dessa diskar genom att välja den **+ ny datadisk** länk. Den här åtgärden visar ytterligare fält för att fylla.

#### <a name="lun-vhd-url"></a>LUN VHD URL

Ange den [signatur för delad åtkomst URI](../cloud-partner-portal/virtual-machine/cpp-get-sas-uri.md) för din datadisk.

#### <a name="lun-number"></a>LUN-numret

Tilldela denna LUN till ett tal. Det här talet reserveras för den här datadisk i denna SKU.

>[!Note]
>När du har publicerat en SKU med en viss VM-version och vilka datadiskar blir utelåst och kan inte ändras. Antalet datadiskar som krävs för stöd för att ytterligare VM-versioner som läggs till i SKU: N, kan inte ändra.

#### <a name="common-sas-url-issues--fixes"></a>Vanliga problem med SAS-URL och korrigeringar

| Problem                                                                 | Meddelande                                                                           | Korrigera                                                           |  Länk till dokumentationen                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| Det gick inte att kopiera avbildningar - ””? inte hittas i SAS-url                | Fel: Kopiera avbildningar. Det går inte att hämta blob med hjälp av angivna SAS-Uri.       | Uppdatera SAS-Url med rekommenderade verktyg                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Det gick inte att kopiera avbildningar - ”a” och ”se” parametrar inte i SAS-url   | Fel: Kopiera avbildningar. Det går inte att hämta blob med hjälp av angivna SAS-Uri.        | Uppdatera SAS-webbadressen med Start- och slutdatumen på den             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Det gick inte att kopiera avbildningar - ”sp = rl” inte i SAS-url                    | Fel: Kopiera avbildningar. Kunde inte hämta blob med hjälp av angivna SAS-Uri         | Uppdatera SAS-webbadressen med behörigheterna inställda som ”läsa” och ”-lista     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Det gick inte att kopiera avbildningar - SAS-webbadress har blanksteg i namn på virtuell hårddisk     | Fel: Kopiera avbildningar. Det går inte att hämta blob med hjälp av angivna SAS-Uri.        | Uppdatera SAS-URL: en utan blanksteg                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| Det gick inte att kopiera avbildningar – Url-auktorisering i SAS-fel               | Fel: Kopiera avbildningar. Det går inte att ladda ned blob på grund av auktoriseringsfel     | Återskapa SAS-Url                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>Konfigurera Marketplace

Använd vyn Marketplace för att konfigurera de fält som visas för erbjudandet på [Azure Marketplace](https://azuremarketplace.microsoft.com) på [Azure-portalen](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Förhandsversion av prenumerations-ID: N

Lista med Azure-prenumeration-ID som du vill ha åtkomst till erbjudandet om erbjudandet har publicerats. Dessa white visas prenumerationer kan du testa förhandsgranskade erbjudandet innan du gör den live. Partnerportalen låter dig placera upp till 100 prenumerationer i listan över tillåtna.

### <a name="suggested-categories"></a>Föreslagna kategorier

Välj upp till fem kategorier från den angivna listan som ditt erbjudande bäst kan associeras med. De valda kategorierna som används för att mappa ditt erbjudande till produktkategorierna i [Azure Marketplace](https://azuremarketplace.microsoft.com) och [Azure-portalen](https://portal.azure.com/).

I följande exempel visas information för marketplace i Azure Marketplace och Azure-portalen.

**Azure Marketplace**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


### <a name="logo-guidelines"></a>Riktlinjer för logotyp

Följ dessa riktlinjer för logotyper som överförts till Cloud Partner Portal:

-   Azure-designen har en enkel färgpalett. Håll nere antalet primära och sekundära färger på din logotyp.

-   Temafärger i Azure Portal är vit och svart. Undvik att använda dessa färger som bakgrundsfärgen för din logotyper. Använd en färg som gör din logotyper framstående i Azure-portalen. Vi rekommenderar enkla primärfärger.

    >[!Note] 
    >Om du använder en genomskinlig bakgrund och sedan kontrollera att logotyper/text inte är vit, svart eller blå.

-   Använd inte en tonad bakgrund för logotypen.

-   Undvik att placera text, inklusive företaget eller varumärke på logotypen.  Utseendet och känslan av din logotyp måste vara *fast* och du bör undvika att toningar.

-   Logotypen bör inte att sträckas ut.

#### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfritt. Utgivaren kan du inte ladda upp en Hero-logotyp. Men när logotypen har överförts, den kan inte tas bort. Partnern måste följa riktlinjerna för Azure Marketplace för Hero-ikoner.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Riktlinjer för ikonen Hero-logotyp

-   Visningsnamn för utgivaren, plan rubrik och erbjudandets lång sammanfattning visas med ett vitt färgade teckensnitt. Undvik att använda alla ljusare i bakgrunden. Svart, vit och transparent bakgrund är inte tillåtna för Hero-ikoner.

-   Utgivaren visningsnamn, plan rubrik, lång sammanfattning av erbjudandet och knappen Skapa är inbäddade programmässigt inuti Hero-logotypen när erbjudandet anges. Ange inte någon text när du utformar Hero-logotypen. Lämna ett tomt utrymme till höger om logotypen. Här ska vara 415 × 100 bildpunkter och förskjutas av 370 px från vänster.

![Exempel på Hero-logotyp](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>Lead-hantering

Så här konfigurerar du lead hanteringsinställningar erbjudandets [instruktionerna](./cloud-partner-portal-get-customer-leads.md).

## <a name="to-configure-support"></a>Konfigurera stöd

Använd vyn stöd för att ange följande information:

- Stöd för kontakter från ditt företag, till exempel tekniker.
- Kontakter för Customer support.

## <a name="to-publish-the-offer"></a>Att publicera erbjudandet

Det sista steget är att publicera erbjudandet. Följ [dessa instruktioner för att publicera ditt erbjudande](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md).
