---
title: Azure SaaS-erbjudande storefront programkonfiguration | Azure Marketplace
description: Konfigurera butik för erbjudande för SaaS-program på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 1c6f2553bdff2aed0722bbb37e851b00a16b59d5
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941747"
---
# <a name="saas-application-storefront-details-tab"></a>Fliken för SaaS-program Storefront-information

Den här artikeln visar hur du använder fliken Storefront information att beskriva din SaaS-app och ge marknadsföring tillgångar. Den här fliken innehåller följande sätt: Översikt över, marknadsföring artefakter, Lead hantering och juridiska krav. 


## <a name="overview"></a>Översikt

Översikt över formuläret har obligatoriska och valfria fält som visas i nästa skärmdump. En asterisk (*) läggs till fältnamnet anger att det krävs.

![Storefront översiktsformulär](./media/saas-storefront-overview.png)

I följande tabell beskriver vad butik som du kan ange för erbjudandet. Required fields are indicted by an asterisk (*).

|     Fält               |      Beskrivning       |
|     ------              |      -----------       |
|  **Sammanfattning av erbjudandet\***    | Sammanfattning av ditt erbjudande förslagsvärde. Den visas på söksidan för ditt erbjudande. Det bör vara högst 100 tecken.  |
| **Beskrivning av erbjudande**  | Beskrivningen som ska visas på detaljsidan för ditt program. Högsta tillåtna antalet är 1300 tecken. Du kan använda grundläggande HTML-taggar för att formatera innehållet. Till exempel &lt;p&gt;, &lt;h1&gt;, &lt;h2&gt;, och &lt;li&gt;. Om du vill se hur den formaterade beskrivningen visas, använda ett online i realtid HTML-verktyg som https://htmledit.squarefree.com    |
|  **Branscher**   | Välj branscher som ditt erbjudande är bäst anpassad efter. Om din app är kopplad till flera olika branscher, kan du välja högst två.   |
| **Föreslås kategorier (Max 3)\*** | Välj kategorier som ditt erbjudande är bäst anpassad efter. Du kan välja högst tre kategorier. |
| **Appversion**         | Ange det lägre versionsnumret för ditt program. |
| **Sökord (Max 3)** | Ange upp till tre sökord som kunder kan använda för att hitta dina program på Marketplace storefront-webbplatsen.  |
|   |   |


## <a name="marketing-artifacts"></a>Marknadsföring artefakter

Använd den **marknadsföring artefakter** formuläret för att identifiera Azure Marketplace marknadsföring tillgångar som till exempel logotyper, videor, skärmbilder och dokument.

![Storefront marknadsföring artefakter formulär](./media/saas-storefront-artifacts.png)

I följande tabell beskrivs fälten för marknadsföring artefakter.  Required fields are indicted by an asterisk (*).

|       Fält       |            Beskrivning            |
|       ------      |            -----------            |
|    **Logotyper**      |  Om det här är en sälj via Microsoft SaaS-app, bör du ge alla bilder med logotyper. Om det här är en lista, krävs endast 2 logotyper. Använd följande riktlinjer för logotyper laddades upp i partnerportalen i molnet:<br><ul><li>Håll nere antalet primära och sekundära färger på din logotyp. Azure-designen har en enkel färgpalett. </li><li>Undvik att använda svart eller vitt som bakgrundsfärgen för din logotyp. Temafärger i Azure-portalen är svart och vit. Använd istället en färg som gör din logotyp framstående i Azure Portal. Vi rekommenderar enkla primärfärger. Om du använder en genomskinlig bakgrund, se till att logotypen och texten inte är svart, vit eller blå. </li><li>Använd inte en toning bakgrund på logotypen. </li><li>Undvik att placera text, även företaget eller varumärke namn på logotypen. Utseendet och känslan av din logotyp måste vara ”fast” och Undvik toningar.</li><li>Med logobilden bör inte vara har sträckts ut.</li></ul>    |
|   **Videoklipp**       | Kan du lägga till länkar för videor i ditt erbjudande. Du kan använda länkar till YouTube eller Vimeo videor som visas tillsammans med ditt erbjudande till kunder. Du måste också ange en miniatyrbild av videon med en png-bild av 1 280 x 720 bildpunkter. Du kan ha högst fyra videor per erbjudandet. |
|  **Dokument**     | Kan du lägga till marknadsföring dokument till ditt erbjudande. Alla dokument måste vara i PDF-format och du kan ha högst tre dokument per erbjudandet.   |
|  **Skärmbilder**   | Kan du lägga till skärmdumpar av ditt erbjudande. Det finns högst fem skärmbilder som kan läggas till per erbjudandet. Maximal avbildningens storlek är 1 280 x 720 bildpunkter.  |
|  **Användbara länkar**  | Kan du lägga till externa URL: er för ditt erbjudande för att peka på Arkitekturdiagram eller andra webbplatser som en kund vill se. |
|  |  |


### <a name="marketing-examples"></a>Exempel för marknadsföring

Nästa skärmdump visar ett exempel på ett Marketplace-sökresultat.

![Marketplace-sökresultat](./media/saas-marketplace-search-result.png)

Följande bild visar hur erbjudandet visas i Marketplace efter en kund klickar på erbjudandets panel i sökresultatet.

![Information om Marketplace search-resultat](./media/saas-marketplace-search-result-details.png)


## <a name="lead-management"></a>Lead-hantering

Om du vill konfigurera lead-hantering, Välj den **Lead mål** från den nedrullningsbara listan. Nästa skärmdump visar tillgängliga mål.

![Storefront information om lead-hantering](./media/saas-storefront-lead-destination.png)

>[!TIP] 
>Välj informationsikonen ska kunna se meddelandet: ”Välj systemet där dina leads ska lagras. Lär dig hur du ansluter till CRM-systemet [här](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) ”.


## <a name="legal"></a>Juridisk information

Använda juridiska formuläret för att tillhandahålla juridisk dokumentation som krävs för varje erbjudande.

![Storefront juridiska i formuläret](./media/saas-storefront-lead-legal.png)

Ange följande information:

- **Sekretesswebbadress för principen\***  – ange en länk till din Apps sekretesspolicy.
- **Användningsvillkor\***  – ange villkor för användning av din app. Kunder måste acceptera villkoren innan de kan testa din app.


## <a name="next-steps"></a>Nästa steg

[Fliken Kontakter](./cpp-contacts-tab.md)
