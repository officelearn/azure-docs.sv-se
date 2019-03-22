---
title: Anslut till Dropbox – Azure Logic Apps
description: 'Ladda upp och hantera filer med Dropbox REST API: er och Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 5a1bfe8ca38fc23f09b13195fb8ca5bd443a4afd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314424"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Ladda upp och hantera filer i Dropbox med hjälp av Azure Logic Apps

Du kan skapa automatiserade arbetsflöden som laddar upp och hantera filer i ditt Dropbox-konto med Dropbox-anslutningsprogrammet och Azure Logic Apps. 

Den här artikeln visar hur du ansluter till Dropbox från din logikapp och Lägg sedan till Dropbox **när en fil skapas** utlösare och Dropbox **hämta filinnehåll med hjälp av sökvägen** åtgärd.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* En [Dropbox-konto](https://www.dropbox.com/), som du kan registrera dig kostnadsfritt. Autentiseringsuppgifterna för ditt konto är nödvändiga för att skapa en anslutning mellan din logikapp och ditt Dropbox-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). I det här exemplet behöver du en tom logikapp.

## <a name="add-trigger"></a>Lägg till utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Under sökrutan väljer du **Alla**. I sökrutan anger du ”dropbox” som filter.
Välj den här utlösaren från listan över utlösare: **När en fil skapas**

   ![Välj Dropbox-utlösare](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Logga in med dina autentiseringsuppgifter för Dropbox-konto och auktorisera åtkomst till dina data i Dropbox för Azure Logic Apps.

1. Ange nödvändig information för utlösaren. 

   I det här exemplet väljer du den mapp där du vill spåra filer skapas. Om du vill bläddra mapparna, Välj mappikonen bredvid den **mappen** box.

## <a name="add-action"></a>Lägg till åtgärd

Nu ska du lägga till en åtgärd som hämtar innehållet från en ny fil.

1. Under utlösaren väljer **nästa steg**. 

1. Under sökrutan väljer du **Alla**. I sökrutan anger du ”dropbox” som filter.
Välj den här åtgärden från åtgärdslistan över: **Hämta filinnehåll med hjälp av sökvägen**

1. Om du inte har redan behörighet Azure Logic Apps för att få åtkomst till Dropbox, bevilja åtkomst nu.

1. Bläddra till sökvägen till filen som du vill använda bredvid den **filsökväg** väljer du ellipserna (**...** ) knappen. 

   Du kan också klicka på den **filsökväg** och från den dynamiska innehållslistan, väljer **filsökväg**, vars värde är tillgänglig som utdata från utlösaren som du lade till i föregående avsnitt.

1. När du är klar sparar du din logikapp.

1. Skapa en ny fil i Dropbox för att utlösa logikappen.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) fil, finns i den [anslutningsappens-referenssida](/connectors/dropbox/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
