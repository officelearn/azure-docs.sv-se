---
title: Förbereda och testa ditt erbjudande för distribution till Azure Marketplace | Microsoft Docs
description: Detaljerade anvisningar om hur du tillhandahåller marknadsföring innehåll, konfiguration av prisinformation och testa ditt erbjudande innan du distribuerar på Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714967"
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Skapa erbjudandet med marknadsföringsinnehåll
I det här steget av publiceringsprocessen behöver att tillhandahålla vissa marknadsföringsinnehåll och information om ditt erbjudande och/eller SKU: er i Azure Marketplace. Du kommer till exempel ange en beskrivning av din produkt, logotyp, priset planer, information om planer och annan information som krävs för att skicka ditt erbjudande och/eller SKU: N till mellanlagring. Den här informationen används som marknadsföringsinnehåll i Azure-portalen. Du börjar den här processen i den [publiceringsportalen][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Steg 1: Ange Marketplace marknadsföring innehåll
**Engelska är standard och enda språk som stöds.** Se till att all information som anges i fälten är på engelska. Du kan redigera all information när du vill fram till dess att du väljer att mellanlagra.

1. Gå till publiceringsportalen, [ https://publish.windowsazure.com ](https://publish.windowsazure.com).
2. På menyn till vänster klickar du på den **marknadsföring** fliken.
3. I den huvudsakliga panelen klickar du på den **engelska (US)** knappen.
   
   > [!IMPORTANT]
   > Alla fält måste vara ifyllda, inklusive bilderna, för att du ska kunna skicka till mellanlagringen.
   > 
   > 

### <a name="details-and-plans"></a>Uppgifter och planer
1. Ange rubriken för erbjudandet (högst 50 tecken), erbjuder Sammanfattning (högst 100 tecken), erbjuder lång Sammanfattning (högst 256 tecken), erbjuder beskrivning (maximalt 1300 tecken), logotyper under den **information** fliken
2. Ange plan rubrik (högst 50 tecken), plan Sammanfattning (högst 100 tecken), bör du planera beskrivning (maximalt 2 000 tecken) under den **planer** fliken.
   
   > [!NOTE]
   > Du kan använda följande HTML-taggar för att formatera sammanfattningen, lång sammanfattning och beskrivning av erbjudandet och planer. Tillåtna HTML-taggar är h1, h2, h3, h4, h5, p, ol, ul, li, en [target | href], stark, em, b, jag.
   > 
   > 
3. Ange inte dubbla text under erbjudandet och planera beskrivning.
4. Ange inte dubbla text under planens rubrik och lång sammanfattning av erbjudandet.
5. Inte ange dubbla text under planen titeln och erbjuder sammanfattning.
6. Ange inte identiska plan rubriker för ett erbjudande med flera planer.
7. Ladda upp bilder enligt specifikationerna (som nämns i Publiceringsportalen) i PNG-format, en för varje storlek.
8. Se till att logotyperna följer Azure Marketplace-Logotypriktlinjerna nedan.
   
   ![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Riktlinjer för Azure Marketplace-logotyp**

Alla logotyper som laddades upp i Publiceringsportalen bör följa den riktlinjerna nedan:

* Azure-designen har en enkel färgpalett. Håll nere antalet primära och sekundära färger på din logotyp.
* Temafärger i Azure Portal är vit och svart. Undvik därför att använda dessa färger som bakgrundsfärgen för din logotyper. Använd en färg som gör din logotyper framstående i Azure-portalen. Vi rekommenderar enkla primärfärger. **Om du använder genomskinlig bakgrund, se till att logotyper/text inte är vit eller svart eller blå.**
* Använd inte en toning bakgrund på logotypen.
* Undvik att placera text, även företaget eller varumärke namn på logotypen. Utseendet och känslan av din logotyp måste vara ”fast” och Undvik toningar.
* Logotypen bör inte vara har sträckts ut.
* Liten logotyp bör vara stora 40 X 40 bildpunkter
* Medelhög logotyp bör vara stora 90 X 90 bildpunkter
* Stor logotyp bör vara stora 115 X 115 px
* Brett logotypen måste vara av storleken 255 X 115 px
* Hero-logotypen bör vara stora 815 X 290 px

> [!NOTE]
> Hero-logotypen är valfritt. Utgivaren kan du inte ladda upp en Hero-logotyp. Men en gång överförda ikonen hero-komponenten inte kan tas bort från publiceringen portal. Då måste partnern följer du riktlinjerna för Azure Marketplace för Hero-ikoner.
> 
> 

  ![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Riktlinjer för Hero-logotypen ikonen (valfritt)**

* Hero-logotypen är valfritt. Utgivaren kan du inte ladda upp en Hero-logotyp. **Men en gång överförda ikonen hero-komponenten inte kan tas bort från publiceringen portal. Då måste partnern följer du riktlinjerna för Azure Marketplace för Hero ikoner annan erbjudandet inte kommer godkännas till produktion.**
* Visningsnamn för utgivaren, plan rubrik och erbjudandet lång sammanfattning visas i vit teckenfärg. Därför bör du undvika att hålla alla ljusare i bakgrunden på Hero-ikonen. Svart, vit och transparent bakgrund är inte tillåtet för Hero-ikoner.
* Visningsnamn för utgivaren, plan rubrik, lång sammanfattning av erbjudandet och knappen Skapa är inbäddade programmässigt inuti Hero-logotypen när erbjudandet går visas. Så bör du inte ange valfri text när du utformar Hero-logotypen. Lämna tomt utrymme till höger eftersom text (d.v.s. publisher visningsnamn, plan titel, lång sammanfattning av erbjudandet) kommer att inkluderas programmässigt av oss där. Det tomma utrymmet för texten bör vara 415 × 100 till höger (och det är förskjutas av 370px från vänster).
  
  ![Rita](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Länkar
På den **länkar** fliken i det vänstra fältet, anger du länkar till information som kan hjälpa kunderna. Ange ett namn och en URL för varje länk.

![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Exempelbilder (valfritt)
> [!NOTE]
> Det är ett valfritt steg för att inkludera en exempelbild.
> Trots att du kan ladda upp flera Exempelbilder i avsnittet Publicera portal, endast en avbildning (slumpmässigt vald av systemet) hämtar visas i Azure-portalen. Därför rekommenderar vi att ladda upp högst en exempel-avbildning.
> 
> 

På den **Exempelbilder** fliken på den vänstra menyn, ladda upp en ny avbildning genom att klicka på **ladda upp en ny avbildning**. Om du har en befintlig avbildning och du vill ersätta den, klickar du på **Ersätt avbildningen**.

![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Juridisk information
På den **juridiska** fliken, anger du en länk till dina policyer/villkor för användning. Skriv eller klistra in villkoren i den stora **användningsvillkor** box. Maximalt antal tillåtna tecken för de juridiska villkoren för användning är 1 000 000 tecken.

![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Obs:** för virtuell dator-erbjudanden, när ett erbjudande/SKU mellanlagras i Azure-portalen du inte ändra de fält som anges nedan:

* **Erbjudande-ID:** [Publiceringsportal -> virtuella datorer -> ditt erbjudande avbildningar av Virtuella datorer -> fliken erbjuder ID ->]
* **SKU-ID:** [Publiceringsportal -> virtuella datorer -> Välj ditt erbjudande -> SKU: er fliken -> Lägg till en SKU]
* **Publisher-Namespace:** [Publiceringsportal -> virtuella datorer -> genomgången fliken -> berätta för oss om ditt företag (finns Under ”steg 2 registrera ditt företag”) -> Publisher Namespace Namespace ->]

VM-erbjudanden när erbjudande/SKU visas i Azure Marketplace, kan inte du ändra de fält som anges nedan:

* **Erbjudande-ID:** [Publiceringsportal -> virtuella datorer -> Välj ditt erbjudande -> erbjuder identifierare för avbildningar av Virtuella datorer ->]
* **SKU-ID:** [Publiceringsportal -> virtuella datorer -> Välj ditt erbjudande -> SKU: er fliken -> Lägg till en SKU]
* **Publisher-Namespace:** [Publiceringsportal -> virtuella datorer -> genomgången fliken -> berätta för oss om ditt företag (hittades Under steg 2 registrera) Publisher Namespace Namespace ->]
* **Portar:** [Publiceringsportal -> virtuella datorer -> ditt erbjudande avbildningar av Virtuella datorer -> fliken -> Öppna portar]
* **Priser för ändring av listan SKU: er**
* **Fakturering Modelländringen för angivna SKU: er**
* **Borttagning av fakturering regioner för angivna SKU: er**
* **Ändra datadiskar för angivna SKU: er**

## <a name="step-2-set-your-prices"></a>Steg 2: Ange priser
### <a name="pricing-models"></a>Prismodellerna
| Prismodell | Beskrivning |
| --- | --- |
| Bas |Fast månadskostnad betald vid inköpstillfället; t.ex. $10 per månad. |
| Förbrukning (alias) användning, mätaren) |Betala per användning, som definieras av utgivaren av erbjudandet. Överförbrukning kan inte definieras per plats per användare och så vidare, eftersom det inte finns något begrepp om en bråkdel av en användare eller möjligheten att göra kostnaden. Partnern rapporterar användning per timme. Kunden betalar på på av månatliga faktureringsperiod, till skillnad från direkt like månatliga planer. |
| Kostnadsfri utvärderingsversion |Kunder kan använda utan kostnad under en begränsad tid, och betala normala avgifter för därefter. |
| Gratis nivå |Prenumerationen är alltid kostnadsfria. |
| Migrering (kallas även) konvertering eller uppgradera/nedgradera) för plan |Konceptet för en användare som flyttar från deras aktuella planen till en annan godkända abonnemang. definieras av partner. |

**Priser för modeller som är tillgängliga efter typ av erbjudande**

> [!IMPORTANT]
> Tillgängligheten för vissa prismodellerna varierar beroende på typ av erbjudande. Se tabellen nedan.
> 
> 

|  | Base endast | Endast användning | Base + förbrukning |
| --- | --- | --- | --- |
| Avbildning av virtuell dator |Nej |Ja |Nej |
| Developer-tjänsten |Ja |Ja |Ja |

### <a name="21-set-your-vm-prices"></a>2.1. Ange priset för virtuella datorer
För närvarande för virtuella datorer, har vi följande **3 typer av faktureringsmodellerna:**

* **Varje timme:** kunder debiteras på basis av per timme baserat på priserna som anges av utgivare på VM-storlekar. I följande fall **timme** modell av SKU: er, blir det totala priset summering av kostnad för programvara som debiteras utgivaren och infrastrukturkostnader debiteras av Microsoft. Den totala kostnaden kommer att visas för kunden som ett pris per timme och månatliga när de överväger köpet (se skärmbilden nedan). **Utgivare får 80% av kostnad för programvara som debiteras av dem.** Därför kan du se priser för beräkning i enlighet med detta innan inställningen för dina SKU: er.
  
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Kostnadsfri utvärderingsversion:** det här är en annan smak av modellen per timme. Kunden debiteras inte här för kostnad för programvara för de första 30 days(Free) när du har distribuerat den virtuella datorn. Efter 30days debiteras de på basis av per timme baserat på priserna som anges av utgivare i modellen per timme.
* **Bring-Your-Own-License (BYOL):** utgivarna hantera licensieringen av den programvara som körs på den virtuella datorn.

**Viktigt:** när erbjudande/SKU är listad i Azure Marketplace kan du inte ändra de fält som anges nedan.

* **Priser för ändring av listan SKU: er**
* **Fakturering Modelländringen för angivna SKU: er**
* **Borttagning av fakturering regioner för angivna SKU: er**
* **Ändra datadiskar för angivna SKU: er**
* **Erbjudande-ID:** [Publiceringsportal -> virtuella datorer -> Välj ditt erbjudande -> erbjuder identifierare för avbildningar av Virtuella datorer ->]
* **SKU-ID:** [Publiceringsportal -> virtuella datorer -> Välj ditt erbjudande -> SKU: er fliken -> Lägg till en SKU]
* **Publisher-Namespace:** [Publiceringsportal -> virtuella datorer -> genomgången fliken -> berätta för oss om ditt företag (hittades Under steg 2 registrera) Publisher Namespace Namespace ->]
* **Portar:** [Publiceringsportal -> virtuella datorer -> ditt erbjudande avbildningar av Virtuella datorer -> fliken -> Öppna portar]

### <a name="sell-to-countries-of-the-sku"></a>”Sälja till” länder av SKU
Du måste du noga överväga där du tillgängliggöra dina SKU: er. Vissa länder klassificeras som ”Microsoft uppdraget” och andra klassificeras som ”ISV uppdraget”.

* I ”Microsoft”-remitteringsländer, Microsoft samlar in skatter från kunder och betalar (inkomna ärenden) skatt till myndigheterna.
* Partner är ansvarig för att samla in skatter kunder och betala in skatt till myndigheterna i ”ISV betala in skatter” länder/regioner. Om du vill sälja i ”ISV betala in skatter” länder/regioner, måste du ha möjlighet att beräkna och betalar in i de länder/regioner som du väljer.

> [!NOTE]
> Din SKU blir inte tillgängliga i de länder/regioner, såvida inte du ange deras priser i den [Publiceringsportal](https://publish.windowsazure.com). Vägledning för att hämta ange priser för varje timme och BYOL-SKU: er anges nedan.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 hur du ställer in per timme prismodellen för en SKU
Följ anvisningarna nedan för att konfigurera varje timme prismodellen för en SKU:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Navigera till den **VIRTUELLA datorer** fliken och väljer ditt erbjudande.
3. I den vänstra menyn, klickar du på den **SKU: er** fliken.
4. Se till att SKU: N har markerats som ”fakturering Timmodell”. Om inte, klicka sedan på den **redigera** knappen för att återställa faktureringsmodellen. Ett fönster öppnas. Avmarkera kryssrutan ”support för fakturering och licensiering görs externt från Azure (även kallat Bring Your Own License)” och spara ändringarna.
5. Om du vill aktivera kostnadsfri utvärderingsversion för den första 30days av SKU-distribution, Välj alternativet ”en månad” för frågan ”är en kostnadsfri utvärderingsversion tillgänglig”? Annars väljer du alternativet ”Nej utvärdering”. Nu ska du följa anvisningarna nedan.
6. I den vänstra menyn, klickar du på den **priser** fliken.
7. Välj grundläggande region.
   
   ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Ange priser för alla kärnor. **Du måste ange pris för alla kärnor i en SKU även om inte stöds av din SKU.**
   
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Ange priser för andra regioner manuellt eller du kan använda guiden AUTOPRICE för att ange priserna för andra regioner som är baserad på den grundläggande regionen. Att använda AUTOPRICE guiden Klicka på knappen **AUTOPRICE andra marknader baserat på PRISET i USA.** **Obs:** knappens etikett kan skilja sig beroende på den region som du har valt. Eftersom vi har valt USA när du skapar det här dokumentet, så knappen har etiketten som ”automatisk pris andra marknader som baseras på priserna i USA” i skärmbilden nedan.
   
   ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Pris-guiden automatiskt öppnas. Den första sidan visar valet för grundläggande marknaden. Gör din avsnittet och gå till nästa sida genom att klicka på knappen ”->”.
    
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. Alternativet för att välja kärnor och planer visas på sidan 2. Välj önskad planer och klicka på ”->” knappen. Klicka på den **växla alla** för att välja alla den **Service planer** och **taxor** manuellt kontrollera kryssrutorna. **Du måste ange pris för alla kärnor i en SKU även om inte stöds av din SKU.** Se därför till att alla core-storlekar är markerade.
    
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. Sidan 3 visas marknader/regioner. Klicka på den **växla alla** för att välja alla regioner eller manuellt markerar du kryssrutorna för regionen. Klicka på knappen ”->” för att flytta till nästa sida. **Obs:** Microsoft skatt efterges länder är enligt ett hus som symbolen. Mer information finns i avsnittet ”sälja till” länderna i SKU: N för den här sidan.
    
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. Sida 4 visar växelkurserna. Klicka på Slutför för att slutföra stegen.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 hur du ställer in BYOL prismodellen för en SKU
Följ anvisningarna nedan för att konfigurera BYOL prismodellen för en SKU:

1. Logga in på den [Publiceringsportal](https://publish.windowsazure.com).
2. Navigera till den **VIRTUELLA datorer** fliken och väljer ditt erbjudande.
3. I den vänstra menyn, klickar du på den **SKU: er** fliken.
4. Se till att SKU: N har markerats som ”ta med din egen licens-SKU: N”. Annars kan du klicka på knappen Redigera om du vill återställa faktureringsmodellen. Ett fönster öppnas. Markera kryssrutan ”support för fakturering och licensiering görs externt från Azure (även kallat Bring Your Own License)” och spara ändringarna.
   
   ![Rita](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. I den vänstra menyn, klickar du på den **priser** fliken.
6. Välj grundläggande region och tillgängliggöra SKU i regionen genom att markera kryssrutan med SKU: N under avsnittet EXTERNALLY-LICENSED (BYOL) tillgänglighet för SKU (se skärmbilden nedan).
   
   ![Rita](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Tillgängliggöra SKU: N i andra regioner manuellt eller använda guiden AUTOPRICE för detta ändamål. Referera till de punkter #9 till #13 (som beskriver användningen av guiden AUTOPRICE) i avsnittet **”2.1.1 hur du ställer in varje timme prismodellen för en SKU”** på den här sidan.

### <a name="22-set-your-developer-service-prices"></a>2.2. Ange Developer servicepriser
Planerna kan vara valfri kombination av bas + förbrukning, där base är månadspriset och överförbrukning är priset för betalning per användning. (Se nedan för mer information.)

**Exempel:** Contoso developer tjänst-erbjudande

| Planera | Pris | Inkluderar | Migreringsvägen |
| --- | --- | --- | --- |
| Kostnadsfri |$0/ månad |Grundläggande funktioner. |Kan migrera till annan plan |
| Brons |10 USD/månad |Grundläggande funktioner och en kvot på 1 000 av X-funktionen. |Kan migrera till Brons Plus-, Silver och Gold |
| Brons Plus |Kostnadsfria utvärderingsperioden: $0 per månad + $0/meter01 |Grundläggande funktioner och en kvot på 10 000 av X-funktionen.  När du använder funktionen X kvot, kan kunden betala per användning via meter01. |Kan migrera till Silver Plus och Gold planer |
| Brons Plus |Betald tidsperiod (alias) kostnadsfri utvärderingsversion har gått ut): 10 USD per månad + $0.05/meter01 |Grundläggande funktioner och en kvot på 10 000 av X-funktionen.  När du använder funktionen X kvot, kan kunden betala per användning via meter01. |Kan migrera till Silver Plus och Gold planer |
| Silver |$0.15/meter01 |Kunden kan betala per användning via meter01 för funktionen X. |Kan migrera till Brons och Gold planer |
| Silver Plus |$20/ månad + $0.15/meter01 + $0.01/meter02 |Grundläggande funktioner och en kvot på 10 000 av X-funktionen och 100 av Y-funktionen.  När du använder funktionen X-kvot, kan kunden betala per användning via meter01.  När du använder funktionen Y-kvot, kan kunden betala per användning via meter02. |Kan migrera till Brons Plus och Gold planer |
| Guld |$ 1 000/månad |Kvot på 10 000 av funktionen X, 1 000 av funktionen Y, och funktionen Z. |Kan migrera för alla planer förutom kostnadsfritt |

## <a name="step-3-provide-support-information"></a>Steg 3: Ge support information
Kontaktuppgifter används för intern kommunikation mellan partner och Microsoft endast. Stöd för URL: en blir tillgänglig för slutkunder.

1. Gå till den **Support** rubriken på vänster sida i publiceringsportalen.
2. Ange information under **Engineering Contact**.
3. Ange information under **kundsupport**. Om du anger e-support kan ange ett påhittat telefonnummer och din angivna e-postadress som ska användas i stället.
4. Ange Webbadressen till support.

## <a name="step-4-choose-azure-marketplace-categories"></a>Steg 4: Välj Azure Marketplace-kategorier
Den **kategorier** fliken innehåller en matris med val. Ditt erbjudande kan hamna under något av dessa, och du kan välja upp till fem kategorier.

## <a name="how-your-marketing-will-appear"></a>Hur din marknadsföring visas
Nedan visas en detaljerad vy av hur erbjudandet marknadsföring information används på den [webbplatsen för Azure Marketplace](https://azure.microsoft.com/marketplace/) och i den [Azure-portalen](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Azure Marketplace-webbplats
![Rita](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![Rita](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Lista över erbjudanden på Azure Marketplace-webbplatsen*

![Rita](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Beskrivning av erbjudandeinformationen på webbplatsen för Azure Marketplace*

![Rita](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Erbjud beskrivning prisinformation på webbplatsen för Azure Marketplace*

### <a name="azure-portal"></a>Azure Portal
![Rita](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Lista över erbjudanden i Azure Portal*

![Rita](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Beskrivning av erbjudandeinformationen i Azure portal*

## <a name="next-steps"></a>Nästa steg
Nu när dina Marketplace-innehållet har lästs in, kan vi gå vidare för med testa ditt erbjudande i mellanlagringen. Du måste dock välja lämplig erbjudandetypen i listan nedan, eftersom stegen variera beroende på typ av erbjudande.

* [Testa ditt VM-erbjudande i mellanlagringen](marketplace-publishing-vm-image-test-in-staging.md)
* [Testa ditt lösning mall erbjudande i mellanlagringen](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Se också
* [Komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
