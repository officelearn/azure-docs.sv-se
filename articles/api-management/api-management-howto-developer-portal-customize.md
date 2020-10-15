---
title: Komma åt och anpassa den hanterade Developer-portalen – Azure API Management | Microsoft Docs
description: Lär dig hur du använder den hanterade versionen av Developer-portalen i API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: 3ceb8fd154e8ad533f4bf6bc9eb0ec3900749f8b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075373"
---
# <a name="access-and-customize-developer-portal"></a>Komma åt och anpassa Developer-portalen

Developer Portal är en automatiskt genererad, helt anpassningsbar webbplats med dokumentationen för dina API: er. Det är där API-konsumenter kan identifiera dina API: er, lära sig hur de används och begära åtkomst.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Öppna den hanterade versionen av Developer-portalen
> * Navigera i det administrativa gränssnittet
> * Anpassa innehållet
> * Publicera ändringarna
> * Visa den publicerade portalen

Du hittar mer information om Developer-portalen i [Översikt över Azure API Management Developer-portalen](api-management-howto-developer-portal.md).

![API Management Developer-portalen-admin-läge](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Importera och publicera en Azure API Management-instans. Mer information finns i [Importera och publicera](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Få åtkomst till portalen som administratör

Följ stegen nedan för att få åtkomst till den hanterade versionen av portalen.

1. Gå till din API Management tjänst instans i Azure Portal.
1. Klicka på **Developer Portal** -knappen i det övre navigerings fältet. En ny flik i webbläsaren med en administrativ version av portalen öppnas.

## <a name="understand-the-portals-administrative-interface"></a>Förstå portalens administrativa gränssnitt

### <a name="default-content"></a>Standard innehåll 

Om du ansluter till portalen för första gången, kommer standard innehållet att tillhandahållas automatiskt i bakgrunden. Standard innehållet har utformats för att presentera portalens funktioner och minimera antalet anpassningar som krävs för att anpassa portalen. Du kan läsa mer om vad som ingår i Portal innehållet i [Översikt över Azure API Management Developer-portalen](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Visuell redigerare

Du kan anpassa portalens innehåll med den visuella redigeraren. I meny avsnitten till vänster kan du skapa eller ändra sidor, media, layouter, menyer, format eller webbplats inställningar. Med meny alternativen längst ned kan du växla mellan visnings punkter (till exempel mobil eller stationär), Visa elementen i portalen som är synliga för autentiserade eller anonyma användare eller Spara eller ångra åtgärder.

Du kan lägga till rader på en sida genom att klicka på en blå ikon med ett plus tecken. Widgetar (till exempel text, bilder eller API-listor) kan läggas till genom att trycka på en grå ikon med ett plus tecken. Du kan arrangera om objekt på en sida med dra och släpp-interaktionen. 

### <a name="layouts-and-pages"></a>Layouter och sidor

![Sidor och layouter](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Layouter definierar hur sidor visas. I standard innehållet finns det till exempel två layouter – en gäller för start sidan och den andra till alla återstående sidor.

En layout tillämpas på en sida genom att matcha dess URL-mall till sidans URL. Till exempel används layout med en URL-mall på `/wiki/*` varje sida med `/wiki/` segmentet i URL: en: `/wiki/getting-started` , `/wiki/styles` osv.

I bilden ovan markeras innehåll som hör till layouten i blått, medan sidan markeras med rött. Meny avsnitten markeras.

### <a name="styling-guide"></a>Hand bok

![Hand bok](media/api-management-howto-developer-portal-customize/styling-guide.png)

Hand boken är en panel som skapats med designers i åtanke. Det gör det möjligt för att visa och formatera alla visuella element i portalen. Formatet är hierarkiskt-många element ärver egenskaper från andra element. Till exempel använder knapp element färger för text och bakgrund. Om du vill ändra färg på en knapp måste du ändra den ursprungliga varianten av färg.

Om du vill redigera en variant klickar du på den och väljer Penn ikonen som visas överst. När du har gjort ändringarna i popup-fönstret stänger du det.

### <a name="save-button"></a>Knappen Spara

![Knappen Spara](media/api-management-howto-developer-portal-customize/save-button.png)

När du gör en ändring i portalen måste du spara den manuellt genom att trycka på knappen **Spara** på menyn längst ned. När du sparar ändringarna överförs det ändrade innehållet automatiskt till din API Management-tjänst.

## <a name="customize-the-portals-content"></a>Anpassa portalens innehåll

Innan du gör din portal tillgänglig för besökarna bör du anpassa det automatiskt genererade innehållet. Rekommenderade ändringar omfattar layouter, format och innehållet på Start sidan.

> [!NOTE]
> På grund av integrations överväganden kan följande sidor inte tas bort eller flyttas under en annan URL:,,,,,,,,, `/404` `/500` `/captcha` `/change-password` `/config.json` `/confirm/invitation` `/confirm-v2/identities/basic/signup` `/confirm-v2/password` `/internal-status-0123456789abcdef` `/publish` `/signin` `/signin-sso` , `/signup` .

### <a name="home-page"></a>Startsida

Standard **Start** sidan är fylld med innehåll i dummy. Du kan antingen ta bort hela avsnitten med innehållet eller behålla strukturen och justera elementen en i taget. Ersätt den genererade texten och bilderna med dina egna och se till att länkarna pekar på önskade platser.

### <a name="layouts"></a>Former

Ersätt den automatiskt genererade logo typen i navigerings fältet med din egen bild.

### <a name="styling"></a>Formatera

Även om du inte behöver justera några format kan du överväga att justera vissa element. Ändra till exempel den primära färgen så att den matchar ditt märkes färg.

### <a name="customization-example"></a>Exempel på anpassning

I videon nedan visar vi hur du redigerar innehållet i portalen, anpassar webbplatsens utseende och publicerar ändringarna.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publicera portalen

Om du vill göra din portal och dess senaste ändringar tillgängliga för besökare måste du publicera den.

1. Se till att du har sparat ändringarna genom att klicka på ikonen **Spara** .
1. Klicka på **Publicera webbplats** i avsnittet **åtgärder** på menyn. Den här åtgärden kan ta några minuter.  
    ![Publicera Portal](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> Portalen måste återpubliceras efter API Management tjänst konfigurations ändringar, till exempel tilldela en anpassad domän, uppdatera identitets leverantörer, ställa in delegering, ange inloggnings-och produkt villkor med mera.

## <a name="visit-the-published-portal"></a>Besök den publicerade portalen

När du har publicerat portalen kan du använda den på samma URL som administrations panelen, till exempel `https://contoso-api.developer.azure-api.net` . Visa den i en separat webbläsarsession (Incognito/Private bläddringsläge) som extern besökare.

## <a name="apply-the-cors-policy-on-apis"></a>Använd CORS-principen för API: er

Du måste aktivera CORS (resurs delning mellan ursprung) i dina API: er så att besökare av portalen kan testa API: erna via den inbyggda interaktiva konsolen. Mer information finns i [den här dokumentations artikeln](api-management-howto-developer-portal.md#cors) .

## <a name="next-steps"></a>Nästa steg
- [Optimera och Spara på dina moln utgifter](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Läs mer om Developer-portalen:

- [Översikt över Azure API Management Developer-portalen](api-management-howto-developer-portal.md)