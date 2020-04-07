---
title: Översikt över skapande av Power BI-appar – Azure Marketplace
description: I den här artikeln beskrivs stegen på hög nivå för att publicera en Power BI-app till Microsoft AppSource. De tekniska och affärsmässiga krav som din Power BI-app måste uppfylla för att publiceras på den kommersiella marknadsplatsen tillhandahålls också.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: c348a172b16e12334d33cf2718609694147fdce3
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674532"
---
# <a name="power-bi-app-creation-overview"></a>Översikt över skapande av Power BI-appar

> [!IMPORTANT]
> Vi flyttar hanteringen av dina Power BI-apperbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [Power BI-appens erbjudande](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) för Cloud Partner Portal för att hantera dina erbjudanden.

I den här artikeln beskrivs hur du publicerar en Power BI-app på Microsoft [AppSource](https://appsource.microsoft.com/). Ett Power BI-apppaket paketerar anpassningsbart innehåll, inklusive datauppsättningar, rapporter och instrumentpaneler. Du kan sedan använda appen med andra Power BI-plattformar med Hjälp av AppSource, utföra de justeringar och anpassningar som tillåts av utvecklaren och ansluta den till dina egna data.

## <a name="publishing-benefits"></a>Publiceringsförmåner

Fördelar med att publicera på den kommersiella marknaden:

- Marknadsför ditt företag med hjälp av Microsofts varumärke.
- Potentiellt nå mer än 100 miljoner Office 365- och Dynamics 365-användare på AppSource och mer än 200 000 organisationer via Azure Marketplace.
- Få högkvalitativa leads från dessa marknadsplatser.
- Få dina tjänster marknadsförda av Microsoft-fält- och telefonförsäljningsteamen.

## <a name="overview"></a>Översikt

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="översikt över stegen för att publicera en Power BI-app" border="false":::

Det här är de viktigaste publiceringsstegen:

1. Skapa ditt program i Power BI. Du får en länk för paketinstallation, som är den viktigaste tekniska tillgången för erbjudandet. Skicka testpaketet till förproduktion innan du skapar erbjudandet i Partner Center. Mer information finns i [Vad är Power BI-appar?](https://docs.microsoft.com/power-bi/service-template-apps-overview).
2. Lägg till marknadsföringsmaterial, till exempel officiellt namn, beskrivning och logotyper.
3. Inkludera erbjudandets juridiska dokument och supportdokument, till exempel användarvillkor, sekretesspolicy, supportpolicy och användarhjälp.
4. Skapa erbjudandet: Använd Partnercenter för att redigera informationen, inklusive erbjudandebeskrivning, marknadsföringsmaterial, juridisk information, supportinformation och tillgångsspecifikationer.
5. Skicka in den för publicering.
6. Övervaka processen i Partner Center, där AppSource-introduktionsteamet testar, validerar och certifierar din app.
7. När den har certifierats granskar du appen i dess testmiljö och släpper den. Detta kommer att lista det på AppSource (det "går live").
8. I Power BI skickar du paketet i produktion. Mer information finns [i Hantera power bi-appversionen](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Innan du börjar

Gå igenom länkarna nedan, som innehåller mallar, tips och exempel.

- [Skapa en Power BI-app](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Tips om hur du redigerar en Power BI-app](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [Exempel](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Krav

För att publiceras på den kommersiella marknaden måste ditt Power BI-apperbjudande uppfylla följande tekniska krav och affärskrav.

### <a name="technical-requirements"></a>Tekniska krav

Den viktigaste tekniska tillgången du behöver är en [Power BI-app](https://go.microsoft.com/fwlink/?linkid=2028636). Det här är en samling primära datauppsättningar, rapporter eller instrumentpaneler. Den innehåller även valfria anslutna tjänster och inbäddade datauppsättningar, tidigare kallade [innehållspaket](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction). Mer information om hur du utvecklar den här typen av app finns i [Vad är Power BI-appar?](https://go.microsoft.com/fwlink/?linkid=2028636)

#### <a name="get-an-installation-web-address"></a>Skaffa en installationswebbadress

Du kan bara skapa en Power BI-app i [Power BI-miljön.](https://powerbi.microsoft.com/)

1. Logga in med en [Power BI Pro-licens](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).
2. Skapa och testa din app i Power BI.
3. När du får webbadressen till appinstallationen lägger du till den på sidan **Teknisk konfiguration** i Partnercenter.

När appen har skapats och testats i Power BI sparar du webbadressen för programinstallationen, eftersom du behöver den för att [skapa ett Power BI-apperbjudande](https://aka.ms/AzureCreatePBIServiceApp).

### <a name="business-requirements"></a>Affärskrav

Affärskraven omfattar processuella, avtalsenliga och rättsliga skyldigheter. Du måste:

- Bli en registrerad kommersiell marknadsplats utgivare. Om du inte är registrerad följer du stegen i [Bli en kommersiell Marketplace Publisher](https://docs.microsoft.com/azure/marketplace/become-publisher).
- Ange innehåll som uppfyller kriterierna för att ditt erbjudande ska visas på AppSource. Mer information finns i [Har du en app att lista på AppSource? Så här gör du.](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)
- Godkänn och följ [Microsofts sekretesspolicy](https://privacy.microsoft.com/privacystatement).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett Power BI-apperbjudande i Partner Center](https://aka.ms/AzureCreatePBIServiceApp)