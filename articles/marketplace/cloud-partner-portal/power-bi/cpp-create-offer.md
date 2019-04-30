---
title: Skapa ett erbjudande med Power BI-appen – Azure Marketplace | Microsoft Docs
description: Så här skapar du ett erbjudande med Power BI-appen för Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: 6a4f7daa337618278c3652fad3053c20557a9e28
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62102058"
---
# <a name="create-a-power-bi-app-offer"></a>Skapa ett erbjudande med Power BI-appen

Den här artikeln beskriver vad du kan följa för att skapa ett nytt erbjudande för Power BI-appen för [AppSource](https://appsource.microsoft.com). Varje erbjudande visas som en egen enhet i AppSource. När du skapar ett nytt erbjudande i den [Cloud Partner Portal](https://cloudpartner.azure.com/), så måste du ange fyra grupper av tillgångar för ditt erbjudande.

Tillgångsgrupper beskrivs i följande tabell:

|   Grupp      | Beskrivning                                                                         |
| ----------------   | ----------------                                                                    |
| Erbjudandeinställningar     | Primär identifiering och namn på erbjudandet.                                      |
| Teknisk information     | Installationsprogrammet URL som används för att installera appen på klientens Power BI-arbetsyta. Läs mer om hur du skapar denna URL [Power BI-appen dokumentation](https://go.microsoft.com/fwlink/?linkid=2028636). |
| Storefront information | Innehåller material för marknadsföring, juridiska och lead-hantering. Marknadsföring-material inkluderar en beskrivning av erbjudande och logotyper. Juridiska tillgångar är en sekretesspolicy, användningsvillkor och andra juridisk dokumentation. Lead-hantering av principen kan du ange hur du hanterar leads från AppSource-användarportalen. |
| Contacts           | Innehåller information om support kontakta och principen.                                     |

## <a name="new-offer-form"></a>Nytt erbjudande formulär

När du loggar in på partnerportalen i molnet väljer **nytt erbjudande** i den vänstra rutan. Om du vill visa nytt erbjudande-formulär och starta processen med att definiera tillgångar till en ny app, Välj **Power BI-appar**.

![Menyalternativet för Power BI-erbjudande](./media/new-offer-menu.png)

> [!NOTE] 
> Om den **Power BI-appar** alternativ visas inte eller har inte aktiverats, ditt konto har inte behörighet att skapa den här erbjudandetypen. Kontrollera att du har uppfyllt alla de [krav](./cpp-prerequisites.md) för den här erbjudandetypen, inklusive registrering för ett utvecklarkonto.


## <a name="next-steps"></a>Nästa steg

Följande artiklar beskriver flikarna på den **nytt erbjudande** för erbjudandetypen en Power BI-appen. Varje artikel beskriver tillgångar och stödtjänster till din nya erbjudandet för Power BI-appen.

-  [fliken Erbjudandeinställningar](./cpp-offer-settings-tab.md)
-  [Fliken Teknisk information](./cpp-technical-info-tab.md)
-  [Fliken Butiksinformation](./cpp-storefront-details-tab.md)
-  [Fliken Kontakter](./cpp-contacts-tab.md)
