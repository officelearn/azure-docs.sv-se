---
title: Azure Functions SendGrid bindningar | Microsoft Docs
description: Azure Functions SendGrid bindningar referens
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: rachelap
ms.openlocfilehash: 4cdafbe05e29d8b483c6b0e1daf41a36583d7b5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid bindningar

Den här artikeln beskriver hur du konfigurerar och arbetar med SendGrid-bindningar i Azure Functions. Med SendGrid, kan du använda Azure Functions skicka anpassade e-post via programmering.

Den här artikeln är referensinformation för Azure Functions-utvecklare. Om du har använt Azure Functions, börja med följande resurser:

[Skapa din första Azure-funktion](functions-create-first-azure-function.md). 
[C#](functions-reference-csharp.md), [F #](functions-reference-fsharp.md), eller [nod](functions-reference-node.md) developer referenser.

## <a name="functionjson-for-sendgrid-bindings"></a>Function.JSON för SendGrid bindningar

Azure Functions innehåller en output-bindning för SendGrid. SendGrid utdata bindning kan du skapa och skicka e-post via programmering. 

SendGrid bindningen stöder följande egenskaper:

|Egenskap  |Beskrivning  |
|---------|---------|
|**Namn**| Obligatoriskt - variabelnamnet som används i Funktionskoden för begäran eller begärandetexten. Det här värdet är ```$return``` när det är endast ett returvärde. |
|**typ**| Krävs – måste vara inställd på `sendGrid`.|
|**riktning**| Krävs – måste vara inställd på `out`.|
|**apiKey**| Krävs – måste anges till namnet på din API-nyckel som lagras i appen funktionen app-inställningar. |
|**att**| mottagarens e-postadress. |
|**från**| avsändarens e-postadress. |
|**ämne**| ämnet för e-postmeddelandet. |
|**text**| e-postinnehåll. |

Exempel på **function.json**:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

> [!NOTE]
> Azure Functions lagrar dina anslutningsinformationen och API-nycklar som app-inställningar så att de inte kontrolleras i din källkontroll. Den här åtgärden skyddar känslig information.
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>C#-exempel på SendGrid utdatabindning

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

## <a name="node-example-of-the-sendgrid-output-binding"></a>Noden exempel på SendGrid utdatabindning

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>Nästa steg
Information om andra bindningar och utlösare för Azure Functions finns i 
- [Azure Functions-utlösare och bindningar för utvecklare](functions-triggers-bindings.md)

- [Metodtips för Azure Functions](functions-best-practices.md) listar några rekommendationer att använda när du skapar Azure Functions.

- [Azure Functions för utvecklare](functions-reference.md) programmerare om att koda funktioner och definiera utlösare och bindningar.
