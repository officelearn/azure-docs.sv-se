---
title: Uppdatera ett befintligt erbjudande för handels Marketplace
description: Hur du gör uppdateringar av ett befintligt erbjudande för handels Marketplace, inklusive redigering, borttagning av ett utkast, avbryter en publicerings förfrågan, slutar att sälja ett erbjudande eller en plan och synkroniserar privata mål grupper.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 01/16/2020
ms.openlocfilehash: 810c38397981a100c2d5a68492f8a15b75cd79bd
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276048"
---
# <a name="update-an-existing-offer-in-the-commercial-marketplace"></a>Uppdatera ett befintligt erbjudande i den kommersiella Marketplace

Du kan visa dina befintliga erbjudanden på fliken **Översikt** i den [kommersiella Marketplace-portalen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) i Partner Center.

Så här uppdaterar du ett befintligt erbjudande som för närvarande är aktivt på den kommersiella Marketplace:

1. Välj namnet på erbjudandet som du vill uppdatera. Status för erbjudandet kan visas som för **hands version**, **Live**, **publicering**pågår, **utkast**, **åtgärd krävs**eller **inte tillgängligt** (om du tidigare har valt att sluta sälja erbjudandet). När du har valt så öppnas sidan **erbjudande översikt** för det erbjudandet.
2. Välj **Uppdatera** från kortet på sidan erbjudande översikt eller meny alternativet i det vänstra navigerings fältet för det fält som du vill uppdatera. Du kanske vill uppdatera **erbjudande konfiguration**, **Egenskaper**, **erbjudande lista**, för **hands version**, **teknisk konfiguration**, **planerings översikt**eller **test enhet**.
3. Gör dina ändringar och välj **Spara utkast**. Upprepa processen tills alla ändringar har slutförts.

## <a name="review-and-publish-an-updated-offer"></a>Granska och publicera ett uppdaterat erbjudande

När du är redo att publicera ditt uppdaterade erbjudande väljer du **Granska och publicera** från vilken sida som helst. Sidan **Granska och publicera** öppnas. På den här sidan kan du:

- Se slut för ande status för avsnitten i erbjudandet som du uppdaterade: 
    - **Opublicerade ändringar**: avsnittet har uppdaterats och är klart. Alla begärda data har angetts och inga fel infördes i uppdateringarna.
    - **Ofullständig**: uppdateringar som görs i avsnittet introducerade fel som måste åtgärdas eller som kräver mer information.
- Ge ytterligare information till certifierings test teamet för att säkerställa att testningen går smidigt.
- Skicka det uppdaterade erbjudandet om publicering genom att välja **publicera**.  Vi skickar e-post till dig när en för hands version av det uppdaterade erbjudandet kan granskas och signeras.

> [!IMPORTANT]
> Du måste granska din för hands version av erbjudandet när den är tillgänglig och välja **Go-Live** för att publicera ditt uppdaterade erbjudande till din avsedda mål grupp (offentlig eller privat).

## <a name="add-a-plan-to-an-existing-offer"></a>Lägg till en plan i ett befintligt erbjudande

Så här lägger du till en ny plan i ett befintligt erbjudande som du redan har publicerat:

1. På sidan **erbjudande översikt** för det befintliga erbjudandet öppnar du sidan **plan översikt** och väljer sedan **Skapa ny plan**.
1. Skapa en ny plan enligt [rikt linjerna](./create-new-saas-offer.md#plan-overview) genom att använda **pris modellen befintliga planer**.
1. Välj **Spara utkast** när du har ändrat namnet på planen.
1. Välj **publicera** när du är redo att publicera dina uppdateringar. Sidan **[Granska och publicera](#review-and-publish-an-updated-offer)** öppnas och ger en slut för ande status för dina uppdateringar.

## <a name="update-a-plan-within-an-existing-offer"></a>Uppdatera en plan i ett befintligt erbjudande

För att göra ändringar i en plan i ett befintligt erbjudande som du redan har publicerat:

1. På sidan **erbjudande översikt** för det befintliga erbjudandet väljer du den plan som du vill ändra. Om planen inte är tillgänglig från listan **plan översikt** väljer du **Se alla planer**.
1. Välj plan **namn**, **pris modell**eller **tillgänglighet**. *För närvarande är planer bara tillgängliga på engelska (USA)*.
1. Välj **Spara utkast** när du har ändrat plan namn, beskrivning eller tillgänglighet för mål gruppen.
1. Välj **Granska och publicera** när du är redo att publicera dina uppdateringar. Sidan **[Granska och publicera](#review-and-publish-an-updated-offer)** öppnas och ger en slut för ande status för dina uppdateringar.
1. Skicka in den uppdaterade planen för publicering genom att välja **publicera**. Vi skickar e-post till dig när en för hands version av det uppdaterade erbjudandet kan granskas och signeras.

## <a name="offer-a-virtual-machine-plan-at-a-new-price"></a>Erbjud en virtuell dator plan till ett nytt pris

När en virtuell dator plan har publicerats går det inte att ändra dess pris. Om du vill erbjuda samma plan till ett annat pris måste du dölja planen och skapa en ny med det uppdaterade priset. Börja med att dölja planen med det pris som du vill ändra:

1. På sidan **erbjudande översikt** för det befintliga erbjudandet väljer du den plan som du vill ändra. Om planen inte är tillgänglig från listan **plan översikt** väljer du **Se alla planer**.
1. Markera kryss rutan **Dölj plan** . Spara utkastet innan du fortsätter.

Nu när du har dolt planen med det gamla priset skapar du en kopia av planen med det uppdaterade priset:

1. Gå tillbaka till **plan översikt**i Partner Center.
2. Välj **Skapa ny plan**. Ange ett **plan-ID** och ett **plan namn**och välj sedan **skapa**.
1. Om du vill återanvända den tekniska konfigurationen från den plan som du har dolt, markerar du kryss rutan **Återanvänd teknisk konfiguration** . Läs [skapa planer för ett virtuellt dator erbjudande](../azure-vm-create-plans.md) om du vill veta mer.
    > [!IMPORTANT]
    > Om du väljer **den här planen återanvänder teknisk konfiguration från en annan plan**kan du inte sluta sälja den överordnade planen senare. Använd inte det här alternativet om du inte vill sluta att sälja den överordnade planen.
3. Slutför alla nödvändiga avsnitt för den nya planen, inklusive det nya priset.
1. Välj **Spara utkast**.
1. När du har slutfört alla nödvändiga avsnitt för den nya planen väljer du **Granska och publicera**. Detta skickar ditt erbjudande om granskning och publicering. Läs [Granska och publicera ett erbjudande på den kommersiella Marketplace](../review-publish-offer.md) för mer information.

## <a name="compare-changes-to-commercial-marketplace-offers"></a>Jämför ändringar på kommersiella Marketplace-erbjudanden

Du kan granska de ändringar du gör i ett [publicerat](#compare-changes-to-published-offer) eller [förhands gransknings](#compare-changes-to-a-preview-offer) erbjudande innan du gör dem live genom att använda **Jämför**.

> [!NOTE]
> Ett publicerat erbjudande är ett erbjudande som har publicerats för förhands granskning eller Live-tillstånd.

Se nedan för allmän gransknings information:

- Du kan använda **Jämför** när som helst under redigerings processen.
- Välj ett fält på sidan **Jämför** för att navigera till det värde som du vill ändra.
- Om du är redo att publicera dina uppdateringar väljer du **Granska och publicera**.
- Om du vill se värdena för alla fält, till och med fält som inte har uppdaterats, väljer du filtret **alla fält** . Du kan ändra filtren i dessa fält genom att välja **ändrade fält**och sedan välja ett av filtren nedan:
    - Filter för **borttagna värden** visar fält som du har publicerat och som du nu tar bort helt.
    - Filtret för **tillagda värden** visar fält som du inte ursprungligen publicerade och som nu läggs till.
    - Filtret för **redigerade värden** visar fält som har publicerats men nu har uppdaterat innehållet.

      >[!NOTE]
      > Om något av dessa filter inte är tillgängligt indikerar det att du inte har gjort en uppdatering av den typen.

- Om du bara vill se värden som inte har uppdaterats väljer du filtret **oförändrade fält** . Fältvärdena som visas för den publicerade och utkast versionen är samma.

  ![Filter för att jämföra uppdateringar med ditt publicerade eller för hands versions erbjudande](./media/compare-changes-marketplace.png)

>[!NOTE]
> Följande sidor stöder för närvarande inte **jämförelse**:
>- CSP åter försäljarens publik
>- Teknisk konfiguration för test enhet
>- Testa enhetens Marketplace-lista
>- Co-försäljning
>- Kompletterande filer

### <a name="compare-changes-to-published-offer"></a>Jämför ändringar i publicerat erbjudande

Följ instruktionerna nedan för att jämföra ändringarna från det publicerade erbjudandet:

1. Välj **Jämför** i kommando fältet på sidan. På sidan **Jämför** får du sida-vid-sida-versioner av de sparade ändringarna i erbjudandet och det publicerade Marketplace-erbjudandet.
2. När du har åtkomst till **Jämför** från en speciell sida i erbjudandet visar standardinställningen bara de ändringar som gjorts på den sidan.
    - Om du vill jämföra ändringar på alla sidor ändrar du sidan från **Välj en sida som ska jämföras**.  
    - Om du vill jämföra ändringar i erbjudandet på alla sidor väljer du **alla sidor**.

Som standard **jämförs** de nya ändringarna i erbjudandet för Live Marketplace.

Kom ihåg att publicera om erbjudandet igen när du har gjort uppdateringar för att ändringarna ska börja gälla.

### <a name="compare-changes-to-a-preview-offer"></a>Jämför ändringar i ett för hands erbjudande

Om du har ändringar i för hands versionen som inte är Live kan du jämföra nya ändringar med erbjudandet för hands versions Marketplace.

Följ anvisningarna nedan om du vill jämföra nya ändringar med ditt för hands versions erbjudande i Marketplace:

1. Välj **Jämför** i kommando fältet på sidan.
2. Välj List rutan **med** och ändra den från **Live-erbjudandet** till för **hands versions erbjudandet**.
3. Sidan **Jämför** innehåller versioner sida vid sida som visar ändringarna.

>[!NOTE]
>Om ditt erbjudande inte har blivit Live ännu men du har publicerat till för hands version har du inte möjlighet att jämföra med ett Live-erbjudande.

Kom ihåg att publicera om erbjudandet igen när du har gjort uppdateringar för att ändringarna ska börja gälla.

## <a name="delete-a-draft-offer"></a>Ta bort ett utkast erbjudande

Du kan ta bort ett utkast erbjudande (ett som inte har publicerats) genom att välja **ta bort utkast** på sidan **erbjudande översikt** . Det här alternativet är inte tillgängligt för dig om du tidigare har publicerat erbjudandet.

När du har bekräftat att du vill ta bort utkast erbjudandet kommer erbjudandet inte längre vara synligt eller tillgängligt i Partner Center och sidan **alla erbjudanden** öppnas.

## <a name="delete-a-draft-plan"></a>Ta bort ett utkast till plan

Om du vill ta bort en plan som inte har publicerats väljer du **ta bort utkast** från sidan **plan översikt** . Det här alternativet är inte tillgängligt för dig om du tidigare har publicerat erbjudandet.

När du har bekräftat att du vill ta bort utkast planen kommer planen inte längre att vara synlig eller tillgänglig i Partner Center.

## <a name="cancel-publishing"></a>Avbryt publicering

Så här avbryter du ett erbjudande med status **publicering pågår** :

1. Välj namnet på erbjudandet för att öppna sidan **erbjudande översikt** .
1. Välj **Avbryt publicera** i det övre högra hörnet på sidan.
1. Bekräfta att du vill stoppa att erbjudandet publiceras.

Om du vill publicera erbjudandet vid ett senare tillfälle måste du starta publicerings processen.

> [!NOTE]
> Du kan förhindra att ett erbjudande bara publiceras om erbjudandet ännu inte har gått vidare till utgivarens signerings steg. När du har valt **Go Live** har du inte möjlighet att avbryta publiceringen längre.

## <a name="stop-selling-an-offer-or-plan"></a>Sluta sälja ett erbjudande eller en plan

Av olika anledningar kan du välja att ta bort erbjudande listan från Microsofts kommersiella marknads plats. Erbjudande borttagning garanterar att nya kunder inte längre kan köpa eller distribuera ditt erbjudande, men inte har någon inverkan på befintliga kunder.

Om du vill sluta att sälja ett erbjudande när du har publicerat det väljer du **sluta sälja** från sidan **erbjudande översikt** .

När du har bekräftat att du vill sluta sälja erbjudandet inom några timmar kommer det inte längre att vara synligt på den kommersiella marknads platsen och inga nya kunder kan ladda ned det.

Om du vill sluta sälja en plan väljer du **sluta sälja** från sidan **plan översikt** . Alternativet att sluta sälja en plan är bara tillgängligt om du har mer än en prenumeration på erbjudandet. Du kan välja att sluta sälja en plan utan att påverka andra planer i erbjudandet. När du har bekräftat att du vill sluta sälja planen måste du publicera om erbjudandet för att ändringen ska börja gälla. När erbjudandet har återpublicerats visas inte längre planen på den kommersiella marknads platsen och inga nya kunder kan hämta den.

Kunder som tidigare förvärvade erbjudandet eller abonnemanget kan fortfarande använda det. De kan ladda ned den igen, men de får inga uppdateringar om du uppdaterar och publicerar om erbjudandet eller planen vid ett senare tillfälle.

När din begäran om att sluta sälja erbjudandet/planen har slutförts ser du fortfarande den på den kommersiella Marketplace-portalen på Partner Center med statusen **inte tillgänglig** .

Om du väljer att lista eller sälja erbjudandet eller planeraren igen följer du anvisningarna för att [Uppdatera ett befintligt erbjudande](#update-an-existing-offer-in-the-commercial-marketplace). Glöm inte att du behöver **publicera** erbjudandet eller planera igen när du har gjort några ändringar.

## <a name="remove-offers-from-existing-customers"></a>Ta bort erbjudanden från befintliga kunder

[Logga en supportbegäran](https://aka.ms/marketplacepublishersupport)om du vill ta bort erbjudanden från befintliga kunder. I listan support ämne väljer du erbjudande för **extern Marketplace**  >  **eller app-delistor, borttagning eller uppsägning** och skicka begäran. Support teamet leder dig genom borttagnings processen för erbjudandet.

## <a name="sync-private-plan-audiences"></a>Synkronisera privat planens mål grupper

Om erbjudandet innehåller en eller flera planer som är konfigurerade för att endast vara tillgängliga för en privat begränsad publik, är det möjligt att endast uppdatera den mål grupp som har åtkomst till det privata schemat utan att publicera andra ändringar i erbjudandet. 

Så här uppdaterar och synkroniserar du den privata mål gruppen för dina abonnemang:

- Ändra mål gruppen i ett eller flera privata planer med knappen + **Lägg till ID** eller **Importera kunder (CSV)** och spara sedan ändringarna.
- Välj **Synkronisera privat publik** på sidan **plan översikt** .

**Synkronisera privat publik** publicerar bara ändringar i dina privata mål grupper, utan att publicera några andra uppdateringar som du har gjort i utkastet till erbjudandet.

## <a name="next-steps"></a>Nästa steg

- [Kontrol lera publicerings statusen för ditt kommersiella Marketplace-erbjudande](./publishing-status.md)
