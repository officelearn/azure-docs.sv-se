---
title: Översikt över Azure API Management Developer-portalen – Azure API Management | Microsoft Docs
description: Lär dig mer om Developer-portalen i API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 6bf8c8690977ef1036c853d8c1c01a3a366b50df
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011485"
---
# <a name="azure-api-management-developer-portal-overview"></a>Översikt över Azure API Management Developer-portalen

Developer Portal är en automatiskt genererad, helt anpassningsbar webbplats med dokumentationen för dina API: er. Det är där API-konsumenter kan identifiera dina API: er, lära sig hur de används, begära åtkomst och testa dem.

I den här artikeln beskrivs skillnaderna mellan egna värdbaserade och hanterade versioner av Developer-portalen i API Management. Den förklarar också dess arkitektur och ger svar på vanliga frågor.

> [!WARNING]
> Den nya Developer-portalen är för närvarande distribuerad till API Management tjänster.
> Om din tjänst har skapats nyligen eller är en tjänst för utvecklare, bör du redan ha den senaste versionen. Annars kan det uppstå problem (till exempel med publicerings funktionen). Funktions distributionen förväntas slutföras senast den 22 november 2019.
>
> [Lär dig hur du migrerar från för hands versionen till den allmänt tillgängliga versionen](#preview-to-ga) av Developer-portalen.

![API Management Developer-portalen](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Hanterade och egen värdbaserade versioner

Du kan bygga din utvecklings Portal på två sätt:

- **Hanterad version** – genom att redigera och anpassa portalen, som är inbyggd i API Management-instansen och är tillgänglig via URL: en `<your-api-management-instance-name>.developer.azure-api.net`. Läs [den här dokumentations artikeln](api-management-howto-developer-portal-customize.md) för att lära dig hur du får åtkomst till och anpassar den hanterade portalen.
- **Egen värd version** – genom att distribuera och självbetjäning av portalen utanför en API Management-instans. Med den här metoden kan du redigera portalens kodbas och utöka de angivna kärn funktionerna. Du måste också uppgradera portalen till den senaste versionen själv. Mer information och anvisningar finns i GitHub- [lagringsplatsen med käll koden för portalen][1]. [Självstudien för den hanterade versionen](api-management-howto-developer-portal-customize.md) går igenom portalens administrations panel, som också finns i den själv värdbaserade versionen.

## <a name="portal-architectural-concepts"></a>Arkitektur koncept för portalen

Portal komponenterna kan delas upp logiskt i två kategorier: *kod* och *innehåll*.

*Koden* underhålls i [GitHub-lagringsplatsen][1] och inkluderar:

- Widgetar – som representerar visuella element och kombinerar HTML, Java Script, format funktioner, inställningar och innehålls mappning. Exempel är en bild, ett text stycke, ett formulär, en lista över API: er osv.
- Format definitioner – som anger hur widgetar kan formateras
- Motor – som genererar statiska webb sidor från Portal innehåll och som är skrivet i Java Script
- Visuell redigerare – som möjliggör anpassning och redigering i webbläsare

*Innehållet* är uppdelat i två under Kategorier: *portal innehåll* och *API Management innehåll*.

*Portal innehållet* är särskilt för portalen och innehåller:

- Sidor – till exempel landnings sida, API-självstudier, blogg inlägg
- Media – bilder, animationer och annat filbaserat innehåll
- Layout – mallar som matchas mot en URL och definierar hur sidor visas
- Stilar – värden för format definitioner, t. ex. teckensnitt, färger, kant linjer
- Inställningar – konfiguration, t. ex. favicon, webbplatsens metadata

*Portal innehåll*, förutom media, uttrycks som JSON-dokument.

*API Management innehåll* innehåller entiteter som API: er, åtgärder, produkter, prenumerationer.

Portalen baseras på en anpassad förgrening av [Paperbits-ramverket](https://paperbits.io/). De ursprungliga Paperbits-funktionerna har utökats för att tillhandahålla API Management-/regionsspecifika widgetar (t. ex. en lista över API: er, en lista över produkter) och en koppling till API Management tjänsten för att spara och hämta innehåll.

## <a name="faq"></a>Vanliga frågor och svar

I det här avsnittet svarar vi på vanliga frågor om den nya Developer-portalen, som är av allmän natur. För frågor som är speciella för den självbetjänings versionen, se [wiki-avsnittet i GitHub-lagringsplatsen](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> hur kan jag migrera från för hands versionen av portalen?

Med hjälp av för hands versionen av Developer-portalen etablerade du att förhands gransknings innehållet i API Managements tjänsten. Standard innehållet har ändrats avsevärt i den allmänt tillgängliga versionen för bättre användar upplevelse. Den innehåller också nya widgetar.

Om du använder den hanterade versionen återställer du portalens innehåll genom att klicka på **Återställ innehåll** i menyn **åtgärder** . Om du bekräftar åtgärden tas allt innehåll i portalen bort och det nya standard innehållet etableras. Portalens motor har uppdaterats automatiskt i din API Management-tjänst.

![Återställ Portal innehåll](media/api-management-howto-developer-portal/reset-content.png)

Om du använder den själv värdbaserade versionen använder du `scripts/cleanup.bat` och `scripts/generate.bat` från GitHub-lagringsplatsen för att ta bort befintligt innehåll och etablera nytt innehåll. Se till att du uppgraderar din portal kod till den senaste versionen från GitHub-databasen i förväg.

Om du inte vill återställa portalens innehåll kan du överväga att använda nyligen tillgängliga widgetar på alla sidor. Befintliga widgetar har uppdaterats automatiskt till de senaste versionerna.

Om portalen etablerades efter det allmänna tillgänglighets meddelandet bör den redan ha det nya standard innehållet. Ingen åtgärd krävs från din sida.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Hur kan jag migrera från den gamla Developer-portalen till den nya Developer-portalen?

Portalerna är inkompatibla och du måste migrera innehållet manuellt.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Har den nya portalen alla funktioner i den gamla portalen?

Den nya Developer-portalen stöder inte *program* och *problem*. Om du har använt *problem* i den gamla portalen och behöver dem i den nya, så publicera en kommentar i [ett dedikerat GitHub-problem](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="has-the-old-portal-been-deprecated"></a>Har den gamla portalen föråldrats?

De gamla portarna för utvecklare och utgivare är nu *äldre* funktioner – de kommer endast att ta emot säkerhets uppdateringar. Nya funktioner kommer endast att implementeras i den nya Developer-portalen.

Utfasningen av äldre portaler kommer att meddelas separat. Om du har frågor, rör eller kommentarer kan du höja dem [i ett dedikerat GitHub-problem](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="how-can-i-automate-portal-deployments"></a>Hur kan jag automatisera Portal distributioner?

Du kan program mässigt komma åt och hantera Developer portalens innehåll via REST API, oavsett om du använder en hanterad eller en egen version.

API: et finns dokumenterat i [GitHub-lagringsplatsens wiki-avsnitt][2]. Det kan också användas för att automatisera migrering av Portal innehåll mellan miljöer, till exempel från en test miljö till produktions miljön. Du kan lära dig mer om den här processen [i den här dokumentations artikeln](https://aka.ms/apimdocs/migrateportal) på GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Stöder portalen Azure Resource Manager mallar och/eller är den kompatibel med API Management DevOps Resource Kit?

Nej.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Måste jag aktivera ytterligare VNET-anslutning för de hanterade portalernas beroenden?

Nej.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>Jag får ett CORS-fel när jag använder den interaktiva konsolen. Vad ska jag göra?

Den interaktiva konsolen gör en API-begäran på klient sidan från webbläsaren. Du kan lösa CORS-problemet genom att lägga till [en CORS-princip](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) på dina API: er. Du kan ange alla parametrar manuellt eller använda jokertecken `*` värden. Exempel:

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

## <a name="next-steps"></a>Nästa steg

Läs mer om den nya Developer-portalen:

- [Komma åt och anpassa Managed Developer-portalen](api-management-howto-developer-portal-customize.md)
- [Konfigurera en egen värd version av portalen][2]

Bläddra bland andra resurser:

- [GitHub-lagringsplats med käll koden][1]
- [Offentlig översikt över projektet][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects