---
title: Uppdatera ett befintligt kommersiella Marketplace-erbjudande
description: Hur du gör uppdateringar i ett befintligt kommersiella Marketplace-erbjudande, inklusive redigera, ta bort ett utkast, avbryter en publiceringsbegäran stoppa sälja ett erbjudande eller en plan och synkroniserar privata målgrupper.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 22490599b6f7a8d87c546cd84ca2fc6fbb2bfa3c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806168"
---
# <a name="update-an-existing-offer-in-the-commercial-marketplace"></a>Uppdatera ett befintligt erbjudande i Marketplace för kommersiella

Du kan visa dina befintliga erbjudanden på den **erbjuder** fliken den [kommersiella Marketplace portal](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) Partner Center.

Om du vill uppdatera ett befintligt erbjudande som används för närvarande Direktmigrering i marketplace: 

1. Välj namnet på det erbjudande som du vill uppdatera. Status för erbjudandet kan anges som **Live**, **pågående publicering**, **Draft**, **uppmärksamhet behövs**, eller **inte säljs i marketplace** (om du tidigare har valt att stoppa sälja erbjudandet). När den **erbjuder översikt** sidan för erbjudandet öppnas.
2. Välj **uppdatera** från kortet översikt eller menyalternativ i det vänstra navigeringsfönstret för det område som du vill uppdatera. Du kanske vill uppdatera den **erbjuder installationsprogrammet**, **egenskaper**, **erbjuder lista**, **förhandsversion**, **teknisk konfiguration** , **Planera översikt**, eller **Testkör**. 
3. Gör dina ändringar och välj sedan **spara**. Upprepa processen tills alla ändringar har slutförts.

## <a name="review-and-publish-an-updated-offer"></a>Granska och publicera ett erbjudande som är uppdaterade

När du är redo att publicera erbjudandet uppdaterade väljer **publicera** från valfri sida. Den **granskning och publicera** öppnas. På den här sidan kan du:


- Se Slutförandestatus för delar av erbjudandet som du uppdaterade: 
    - **Opublicerade ändringar**: Avsnittet har uppdaterats och är klar. Alla nödvändiga data har angetts och det fanns inga fel som introducerades i uppdateringarna.
    - **Ofullständig**: Uppdateringar som görs till avsnittet introduceras fel som behöver åtgärdas eller kräver mer information anges.
- Ange ytterligare information till testgrupp certifiering för att se till att testa går smidigt.
- Skicka uppdaterade erbjudandet för publicering genom att välja **skicka**.  Vi mejlar dig när en förhandsversion av det uppdaterade erbjudandet är tillgängligt för dig att granska och signering.

**VIKTIGT!** Du måste granska förhandsversionen av erbjudandet, när den är tillgänglig och välj **Go live** att publicera erbjudandet uppdaterade till målgruppen (offentliga eller privata).

## <a name="update-a-plan-within-an-existing-offer"></a>Uppdatera en plan inom ett befintligt erbjudande

Att göra ändringar i en plan inom ett befintligt erbjudande som du redan har publicerat:

- Med den **erbjuder översikt** sidan öppen för din befintliga erbjudandet, Välj det avtal som du vill ändra. Om planen inte kan nås från den **– översikt** väljer **finns i alla prenumerationer**.
- Du kan välja att välja planen **namn**, **prismodellen**, eller **tillgänglighet**. *Planer är för närvarande bara tillgänglig på engelska (USA)*.
- Välj **spara** när du har gjort nödvändiga ändringar till planens namn, beskrivning, prissättning, eller målgrupp tillgänglighet. 
- Välj **publicera** när du är redo att publicera dina uppdateringar. Den **[granskning och publicera](#review-and-publish-an-updated-offer)** sidan öppnas och ger en Slutförandestatus för dina uppdateringar. 

## <a name="delete-a-draft-offer"></a>Ta bort ett erbjudande med draft

Du kan ta bort ett utkast-erbjudande (ett som inte har publicerats) genom att välja **ta bort draft** från den **erbjuder översikt** sidan. Det här alternativet är inte tillgängliga för dig om du tidigare har publicerat erbjudandet.

När du har bekräftat att du vill ta bort draft-erbjudandet erbjudandet kommer inte längre att visas eller komma åt i Partnercenter och **alla erbjuder** öppnas.

## <a name="delete-a-draft-plan"></a>Ta bort en plan för utkast

Om du vill ta bort en plan som inte har publicerats, Välj **ta bort draft** från den **– översikt** sidan. Det här alternativet kan inte tillgängliga för dig om du tidigare har publicerat erbjudandet.

När du har bekräftat att du vill ta bort planen draft kommer planen inte längre att visas eller tillgängliga Partner Center.

## <a name="cancel-publishing"></a>Avbryt publicering

Annullera ett erbjudande med den **pågående publicering** status:

- Välj erbjudandenamn för att öppna den **erbjuder översikt** sidan. 
- Välj **Avbryt publicera** från det övre högra hörnet på sidan.
- Bekräfta att du vill stoppa erbjudande publiceras. 

Om du vill publicera erbjudande vid ett senare tillfälle, behöver du publiceringsprocessen börja om från början.

> [!NOTE]
> Du kan stoppa ett erbjudande publiceras bara om erbjudandet ännu inte har gått framåt till utgivaren signering steg. När du har valt **Driftsätt** du inte alternativet att avbryta publicera längre.

## <a name="stop-selling-an-offer-or-plan"></a>Stoppa sälja ett erbjudande eller en plan

Om du vill stoppa sälja ett erbjudande när du redan har publicerat den, Välj **stoppa sälja** från den **erbjuder översikt** sidan. Om du vill stoppa sälja en plan, Välj **stoppa sälja** från den **– översikt** sidan. (Välja att sluta sälja en plan är endast tillgängligt om du har flera planer i erbjudandet. Du kan välja att stoppa sälja en plan utan att påverka andra planer i erbjudandet.)

När du har kontrollerat att du vill stoppa sälja erbjudandet eller planerar inom några timmar den längre inte visas i marketplace och inga nya kunder kan ladda ned den. 

Alla kunder som tidigare har köpt erbjudandet eller planerar kan fortfarande använda den. De kan ladda ned det igen, men kommer inte att hämta uppdateringar om du uppdaterar och publicera erbjudandet eller planerar vid ett senare tillfälle. 

När din begäran om att stoppa sälja erbjudandeplanen/har slutförts kan visas du fortfarande den i kommersiella Marketplace portal på Partner Center. Om du vill ta bort utkastet, Välj **ta bort draft** från antingen den **erbjuder översikt** eller **planera översikt** sidan. 

Om du vill lista eller sälja det här erbjudandet eller planerar igen, följ instruktionerna för att [uppdatera ett befintligt erbjudande](#update-an-existing-offer-in-the-commercial-marketplace). Glöm inte att du behöver **publicera** erbjudandet eller planerar igen när du gör några ändringar.

## <a name="remove-offers-from-existing-customers"></a>Ta bort erbjudanden från befintliga kunder

Ta bort erbjudanden från befintliga kunder [loggar en supportbegäran om](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Välj specifika erbjudandetypen från den **problemtyp** listan och välj sedan **ta bort ett erbjudande för publicerade** i den **kategori** lista. Skicka din begäran. Supportteamet vägleder dig genom borttagningen erbjudandet.

## <a name="sync-private-plan-audiences"></a>Synkronisera privata plan målgrupper

Om erbjudandet omfattar en eller flera planer som är konfigurerade att endast är tillgänglig för en privat begränsad publik, är det möjligt att uppdatera målgruppen som har åtkomst till den privata planen utan att publicera andra ändringar till erbjudandet. 

Uppdatera och synkronisera den privata målgruppen för din plan eller de planer:

- Ändra målgruppen i en eller flera privata planer med hjälp av den + **lägga till ID** eller **importera kunder (csv)** knappen och sedan spara ändringarna.
- Välj **Synkronisera privata målgrupp** från den **planera översikt** sidan.

**Synkronisera privata målgrupp** publiceras bara ändringarna för ditt privata målgrupper, utan att publicera andra uppdateringar som du har gjort till draft-erbjudande.

## <a name="next-steps"></a>Nästa steg

- [Kontrollera publiceringsstatus för ditt kommersiella Marketplace-erbjudande](./publishing-status.md)
