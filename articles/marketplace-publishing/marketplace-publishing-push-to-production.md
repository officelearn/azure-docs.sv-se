---
title: "Distribuera ditt erbjudande på Azure Marketplace | Microsoft Docs"
description: "Lär dig mer om och går igenom instruktionerna för att distribuera erbjudandet--avbildning av virtuell dator, developer service, datatjänst, etc.--på Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: mbaldwin
ms.openlocfilehash: 8df7b0e49e17612743b02596e99f7d1fbe8c6803
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Distribuera ditt erbjudande på Azure Marketplace
När du är nöjd med erbjudandet (det vill säga du har testat kundscenarier marknadsföring innehåll, etc.) och du är redo att starta begäran **Push till produktion** på den **publicera** fliken.  

1. Fyra steg under GENOMGÅNGEN sidan i publicering portal ska vara slutförd och grön. Se till att följande riktlinjer följs för virtuell dator erbjudanden.
   
    ![Rita][img-pubportal-walkthru-checked]
2. Välj den **publicera** fliken i listan till vänster.
   
    ![Rita][img-pubportal-menu-publish]
3. Klicka på knappen **begära tillstånd att skicka till produktion**. När begäran görs godkännande teamet körs en slutgiltig granskning och sedan erbjudandet blir tillgängligt i Azure Marketplace.
   
    ![Rita][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> Om virtuella datorer, när du klickar på knappen godkännande att skicka till produktion har, utförs följande steg bakom scenen. Du kommer att kunna visa förloppet för varje steg under fliken Publicera i publicering portal. Du måste kontrollera den här sidan med regelbundna intervall (tills status visar ”listan”) för alla felinformation som behöver korrigering från din slutpunkt.
> 
> * Först går produktions-begäran till certifieringsteam som verifiera den virtuella hårddisken. Men om du uppdaterar redan listade erbjudandet och begäran har fick endast marknadsföring ändring, hoppas certifikatutfärdare steget över.
> * Vid nästa steg kommer begäran till det innehållsvalidering team som Kontrollera marknadsföring innehållet i erbjudandet.
> * Om ovanstående steg lyckas, godkänns erbjudandet i produktion. För tillfället status blir ”visas” i publishing portal. Dock innebär inte statusen ”listan” att processen har slutförts. Följande steg måste slutföras innan erbjudandet är tillgängligt i Azure Marketplace.
> * När erbjudandet har godkänts i produktion i steget ovan, starta replikering av erbjudandet i de Azure datacenter. Normalt tar 24-48hours att slutföra replikeringen men kan ta upp till en vecka beroende på storleken på den virtuella hårddisken. Om du uppdaterar redan listade erbjudandet och får endast marknadsföring ändring sedan är replikeringen dock snabbare.
> * När replikeringen är klar, sedan blir erbjudandet tillgängligt i Azure Marketplace.
> 
> Du kan alltid ta bort erbjudandet när den är i ett **utkast** status (d.v.s. aldrig **Push till Förproduktion** eller **Push till produktion**). På den **historik** klickar du på den **Kasta utkast** längst ned på sidan för att ta bort ett utkast.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>Checklista för produktion för alla virtuella erbjudanden
* Kontrollera att du är en Microsoft Azure Certified partner
* Fliken SKU: er, ska alternativet ”dölja det här SKU från Marketplace eftersom alltid ska köpas via en lösningsmall” vara markerad som Ja endast om SKU: N är en del av en Lösningsmall. I alla andra fall, bör det här alternativet alltid markeras som Nej.
* Kom ihåg: Du bör inte ändra den SKU synlighet inställningen när SKU: N visas. Vi stöder inte den här funktionen.
* Se till att logotyperna följer Azure Marketplace-Logotypriktlinjerna nedan.
* Erbjudandet och SKU beskrivningen får inte vara samma.
* Rubrik på SKU: er och erbjuda lång sammanfattning får inte vara samma.
* SKU-rubrik och erbjuda sammanfattning får inte vara samma.
* SKU-titlar får inte vara identiska för ett erbjudande med flera SKU: er.

**Riktlinjer för Azure Marketplace-logotyp**

* Azure designen har en enkel färgpalett. Håll nere antalet primära och sekundära färgerna på din logotyp.
* Färger med Azure-portalen är vit och svart. Undvik därför att använda färgerna som bakgrundsfärgen för din logotyper. Använd en färg som gör dina logotyper framträdande i Azure-portalen. Vi rekommenderar enkla primära färger. Om du använder genomskinlig bakgrund, se till att den logo texten inte är vit eller svart.
* Använd inte en toning bakgrund på logotypen.
* Undvik att text, även ditt företag eller varumärke, på logotypen.
* Utseendet och känslan av din logotyp måste vara 'flat' och undvika toningar.
* Logotypen bör inte anpassas.

**Riktlinjer för logotypen hjälte:**

* Logotypen hjälte är valfritt. Utgivaren kan du inte överföra en hjälte logotyp. **Men en gång överförda ikonen hjälte inte kan tas bort från publicering portal. Partnern måste följa riktlinjerna för Azure Marketplace för hjälte ikoner annan erbjudandet inte kommer godkännas till produktion som helst.**
* Visa Utgivarnamn, SKU rubrik och erbjudandet lång sammanfattning visas i vit teckensnittsfärg. Därför bör du undvika att hålla alla ljusare i bakgrunden på ikonen hjälte. Svart, vit och genomskinlig bakgrund är inte tillåtet för hjälte ikoner.
* Utgivaren visar namn, SKU rubrik, erbjudandet lång sammanfattning och knappen Skapa bäddas programmässigt i logotypen hjälte när erbjudandet går listan. Därför bör du inte ange text när du utformar hjälte logotypen. Lämna tomt utrymme till höger eftersom texten (d.v.s. utgivarens namn, SKU rubrik, erbjudandet lång sammanfattning) ska tas med via programmering genom oss där. Det tomma utrymmet för texten som ska vara 415 × 100 till höger (och 370px från vänster kompenseras).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Ytterligare produktion checklista för virtuell dator är redan listade erbjuder
* Kontrollera om det finns redan ett erbjudande med samma erbjudandenamn från ditt företag. Om Ja, bör du lägga till en ny version av SKU: N i befintliga erbjudandet i stället för att skapa en ny dubbla erbjudandet.
* Datadisk bör inte ändra mellan två versioner av samma SKU: N.
* Azure Marketplace stöder inte prisnivå ändring av den angivna SKU: er som den påverkar faktureringen av befintliga kunder. Se till att du inte ändrar prissättningen av listan SKU: er i regioner där SKU: N är tillgänglig. Du kan lägga till nya SKU: er eller lägga till nya områden i en befintlig SKU.

## <a name="next-steps"></a>Nästa steg
Testa kundscenarier för att verifiera att alla kontrakt och funktioner fungerar i produktionsmiljön som testats och verifierats i mellanlagringsmiljön när erbjudandet lanseras.

## <a name="see-also"></a>Se också
* [Komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
