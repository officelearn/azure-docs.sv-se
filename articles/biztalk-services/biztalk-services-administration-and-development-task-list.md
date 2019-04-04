---
title: Administration och utveckling uppgiftslista i BizTalk Services | Microsoft Docs
description: Planerings- och jobbet stöd för att distribuera Azure BizTalk Services.
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 6b8e0fea73dc24f7e680482be1f06fba6d702804
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916248"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administration och utveckling uppgiftslista i BizTalk Services

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Komma igång
När du arbetar med Microsoft Azure BizTalk Services, finns det flera lokala och molnbaserade komponenter för att tänka på. Överväg följande processflöde för att komma igång:  

| Steg | Vem är ansvarig | Aktivitet | Relaterade länkar |
| --- | --- | --- | --- |
| 1. |Administratör |Skapa Microsoft Azure-prenumeration med hjälp av ett Microsoft-konto eller ett organisationskonto |[Azure Portal](https://portal.azure.com) |
| 2. |Administratör |Skapar eller etablerar en BizTalk Service. |[Skapa en BizTalk-tjänst](/previous-versions/azure/reference/dn232347(v=azure.100)) |
| 3. |Administratör |Registrera dig eller ditt företag BizTalk Services-distribution |[Registrera och uppdatera en BizTalk-tjänst-distribution på BizTalk Services-portalen](/previous-versions/azure/hh689837(v=azure.100)) |
| 4. |Administratör |Gäller om programmet använder BizTalk Adapter-tjänsten för att ansluta till ett lokalt Line-of-Business (LOB)-system eller använder en kö eller ämne mål.  Skapa Azure Service Bus Namespace. Ge det här namnområdet, Service Bus-Utfärdarens namn och nyckel Service Bus-värden till utvecklaren. |[Anvisningar: Skapa eller ändra en Service Bus-tjänsten Namespace](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) och [hämta Utfärdarens namn och nyckel värden](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |Installera SDK och skapa BizTalk Service-projekt i Visual Studio. |[Installera Azure BizTalk Services SDK](/previous-versions/azure/hh689760(v=azure.100)) och [skapa omfattande Messaging slutpunkter på Azure](/previous-versions/azure/hh689766(v=azure.100)) |
| 6. |Developer |Distribuera din BizTalk Service-projektet för att din BizTalk Service finns i Azure. |[Distribuera och uppdatera BizTalk Services-projekt](/previous-versions/azure/hh689881(v=azure.100)) |
| 7. |Administratör |Gäller om du använder EDI.  Du kan lägga till partner och skapa avtal för Microsoft Azure BizTalk Services-portalen. När du skapar ett avtal kan du lägga till brygga och/eller transformeringar som skapats av utvecklare avtal-inställningar. |[Konfigurera EDI, AS2 och EDIFACT på BizTalk Services-portalen](/previous-versions/azure/hh689853(v=azure.100)) |
| 8. |Administratör |Med hjälp av [REST](/previous-versions/azure/reference/dn232347(v=azure.100)), övervaka hälsotillståndet för din BizTalk Service, inklusive prestandamått. |[BizTalk Services: Flikarna instrumentpanel, Övervakare och skalning](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administratör |Med Microsoft Azure BizTalk Services-portalen kan hantera de artefakter som används av BizTalk Services och spåra meddelanden när de bearbetas av bridge-filer. |[Använda BizTalk Services-portalen](/previous-versions/azure/dn874043(v=azure.100)) |
| 10. |Administratör |Skapa en plan för säkerhetskopiering för säkerhetskopiering av BizTalk Service. |[Affärskontinuitet och Haveriberedskap i BizTalk Services](/previous-versions/azure/dn509557(v=azure.100)) |

## <a name="next-steps"></a>Nästa steg
[Självstudier och exempel](/previous-versions/azure/hh689895(v=azure.100))

[Skapa projektet i Visual Studio](/previous-versions/azure/hh689811(v=azure.100))

[Installera SDK för Azure BizTalk Services](/previous-versions/azure/hh689760(v=azure.100))

## <a name="concepts"></a>Begrepp
[Skapa projektet i Visual Studio](/previous-versions/azure/hh689811(v=azure.100))  
[EDI, AS2 och EDIFACT-meddelanden (Business to Business)](/previous-versions/azure/hh689898(v=azure.100))  

## <a name="other-resources"></a>Andra resurser
[Lägg till källa, mål och brygga slutpunkter för meddelanden](/previous-versions/azure/hh689877(v=azure.100))  
[Lär dig och skapa meddelande kartor och transformeringar](/previous-versions/azure/hh689905(v=azure.100))  
[Med BizTalk Adapter-tjänsten (BAS)](/previous-versions/azure/hh689889(v=azure.100))  
[Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)

