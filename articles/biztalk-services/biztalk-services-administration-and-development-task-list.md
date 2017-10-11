---
title: "Administration och utveckling aktivitetslista i BizTalk-tjänst | Microsoft Docs"
description: "Planerings- och jobbet stöd för att distribuera Azure BizTalk-tjänst."
services: biztalk-services
documentationcenter: 
author: msftman
manager: erikre
editor: 
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 7d4532daf5e4b8f45de94bbec230633978814a6e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Administration och utveckling uppgiftslista i BizTalk-tjänst

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

## <a name="getting-started"></a>Komma igång
När du arbetar med Microsoft Azure BizTalk-tjänster, finns det flera lokala och molnbaserade komponenter att tänka på. Överväg följande processflöde för att komma igång:  

| Steg | Vem är ansvarig | Aktivitet | Relaterade länkar |
| --- | --- | --- | --- |
| 1. |Administratör |Skapa Microsoft Azure-prenumeration med hjälp av ett Microsoft-konto eller ett organisationskonto |[Klassisk Azure-portal](http://go.microsoft.com/fwlink/p/?LinkID=213885) |
| 2. |Administratör |Skapa eller etablera en BizTalk Service. |[Skapa en BizTalk Service med klassiska Azure-portalen](http://go.microsoft.com/fwlink/p/?LinkID=302280) |
| 3. |Administratör |Registrerar du eller ditt företag BizTalk-distribution |[Registrera och uppdatera en BizTalk-tjänst-distribution på BizTalk-Services-portalen](https://msdn.microsoft.com/library/azure/hh689837.aspx) |
| 4. |Administratör |Gäller om programmet använder BizTalk-tjänst för nätverkskort för att ansluta till ett lokalt branschspecifika (LOB)-system eller använder en kö eller avsnittet mål.  Skapa Azure Service Bus-Namespace. Ge det här namnområdet, Service Bus Utfärdarens namn och Service Bus utfärdaren Key värden till utvecklaren. |[Så här: skapa eller ändra en Service Bus-tjänsten Namespace](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) och [hämta Utfärdarens namn och utfärdaren nyckel](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |Installera SDK och skapa BizTalk Service-projekt i Visual Studio. |[Installera Azure BizTalk Services SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx) och [skapa omfattande Messaging slutpunkter i Azure](https://msdn.microsoft.com/library/azure/hh689766.aspx) |
| 6. |Developer |Distribuera din BizTalk Service-projekt som BizTalk Service finns i Azure. |[Distribuera och uppdatera BizTalk-Services-projekt](https://msdn.microsoft.com/library/azure/hh689881.aspx) |
| 7. |Administratör |Gäller om du använder EDI.  Du kan lägga till partner och skapa avtal för Microsoft Azure BizTalk-Services-portalen. När du skapar ett avtal kan du lägga till bridge och/eller transformeringar som skapats av utvecklare avtal inställningar. |[Konfigurera EDI-, AS2- och EDIFACT på BizTalk-Services-portalen](https://msdn.microsoft.com/library/azure/hh689853.aspx) |
| 8. |Administratör |Med den klassiska Azure-portalen kan övervaka hälsotillståndet för din BizTalk Service, inklusive prestandamått. |[BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](http://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administratör |Med Microsoft Azure BizTalk-Services-portalen kan hantera artefakter som används av BizTalk-tjänst och spåra meddelanden när de bearbetas av brygga-filer. |[Med BizTalk-Services-portalen](https://msdn.microsoft.com/library/azure/dn874043.aspx) |
| 10. |Administratör |Skapa en säkerhetskopieringsplan för att säkerhetskopiera BizTalk Service. |[Kontinuitet för företag och katastrofåterställning i BizTalk-tjänst](https://msdn.microsoft.com/library/azure/dn509557.aspx) |

## <a name="next-steps"></a>Nästa steg
[Självstudiekurser och exempel](https://msdn.microsoft.com/library/azure/hh689895.aspx)

[Skapa projektet i Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)

[Installera Azure BizTalk Services SDK](https://msdn.microsoft.com/library/azure/hh689760.aspx)

## <a name="concepts"></a>Koncept
[Skapa projektet i Visual Studio](https://msdn.microsoft.com/library/azure/hh689811.aspx)  
[EDI AS2 och EDIFACT-meddelanden (Business to Business)](https://msdn.microsoft.com/library/azure/hh689898.aspx)  

## <a name="other-resources"></a>Andra resurser
[Lägg till källan och målet Bridge Messaging slutpunkter](https://msdn.microsoft.com/library/azure/hh689877.aspx)  
[Läs och skapa meddelande Maps och transformeringar](https://msdn.microsoft.com/library/azure/hh689905.aspx)  
[Med hjälp av tjänsten BizTalk-kort (BAS)](https://msdn.microsoft.com/library/azure/hh689889.aspx)  
[Azure BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=303664)

