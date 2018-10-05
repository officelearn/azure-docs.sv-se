---
title: Azure-tabell | Microsoft Docs
description: Konfigurera lead-hantering för Azure Table.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811794"
---
<a name="lead-management-instructions-for-azure-table"></a>Leda Management anvisningar om hur Azure-tabell
============================================

Den här artikeln beskriver hur du konfigurerar Azure-tabell för att lagra säljleads. Azure-tabell kan du lagra och anpassa kundinformation.

## <a name="to-configure-azure-table"></a>Konfigurera Azure-tabell

1.  Om du inte har ett Azure-konto, kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

2.  När ditt Azure-konto är aktiv, logga in i den [Azure-portalen](https://portal.azure.com).
3.  Skapa ett lagringskonto i Azure-portalen. Nästa skärmdump visar hur du skapar ett lagringskonto. Mer information om priser för storage finns i [priser för storage](https://azure.microsoft.com/pricing/details/storage/).

    ![Steg för att skapa ett Azure storage-konto](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Kopiera anslutningssträngen för lagringskonto för nyckeln och klistra in den i den **Lagringskontots anslutningssträng** på partnerportalen i molnet. Ett exempel på en anslutning förekomster av textsträngen är `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Azure-lagringsnyckel](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Du kan använda [Azure Lagringsutforskaren](http://azurestorageexplorer.codeplex.com/) eller något annat verktyg för att se data i ditt storage-tabell. Du kan också exportera data i Azure Table.
data.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(Valfritt)**  Du använder Azure Functions med en Azure-tabell

Om du vill anpassa hur du får leads, Använd [Azure Functions](https://azure.microsoft.com/services/functions/) med en Azure-tabell. Azure Functions-tjänsten kan du automatisera processen för lead-generering.

Följande steg visar hur du skapar en Azure-funktion som använder en timer. Var femte minut funktionen söker i Azure-tabell för nya poster och använder sedan SendGrid-tjänsten för att skicka ett e-postmeddelande.


1.  [Skapa](https://portal.azure.com/#create/SendGrid.SendGrid) ett ledigt SendGrid-tjänstkonto i Azure-prenumerationen.

    ![Skapa SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  Skapa en SendGrid API-nyckel 
    - Välj **hantera** att gå till SendGrid UI
    - Välj **inställningar**, **API-nycklar**, och sedan skapa en nyckel med skicka e -\> fullständig åtkomst
    - Spara API-nyckel


    ![SendGrid API-nyckel](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  [Skapa](https://portal.azure.com/#create/Microsoft.FunctionApp) en funktionsapp med hjälp av alternativet som är värd för Plan med namnet ”Standardförbrukningsplanen”.

    ![Skapa Azure Functions-App](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  Skapa en ny funktionsdefinition.

    ![Skapa en Definition för Azure-funktion](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  För att få funktionen för att skicka en uppdatering på en viss tid, Välj den **TimerTrigger-CSharp** som alternativet starter.

     ![Azure Function tid utlösaralternativ](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  Ersätt ”utveckla” koden med följande kodexempel. Redigera e-postadresser med adresser som du vill använda för avsändaren och mottagaren.

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Azure Function-kodfragment](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  Välj **integrera** och **indata** Azure Table-anslutningen ska definieras.

    ![Integrera Azure-funktion](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  Ange tabellnamnet och ange anslutningssträngen genom att välja **nya**.


    ![Azure Function-tabellanslutning](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  Nu definierar utdata som SendGrid och behåller du standardvärdena.

    ![SendGrid-utdata](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![Standardinställningar för SendGrid-utdata](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Lägg till SendGrid API-nyckel till Funktionsappinställningarna med namnet ”SendGridApiKey” och värdet som hämtas från API-nycklar i SendGrid UI

    ![Hantera SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid hantera nyckel](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

När du har konfigurerat funktionen koden i avsnittet integrera bör se ut som i följande exempel.

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. Det sista steget är att gå till utveckla-Gränssnittet för funktionen och välj **kör** att starta timern. Nu ska du få ett meddelande varje gång ett nytt lead kommer in.
