---
title: Snabbstart om Azure – Skapa en händelsehubb med hjälp av Azure Portal | Microsoft Docs
description: I den här snabbstarten lär du dig hur du skapar en Azure-händelsehubb med hjälp av Azure Portal och hur du sedan skickar och tar emot händelser med hjälp av .NET Standard SDK.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 3a1054b0c753b3e5dca4cd66ac42e6bc8bdd16ae
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960710"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Snabbstart: Skapa en händelsehubb med Azure Portal
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här snabbstarten skapar du en händelsehubb med hjälp av [Azure-portalen](https://portal.azure.com).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten behöver du följande:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) eller senare.
- [SDK för .NET Standard](https://www.microsoft.com/net/download/windows) version 2.0 eller senare.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk samling Azure-resurser. Alla resurser distribueras och hanteras i en resursgrupp. Skapa en resursgrupp på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Resursgrupper** i det vänstra navigeringsfönstret. Klicka sedan på **Lägg till**.

   ![Resursgrupper – Lägg till, knapp](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Ange ett unikt namn för resursgruppen. Systemet kontrollerar direkt om namnet är tillgängligt i den valda Azure-prenumerationen.

3. I **Prenumeration** klickar du på namnet på den Azure-prenumeration som du vill skapa resursgruppen i.

4. Välj en geografisk plats för resursgruppen.

5. Klicka på **Skapa**.

   ![Resursgrupp – skapa](./media/event-hubs-quickstart-portal/resource-groups2.png)

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett Event Hubs-namnområde innehåller en unik omfattningscontainer som refereras till av dess fullständigt kvalificerade domännamn, där du skapar en eller flera händelsehubbar. Gör följande om du vill skapa ett namnområde i din resursgrupp med Portal:

1. I Azure-portalen klickar du på **Skapa en resurs** högst upp till vänster på skärmen.

2. Klicka på **Sakernas Internet** och sedan på **Event Hubs**.

3. Under **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.

   ![Skapa ett namnområde för en händelsehubb](./media/event-hubs-create/create-event-hub1.png)

4. När du har kontrollerat att namnområdet är tillgängligt väljer du prisnivå (Basic eller Standard). Välj även en Azure-prenumeration, resursgrupp och plats där du vill skapa resursen.
 
5. Klicka på **Skapa** för att skapa namnområdet. Du kan behöva vänta några minuter på att systemet ska bli klart med att etablera resurserna.
6. Välj **Aviseringar** och välj sedan **distributionen** med samma namn som namnet på händelsehubbens namnområde. 

   ![Resursgrupp – avisera från skapande](./media/event-hubs-quickstart-portal/create-alert.png)
6. Välj händelsehubbens namnområde i listan över resurser som skapats i distributionen. 

   ![Välja namnområde från distributionen](./media/event-hubs-quickstart-portal/deployment-namespace.png)
7. På sidan **Event Hubs-namnområde** väljer du **Policyer för delad åtkomst** och klickar sedan på **RootManageSharedAccessKey**.
    
8. Klicka på kopieringsknappen för att kopiera anslutningssträngen **RootManageSharedAccessKey** till Urklipp. Spara den här anslutningssträngen på en tillfällig plats, till exempel i Anteckningar, för senare användning.
    
## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Gör följande om du vill skapa en händelsehubb i namnområdet:

1. På sidan Event Hubs-namnområde väljer du **Event Hubs**.
   
    ![Välj Event Hubs på menyn till vänster](./media/event-hubs-quickstart-portal/create-event-hub3.png)

1. Klicka på **+ Event Hub** längst upp i fönstret.
   
    ![Lägg till Event Hub – knapp](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Skriv ett namn för din händelsehubb och klicka sedan på **Skapa**.
   
    ![Skapa händelsehubb](./media/event-hubs-quickstart-portal/create-event-hub5.png)


Grattis! Du har skapat ett Event Hubs-namnområde och en händelsehubb i namnområdet med Portal. 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en resursgrupp, en Event Hubs-namnrymd och en händelsehubb. Stegvisa instruktioner för att skicka händelser till eller ta emot händelser från en händelsehubb finns i följande självstudier:  

- **Skicka händelser till en händelsehubb**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Ta emot händelser från en händelsehubb**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
