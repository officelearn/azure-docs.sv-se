---
title: "Förbereda och testa erbjudandet för distribution till Azure Marketplace | Microsoft Docs"
description: "Detaljerade anvisningar om att marknadsföring innehåll, konfigurera prisnivå planer och testning erbjudandet innan du distribuerar till Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Skapa erbjudande med marknadsföring innehåll
I det här steget publiceringsprocessen behöver att tillhandahålla vissa marknadsföring innehåll och information om erbjudandet och/eller SKU: er i Azure Marketplace. Exempelvis kan du ange en beskrivning av produkten, logotyp, pris planer, information om planer och annan information som behövs för att push-erbjudandet och/eller SKU till Förproduktion. Den här informationen används som marknadsföring innehåll i Azure-portalen. Du börjar den här processen i den [Publiceringsportal][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Steg 1: Ange Marketplace marknadsföring innehåll
**Engelska är standard och bara språk som stöds.** Se till att all information i fälten är på engelska. Du kan redigera all information när du vill fram till dess att du väljer att mellanlagra.

1. Gå till portalen publishing [https://publish.windowsazure.com](https://publish.windowsazure.com).
2. På den vänstra menyn klickar du på den **marknadsföring** fliken.
3. I panelen huvudsakliga klickar du på den **engelska (USA)** knappen.
   
   > [!IMPORTANT]
   > Alla fält måste ha transaktioner, inklusive avbildningar för att du ska kunna push till Förproduktion.
   > 
   > 

### <a name="details-and-plans"></a>Uppgifter och planer
1. Ange rubriken erbjudandet (högst 50 tecken), erbjuda Sammanfattning (högst 100 tecken), erbjuda lång Sammanfattning (högst 256 tecken), erbjuda beskrivning (högst 1300 tecken), logotyper under den **information** fliken
2. Ange plan rubrik (högst 50 tecken), planera Sammanfattning (högst 100 tecken), bör du planera beskrivning (högst 2000 tecken) under den **planer** fliken.
   
   > [!NOTE]
   > Du kan använda följande HTML-taggar för att formatera sammanfattningen lång sammanfattning och beskrivning av erbjudandet och planer. De tillåtna HTML-taggarna som h1, h2, h3, h4, h5, p, ol, ul, li, en [target | href], starkt, em, b, i.
   > 
   > 
3. Ange inte dubbla text under erbjudandet och planera beskrivning.
4. Ange inte dubbla texten under planens rubrik och erbjudande lång sammanfattning.
5. Ange dubbla text enligt planen avdelning inte och erbjuda sammanfattning.
6. Ange inte identiska plan titlar för ett erbjudande med flera planer.
7. Ladda upp bilder med obligatoriska specifikationer (anges i Publishing Portal) i PNG-format, ett för varje storlek.
8. Se till att logotyperna följer Azure Marketplace-Logotypriktlinjerna nedan.
   
   ![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Riktlinjer för Azure Marketplace-logotyp**

Alla logotyper som överförts i Publishing Portal bör följa den nedan riktlinjer:

* Azure designen har en enkel färgpalett. Håll nere antalet primära och sekundära färgerna på din logotyp.
* Färger med Azure-portalen är vit och svart. Undvik därför att använda färgerna som bakgrundsfärgen för din logotyper. Använd en färg som gör dina logotyper framträdande i Azure-portalen. Vi rekommenderar enkla primära färger. **Om du använder genomskinlig bakgrund, se till att logotyper/text inte är vit eller svart eller blå.**
* Använd inte en toning bakgrund på logotypen.
* Undvik att text, även ditt företag eller varumärke, på logotypen. Utseendet och känslan av din logotyp måste vara 'flat' och undvika toningar.
* Logotypen bör inte anpassas.
* Liten logotyp måste vara av storlek 40 X 40 px
* Medelhög logotypen måste vara av storlek 90 X 90 px
* Stora logotypen måste vara av storlek 115 X 115 px
* Wide logotypen måste vara av storleken 255 X 115 px
* Hjälte logotypen måste vara av storlek 815 X 290 px

> [!NOTE]
> Logotypen hjälte är valfritt. Utgivaren kan du inte överföra en hjälte logotyp. Men en gång överförda ikonen hjälte inte kan tas bort från publicering portal. Partnern måste följa riktlinjerna för Azure Marketplace för hjälte ikoner som helst.
> 
> 

  ![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Riktlinjer för hjälte logotypen ikonen (valfritt)**

* Logotypen hjälte är valfritt. Utgivaren kan du inte överföra en hjälte logotyp. **Men en gång överförda ikonen hjälte inte kan tas bort från publicering portal. Partnern måste följa riktlinjerna för Azure Marketplace för hjälte ikoner annan erbjudandet inte kommer godkännas till produktion som helst.**
* Visa Utgivarnamn, planera rubrik och erbjudandet lång sammanfattning visas i vit teckensnittsfärg. Därför bör du undvika att hålla alla ljusare i bakgrunden på ikonen hjälte. Svart, vit och genomskinlig bakgrund är inte tillåtet för hjälte ikoner.
* Visningsnamnet publisher plan rubrik, erbjudandet lång sammanfattning och knappen Skapa bäddas programmässigt i logotypen hjälte när erbjudandet går i listan. Därför bör du inte ange text när du utformar hjälte logotypen. Lämna tomt utrymme till höger eftersom texten (dvs. Visa Utgivarnamn, planera rubrik erbjudandet lång sammanfattning) ska tas med via programmering genom oss där. Det tomma utrymmet för texten som ska vara 415 × 100 till höger (och 370px från vänster kompenseras).
  
  ![Rita](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Länkar
På den **länkar** fältet till vänster och anger några länkar till information som kan hjälpa kunderna. Ange ett namn och en URL för varje länk.

![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Exempel avbildningar (valfritt)
> [!NOTE]
> Inklusive en exempelbild är ett valfritt steg.
> Även om du kan ladda upp flera exempel bilder i publicering portalen bara en bild (slumpmässigt vald av systemet) hämtar visas i Azure-portalen. Därför rekommenderar vi att ladda upp högst en exempelbild.
> 
> 

På den **exempel bilder** fliken på den vänstra menyn, ladda upp en ny avbildning genom att klicka på **ladda upp en ny avbildning**. Om du har en befintlig avbildning och du vill ersätta det klickar du på **ersätta bilden**.

![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Juridisk information
På den **juridiska** fliken, ange en länk till dina principer/villkor för användning. Skriv eller klistra in licensvillkoren i den stora **användningsvillkoren** rutan. Maximalt antal tillåtna tecken för de juridiska villkoren för användning är 1 000 000 tecken.

![Rita](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Obs:** för virtuell dator erbjudanden när ett erbjudande/SKU mellanlagras i Azure-portalen du inte ändra de fält som anges nedan:

* **Erbjuder identifierare:** [publicering av portal -> virtuella datorer -> erbjudandet VM-avbildningar -> fliken -> erbjuder identifierare]
* **SKU-ID:** [publicering av portal -> virtuella datorer -> Välj erbjudandet -> SKU: er fliken -> Lägg till en SKU]
* **Publisher Namespace:** [publicering av portal -> virtuella datorer -> genomgången fliken -> berätta för oss om ditt företag (finns Under ”steg 2 registrera företagsnamn”) -> Publisher Namespace -> Namespace]

För virtuella erbjudanden när erbjudandet/SKU visas i Azure Marketplace ändra du inte de fält som anges nedan:

* **Erbjuder identifierare:** [publicering av portal -> virtuella datorer -> Välj erbjudandet -> erbjuder identifierare för VM-avbildningar ->]
* **SKU-ID:** [publicering av portal -> virtuella datorer -> Välj erbjudandet -> SKU: er fliken -> Lägg till en SKU]
* **Publisher Namespace:** [publicering av portal -> virtuella datorer -> genomgången fliken -> berätta för oss om ditt företag (hitta Under steg 2 Register) Publisher Namespace -> Namespace]
* **Portar:** [publicering av portal -> virtuella datorer -> erbjudandet VM-avbildningar -> fliken -> Öppna portar]
* **Priser för ändring av listan SKU(s)**
* **Fakturering modellen ändring av listan SKU(s)**
* **Borttagning av fakturering områden i listan SKU(s)**
* **Ändring av listan SKU(s) datadiskar**

## <a name="step-2-set-your-prices"></a>Steg 2: Ange din priser
### <a name="pricing-models"></a>Prissättningsmodeller
| Prismodell | Beskrivning |
| --- | --- |
| Bas |Flat månatliga hastighet betald i samband med köp; t.ex. $10/ månad. |
| Förbrukning (kallas även användning, mätaren) |Betala per användning, som definieras av utgivaren av erbjudandet. Kan inte definieras överförbrukning per klient, per användare, etc., eftersom det inte finns något begrepp om en del av en användare eller möjligheten att göra proration. Användning har rapporterats av partner timme. Kunden betalar på den av månatliga faktureringscykel, till skillnad från direkt like månatliga planer. |
| Kostnadsfri utvärderingsversion |Kunden kan använda gratis under en begränsad tid och betalar normal priser därefter. |
| Gratis nivå |Planera är alltid kostnadsfri. |
| Migrering (kallas även konvertering eller uppgradering/nedgradering) av plan |Konceptet för en användare som flyttas från deras aktuella planen till en annan godkända planen. Beskrivning av partner. |

**Priser tillgängliga modeller av typen standarderbjudande**

> [!IMPORTANT]
> Tillgängligheten för vissa prisnivå modeller varierar beroende på typ av prenumerationserbjudande. Se tabellen nedan.
> 
> 

|  | Base endast | Endast förbrukning | Base + förbrukning |
| --- | --- | --- | --- |
| Avbildning av virtuell dator |Nej |Ja |Nej |
| Tjänsten för utvecklare |Ja |Ja |Ja |

### <a name="21-set-your-vm-prices"></a>2.1. Ange dina VM-priser
För närvarande för virtuella datorer har vi följande **3 typer av fakturering modeller:**

* **Varje timme:** kunder hämta debiteras på grundval av per timme baserat på de priser som anges av utgivare på storlek på Virtuella datorer. I händelse av **varje timme fakturering** modell för SKU: er, det totala priset kommer att vara summan av den programvara som utgivaren och infrastruktur materialkostnad debiteras av Microsoft. Den totala kostnaden visas till kunden som ett pris per timme och en månadsvis när de överväger köpet (se skärmbilden nedan). **Utgivare får 80% av programvara kostnaden debiteras av dem.** Du därför kontrollera beräkningen därefter innan inställningen priser för din SKU: er.
  
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Kostnadsfri utvärderingsversion:** det här är en annan version av varje timme modellen. Här få kunden inte debiteras för programvara kostnaden för den första 30 days(Free) när du har distribuerat den virtuella datorn. Efter 30days hämta de debiteras på grundval av per timme baserat på de priser som utgivare i varje timme modellen.
* **Bring-Your-äger-licens (BYOL):** av utgivare hantera licensiering av programmet som körs på den virtuella datorn.

**Viktigt:** när erbjudandet/SKU är listad i Azure Marketplace kan du inte ändra de fält som anges nedan.

* **Priser för ändring av listan SKU(s)**
* **Fakturering modellen ändring av listan SKU(s)**
* **Borttagning av fakturering områden i listan SKU(s)**
* **Ändring av listan SKU(s) datadiskar**
* **Erbjuder identifierare:** [publicering av portal -> virtuella datorer -> Välj erbjudandet -> erbjuder identifierare för VM-avbildningar ->]
* **SKU-ID:** [publicering av portal -> virtuella datorer -> Välj erbjudandet -> SKU: er fliken -> Lägg till en SKU]
* **Publisher Namespace:** [publicering av portal -> virtuella datorer -> genomgången fliken -> berätta för oss om ditt företag (hitta Under steg 2 Register) Publisher Namespace -> Namespace]
* **Portar:** [publicering av portal -> virtuella datorer -> erbjudandet VM-avbildningar -> fliken -> Öppna portar]

### <a name="sell-to-countries-of-the-sku"></a>”Säljer till” land av SKU: N
Du behöver tänka på när du gör dina SKU: er tillgängliga. Vissa länder klassificeras som ”Microsoft uppdrag” och andra klassificeras som ”ISV uppdrag”.

* Microsoft samlar in skatter från kunder i ”Microsoft uppdrag” länder och betalar moms (inkomna ärenden) till offentliga.
* Partners är ansvarig för att samla in skatter kunder och betalar skatter till staten i ”ISV betala” länder. Om du väljer att sälja i ”ISV betala” länder, har du möjlighet att beräkna och betalar i länder som du väljer.

> [!NOTE]
> Din SKU blir inte tillgängliga i länder om du inte anger deras prisnivå den [publicering portal](https://publish.windowsazure.com). Vägledning för att hämta ange priser för varje timme och BYOL SKU: er anges nedan.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 hur du ställer in varje timme priserna för en SKU
Följ anvisningarna nedan för att konfigurera varje timme Prismodell för en SKU:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Navigera till den **VIRTUELLA datorer** fliken och markera erbjudandet.
3. I den vänstra sida menyn klickar du på den **SKU: er** fliken.
4. Kontrollera att SKU: N är markerad som ”varje timme fakturering modell”. Om inte, klicka på den **redigera** för att återställa vilken faktureringsmodell som tillämpas. Ett fönster öppnas. Avmarkera kryssrutan 'debitering och licensiering görs externt från Azure (aka Bring Your Own License)' och spara ändringarna.
5. Om du vill aktivera kostnadsfri utvärderingsversion för den första 30days av SKU-distribution, Välj alternativet ”en månad” för frågan ”finns en kostnadsfri utvärderingsversion”? Annars väljer du alternativet ”Ingen utvärderingsversion”. Nu ska du följa anvisningarna nedan.
6. I den vänstra sida menyn klickar du på den **priser** fliken.
7. Välj grundläggande region.
   
   ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Ange priser för alla kärnor. **Du måste ange priset för alla kärnor i en SKU trots din SKU inte stöder.**
   
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Ange priser för andra områden manuellt eller du kan använda guiden AUTOPRICE för att ange priser för andra regioner baserat på den grundläggande regionen. Att använda AUTOPRICE guiden Klicka på knappen **AUTOPRICE andra marknader baserat på priser i UNITED STATES.** **Obs:** knappens etikett kan vara olika beroende på region som du har valt. Eftersom vi har valt USA när det här dokumentet, så knappen är märkt som ”automatisk pris andra marknader utifrån priser i USA” i skärmbilden nedan.
   
   ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Guiden automatiskt pris öppnas. Den första sidan visar valet för grundläggande marknaden. Se avsnittet och flyttar till nästa sida genom att klicka på knappen ”->”.
    
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. Alternativet för att välja kärnor och planer visas på sidan 2. Välj önskad planer och klicka på ”->” knappen. Klicka på den **växla alla** för att välja alla den **tjänsten planer** och **mätare** eller du manuellt kan kontrollera kryssrutorna. **Du måste ange priset för alla kärnor i en SKU trots din SKU inte stöder.** Se därför till att alla core-storlekar är markerade.
    
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. Sidan 3 visar marknader/regioner. Klicka på den **växla alla** för att markera alla regioner eller manuellt kryssrutorna för region. Klicka på knappen ”->” om du vill flytta till nästa sida. **Obs:** Microsoft skatt efterges länder betecknas ett hus som symbolen. Mer information finns i avsnittet ”säljer till” länder för SKU: N på den här sidan.
    
    ![Rita](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. Sidan 4 visar de exchange-satser. Klicka på Slutför för att slutföra stegen.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 så installationsprogrammet BYOL priserna för en SKU
Följ anvisningarna nedan för att konfigurera BYOL Prismodell för en SKU:

1. Logga in på den [publicering portal](https://publish.windowsazure.com).
2. Navigera till den **VIRTUELLA datorer** fliken och markera erbjudandet.
3. I den vänstra sida menyn klickar du på den **SKU: er** fliken.
4. Se till att SKU: N har markerats som ”ta med din egen licens-SKU”. Om inte, klicka på knappen Redigera om du vill återställa vilken faktureringsmodell som tillämpas. Ett fönster öppnas. Markera kryssrutan 'debitering och licensiering görs externt från Azure (aka Bring Your Own License)' och spara ändringarna.
   
   ![Rita](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. I den vänstra sida menyn klickar du på den **priser** fliken.
6. Välj grundläggande region och tillhandahålla SKU: N i region genom att markera kryssrutan mot SKU under avsnittet EXTERNALLY-LICENSED (BYOL) SKU tillgänglighet (se skärmbilden nedan).
   
   ![Rita](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Tillgängliggöra SKU: N i andra regioner manuellt eller kan du använda guiden AUTOPRICE för detta ändamål. Referera till punkterna #9-#13 (som beskriver användningen av guiden AUTOPRICE) i avsnittet **”2.1.1 hur du ställer in varje timme Prismodell för en SKU”** på den här sidan.

### <a name="22-set-your-developer-service-prices"></a>2.2. Ange din tjänst priser för utvecklare
Planer kan vara valfri kombination av bas + förbrukning, där base är månadspriset och överförbrukning per tillfälle priset. (Se nedan för mer information.)

**Exempel:** Contoso developer tjänsterbjudande

| Planera | Pris | Inkluderar | Sökväg för migrering |
| --- | --- | --- | --- |
| Kostnadsfri |$0/ månad |Grundläggande funktioner. |Kan migrera till annan plan |
| Brons |$10/ månad |Grundläggande funktioner och en kvot på 1 000 av X-funktionen. |Kan migrera till Brons Plus, Silver och guld planer |
| Brons Plus |Utvärderingsperioden: 0/ månad + $0/meter01 |Grundläggande funktioner och en kvot på 10 000 av X-funktionen.  När funktionen X kvoten används, kan kunden betala per användning via meter01. |Kan migrera till Silver Plus och guld planer |
| Brons Plus |Betald tidsperiod (kallas även kostnadsfri utvärderingsversion har upphört att gälla): $10/månad + $ 0,05/meter01 |Grundläggande funktioner och en kvot på 10 000 av X-funktionen.  När funktionen X kvoten används, kan kunden betala per användning via meter01. |Kan migrera till Silver Plus och guld planer |
| Silver |$ 0,15/meter01 |Kunden kan betala per användning via meter01, vilket är för X-funktionen. |Kan migrera till Brons och guld planer |
| Silver Plus |$20/ månad + $ 0,15/meter01 + $ 0,01/meter02 |Grundläggande funktioner och en kvot på 10 000 av funktionen X och 100 av Y-funktionen.  När funktionen X kvoten används, kan kunden betala per användning via meter01.  När funktionen Y kvoten används, kan kunden betala per användning via meter02. |Kan migrera till Brons Plus och guld planer |
| Guld |$ 1 000/månad |Kvoten till 10 000 av funktionen X, 1 000 av funktionen Y, och obegränsad av Z-funktionen. |Kan migrera till alla planer förutom ledigt |

## <a name="step-3-provide-support-information"></a>Steg 3: Ange support information
Kontaktuppgifterna används för intern kommunikation mellan partner och Microsoft endast. Support-URL ska vara tillgängliga för end-kunder.

1. Gå till den **stöd** rubriken på vänster sida av publishing portal.
2. Ange information under **Engineering Kontakta**.
3. Ange information under **kundsupport**. Om du ger endast poststöd för e-, ange ett dummy telefonnummer och din angivna e-post används i stället.
4. Ange URL till.

## <a name="step-4-choose-azure-marketplace-categories"></a>Steg 4: Välj kategorier som Azure Marketplace
Den **kategorier** fliken finns en matris med val. Erbjudandet kan omfattas av dessa och du kan välja upp till fem kategorier.

## <a name="how-your-marketing-will-appear"></a>Hur din marknadsföring visas
Nedan visas en detaljerad vy av hur erbjudandet marknadsföring information används på den [Azure Marketplace webbplats](https://azure.microsoft.com/marketplace/) och i den [Azure-portalen](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Azure Marketplace-webbplatsen
![Rita](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![Rita](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Lista över erbjudanden på webbplatsen för Azure Marketplace*

![Rita](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Erbjudandeinformationen beskrivning på Azure Marketplace-webbplatsen*

![Rita](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Erbjuder beskrivning prisinformationen på Azure Marketplace-webbplatsen*

### <a name="azure-portal"></a>Azure Portal
![Rita](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Lista över erbjudanden i Azure Portal*

![Rita](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Erbjudandeinformationen beskrivning i Azure-portalen*

## <a name="next-steps"></a>Nästa steg
Nu när din Marketplace-innehållet har lästs in, ska vi gå med testning erbjudandet i Förproduktion. Dock måste du välja lämpliga erbjudande typ från listan nedan, eftersom stegen varierar beroende på typ av prenumerationserbjudande.

* [Testa VM erbjudandet i Förproduktion](marketplace-publishing-vm-image-test-in-staging.md)
* [Testa lösningen mallen erbjudandet i Förproduktion](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Se även
* [Komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
