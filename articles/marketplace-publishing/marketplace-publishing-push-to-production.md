---
title: Distribuera ditt erbjudande på Azure Marketplace | Microsoft Docs
description: Lär dig mer om och gå igenom anvisningarna för att distribuera ditt erbjudande--avbildning av virtuell dator, developer-tjänst, datatjänst, etc.--på Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: 058f50853795453617593a6a07e2951f15f28174
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076119"
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Distribuera ditt erbjudande på Azure Marketplace
När du är nöjd med ditt erbjudande (det vill säga du har testat kundscenarier, marknadsföring innehåll och så vidare) och du är redo att starta, begära **Push till produktion** på den **publicera** fliken.  

1. De fyra stegen under den här GENOMGÅNGEN sidan i avsnittet Publicera portal ska vara slutförd och grön. Se till att följande riktlinjer följs för virtuell dator-erbjudanden.
   
    ![Rita][img-pubportal-walkthru-checked]
2. Välj den **publicera** fliken i listan till vänster.
   
    ![Rita][img-pubportal-menu-publish]
3. Klicka på knappen **begära tillstånd att skicka till produktion**. När begäran görs godkännande-teamet körs en slutgiltig granskning och sedan ditt erbjudande blir tillgängliga i Azure Marketplace.
   
    ![Rita][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> När det gäller virtuella datorer, när du klickar på knappen godkännande av begäran till produktion, utförs följande steg bakom scenen. Du kommer att kunna visa förloppet för varje steg under fliken Publicera i publiceringen portal. Du måste markera den här sidan med regelbundna intervall (tills statusen ”listan”) för fel som behöver korrigering från din sida.
> 
> * Först går din produktion-begäran till certifieringsteam som verifiera den virtuella hårddisken. Men om du uppdaterar erbjudandet redan listade och begäran har endast marknadsföring ändringen, hoppas certifieringssteget över.
> * I nästa steg kommer begäran till innehållsvalidering-teamet som verifierar marknadsföringsinnehåll i erbjudandet.
> * Om stegen ovan lyckas, godkänns erbjudandet i produktion. För närvarande status bli ”visas” i publiceringsportalen. Dock innebär inte den här statusen ”listan” att processen har slutförts. Följande steg måste slutföras innan erbjudandet är tillgängligt på Azure Marketplace.
> * När erbjudandet har godkänts i produktion i ovanstående steg, starta replikeringen av erbjudandet i alla Azure datacenter. Vanligtvis tar 24-48hours att slutföra replikeringen men kan ta upp till en vecka beroende på storleken på den virtuella hårddisken. Om du uppdaterar erbjudandet redan listade och den har endast marknadsföring ändringen, sedan är replikeringen dock snabbare.
> * När replikeringen är klar, så erbjudandet kommer att vara tillgängliga på Azure Marketplace.
> 
> Du kan alltid ta bort erbjudandet i en **Draft** status (d.v.s. aldrig **väljer att mellanlagra** eller **Push till produktion**). På den **historik** klickar du på den **Kasta utkast** längst ned på sidan för att ta bort ett utkast.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Checklista för produktion för alla VM-erbjudanden
* Se till att du är en Microsoft Azure Certified-partner
* Under fliken SKU: er bör alternativet ”dölja den här SKU: N från Marketplace eftersom det alltid ska köpas via en lösningsmall” markeras som Ja endast om SKU: N är en del av en Lösningsmall. I alla andra fall, bör det här alternativet alltid markeras som Nej.
* Kom ihåg: Du bör inte ändra den SKU synlighet inställningen när SKU: N visas. Vi stöder inte den här funktionen.
* Se till att logotyperna följer Azure Marketplace-Logotypriktlinjerna nedan.
* Beskrivning av erbjudande och en SKU bör inte vara samma.
* Rubrik på SKU: er och erbjuda lång sammanfattning får inte vara samma.
* SKU-rubrik och erbjuder sammanfattning får inte vara samma.
* SKU-rubriker får inte vara identiska för ett erbjudande med flera SKU: er.

**Riktlinjer för Azure Marketplace-logotyp**

* Azure-designen har en enkel färgpalett. Håll nere antalet primära och sekundära färger på din logotyp.
* Temafärger i Azure Portal är vit och svart. Undvik därför att använda dessa färger som bakgrundsfärgen för din logotyper. Använd en färg som gör din logotyper framstående i Azure-portalen. Vi rekommenderar enkla primärfärger. Om du använder genomskinlig bakgrund, se till att logotyp/text inte är vit eller svart.
* Använd inte en toning bakgrund på logotypen.
* Undvika att placera text, även företaget eller varumärken namn på logotypen.
* Utseendet och känslan av din logotyp måste vara ”fast” och Undvik toningar.
* Logotypen bör inte vara har sträckts ut.

**Riktlinjer för Hero-logotypen:**

* Hero-logotypen är valfritt. Utgivaren kan du inte ladda upp en Hero-logotyp. **Men en gång överförda ikonen hero-komponenten inte kan tas bort från publiceringen portal. Då måste partnern följer du riktlinjerna för Azure Marketplace för Hero ikoner annan erbjudandet inte kommer godkännas till produktion.**
* Visningsnamn för utgivaren, SKU rubrik och erbjudandet lång sammanfattning visas i vit teckenfärg. Därför bör du undvika att hålla alla ljusare i bakgrunden på Hero-ikonen. Svart, vit och transparent bakgrund är inte tillåtet för Hero-ikoner.
* Utgivaren visningsnamn, SKU rubrik, lång sammanfattning av erbjudandet och knappen Skapa är inbäddade programmässigt inuti Hero-logotypen när erbjudandet går listan. Så bör du inte ange valfri text när du utformar Hero-logotypen. Lämna tomt utrymme till höger eftersom text (det vill säga visningsnamn för utgivaren, SKU rubrik, lång sammanfattning av erbjudandet) kommer att inkluderas programmässigt av oss där. Det tomma utrymmet för texten bör vara 415 × 100 till höger (och 370 kompenseras px från vänster).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Erbjuder ytterligare produktion checklista för redan listade virtuell dator
* Kontrollera om det finns redan ett erbjudande med samma erbjudandenamn från ditt företag. Om Ja, bör du lägga till en ny version av SKU: N i befintliga erbjudandet istället för att skapa ett nytt duplicerade erbjudande.
* Datadisk bör inte ändra mellan två versioner av samma SKU.
* Azure Marketplace stöder inte prissättningsändringen listade SKU: er eftersom det påverkar att faktureringen för befintliga kunder. Se till att du inte ändrar prissättningen av de listade SKU: erna i regioner där SKU: N är tillgänglig. Du kan dock lägga till nya SKU: er eller lägga till nya regioner i en befintlig SKU.

## <a name="next-steps"></a>Nästa steg
När erbjudandet går live, testa kundscenarier för att verifiera att alla kontrakt och funktioner fungerar korrekt i produktionsmiljön som testats och godkänts i mellanlagringsmiljön.

## <a name="see-also"></a>Se också
* [Komma igång: Hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
