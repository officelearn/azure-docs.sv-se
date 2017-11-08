---
title: "Hantera kostnader med hjälp av Azure kostnaden Management | Microsoft Docs"
description: "Hantera kostnader med hjälp av kostnaden tilldelning och showback och chargeback-rapporter."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/06/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 83ddc0cb4227235069b0027a24a52f4d8e818126
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="manage-costs-by-using-azure-cost-management"></a>Hantera kostnader med hjälp av Azure kostnaden Management

Du hanterar kostnader och skapa showback-rapporter i Azure kostnaden Management med Cloudyn genom att allokera kostnaderna baserat på taggar. Processen för att fördela tilldelar förbrukade molnresurser kostnader. Kostnaderna fördelas fullständigt när alla resurser som är kategoriserade med taggar. När kostnader fördelas kan du tillhandahålla showback eller återbetalning till användarna med instrumentpaneler och rapporter. Många resurser kan dock ej taggade eller untaggable när du börjar använda hantering av kostnaden.

Du kanske vill få ersättning för engineering kostnader. Du behöver för att kunna visa din teknikteamet som du behöver ett specifikt belopp baserat på resurskostnader. Visa en rapport för alla förbrukade resurser som är märkta *engineering*.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Använd anpassade taggar när du vill fördela kostnader.
> * Skapa showback och återbetalningsrapporter.

## <a name="use-custom-tags-to-allocate-costs"></a>Använd anpassade taggar när du vill fördela kostnader

När du börjar kostnadsallokering, är det första du gör definiera omfattningen med hjälp av en modell för kostnad. Metoden ändras inte kostnader, distribuerar dem. När du skapar en kostnad modell segmentera dina data genom kostnaden entiteten, konto eller prenumeration och flera taggar. Vanliga exempel taggar kan innehålla ett fakturering koden, kostnadsställe eller gruppnamn. Taggar hjälpa dig att utföra showback eller återbetalning till andra delar av organisationen.

Om du vill skapa en anpassad kostnad allokering modell **kostnaden** &gt; **kostnaden Management** &gt; **kostnadsallokering 360 graders** på rapportens-menyn.

![Kostnad allokering 360 markering](./media/tutorial-manage-costs/cost-allocation-360.png)

På den **kostnaden allokering 360** väljer **Lägg till** och ange sedan ett namn och beskrivning för kostnad-modellen. Markera alla konton eller enskilda konton. Om du vill använda enskilda konton kan du välja flera konton från flera leverantörer av molntjänster. Klicka sedan på **kategorisering** att välja vilka identifierade taggar som kategorisera informationen. Välj taggar (kategori) som du vill inkludera i din modell. I följande exempel visas den **enhet** taggen är markerad.

![Exempel kostnaden modellen kategorisering](./media/tutorial-manage-costs/cost-model01.png)



Exemplet visar att $14,444 är ej kategoriserade (utan taggar).

Välj därefter **okategoriserade resurser** och välj tjänster som har tilldelats kostnader. Definiera regler för att allokera kostnader.

Du kanske vill vidta Azure storage-kostnaderna och fördela kostnaderna lika till virtuella Azure-datorer (VM). Om du vill göra det, Välj den **Azure-lagring** tjänsten, Välj **proportionell mot kategoriserat**, och välj sedan **Virtuella och Azure**. Markera **skapa**.

![Exempel kostnaden modellen allokeringsregel för jämn fördelning](./media/tutorial-manage-costs/cost-model02.png)



I ett annat exempel kanske du vill allokera alla Azure-nätverkskostnader för en specifik enhet i din organisation. Om du vill göra det, Välj den **Azure/Network** tjänst och välj sedan **Explicit Distribution**. Sedan värdet 100 procent för distribution och väljer du enheten –**G&amp;A** i följande bild:

![Exempel kostnaden modellen allokeringsregel för en viss affärsenhet](./media/tutorial-manage-costs/cost-model03.png)



Skapa ytterligare allokeringsregler för alla återstående kategoriserad resurser.

Om du har några lediga Amazon Web Services (AWS) reserverade instanser kan du tilldela dem taggade kategorier med **reserverade instanser**.

Om du vill visa information om de val du gjorde att allokera kostnader, Välj **sammanfattning**. Spara din information och fortsätta arbeta med ytterligare regler senare väljer **Spara som utkast**. Om du vill spara din information och har Cloudyn börja bearbeta kostnaden allokering modellen, väljer du **spara och aktivera**.

I listan över modeller kostnaden visar dina nya kostnaden modellen med **Bearbetningsstatus**. Det kan ta lite tid innan Cloudyn-databasen uppdateras med din modell kostnaden. När bearbetningen är klar uppdateras status till **slutförd**. Du kan sedan visa data från kostnaden modellen i analysrapporten kostnad under **utökad filter** &gt; **kostnaden modellen**.

### <a name="category-manager"></a>Kategori Manager

Kategorin är en Datarensning verktyg som hjälper dig att koppla värdena i flera kategorier (taggar) för att skapa nya. Det är ett enkelt regelbaserad verktyg där du väljer en kategori och skapa regler för att koppla befintliga värden. Du kan till exempel ha befintliga kategorierna för **R&amp;D** och **dev** där både motsvarar gruppen utveckling.

Klicka på symbolen växel i övre högra och välj i portalen Cloudyn **kategori Manager**. Välj om du vill skapa en ny kategori på plustecknet (**+**). Ange ett namn för kategori och sedan under **nycklar**, ange kategori-nycklar som du vill inkludera i den nya kategorin.

När du definierar en regel kan du lägga till flera värden med ett eller-villkor. Du kan också göra vissa grundläggande sträng-åtgärder. Klicka på symbolen tre punkter för båda fallen (**...** ) till höger om **regel**.

Definiera en ny regel i den **regler** område, skapa en ny regel. Ange till exempel **dev** under **regler** och ange sedan **R&amp;D** under **åtgärder**. När du är klar sparar du en ny kategori.

Följande bild visar ett exempel på regler som har skapats för en ny kategori som heter **arbetsbelastning**:

![Exempel kategori](./media/tutorial-manage-costs/category01.png)



## <a name="create-showback-and-chargeback-reports"></a>Skapa showback och återbetalningsrapporter

Metoden organisationer använder för att utföra showback och återbetalning varierar avsevärt. Du kan dock använda alla instrumentpaneler och rapporter i Cloudyn portal som bas för något ändamål. Du kan ge användaråtkomst till alla i din organisation så att de kan visa instrumentpaneler och rapporter på begäran. Alla kostnader analys rapporterna stöder showback eftersom de visar användare resurser som de förbrukas. Och de tillåta användare att detaljerat kostnad eller användning av data som är specifik för deras grupp i din organisation.

Om du vill visa resultat för att fördela öppna rapporten kostnad och välj modellen kostnad som du skapade. Sedan kan du lägga till en gruppering av en eller flera taggar om du valde i modellen kostnaden.

![Kostnad analysrapporten](./media/tutorial-manage-costs/cost-analysis.png)

Du kan enkelt skapa och spara rapporter som fokuserar på specifika tjänster som används av specifika grupper. Du kan till exempel ha en avdelning som använder virtuella Azure-datorer i stor utsträckning. Du kan skapa en rapport som har filtrerats på Azure Virtual Machines att visa användnings- och kostnader.

Om du behöver ange ögonblicksbilddata till andra grupper kan du exportera en rapport i PDF- eller CSV-format.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd anpassade taggar när du vill fördela kostnader.
> * Skapa showback och återbetalningsrapporter.



Mer information om att komma igång med Cloudyn och dess funktioner i förväg Cloudyn-dokumentationen.

> [!div class="nextstepaction"]
> [Cloudyn dokumentation](https://support.cloudyn.com/hc/)
