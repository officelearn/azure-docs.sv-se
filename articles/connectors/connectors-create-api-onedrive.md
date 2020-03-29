---
title: Komma åt och hantera filer i Microsoft OneDrive
description: Ladda upp och hantera filer på OneDrive genom att skapa automatiserade arbetsflöden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: edfbf090c3409d583cda6fd2c9957c37be5dfb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378440"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Komma åt och hantera filer i OneDrive-anslutningsappen med hjälp av Azure Logic Apps

Genom att använda [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [OneDrive-anslutningen](/connectors/onedriveconnector/)kan du skapa automatiserade uppgifter och arbetsflöden för att hantera dina filer, inklusive ladda upp, hämta, ta bort filer med mera. Med OneDrive kan du utföra följande uppgifter:

* Skapa arbetsflödet genom att lagra filer på OneDrive eller uppdatera befintliga filer på OneDrive. 
* Använd utlösare för att starta arbetsflödet när en fil skapas eller uppdateras på OneDrive.
* Använd åtgärder för att skapa en fil, ta bort en fil med mera. När ett nytt Office 365-e-postmeddelande tas emot med en bifogad fil (en utlösare) skapar du till exempel en ny fil i OneDrive (en åtgärd).

I den här artikeln visas hur du använder OneDrive-kopplingen i en logikapp och även en lista över utlösare och åtgärder.

Mer information om Logic Apps finns i [Vad är logikappar](../logic-apps/logic-apps-overview.md) och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Anslut till OneDrive

Innan logikappen kan komma åt alla tjänster skapar du först en *anslutning* till tjänsten. En anslutning ger anslutning mellan en logikapp och en annan tjänst. Om du till exempel vill ansluta till OneDrive behöver du först en *OneDrive-anslutning*. Om du vill skapa en anslutning anger du de autentiseringsuppgifter som du normalt använder för att komma åt den tjänst som du vill ansluta till. Så, med OneDrive, ange autentiseringsuppgifterna till ditt OneDrive-konto för att skapa anslutningen.

### <a name="create-the-connection"></a>Skapa anslutningen

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Använda en utlösare

En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. Utlöser "poll" tjänsten med ett intervall och frekvens som du vill. [Läs mer om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. I Logic App Designer `onedrive` skriver du för att få en lista över utlösare:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Välj **När en fil ändras**. Om det redan finns en anslutning väljer du knappen Visa väljare för att välja en mapp.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Om du uppmanas att logga in anger du inloggningsinformationen för att skapa anslutningen. [I artikeln](connectors-create-api-onedrive.md#create-the-connection) visas stegen i artikeln.

   I det här exemplet körs logikappen när en fil i mappen du väljer uppdateras. Om du vill se resultatet av den här utlösaren lägger du till ytterligare en åtgärd som skickar ett e-postmeddelande till dig. Lägg till exempel till office 365 Outlook *Skicka en e-poståtgärd* som skickar e-post till dig när en fil uppdateras.

3. Markera knappen **Redigera** och ange **frekvens-** och **intervallvärden.** Om du till exempel vill att utlösaren ska avsöka var 15:e minut ställer du in **Frekvensen** på **Minut**och ställer in **intervallet** till **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Spara** ändringarna (det övre vänstra hörnet i verktygsfältet). Logikappen sparas och kan aktiveras automatiskt.

## <a name="use-an-action"></a>Använda en åtgärd

En åtgärd är en åtgärd som utförs av arbetsflödet som definieras i en logikapp. [Läs mer om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Välj plustecknet. Du ser flera alternativ: **Lägg till en åtgärd,** **Lägg till ett villkor**eller något av alternativen **Mer.**

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Välj **Lägg till en åtgärd**.

3. Skriv en lista `onedrive` över alla tillgängliga åtgärder i sökrutan.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. I vårt exempel väljer du **OneDrive - Skapa fil**. Om det redan finns en anslutning väljer du **mappsökvägen** för att placera filen, anger **filnamnet**och väljer önskat **filinnehåll:**  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Om du uppmanas att ange anslutningsinformationen anger du informationen för att [skapa anslutningen enligt beskrivningen](#create-the-connection) i det här avsnittet.

   I det här exemplet skapar du en ny fil i en OneDrive-mapp. Du kan använda utdata från en annan utlösare för att skapa OneDrive-filen. Lägg till exempel till Office 365 Outlook *När ett nytt e-postmeddelande anländer* utlösaren. Lägg sedan till den OneDrive *Create-filåtgärd* som använder fälten Bifogade filer och innehållstyp i en ForEach för att skapa den nya filen i OneDrive.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Spara** ändringarna (det övre vänstra hörnet i verktygsfältet). Logikappen sparas och kan aktiveras automatiskt.

## <a name="connector-specific-details"></a>Anslutningsspecifik information

Visa alla utlösare och åtgärder som definierats i skryt, och se även eventuella gränser i [anslutningsinformationen](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Nästa steg

* [Anslutningsprogram för Azure Logic Apps](apis-list.md)