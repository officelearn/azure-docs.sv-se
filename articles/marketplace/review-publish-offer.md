---
title: Granska och publicera ett erbjudande på Microsofts kommersiella marknads platser
description: Använd partner Center för att skicka in ditt erbjudande till Preview, förhandsgranska ditt erbjudande och publicera det sedan på Microsofts kommersiella marknads plats.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 08/12/2020
ms.openlocfilehash: 53e5331ab36d5466bd5cdb495ac505dae29e57bf
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142306"
---
# <a name="review-and-publish-an-offer-to-the-commercial-marketplace"></a>Granska och publicera ett erbjudande på den kommersiella Marketplace

Den här artikeln visar hur du kan använda Partner Center för att skicka in ditt erbjudande till Preview, förhandsgranska ditt erbjudande och sedan publicera det på Microsofts kommersiella marknads platser. Vi tar också upp hur du kontrollerar publicerings statusen när den går igenom publicerings stegen. Du måste redan ha skapat ett erbjudande som du vill publicera.

## <a name="go-to-your-offer-in-the-commercial-marketplace"></a>Gå till ditt erbjudande på den kommersiella Marketplace

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard/home).
2. På den vänstra navigerings menyn väljer du **kommersiell Marketplace**-  >  **Översikt**.
3. På fliken **Översikt** under **erbjudanden**visas en av följande status indikatorer i kolumnen **status** för varje erbjudande.

    | Status | Beskrivning |
    | ------------ | ------------- |
    | Draft | Erbjudandet har skapats men det har inte publicerats. |
    | Publicering pågår | Erbjudandet fungerar på ett sätt genom publicerings processen. |
    | Åtgärd krävs | Vi har identifierat ett kritiskt problem under certifieringen eller under en annan publicerings fas. |
    | Förhandsgranskning | Vi har certifierat erbjudandet, som nu väntar en slutgiltig verifiering av utgivaren. Välj **Go Live** för att publicera erbjudandet Live. |
    | Direktsändning | Erbjudandet är Live på Marketplace och kan ses och förvärvas av kunder. |
    | Väntande stoppa försäljning | Utgivare valde "stoppa försäljning" på ett erbjudande eller en plan, men åtgärden har ännu inte slutförts. |
    | Inte tillgängligt i Marketplace | Ett tidigare publicerat erbjudande i Marketplace har tagits bort. |
    |||

4. I kolumnen **erbjudande alias** väljer du det erbjudande du vill förhandsgranska och publicera.

## <a name="submit-your-offer-to-preview"></a>Skicka ditt erbjudande till för hands version

1. Om du vill skicka ditt erbjudande till för hands version väljer du **Granska och publicera** i det övre högra hörnet i portalen. Sidan **Granska och publicera** visas.
1. Kontrol lera att kolumnen **status** för varje sida säger **slutförd**. De tre möjliga statusarna är följande:

   - **Inte startat** – sidan är ofullständig.
   - **Ofullständig** – sidan saknar nödvändig information eller innehåller fel som behöver åtgärdas. Du måste gå tillbaka till sidan och uppdatera den.
   - **Klar** – sidan har slutförts. Alla nödvändiga data har angetts och det finns inga fel.

1. Om någon av sidorna har en annan status än **slutförd**väljer du sidans namn i kolumnen **sida** , korrigerar problemet, sparar sidan och väljer sedan **Granska och publicera** igen för att återgå till den här sidan.
1. När alla sidor har slutförts, i rutan **kommentarer för certifiering** , anger du test instruktioner till certifierings teamet för att se till att appen testas korrekt. Ge eventuella kompletterande anteckningar som hjälper dig att förstå din app.
1. Om du vill skicka publicerings erbjudandet väljer du **publicera**. Sidan **erbjudande översikt** visas och visar publicerings status.

## <a name="validation-and-publishing-steps"></a>Validerings-och publicerings steg

När du har valt **publicera**fortsätter validerings-och publicerings processerna i ordning. Den vanligaste publicerings processen visas i den här tabellen:

| Fas | Vad händer |
| ------------ | ------------- | ------------- |
| Automatiserad verifiering | Vi bearbetar en uppsättning automatiserade verifieringar. |
| Certifiering | Vi utför manuella verifieringar. |
| Skapa för hands version | List sidan för för hands versionen av erbjudandet är tillgänglig för alla som har en för hands versions länk. Om ditt erbjudande kommer att säljas via Microsoft (transactable) kan bara den mål grupp som du har angett på sidan för **hands version** av erbjudandet köpa och få åtkomst till erbjudandet om testning. |
| Utgivarens utloggning | Vi skickar dig ett e-postmeddelande med en begäran om att förhandsgranska och godkänna ditt erbjudande. |
| Publicera | Vi kör en serie steg för att kontrol lera att för hands versions erbjudandet publiceras Live till den kommersiella marknads platsen. |
|||

## <a name="automated-validation-phase"></a>Automatiserad validerings fas

Det första steget i publicerings processen är en uppsättning automatiserade verifieringar. Varje validerings steg motsvarar en funktion som du valde när du skapade erbjudandet. Varje verifierings kontroll måste slutföras innan erbjudandet kan gå vidare till nästa steg i publicerings processen.

- **Konfigurera konfiguration av inköps flöde** (<10 min)

   Vi garanterar att ditt erbjudande kan uppfyllas när kunderna köpts via Azure Portal. Det här steget gäller bara för erbjudanden som säljs via Microsoft.

- **Test enhet för data verifiering** (~ 5 min)

   Vi verifierar de data som du angav på sidan teknisk konfiguration i erbjudandet. Vi testar och godkänner funktionen testa enheten. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Test av enhets etablering** (~ 30 min)

    När du har verifierat data och funktioner på test enheten i föregående steg distribuerar vi och replikerar instanser av test enheten så att de är redo för kund användning. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Verifiering och registrering av lead-hantering** (<15 min)

    Vi bekräftar att ditt lead management-system kan ta emot kund leads utifrån den information du angav på sidan **erbjudande konfiguration** . Det här steget gäller bara för erbjudanden där lead management har Aktiver ATS.

## <a name="certification-phase"></a>Certifierings fas

Erbjudanden som skickas till den kommersiella marknaden måste certifieras innan de kan publiceras. Erbjudanden genomgå rigorös testning, vissa automatiserade och andra manuella. Mer information finns i [certifierings principer för kommersiella Marketplace](https://aka.ms/commercial-marketplace-certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Typer av verifieringar som sker under certifieringen

Det finns tre nivåer av verifiering som ingår i certifierings processen för varje skickat erbjudande.

- Affärs berättigande för utgivare
- Innehålls validering
- Teknisk validering

#### <a name="publisher-business-eligibility"></a>Affärs berättigande för utgivare

Varje erbjudande typ kontrollerar en uppsättning nödvändiga bas Berättiganderegler. Detta kriterium kan omfatta utgivar MPN status, kompetenser, kompetens nivåer och så vidare.

#### <a name="content-validation"></a>Innehålls validering

Informationen som angavs när du skapade erbjudandet kontrol leras för kvalitet och relevans. Dessa kontroller granskar dina poster för information om Marketplace-listor, prissättning, tillgänglighet, associerade planer och så vidare. För att uppfylla registrerings villkoren för Azure Marketplace och Microsoft AppSource, kommer vi att validera att erbjudandet innehåller:

- En rubrik som korrekt beskriver erbjudandet
- Välskrivna beskrivningar som ger en grundlig översikt och ett värde förslag
- Kvalitets skärm bilder och videor
- En förklaring av hur erbjudandet använder Microsoft-plattformar och-verktyg.

Läs mer om kriterierna för innehålls validering genom att läsa [allmänna registrerings principer](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Teknisk validering

Under teknisk validering genomgår erbjudandet (paketet eller den binära) följande kontroller.

- Genomsökt efter skadlig kod
- Övervakade nätverks anrop
- Paketet har analyser ATS
- Grundlig genomsökning av erbjudandets funktioner

Erbjudandet testas på olika plattformar och versioner för att säkerställa att det är robust.

### <a name="certification-failure-report"></a>Rapport över certifierings problem

Om erbjudandet Miss lyckas med någon av inställningarna för registrering, teknik eller princip, eller om du inte är berättigad till att skicka ett erbjudande av den typen, e-posta vi en certifierings fel rapport till dig.

Den här rapporten innehåller beskrivningar av eventuella principer som misslyckats, tillsammans med gransknings anteckningar. Läs den här e-postrapporten, åtgärda eventuella problem, gör uppdateringar till ditt erbjudande vid behov och skicka erbjudandet på nytt med hjälp av den [kommersiella Marketplace-portalen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) i Partner Center. Du kan skicka in erbjudandet igen så många gånger som behövs tills du skickar certifieringen.

## <a name="preview-creation-phase"></a>Fasen skapande av för hands version

Under skapandet av för hands versionen skapar vi en version av ditt erbjudande som bara är tillgänglig för den mål grupp som du har angett på sidan för **förhands granskning** av ditt erbjudande. För hands versionen av ditt erbjudande är inte tillgänglig för någon utanför förhands gransknings gruppen förrän du publicerar erbjudandet Live.

> [!NOTE]
> Använd inte förhands gransknings gruppen för att ge personer utanför organisationen insyn i ett erbjudande. Använd alternativet för privat erbjudande i stället. I det här läget har ditt erbjudande inte testats fullständigt och godkänts och är inte redo för distribution utanför företaget.

## <a name="publisher-sign-off-phase"></a>Utgivar signerings fas

När erbjudandet är klart för att du ska kunna granska och vara inloggad skickar vi ett e-postmeddelande till dig om att granska och godkänna din för hands version av erbjudandet. Du kan också uppdatera sidan **erbjudande översikt** i webbläsaren för att se om ditt erbjudande har nått utgivarens signerings fas. Om den har det är knappen **Go Live** och för hands versions länkarna tillgänglig.

Följande skärm bild visar sidan **erbjudande översikt** för ett SaaS-erbjudande. De verifierings steg som visas på den här sidan varierar beroende på erbjudande typen och de val du gjorde när du skapade erbjudandet.

![Visar sidan erbjudande översikt för ett erbjudande i Partner Center. Knappen gå live och för hands versions länkarna visas.](./partner-center-portal/media/publish-status-publisher-signoff.png)

### <a name="to-preview-your-offer-and-sign-off"></a>För hands version av erbjudandet och signeringen

1. På sidan **erbjudande översikt** väljer du länken under knappen **gå live** för att förhandsgranska ditt erbjudande.

   > [!NOTE]
   > Det finns en länk till antingen AppSource Preview, Azure Marketplace Preview eller båda beroende på vilka alternativ du valde när du skapade erbjudandet. Om du har valt att sälja ditt erbjudande via Microsoft kan alla som har lagts till i förhands gransknings gruppen testa förvärvet och distributionen av ditt erbjudande för att säkerställa att det uppfyller dina krav under det här skedet.

1. Om du vill göra ändringar efter att ha förhandsgranskat erbjudandet kan du redigera och skicka om för att publicera en ny för hands version. Mer information finns i [Uppdatera ett befintligt erbjudande i den kommersiella marknads platsen](./partner-center-portal/update-existing-offer.md).

1. När du har godkänt din för hands version för att publicera erbjudandet Live på den kommersiella Marketplace väljer du **Go Live**.

   > [!TIP]
   > Om ditt erbjudande redan är aktivt och tillgängligt för allmänheten på Marketplace, är de uppdateringar du gör inte aktiva förrän du väljer **Go Live**.

## <a name="publish-phase"></a>Publicerings fas

Nu när du har valt att sätta igång med ditt erbjudande, vilket gör det tillgängligt på den kommersiella Marketplace, utför vi en serie slutliga verifierings kontroller för att säkerställa att Live-erbjudandet är konfigurerat precis som för hands versionen av erbjudandet.

- **Konfigurera konfiguration av inköps flöde** (>10 min)

    Vi garanterar att ditt erbjudande kan uppfyllas när kunderna köpts via Azure Portal. Det här steget gäller bara för erbjudanden som säljs via Microsoft.

- **Test enhet för data verifiering** (~ 5 min)

    Vi verifierar de data som du angav på sidan teknisk konfiguration i erbjudandet. Vi testar och godkänner funktionen testa enheten. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Test av enhets etablering** (~ 30 min)

    Vi distribuerar och replikerar instanser av test enheten så att de är redo för kund användning. Det här steget gäller bara för erbjudanden med en aktive rad test enhet.

- **Verifiering och registrering av lead-hantering** (>15 min)

    Vi bekräftar att ditt lead-hanterings system kan ta emot kund leads utifrån informationen på **installations** sidan för erbjudandet. Det här steget gäller bara för erbjudanden där lead management har Aktiver ATS.

- **Slutgiltig publicering (>30 minuter)**

    Vi garanterar att erbjudandet blir offentligt tillgängligt på Marketplace.

När dessa verifierings kontroller har slutförts är ditt erbjudande Live på Marketplace.

### <a name="reporting-usage-of-preview-and-other-internal-usage"></a>Rapportering av användning av för hands version och annan intern användning

Om du vill kontrol lera inköps-och installations flödet från slut punkt till slut punkt köper du ditt erbjudande när det är i för hands version. Meddela först Microsoft med ett [support ärende](https://aka.ms/marketplacesupport) för att se till att vi inte behandlar någon avgift.

## <a name="next-step"></a>Nästa steg

[Få åtkomst till analys rapporter för den kommersiella marknads platsen i Partner Center](./partner-center-portal/analytics.md)
