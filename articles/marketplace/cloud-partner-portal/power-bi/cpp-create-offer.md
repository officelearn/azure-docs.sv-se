---
title: Skapa ett Power BI-apperbjudande | Azure Marketplace
description: Så här skapar du ett Power BI-apperbjudande för Microsoft AppSource Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 43bb34a80d8d62cfa2bde445b94fb97da25ccda3
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745474"
---
# <a name="create-a-power-bi-app-offer"></a>Skapa ett Power BI-apperbjudande

>[!Important]
>Från och med den 30 mars 2020 börjar vi flytta hanteringen av dina Power BI-appar till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [översikten över hur du skapar Power BI-appar](https://aka.ms/AzureCreatePBIServiceApp) för att hantera dina migrerade erbjudanden.

I den här artikeln visas de steg som ska följas för att skapa ett nytt Power BI-apperbjudande för [AppSource](https://appsource.microsoft.com). Varje erbjudande visas som sin egen entitet i AppSource. När du skapar ett nytt erbjudande i [Cloud Partner Portal](https://cloudpartner.azure.com/)måste du ange fyra grupper av tillgångar för ditt erbjudande.

Tillgångsgrupperna beskrivs i följande tabell:

|   Tillgångsgrupp      | Beskrivning                                                                         |
| ----------------   | ----------------                                                                    |
| Inställningar för erbjudande     | De primära identifieringarna och namnet på erbjudandet.                                      |
| Teknisk information     | Installationsadressen som du använder för att installera appen på klientens Power BI-arbetsyta. Mer information om hur du skapar den här WEBBADRESSEN finns i [Power BI App-dokumentationen](https://go.microsoft.com/fwlink/?linkid=2028636). |
| Information om skyltfönster | Innehåller marknadsförings-, legal- och lead management-tillgångar. Marknadsföringstillgångar inkluderar en erbjudandebeskrivning och logotyper. Juridiska tillgångar inkluderar en sekretesspolicy, användarvillkor och annan juridisk dokumentation. Med leadhanteringsprincipen kan du ange hur leads ska hanteras från AppSource-användarportalen. |
| Contacts           | Innehåller supportkontakt- och principinformation.                                     |

## <a name="new-offer-form"></a>Formulär för nytt erbjudande

När du har loggat in på Cloud Partner Portal väljer du **Nytt erbjudande** i den vänstra rutan. Om du sedan vill visa formuläret Nytt erbjudande och starta processen med att definiera resurser för ett nytt apperbjudande väljer du **Power BI Apps**.

![Menyalternativ för Power BI-erbjudande](./media/new-offer-menu.png)

> [!NOTE] 
> Om alternativet **Power BI Apps** inte visas eller inte är aktiverat har ditt konto inte behörighet att skapa den här erbjudandetypen. Kontrollera att du har uppfyllt alla [förutsättningar](./cpp-prerequisites.md) för den här erbjudandetypen, inklusive registrering för ett utvecklarkonto.


## <a name="next-steps"></a>Nästa steg

I följande artiklar beskrivs flikarna på sidan **Nytt erbjudande** för en Power BI-app-erbjudandetyp. I varje artikel beskrivs tillgångsgrupperna och stödtjänsterna för ditt nya Power BI-apperbjudande.

-  [fliken Erbjudandeinställningar](./cpp-offer-settings-tab.md)
-  [Fliken Teknisk information](./cpp-technical-info-tab.md)
-  [Fliken Butiksinformation](./cpp-storefront-details-tab.md)
-  [Fliken Kontakter](./cpp-contacts-tab.md)
