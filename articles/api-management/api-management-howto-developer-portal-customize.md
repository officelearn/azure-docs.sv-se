---
title: Få tillgång till och anpassa den hanterade utvecklarportalen - Azure API Management | Microsoft-dokument
description: Lär dig hur du använder den hanterade versionen av utvecklarportalen i API Management.
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
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244100"
---
# <a name="access-and-customize-developer-portal"></a>Komma åt och anpassa utvecklarportalen

Utvecklarportalen är en automatiskt genererad, helt anpassningsbar webbplats med dokumentation av dina API:er. Det är där API-konsumenter kan identifiera dina API:er, lära dig hur du använder dem och begära åtkomst.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Få tillgång till den hanterade versionen av utvecklarportalen
> * Navigera i det administrativa gränssnittet
> * Anpassa innehållet
> * Publicera ändringarna
> * Visa den publicerade portalen

Du hittar mer information om utvecklarportalen i [azure API Management developer portal overview](api-management-howto-developer-portal.md).

![API Management developer portal - admin-läge](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Krav

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Importera och publicera en Azure API Management-instans. Mer information finns i [Importera och publicera](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Komma åt portalen som administratör

Följ stegen nedan för att komma åt den hanterade versionen av portalen.

1. Gå till din API Management-tjänstinstans i Azure-portalen.
1. Klicka på **utvecklarportalknappen** i det övre navigeringsfältet. En ny webbläsarflik med en administrativ version av portalen öppnas.

## <a name="understand-the-portals-administrative-interface"></a>Förstå portalens administrativa gränssnitt

### <a name="default-content"></a>Standardinnehåll 

Om du använder portalen för första gången etableras standardinnehållet automatiskt i bakgrunden. Standardinnehåll har utformats för att visa upp portalens funktioner och minimera mängden anpassningar som behövs för att anpassa portalen. Du kan läsa mer om vad som ingår i portalinnehållet i [utvecklarportalen](api-management-howto-developer-portal.md)för Azure API Management .

### <a name="visual-editor"></a>Visuell redigerare

Du kan anpassa innehållet i portalen med den visuella redigeraren. Med menyavsnitten till vänster kan du skapa eller ändra sidor, media, layouter, menyer, format mallar eller webbplatsinställningar. Med menyalternativen längst ned kan du växla mellan visningsportar (till exempel mobil eller stationär dator), visa elementen i portalen som är synliga för autentiserade eller anonyma användare eller spara eller ångra åtgärder.

Du kan lägga till rader på en sida genom att klicka på en blå ikon med ett plustecken. Widgetar (till exempel text,bilder eller API:er lista) kan läggas till genom att trycka på en grå ikon med ett plustecken. Du kan ordna om objekt på en sida med dra-och-släpp-interaktionen. 

### <a name="layouts-and-pages"></a>Layouter och sidor

![Sidor och layouter](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Layouter definierar hur sidor ska visas. I standardinnehållet finns det till exempel två layouter – den ena gäller startsidan och den andra på alla återstående sidor.

En layout används på en sida genom att matcha url-mallen med sidans URL. Layout med en URL-mall `/wiki/*` för används till exempel `/wiki/` på varje `/wiki/getting-started`sida `/wiki/styles`med segmentet i webbadressen: , , etc.

I bilden ovan markeras innehåll som hör till layouten i blått, medan sidan är markerad med rött. Menyavsnitten är markerade respektive.

### <a name="styling-guide"></a>Styling guide

![Styling guide](media/api-management-howto-developer-portal-customize/styling-guide.png)

Styling guide är en panel som skapats med designers i åtanke. Det gör det möjligt att övervaka och styling alla visuella element i din portal. Stylingen är hierarkisk - många element ärver egenskaper från andra element. Knappelement använder till exempel färger för text och bakgrund. Om du vill ändra knappens färg måste du ändra den ursprungliga färgvarianten.

Om du vill redigera en variant klickar du på den och väljer pennikonen som visas ovanpå den. När du har gjort ändringarna i popup-fönstret stänger du det.

### <a name="save-button"></a>Knappen Spara

![Knappen Spara](media/api-management-howto-developer-portal-customize/save-button.png)

När du gör en ändring i portalen måste du spara den manuellt genom att trycka på **knappen Spara** i menyn längst ned. När du sparar ändringarna överförs det ändrade innehållet automatiskt till api management-tjänsten.

## <a name="customize-the-portals-content"></a>Anpassa portalens innehåll

Innan du gör portalen tillgänglig för besökarna bör du anpassa det automatiskt genererade innehållet. Rekommenderade ändringar omfattar layouter, format och innehållet på startsidan.

> [!NOTE]
> På grund av integrationsöverväganden kan följande sidor inte `/404`tas `/500` `/captcha`bort `/change-password` `/config.json`eller `/confirm/invitation` `/confirm-v2/identities/basic/signup`flyttas `/confirm-v2/password` `/internal-status-0123456789abcdef`under `/publish` `/signin`en `/signin-sso` `/signup`annan WEBBADRESS: , , , , , , , , , , , , , , .

### <a name="home-page"></a>Startsida

**Standardstartsidan** är fylld med dummyinnehåll. Du kan antingen ta bort hela avsnitten med innehållet eller behålla strukturen och justera elementen en efter en. Ersätt den genererade texten och bilderna med din egen och se till att länkarna pekar på önskade platser.

### <a name="layouts"></a>Layouter

Ersätt den automatiskt genererade logotypen i navigeringsfältet med din egen bild.

### <a name="styling"></a>Styling

Även om du inte behöver justera några format kan du överväga att justera vissa element. Ändra till exempel den primära färgen så att den matchar ditt varumärkes färg.

### <a name="customization-example"></a>Exempel på anpassning

I videon nedan visar vi hur du redigerar innehållet i portalen, anpassar webbplatsens utseende och publicerar ändringarna.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publicera portalen

Om du vill göra portalen och de senaste ändringarna tillgängliga för besökarna måste du publicera den.

1. Kontrollera att du sparade ändringarna genom att klicka på **ikonen Spara.**
1. Klicka på **Publicera webbplats** i avsnittet **Verksamhet** på menyn. Den här åtgärden kan ta några minuter.  
    ![Publicera portal](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> Portalen måste publiceras på nytt efter konfigurationsändringar för API Management-tjänsten, till exempel tilldela en anpassad domän, uppdatera identitetsleverantörerna, ange delegering, ange inloggnings- och produktvillkor med mera.

## <a name="visit-the-published-portal"></a>Besök den publicerade portalen

När du har publicerat portalen kan du komma åt den `https://contoso-api.developer.azure-api.net`på samma URL som administratörspanelen, till exempel . Visa den i en separat webbläsarsession (inkognito / privat surfningsläge) som en extern besökare.

## <a name="apply-the-cors-policy-on-apis"></a>Tillämpa CORS-principen för API:er

Du måste aktivera CORS (resursdelning mellan ursprung) på dina API:er så att besökarna på portalen kan testa API:erna via den inbyggda interaktiva konsolen. Mer information finns i [den här dokumentationsartikeln.](api-management-howto-developer-portal.md#cors)

## <a name="next-steps"></a>Nästa steg

Läs mer om utvecklarportalen:

- [Översikt över utvecklarportalen för Azure API Management](api-management-howto-developer-portal.md)
