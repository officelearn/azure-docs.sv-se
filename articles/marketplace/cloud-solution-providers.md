---
title: Leverantörer av molnlösningar | Azure Marketplace
description: Utgivare kan nu sälja sina erbjudanden via Microsoft Cloud Solution Provider (CSP) partnerkanal.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: dsindona
ms.openlocfilehash: 9d7cbdd5ad551ba48f08f62237d5384aa7998fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281671"
---
# <a name="cloud-solution-providers"></a>Leverantörer av molnlösningar

Programvaruerbjudanden kan nå miljontals kvalificerade Microsoft-kunder som betjänas av partner i CSP-programmet (Cloud Solution Provider), förutom allmänhetens tillgänglighet för erbjudanden via [Microsofts webbfönster.](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)

Utgivare konfigurerar erbjudanden om tillgänglighet i CSP-programmet på opt-in-basis, för ett nytt erbjudande eller ett befintligt erbjudande, vilket gör det möjligt för partner att sälja dina produkter och skapa paketerade lösningar för kunder.

Utgivare ansvarar för att tillhandahålla break-fix-support till slutkunder och för att tillhandahålla en mekanism för partner i CSP-programmet och/eller kunder att kontakta dig för support. Det är en bästa praxis att förse partner i CSP-programmet med användardokumentation, utbildning och meddelanden om hälso- och sjukvård/avbrott för tjänster (beroende på vad som är tillämpligt) så att partner i CSP-programmet är utrustade för att hantera supportförfrågningar på nivå 1 från kunder.  

Följande erbjudanden är berättigade att väljas in för att säljas av partners i CSP-programmet:

- SaaS-transact-erbjudanden (Software-as-a-Service)
- Virtuella datorer (virtuella datorer)
- Lösningsmallar
- Hanterade program

> [!NOTE]
> Behållare och ta med din egen licens (BYOL) VM SKU:er har valt att säljas av partner i CSP-programmet som standard.

## <a name="how-to-configure-an-offering"></a>Så här konfigurerar du ett erbjudande

Opt-in-inställningen för CSP-program är konfigurerad i Partner Center eller Cloud Partner Portal erbjuder upplevelse av att skapa. [Läs mer om den föränderliga förlagsupplevelsen](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Opt-in-partnercenter

I Partner Center hittar du opt-in-upplevelsen under CSP-återförsäljarmodulen.

![CSP-återförsäljares publik](media/marketplace-publishers-guide/csp-reseller-audience.png)

I målgruppsmodulen för CSP-återförsäljare har du tre alternativ att välja mellan:

- Alternativ ett: Alla partner i CSP-programmet
- Alternativ två: Specifika partner i CSP-programmet väljer jag
- Alternativ tre: Inga partner i CSP-programmet

#### <a name="option-one-any-partner-in-the-csp-program"></a>Alternativ ett: Alla partner i CSP-programmet

![Alla partner i CSP-programmet](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Genom att välja det här alternativet är alla partner i CSP-programmet berättigade att sälja ditt erbjudande vidare till sina kunder.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Alternativ två: Specifika partner i CSP-programmet väljer jag

![Specifika partners i CSP-programmet väljer jag](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Genom att välja det här alternativet godkänner du vilka partner i CSP-programmet som är berättigade att sälja ditt erbjudande vidare.

Om du vill auktorisera partner klickar du på **Välj CSP-partner** och en meny visas som gör att du kan söka efter partnernamn eller CSP Azure Active Directory (AAD) klient-ID.

![Välj CSP-meny](media/marketplace-publishers-guide/csp-pop-up-module.png)

Du kan använda sökfilter, till exempel **Land,** **Kompetens**eller **Färdighet**.

![Land-, kompetens- och färdighetsfilter för partnersökning](media/marketplace-publishers-guide/csp-add-resellers.png)

När du har valt listan över partner väljer du **Lägg till**.

![Exempellista över behöriga partner i CSP-programmet](media/marketplace-publishers-guide/csp-add-resellers-details.png)

En tabell med listan över partner som du har valt visas på målgruppssidan för CSP-återförsäljare.

![Tabell med en lista över partner på målgruppssidan för CSP-återförsäljare](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Välj **Spara utkast** om du vill registrera ändringarna.

Om det här erbjudandet är opublicerat måste du publicera erbjudandet för att göra det tillgängligt för dina valda partner.

>[!NOTE]
>Om du godkänner en partner i CSP-programmet i en viss region kan de sälja erbjudandet till alla kunder som tillhör den regionen. Se [Cloud Solution Provider program regionala marknader och valuta](https://docs.microsoft.com/partner-center/regional-authorization-overview) för mer information om hur CSP erbjudanden klassificeras under regioner.

Om du uppdaterar CSP-listan över ett redan publicerat erbjudande lägger du till ytterligare partner och väljer **Synkronisera CSP-målgrupp**.

Om du har ett erbjudande som redan har en lista över auktoriserade partner och du vill använda samma lista för ett annat erbjudande använder du **Importera/Exportera**. Navigera till erbjudandet som har CSP-listan och välj **Exportera CSP: er**. Funktionen utvecklar en CSV-fil som kan importeras till ett annat erbjudande.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Alternativ tre: Inga partner i CSP-programmet

![Inga partner i CSP-programmet](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Genom att välja det här alternativet väljer du ditt erbjudande från CSP-programmet. Du kan ändra det här valet när som helst.

### <a name="cloud-partner-portal-opt-in"></a>Opt-in-portal för molnpartner

I Cloud Partner Portal anges opt-in på fliken Marketplace eller Storefront. Möjligheten att välja specifika partner i CSP-programmet är endast tillgänglig i Partner Center.

![CSP opt-in erfarenhet av CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Avauktorisera partners i CSP-programmet

Om du har auktoriserat en partner i CSP-programmet och den partnern redan har sålt produkten vidare till sina kunder, kommer du inte att tillåtas att avauktorisera den partnern.

Om en partner i CSP-programmet inte har sålt din produkt till sina kunder och du vill ta bort CSP efter att ditt erbjudande har publicerats, använd följande instruktioner:

1. Gå till [sidan Supportbegäran](https://aka.ms/marketplacepublishersupport). De första rullgardinsmenyerna fylls i automatiskt åt dig.

   > [!NOTE]
   > Ändra inte de förifyllda rullgardinsmenyn.

2. För **Välj produktversion**väljer du **Live-erbjudandehantering**.
3. Välj den kategori som refererar till erbjudandet för **att välja en kategori som bäst beskriver problemet.**
4. Välj Uppdatera ett **befintligt erbjudande**för **att välja ett problem som bäst beskrivs.**
5. Välj **Nästa** om du vill dirigeras till **sidan Probleminformation** om du vill ange mer information om problemet.
6. Använd **Avauktorisera CSP** som ärendetitel och fyll i resten av de avsnitt som krävs.




## <a name="navigate-between-options"></a>Navigera mellan alternativ

Använd det här avsnittet om du vill navigera mellan de tre CSP-återförsäljaralternativen.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navigera från alternativ ett: Alla partner i CSP-programmet

Om ditt erbjudande för närvarande är **alternativ 1: En partner i CSP-programmet** och du vill navigera till något av de andra två alternativen använder du följande instruktioner för att skapa en begäran:

1. Gå till [sidan Supportbegäran](https://aka.ms/marketplacepublishersupport). De första rullgardinsmenyerna fylls i automatiskt åt dig.

   > [!NOTE]
   > Ändra inte de förifyllda rullgardinsmenyn.

2. För **Välj produktversion**väljer du **Live-erbjudandehantering**.
3. Välj den kategori som refererar till erbjudandet för **att välja en kategori som bäst beskriver problemet.**
4. Välj Uppdatera ett **befintligt erbjudande**för **att välja ett problem som bäst beskrivs.**
5. Välj **Nästa** om du vill dirigeras till **sidan Probleminformation** om du vill ange mer information om problemet.
6. Använd **Avauktorisera CSP** som ärendetitel och fyll i resten av de avsnitt som krävs.

> [!NOTE]
> Om du försöker navigera till alternativ två och en partner i CSP-programmet redan har sålt erbjudandet vidare till sina kunder, är den partnern som standard redan i din lista över auktoriserade partner.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navigera från alternativ två: Specifika partner i CSP-programmet väljer jag

Om ditt erbjudande för närvarande är **alternativ 2: Specifika partner i CSP-programmet väljer jag** och du vill navigera till alternativ **ett: Alla partner i CSP-programmet**använder du följande instruktioner för att skapa en begäran:

1. Gå till [sidan Supportbegäran](https://aka.ms/marketplacepublishersupport). De första rullgardinsmenyerna fylls i automatiskt åt dig.

   > [!NOTE]
   > Ändra inte de förifyllda rullgardinsmenyn.

2. För **Välj produktversion**väljer du **Live-erbjudandehantering**.
3. Välj den kategori som refererar till erbjudandet för **att välja en kategori som bäst beskriver problemet.**
4. Välj Uppdatera ett **befintligt erbjudande**för **att välja ett problem som bäst beskrivs.**
5. Välj **Nästa** om du vill dirigeras till **sidan Probleminformation** om du vill ange mer information om problemet.
6. Använd **Avauktorisera CSP** som ärendetitel och fyll i resten av de avsnitt som krävs.

 Om ditt erbjudande för närvarande är **alternativ 2: Specifika partner i CSP-programmet jag väljer** och du vill navigera till alternativ **3: Inga partner i CSP-programmet,** kommer du bara att kunna navigera till det alternativet om partnerna i CSP-programmet som du tidigare hade auktoriserat inte har sålt ditt erbjudande till slutkunder. Använd följande instruktioner för att skapa en begäran:

1. Gå till [sidan Supportbegäran](https://aka.ms/marketplacepublishersupport). De första rullgardinsmenyerna fylls i automatiskt åt dig.

   > [!NOTE]
   > Ändra inte de förifyllda rullgardinsmenyn.

2. För **Välj produktversion**väljer du **Live-erbjudandehantering**.
3. Välj den kategori som refererar till erbjudandet för **att välja en kategori som bäst beskriver problemet.**
4. Välj Uppdatera ett **befintligt erbjudande**för **att välja ett problem som bäst beskrivs.**
5. Välj **Nästa** om du vill dirigeras till **sidan Probleminformation** om du vill ange mer information om problemet.
6. Använd **Avauktorisera CSP** som ärendetitel och fyll i resten av de avsnitt som krävs.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navigera från alternativ 3: Inga partner i CSP-programmet

Om ditt erbjudande för närvarande är **alternativ 3: Inga partner i CSP-programmet**kan du navigera till något av de andra två alternativen när som helst.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Dela försäljnings- och supportmaterial med partners i CSP-programmet

För att hjälpa partner i Cloud Solution Provider-programmet att mest effektivt representera ditt erbjudande och samarbeta med din organisation måste du skicka in försäljnings- och supportmaterial som är tillgängligt för återförsäljarna. Dessa resurser kommer inte att exponeras för kunder på marknadsplatsen skyltfönster.

### <a name="partner-center-csp-channel"></a>CSP-kanal för Partner Center

Om du har valt CSP-kanalen i Partner Center måste utgivare ange en webbadress som är värd för relevant marknadsföringsmaterial och kanalkontaktinformation till CSP-kanalen under modulen för erbjudandelistor:

![Information om säkerheter i Partner Center](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>CSP-kanal för Cloud Partner Portal

Om du har valt CSP-kanalen i Cloud Partner Portal måste utgivare ange en webbadress som är värd för relevant marknadsföringsmaterial och kanalkontaktinformation till CSP-kanalen:

![Information om säkerheter för Cloud Partner Portal](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Nästa steg

Besök [Azure Marketplace och AppSource Publisher Guide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Mer information om GTM-marknadsplatstjänster finns i [Gå till marknaden.](https://partner.microsoft.com/reach-customers/gtm)

Logga in [på Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) eller Cloud Partner [Portal](https://cloudpartner.azure.com/) för att skapa och konfigurera ditt erbjudande.
