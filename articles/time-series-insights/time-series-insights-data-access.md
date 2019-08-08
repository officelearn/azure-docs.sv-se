---
title: Konfigurera säkerhet för att komma åt och hantera Azure Time Series Insights för hands versionen | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar säkerhet och behörigheter som principer för hanterings åtkomst och principer för data åtkomst för att skydda Azure Time Series Insights för hands versionen.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 4455d499ab4c52a27a7d9cf878e8130ff38b1c62
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846944"
---
# <a name="grant-data-access-to-an-environment"></a>Bevilja data åtkomst till en miljö

I den här artikeln beskrivs de två typerna av Azure Time Series Insights för för hands versions åtkomst principer.

## <a name="sign-in-to-time-series-insights"></a>Logga in på Time Series Insights

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Leta upp din Time Series Insightss miljö. Ange `Time Series` i sökrutan. Välj **tids serie miljö** i Sök resultaten.
1. Välj Time Series Insights-miljön från listan.

## <a name="grant-data-access"></a>Bevilja åtkomst till data

Följ dessa steg om du vill bevilja data åtkomst för ett huvud konto för användare.

1. Välj **data åtkomst principer**och välj sedan **+ Lägg till**.

    [![Data åtkomst – ett](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Välj **Välj användare**. Sök efter användar namnet eller e-postadressen för att hitta den användare som du vill lägga till. Välj **Välj** för att bekräfta valet.

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Välj **Välj roll**. Välj lämplig åtkomst roll för användaren:

    * Välj **deltagare** om du vill tillåta att användaren ändrar referens data och delar sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer du **läsare** så att användaren kan fråga efter data i miljön och spara personliga, inte delade, frågor i miljön.

   Bekräfta valet av roll genom att klicka på **OK** .

    [![Data åtkomst – tre](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Välj **OK** på sidan **Välj användar roll** .

    [![Data-access-four](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Bekräfta att sidan för **data åtkomst principer** visar användarna och rollerna för varje användare.

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Ge gäst åtkomst från en annan AAD-klient

`Guest`är inte en hanterings roll. Det är en term som används för ett konto som har bjudits in från en klient till en annan. När gäst kontot har bjudits in till klientens katalog kan samma åtkomst kontroll tillämpas på samma sätt som andra konton. Du kan bevilja hanterings åtkomst till en Time Series Insights miljö genom att använda bladet Access Control (IAM). Du kan också bevilja åtkomst till data i miljön via bladet data åtkomst principer. Mer information om Azure Active Directory (Azure AD) gäst åtkomsten finns [i lägga till Azure Active Directory B2B-samarbets användare i Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Följ dessa steg om du vill ge gäst åtkomst till en Time Series Insights miljö till en Azure AD-användare från en annan klient.

1. Välj **data åtkomst principer**och välj sedan **+ Bjud in**.

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Ange e-postadressen för den användare som du vill bjuda in. Den här e-postadressen måste vara kopplad till Azure AD. Du kan också inkludera ett personligt meddelande med inbjudan.

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Leta efter den bekräftelse bubbla som visas på skärmen.

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Välj **Välj användare**. Sök efter e-postadressen för gäst användaren som du har bjudit in för att hitta den användare som du vill lägga till. **Välj** sedan för att bekräfta valet.

    [![Data-access-nine](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Välj **Välj roll**. Välj lämplig åtkomst roll för gäst användaren:

    * Välj **deltagare** om du vill tillåta att användaren ändrar referens data och delar sparade frågor och perspektiv med andra användare av miljön.

    * Annars väljer du **läsare** så att användaren kan fråga efter data i miljön och spara personliga, inte delade, frågor i miljön.

   Bekräfta valet av roll genom att klicka på **OK** .

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Välj **OK** på sidan **Välj användar roll** .

1. Bekräfta att sidan för **data åtkomst principer** visar gäst användaren och rollerna för varje gäst användare.

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Gäst användaren måste nu följa stegen för att få åtkomst till miljön som finns i den Azure-klient som du har bjudit in dem till. Först accepterar de inbjudan som du skickade. Den här inbjudan skickas via e-post till den e-postadress som du använde i steg 5. De väljer **Kom igång** för att acceptera.

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. Sedan godkänner gäst användaren de behörigheter som är associerade med administratörens organisation.

    [![Data-access-thirteen](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. När gäst användaren är inloggad på e-postadressen som du använde för att bjuda in dem, och de accepterar inbjudan, går de till insights.azure.com. När så är fallet väljer de den avataren bredvid e-postadressen i det övre högra hörnet på skärmen.

    [![Data åtkomst – fjorton](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. Sedan väljer gäst användaren din Azure-klient i list rutan Katalog. Den här klienten är den som du har bjudit in dem till.

    [![Data-access-fifteen](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

När gäst användaren har valt klienten, ser de Time Series Insightss miljön som du har fått åtkomst till. De har nu alla funktioner som är kopplade till den roll som du angav i **steg 5**.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du lägger till en händelse källa för Azure-Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) i din Time Series Insights-miljö.

* Skicka [händelser till händelse källan](./time-series-insights-send-events.md).

* Visa [din miljö i Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md).
