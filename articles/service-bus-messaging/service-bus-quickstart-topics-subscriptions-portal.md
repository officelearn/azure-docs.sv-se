---
title: Snabbstart – Använd Azure portal om du vill skapa Service Bus-ämnen och prenumerationer | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du skapar ett Service Bus-ämne och prenumerationer på detta ämne med hjälp av Azure portal.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: 2af346b2c9fa5c46593aa9421c3a762bda78dc2f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610310"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Snabbstart: Använd Azure-portalen för att skapa ett Service Bus-ämne och prenumerationer till ämnet
I den här snabbstarten använder du Azure-portalen för att skapa ett Service Bus-ämne och sedan skapa prenumerationer på detta ämne. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Vad är Service Bus-ämnen och -prenumerationer?
Service Bus-ämnen och -prenumerationer stöder en *publicera/prenumerera*-modell för meddelandekommunikation. När du använder ämnen och prenumerationer så kommunicerar inte komponenterna i ett distribuerat program direkt med varandra. Istället så utbyter de meddelanden via ett ämne, som agerar mellanhand.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Till skillnad från Service Bus-köer, där varje meddelande bearbetas av en enskild konsument så ger ämnen och prenumerationer en en-till-många-form av kommunikation, med hjälp av ett Publicera/prenumerera-mönster. Det är möjligt att registrera flera prenumerationer för ett ämne. När ett meddelande skickas till ett ämne så görs det tillgängligt för varje prenumeration för oberoende hantering/bearbetning. En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Du kan välja att registrera filterregler för ett ämne per prenumeration, där du kan filtrera eller begränsa vilka meddelanden i ett ämne tas emot av olika ämnesprenumerationer.

Service Bus-ämnen och prenumerationer kan du skala för att bearbeta ett stort antal meddelanden över ett stort antal användare och program.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="next-steps"></a>Nästa steg
Om du vill lära dig mer om att skicka meddelanden till ett ämne och reiceve dessa meddelanden via en prenumeration, finns i följande artikel: Välj programmeringsspråket i Innehållsförteckningen. 

> [!div class="nextstepaction"]
> [Publicera och prenumerera på meddelanden](service-bus-dotnet-how-to-use-topics-subscriptions.md)
