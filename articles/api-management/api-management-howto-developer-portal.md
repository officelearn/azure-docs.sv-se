---
title: Komma åt och anpassa den nya developer-portalen – Azure API Management | Microsoft Docs
description: Lär dig hur du använder den nya developer-portalen i API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2019
ms.author: apimpm
ms.openlocfilehash: deb3fdf3069aaad4982d71806c209fe516e3c3d6
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743582"
---
# <a name="access-and-customize-the-new-developer-portal-in-azure-api-management"></a>Komma åt och anpassa nya utvecklarportalen i Azure API Management

Den här artikeln visar hur du kommer åt den nya Azure API Management developer-portalen. Vi går igenom visual redigeraren experience - lägga till och redigera innehåll – samt anpassa utseendet på webbplatsen.

![Ny API Management developer-portalen](media/api-management-howto-developer-portal/cover.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
- Importera och publicera en Azure API Management-instans. Mer information finns i [importera och publicera](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> Ny developer-portalen är för närvarande i förhandsversion.

## <a name="managed-and-self-hosted-versions"></a>Hanterade och egenhanterad versioner

Du kan skapa din developer-portalen på två sätt:

- **Hanterade version** – genom att redigera och anpassa portalen inbyggda i API Management-instans och är åtkomlig via URL: en `<your-api-management-instance-name>.developer.azure-api.net`.
- **Egenhanterad version** – genom att distribuera och lokal som är värd för din portal utanför en API Management-instans. Den här metoden kan du redigera portalens kodbas och utöka angivna grundläggande funktioner. Mer information och instruktioner finns i [GitHub-lagringsplats med källkoden för portalen][1].

## <a name="access-the-managed-version-of-the-portal"></a>Få åtkomst till den hanterade versionen av portalen

Följ stegen nedan för att få åtkomst till den hanterade versionen i portalen.

1. Gå till din API Management-tjänstinstans i Azure-portalen.
1. Klicka på den **nya developer-portalen (förhandsversion)** knappen i det övre navigeringsfältet. En ny webbläsarflik med en administrativ version av portalen öppnas. Om du försöker komma åt portalen för första gången, etableras standardinnehållet automatiskt.

## <a name="edit-and-customize-the-managed-version-of-the-portal"></a>Redigera och anpassa den hanterade versionen av portalen

Vi visar hur du redigerar innehållet i portalen, anpassa webbplatsens utseende och publicera ändringar i videon nedan.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="next-steps"></a>Nästa steg

Läs mer om den nya developer-portalen:

- [GitHub-lagringsplats med källkoden][1]
- [Information om lokal som är värd för portalen][2]
- [Offentliga översikten över projektet][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects