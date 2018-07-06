---
title: Ansluta till FTP-server – Azure Logic Apps | Microsoft Docs
description: Skapa, övervaka och hantera filer på en FTP-server med Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 07/22/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 4355a767d2ecd500662cdf4522e8a7e12de86b80
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866159"
---
# <a name="get-started-with-the-ftp-connector"></a>Kom igång med FTP-anslutningsappen
Använd FTP-anslutningsappen för att övervaka, hantera och skapa filer på en FTP-server. 

Att använda [alla anslutningar](apis-list.md), måste du först skapa en logikapp. Du kan komma igång med [nu skapa en logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-ftp"></a>Ansluta till FTP
Innan din logikapp kan komma åt alla tjänster måste du först måste du skapa en *anslutning* till tjänsten. En [anslutning](connectors-overview.md) tillhandahåller anslutningen mellan en logikapp och en annan tjänst.  

### <a name="create-a-connection-to-ftp"></a>Skapa en anslutning till FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Använda en FTP-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!IMPORTANT]
> FTP-anslutningsappen kräver en FTP-server som är tillgänglig från Internet och är konfigurerad för att fungera med PASSIVT läge. FTP-anslutningsappen är också **inte kompatibel med implicit FTPS (FTP över SSL)**. FTP-anslutningsappen stöder bara explicit FTPS (FTP över SSL).  
> 
> 

I det här exemplet jag visar hur du använder den **FTP - när en fil läggs till eller ändras** utlösa för att initiera en logikappens arbetsflöde när en fil läggs till eller ändras på en FTP-server. Du kan använda den här utlösaren för att övervaka en FTP-mapp för nya filer som representerar order från kunder i en enterprise-exemplet.  Du kan sedan använda en FTP-anslutningstjänsten åtgärd som **hämta filinnehåll** att hämta innehållet i ordningen för vidare bearbetning och lagring i din orderdatabasen.

1. Ange *ftp* i sökrutan på logic apps designer och markera den **FTP - när en fil läggs till eller ändras** utlösare   
   ![FTP-utlösarbild 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   Den **när en fil läggs till eller ändras** kontrollen öppnas  
   ![FTP-utlösarbild 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Välj den **...**  finns på höger sida av kontrollen. Då öppnas mappen väljarkontrollen  
   ![Bild 3 till FTP-utlösare](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Välj den **>** (HÖGERPIL) och bläddra till den mapp som du vill övervaka för nya eller ändrade filer. Välj mapp och Observera mappen visas nu i den **mappen** kontroll.  
   ![FTP-utlösarbild 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

Logikappen har nu konfigurerats med en utlösare som börjar en körning av andra utlösare och åtgärder i arbetsflödet när en fil ändras eller skapas i den specifika FTP-mappen. 

> [!NOTE]
> Det måste innehålla minst en utlösare och en åtgärd för en logikapp ska fungera. Följ stegen i nästa avsnitt för att lägga till en åtgärd.  
> 
> 

## <a name="use-a-ftp-action"></a>Använda en FTP-åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Läs mer om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Nu när du har lagt till en utlösare, Följ dessa steg för att lägga till en åtgärd som ska hämta innehållet i den nya eller ändrade filen som upptäckts av utlösaren.    

1. Välj **+ nytt steg** att lägga till åtgärden för att hämta innehållet i filen på FTP-servern  
2. Välj den **Lägg till en åtgärd** länk.  
   ![Bild av åtgärder för FTP-1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Ange *FTP* att söka efter alla åtgärder som rör FTP.
4. Välj **FTP - hämta filinnehåll** som åtgärden som ska vidtas när en nya eller ändrade filen finns i FTP-mappen.      
   ![Bild av åtgärder för FTP-2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   Den **hämta filinnehåll** styra öppnas. **Obs**: uppmanas du att godkänna din logikapp för att komma åt ditt konto för FTP-server om du inte har gjort det tidigare.  
   ![Bild av åtgärder för FTP-3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Välj den **filen** kontroll (det tomma utrymmet finns under ** filen ***). Här kan använda du någon av de olika egenskaperna från den nya eller ändrade fil som finns på FTP-servern.  
6. Välj den **filinnehåll** alternativet.  
   ![Bild 4 av FTP-åtgärder](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. Kontrollen har uppdaterats, vilket betyder att den **FTP - hämta filinnehåll** åtgärd får den *filinnehåll* av nya eller ändrade filen på FTP-servern.      
   ![Bild 5 av FTP-åtgärder](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Spara ditt arbete och sedan lägga till en fil i mappen FTP för att testa ditt arbetsflöde.    

Logikappen har nu konfigurerats med en utlösare för att övervaka en mapp på en FTP-server och starta arbetsflödet när den hittar en ny fil eller en fil på FTP-servern. 

Logikappen har också konfigurerats med en åtgärd för att hämta innehållet i den nya eller ändrade filen.

Du kan nu lägga till en annan åtgärd som den [SQL Server – infogningsrad](connectors-create-api-sqlazure.md) åtgärder för att infoga innehållet i den nya eller ändrade filen i en SQL-databastabell.  

## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)

