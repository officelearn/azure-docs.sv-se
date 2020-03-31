---
title: Ansluta till Dropbox
description: Automatisera uppgifter och arbetsflöden som laddar upp och hanterar filer i Dropbox med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665759"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Ladda upp och hantera filer i Dropbox med hjälp av Azure Logic Apps

Med Dropbox-anslutningen och Azure Logic Apps kan du skapa automatiserade arbetsflöden som laddar upp och hanterar filer i ditt Dropbox-konto. 

Den här artikeln visar hur du ansluter till Dropbox från din logikapp och sedan lägger till Dropbox **När en fil skapas** utlösare och Dropbox **Get-filinnehållet med hjälp av sökvägsåtgärd.**

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [Dropbox-konto](https://www.dropbox.com/), som du kan registrera dig gratis. Dina kontouppgifter är nödvändiga för att skapa en anslutning mellan logikappen och ditt Dropbox-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). I det här exemplet behöver du en tom logikapp.

## <a name="add-trigger"></a>Lägg till utlösare

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Under sökrutan väljer du **Alla**. I sökrutan anger du "dropbox" som filter.
Välj den här utlösaren i listan utlösare: **När en fil skapas**

   ![Välj Dropbox-utlösare](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Logga in med dina Dropbox-kontouppgifter och ge åtkomst till dina Dropbox-data för Azure Logic Apps.

1. Ange nödvändig information för utlösaren. 

   I det här exemplet markerar du den mapp där du vill spåra filgenerering. Om du vill bläddra bland mapparna väljer du mappikonen **bredvid rutan Mapp.**

## <a name="add-action"></a>Lägg till åtgärd

Lägg nu till en åtgärd som hämtar innehållet från en ny fil.

1. Under utlösaren väljer du **Nästa steg**. 

1. Under sökrutan väljer du **Alla**. I sökrutan anger du "dropbox" som filter.
Välj den här åtgärden i åtgärdslistan: **Hämta filinnehåll med sökvägen**

1. Om du inte redan har auktoriserat Azure Logic Apps för åtkomst till Dropbox godkänner du åtkomst nu.

1. Om du vill bläddra till den filsökväg du vill använda väljer du knappen ellipser (**...**) bredvid rutan **Sökväg.** 

   Du kan också klicka i rutan **Sökväg** och i listan med dynamiskt innehåll välja **Sökväg**för fil – vars värde är tillgängligt som utdata från den utlösare som du lade till i föregående avsnitt.

1. När du är klar sparar du logikappen.

1. Skapa en ny fil i Dropbox om du vill utlösa logikappen.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och begränsningar, enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](/connectors/dropbox/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
