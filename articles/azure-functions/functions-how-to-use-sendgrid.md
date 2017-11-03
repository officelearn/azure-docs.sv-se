---
title: "Hur du använder SendGrid i Azure Functions | Microsoft Docs"
description: "Visar hur du använder SendGrid i Azure Functions"
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: rachelap
ms.openlocfilehash: 563bdac30176de131f6d8fd194713f482ceb75d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Hur du använder SendGrid i Azure Functions

## <a name="sendgrid-overview"></a>Översikt över SendGrid

Azure Functions stöder SendGrid utdata bindningar om du vill aktivera dina funktioner att skicka e-postmeddelanden med några få rader med kod och ett SendGrid-konto.

Om du vill använda SendGrid-API i en Azure-funktion, måste du ha en [SendGrid konto](http://SendGrid.com). Du måste dessutom ha en SendGrid API-nyckel. Logga in på ditt SendGrid-konto och klicka på **inställningar** sedan **API-nyckel** att generera en API-nyckel. Behåll den här nyckeln som är tillgängliga när du använder den i ett kommande steg.

Du är nu redo att skapa en app i Azure-funktion.

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app 

Azure funktionen appar är behållare för en eller flera Azure functions. Azure functions är just - funktionen. Varje Azure-funktion är kopplad till en utlösare som är en händelse som gör att funktionen ska köras.
Varje funktion kan innehålla valfritt antal indata eller utdata bindningar. Bindningar är tjänster som du kan använda i en funktion. SendGrid är en bindning för utdata som du kan använda för att skicka e-post. 

1. Logga in på Azure-portalen och [skapa ett Azure-Funktionsapp](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) eller öppna en befintlig funktionsapp. 
2. Skapa en Azure-funktion. Om du vill göra det enkelt att välja en manuell utlösare och C#. 

 ![Skapa en Azure-funktion](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Konfigurera SendGrid för användning i en app i Azure-funktion

Du måste lagra din SendGrid API-nyckel som en appinställningen för att använda den i en funktion. Fältet ApiKey är inte din faktiska SendGrid API-nyckel, men en app som du du definiera som representerar din faktiska API-nyckel. Lagra din nyckel för det här sättet är för säkerhet, eftersom det är separat från någon kod eller filer som kan kontrolleras i källkodskontroll.

- Skapa en **AppSettings** nyckeln i appen funktionen **programinställningar**.

 ![Skapa en Azure-funktion](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>Konfigurera SendGrid utdata bindningar

SendGrid är tillgänglig som en Azure funktionen utdatabindning. Om du vill skapa en SendGrid utdatabindning:

1. Gå till den **integrera** fliken för funktionen i Azure-portalen.
2. Klicka på **nya utdata** att skapa en SendGrid utdatabindning.
3. Fyll i den **API-nyckel** och **meddelandet parameternamnet** egenskaper. Om du vill kan du ange de andra egenskaperna nu eller code dem i stället. De här inställningarna kan användas som standard.

 ![Konfigurera SendGrid utdata bindningar](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

Om du lägger till en bindning till en funktion skapas en fil med namnet **function.json** i mappen för din funktion. Den här filen innehåller samma information som visas i funktionen Azure **integrera** fliken, men i Json-format. Ange den **ApiKey**, **meddelandet**, och **från** fält skapa följande poster i den **function.json** fil: 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

Om du vill kan du ändra den här filen själv direkt.

Nu när du har skapat och konfigurerat Funktionsapp och funktionen, kan du skriva kod för att skicka ett e-postmeddelande.

## <a name="write-code-that-creates-and-sends-email"></a>Skriva kod som skapar och skickar e-post

SendGrid API innehåller alla kommandon som du behöver skapa och skicka ett e-postmeddelande.  

- Ersätt Koden i funktionen med följande kod:

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

Notera den första raden innehåller den ```#r``` direktiv som refererar till SendGrid-sammansättningen. Sedan kan du använda en ```using``` instruktion för att enkelt komma åt objekt i namnutrymmet. I koden skapar instanser av ```Mail```, ```Personalization```, och ```Content``` objekt från SendGrid-API som ska utgöra den e-postadressen. När du returnerar meddelandet ger SendGrid den. 

Funktionens signaturen innehåller också en extra out-parameter av typen ```Mail``` med namnet ```message```. Både indata och utdata bindningar express sig själva som funktionsparametrar i kod. 

2. Testa din kod genom att klicka på **Test** och ange ett meddelande till den **Begärandetext** fältet, klicka på **kör** knappen.

 ![Testa din kod](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. Kontrollera e-post för att kontrollera att SendGrid har skickat e-postmeddelandet. Det ska gå till adressen i koden från steg 1 och innehålla meddelandet från den **Begärandetext**.

## <a name="next-steps"></a>Nästa steg
Den här artikeln har du lärt dig att använda SendGrid-tjänsten för att skapa och skicka e-post. Mer information om hur du använder Azure Functions i dina appar finns i följande avsnitt: 

- [Metodtips för Azure Functions](functions-best-practices.md) listar några rekommendationer att använda när du skapar Azure Functions.

- [Azure Functions för utvecklare](functions-reference.md) programmerare om att koda funktioner och definiera utlösare och bindningar.

- [Testa Azure Functions](functions-test-a-function.md) beskriver olika verktyg och tekniker för att testa funktioner.