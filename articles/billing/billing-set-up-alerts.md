---
title: Konfigurera aviseringar för fakturering eller kredit för Azure-prenumerationer | Microsoft Docs
description: Beskriver hur du kan ställa in aviseringar på din Azure-faktura för att undvika debiteringsöverraskningar.
keywords: kredit aviseringen fakturering avisering
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: adpick
ms.openlocfilehash: 094bfe041ae04e52b6d998ccfd1d964abf192d6a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095107"
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Konfigurera fakturerings- eller aviseringar för dina Microsoft Azure-prenumerationer
Om du är kontoadministratör för en Azure-prenumeration kan du använda den Azures Fakturapåminnelsetjänst för att skapa anpassade fakturapåminnelser som hjälper dig att övervaka och hantera faktureringen för dina Azure-konton.

Den här tjänsten är i förhandsversion, så måste du aktivera det på sidan funktioner i förhandsversion.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="set-the-alert-threshold-and-email-recipients"></a>Ange signalmottagare tröskelvärde och e-post
1. Besök [sidan för Förhandsfunktioner](https://account.windowsazure.com/PreviewFeatures) och aktivera **Fakturapåminnelsetjänst**.

1. När du har fått e-postbekräftelse faktureringen aktiveras för din prenumeration, besök [prenumerationssidan](https://account.windowsazure.com/Subscriptions) i kontoportalen. Klicka på den prenumeration som du vill övervaka och klicka sedan på **aviseringar**.

    ![Skärmbild av prenumerationsvyn för Azure-kontocenter med aviseringar som är markerat][Image1]

2. Klicka sedan på **Lägg signal** att skapa din första. Du kan ställa in högst fem faktureringsvarningar per prenumeration, med ett annat tröskelvärde och upp till två e-postmottagare för varje avisering.

    ![Skärmbild av aviseringsvyn, där du kan lägga till avisering][Image2]

3. När du lägger till en avisering du ge den ett unikt namn, väljer ett tröskelvärde för utgiftsgräns och välj e-postadresserna där aviseringar skickas. När du konfigurerar tröskelvärdet, kan du välja något en **fakturering totala** eller en **Penningkredit** från den **avisering för** lista. Fakturering Totalt skickas en avisering när prenumerationen utgifter överstiger tröskelvärdet. För penningkrediterbjudanden skickas en avisering när penningkrediter sjunker under gränsen. Det gäller vanligtvis penningkrediter för kostnadsfri utvärderingsversion och Visual Studio-prenumerationer.

    ![Skärmbild av vyn avisering tillägg, där du kan konfigurera mottagare][Image3]

Azure stöder valfri e-postadress men Kontrollera inte att e-postadress fungerar, så dubbelkolla för stavfel.

## <a name="check-on-your-alerts"></a>Kontrollera dina aviseringar
När du har konfigurerat aviseringar Kontocentret visar en lista över dem och visar hur många mer du kan ställa in. För varje avisering visas datum och tid som det har skickats, oavsett om det är en avisering för fakturering totala eller kredit och gränsen som du har konfigurerat. Formatet för datum och tid är 24 timmar universaltid samordna (UTC) och datum är åååå-mm-dd-formatet. Klicka på plustecknet för en avisering i listan om du vill redigera den eller klicka på Papperskorgen för att ta bort den.

## <a name="delete-alerts-or-email-addresses-from-the-azure-billing-alert-service"></a>Ta bort aviseringar eller e-postadresser från den Azures Fakturapåminnelsetjänst
Om du behöver ta bort all information från tjänsten, uppdatera e-postadressen för filen eller ta bort aviseringen helt och hållet.

   ![Skärmbild av aviseringen borttagning vyn, där du kan ta bort personlig information][Image4]

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Faktureringsaviseringar för kunder med Enterprise Agreement (EA)
EA-prenumerationer stöds inte av den här tjänsten, EA-kunder kan i stället få aviseringar för varje avdelning under en registrering genom att ange utgiftsgränser. Se [avdelning utgifter kvoter](https://ea.azure.com/helpdocs/departmentSpendingQuotas) i EA-portalen för att komma igång.

## <a name="learn-more-about-azure-cost-management"></a>Lär dig mer om Azure cost management
- Beräkna kostnader med hjälp av den [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/), [totalkostnad för ägarskap Kalkylatorn](https://aka.ms/azure-tco-calculator), och när du lägger till en tjänst.
- [Granska användning och kostnader regelbundet i Azure-portalen](billing-getting-started.md#costs).
- Aktivera [Azure Advisor kostnad rekommendationer](../advisor/advisor-cost-recommendations.md).

Mer information finns i [Azure cost management vägledning](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
[Image4]: ./media/azure-billing-set-up-alerts/AlertsDeleteScreen1.PNG
