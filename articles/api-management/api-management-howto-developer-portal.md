---
title: Komma åt och anpassa den nya Developer-portalen – Azure API Management | Microsoft Docs
description: Lär dig hur du använder den nya Developer-portalen i API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: c015b1afbc61e1501e656aaa480ee2a4e19ba094
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672795"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Få åtkomst till och anpassa den nya Developer-portalen i Azure API Management

Den här artikeln visar hur du kommer åt den nya Azure API Management Developer-portalen. Den vägleder dig genom den visuella redigerings upplevelsen – lägga till och redigera innehåll – och anpassa webbplatsens utseende.

![Nya API Management Developer-portalen](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a> Krav

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
- Importera och publicera en Azure API Management-instans. Mer information finns i [import och publicering](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Den nya Developer-portalen är för närvarande en för hands version.

## <a name="managed-vs-self-hosted"></a>Hanterade och egen värdbaserade versioner

Du kan bygga din utvecklings Portal på två sätt:

- **Hanterad version** – genom att redigera och anpassa portalen, som är inbyggd i API Management-instansen och kan nås via URL: en `<your-api-management-instance-name>.developer.azure-api.net`.
- **Egen värd version** – genom att distribuera och självbetjäning av portalen utanför en API Management-instans. Med den här metoden kan du redigera portalens kodbas och utöka de angivna kärn funktionerna. Mer information och anvisningar finns i GitHub- [lagringsplatsen med käll koden för portalen][1].

## <a name="managed-access"></a>Få åtkomst till den hanterade versionen av portalen

Följ stegen nedan för att få åtkomst till den hanterade versionen av portalen.

1. Gå till din API Management tjänst instans i Azure Portal.
1. Klicka på knappen **ny utvecklings portal (förhands granskning)** i det övre navigerings fältet. En ny flik i webbläsaren med en administrativ version av portalen öppnas. Om du ansluter till portalen för första gången kommer standard innehållet att tillhandahållas automatiskt.

## <a name="managed-tutorial"></a>Redigera och anpassa den hanterade versionen av portalen

I videon nedan visar vi hur du redigerar innehållet i portalen, anpassar webbplatsens utseende och publicerar ändringarna.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="faq"></a>Vanliga frågor och svar

I det här avsnittet besvarar vi vanliga frågor om den nya Developer-portalen, som är av allmän natur. För frågor som är speciella för den självbetjänings versionen, se [wiki-avsnittet i GitHub-lagringsplatsen](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-content-from-the-old-developer-portal-to-the-new-one"></a>Hur kan jag Migrera innehåll från den gamla Developer-portalen till den nya?

Du kan inte. Portalerna är inkompatibla.

### <a name="when-will-the-portal-become-generally-available"></a>När blir portalen allmänt tillgänglig?

Portalen är för närvarande en för hands version och blir allmänt tillgänglig i slutet av kalender året (2019). För hands versionen bör inte användas i produktions syfte.

### <a name="will-the-old-portal-be-deprecated"></a>Kommer den gamla portalen att bli föråldrad?

Ja, det är inaktuellt när den nya är allmänt tillgänglig. Om du har problem kan du höja dem [i ett dedikerat GitHub-problem](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Har den nya portalen alla funktioner i den gamla portalen?

Målet för allmän tillgänglighet är att tillhandahålla en scenario-baserad funktions paritet med den gamla portalen. Fram till dess kan för hands versions versionen inte ha de valda funktionerna implementerade.

Undantag är *program* och *problem* från den gamla portalen, som inte är tillgängliga i den nya portalen. Om du använder *problem* i den gamla portalen och behöver dem i den nya, så publicera en kommentar i [ett dedikerat GitHub-problem](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="ive-found-bugs-andor-id-like-to-request-a-feature"></a>Jag har hittat buggar och/eller jag vill begära en funktion.

Bra! Du kan ge oss feedback, skicka in en funktions förfrågan eller en fel rapport via [GitHub-databasens problem](https://github.com/Azure/api-management-developer-portal/issues). Även om du är där kommer vi också att uppskatta din feedback om problemen som har marker ATS med etiketten `community`.

### <a name="i-want-to-move-the-content-of-the-new-portal-between-environments-how-can-i-do-that-and-do-i-need-to-go-with-the-self-hosted-version"></a>Jag vill flytta innehållet i den nya portalen mellan miljöer. Hur kan jag göra det och jag måste använda den själv värdbaserade versionen?

Du kan göra det i både hanterade Portal versioner och egen värd. Den nya Developer-portalen stöder extrahering av innehåll via hanterings-API: et för din API Management-tjänst. API: erna dokumenteras [i wiki-avsnittet i GitHub-lagringsplatsen](https://github.com/Azure/api-management-developer-portal/wiki/). Vi har också skrivit [ett skript](https://github.com/Azure/api-management-developer-portal/blob/master/scripts/migrate.bat)som kan hjälpa dig att komma igång.

Vi arbetar fortfarande med att justera den här processen med API Management DevOps Resource Kit.

### <a name="what-do-i-need-to-configure-for-the-new-portal-to-work-in-my-api-management-service-in-vnet"></a>Vad måste jag konfigurera för att den nya portalen ska fungera i API Management tjänsten i VNET?

Även om den nya Developer-portalen är i för hands version måste du tillåta anslutning till Azure Storage-tjänster i regionen Västra USA för att göra den hanterade portalen att fungera i en API Management tjänst i VNET. Mer information finns i [lagrings dokumentationen](../storage/common/storage-network-security.md#available-virtual-network-regions).

Ovanstående konfiguration kommer inte längre att vara nödvändig när den nya portalen blir allmänt tillgänglig.

Den själv värdbaserade versionen av portalen kan kräva ytterligare anslutnings konfiguration, beroende på din konfiguration.

### <a name="how-can-i-select-a-layout-when-creating-a-new-page"></a>Hur kan jag välja en *layout* när jag skapar en ny *sida*?

En *layout* tillämpas på en sida genom att matcha dess URL-mall till *sidans* URL. Till exempel används *layout* med en URL-mall för `/wiki/*` på varje *sida* med `/wiki/`-segmentet: `/wiki/getting-started`, `/wiki/styles` och så vidare.

### <a name="why-doesnt-the-interactive-developer-console-work"></a>Varför fungerar inte den interaktiva Developer-konsolen?

Den är förmodligen relaterad till CORS. Den interaktiva konsolen gör en API-begäran på klient sidan från webbläsaren. Du kan lösa CORS-problemet genom att lägga till [en CORS-princip](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) på dina API: er. Du kan antingen ange alla parametrar manuellt (till exempel ursprung som https://contoso.com) eller använda ett jokertecken `*` -värde.

## <a name="next-steps"></a>Nästa steg

Läs mer om den nya Developer-portalen:

- [GitHub-lagringsplats med käll koden][1]
- [Anvisningar om hur du hanterar portalen][2]
- [Offentlig översikt över projektet][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects