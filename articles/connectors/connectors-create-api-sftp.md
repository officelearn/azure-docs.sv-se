---
title: Ansluta till SFTP-konto från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervakar, skapa, hantera, skicka och ta emot filer för en SFTP-server med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 10/11/2018
ms.openlocfilehash: 77a76aa4fbb051e5999053279798c1b0147ae8e9
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166788"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Övervaka, skapa och hantera SFTP-filer med hjälp av Azure Logic Apps

Med Azure Logic Apps och SFTP-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden som övervakar, skapa, skicka och ta emot filerna med ditt konto på en [SFTP](https://www.ssh.com/ssh/sftp/) server, tillsammans med andra åtgärder, till exempel:

* Övervaka när filer läggs till eller ändras.
* Hämta, skapa, kopiera, uppdatera, lista, och ta bort filer.
* Hämta filinnehåll och metadata.
* Extrahera Arkiv till mappar.

Du kan använda utlösare som få svar från din SFTP-server och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder i dina logic apps för att utföra uppgifter med filer på din SFTP-server. Du kan också ha andra åtgärder som använder utdata från SFTP-åtgärder. Till exempel om du regelbundet hämta filer från din SFTP-server, kan du skicka e-postmeddelande om dessa filer och sitt innehåll med hjälp av anslutningsappen Office 365 Outlook eller Outlook.com-anslutning. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> För filer som är större än 50 MB och upp till 1 GB, använda den [SFTP-SSH-anslutningen](../connectors/connectors-sftp-ssh.md). SFTP-anslutningsappen stöder bara filer som är 50 MB eller mindre om du inte använder [storlekar för att hantera stora meddelanden](../logic-apps/logic-apps-handle-large-messages.md). 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Din SFTP värd postadressen och kontonamnet autentiseringsuppgifter

   Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning och få åtkomst till ditt SFTP-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SFTP-konto. Du kommer igång med en SFTP-utlösare [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en SFTP-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-sftp"></a>Ansluta till SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. För tom logic apps i sökrutan anger du ”sftp” som filter. Välj utlösaren som du vill under listan över utlösare. 

   ELLER

   För befintliga logikappar under det sista steget där du vill lägga till en åtgärd, Välj **nytt steg**. 
   I sökrutan anger du ”sftp” som filter. 
   Välj vilken åtgärd du önska under åtgärder.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange informationen som krävs för anslutningen och välj sedan **skapa**.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="examples"></a>Exempel

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-utlösare: när en fil läggs till eller ändras

Den här utlösaren startar en logikapparbetsflöde när utlösaren identifierar när en fil läggs till eller ändras på en SFTP-server. Till exempel kan du lägga till ett villkor som kontrollerar dess innehåll och beslutar om att hämta innehållet, baserat på om innehållet uppfyller ett angivet villkor. Slutligen kan du lägga till en åtgärd som hämtar filens innehåll och placera innehållet i en mapp på SFTP-server. 

**Enterprise exempel**: du kan använda den här utlösaren för att övervaka en SFTP-mapp för nya filer som representerar kundorder. Du kan sedan använda en SFTP-åtgärd som **hämta filinnehåll**, så du kan hämta den ordning innehåll för vidare bearbetning och lagra den ordningen i en order-databas.

### <a name="sftp-action-get-content"></a>SFTP-åtgärd: hämta innehåll

Den här åtgärden hämtar innehållet från en fil på en SFTP-server. Till exempel kan du lägga till utlösaren från föregående exempel och ett villkor som måste uppfylla dess innehåll. Om villkoret är sant, köra den åtgärd som hämtar innehållet. 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/sftpconnector/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)