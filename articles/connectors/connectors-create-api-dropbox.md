---
title: Anslut till Dropbox
description: Automatisera aktiviteter och arbets flöden som laddar upp och hanterar filer i Dropbox med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75665759"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Ladda upp och hantera filer i Dropbox med hjälp av Azure Logic Apps

Med Dropbox-kopplingen och Azure Logic Apps kan du skapa automatiserade arbets flöden som laddar upp och hanterar filer i ditt Dropbox-konto. 

Den här artikeln visar hur du ansluter till Dropbox från din Logic app och lägger sedan till Dropbox **när en fil skapas** och Dropbox **Hämta fil innehåll med hjälp av Sök vägs** åtgärden.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [Dropbox-konto](https://www.dropbox.com/)som du kan registrera dig kostnads fritt. Dina kontoautentiseringsuppgifter krävs för att skapa en anslutning mellan din Logic app och ditt Dropbox-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). I det här exemplet behöver du en tom Logic-app.

## <a name="add-trigger"></a>Lägg till utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Under sökrutan väljer du **Alla**. Skriv "Dropbox" som filter i rutan Sök.
Välj den här utlösaren i listan utlösare: **när en fil skapas**

   ![Välj Dropbox-utlösare](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Logga in med dina autentiseringsuppgifter för Dropbox-kontot och ge åtkomst till Dropbox-data för Azure Logic Apps.

1. Ange den information som krävs för utlösaren. 

   I det här exemplet väljer du den mapp där du vill spåra hur filen skapas. Om du vill bläddra i mapparna väljer du mappikonen bredvid rutan **mapp** .

## <a name="add-action"></a>Lägg till åtgärd

Lägg nu till en åtgärd som hämtar innehållet från en ny fil.

1. Under utlösaren väljer du **Nästa steg**. 

1. Under sökrutan väljer du **Alla**. Skriv "Dropbox" som filter i rutan Sök.
Välj den här åtgärden i listan åtgärder: **Hämta fil innehåll med hjälp av sökväg**

1. Om du inte redan har auktoriserat Azure Logic Apps för åtkomst till Dropbox ska du godkänna åtkomst nu.

1. Bläddra till den fil Sök väg som du vill använda genom att klicka på knappen med tre punkter (**...**) bredvid rutan **fil Sök väg** . 

   Du kan också klicka i rutan **fil Sök väg** och i listan med dynamiskt innehåll väljer du **fil Sök väg**, vars värde är tillgängligt som utdata från den utlösare som du lade till i föregående avsnitt.

1. När du är klar sparar du din Logic app.

1. Skapa en ny fil i Dropbox för att utlösa din Logic app.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs av kopplingens Swagger-fil, finns på [kopplingens referens sida](/connectors/dropbox/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
