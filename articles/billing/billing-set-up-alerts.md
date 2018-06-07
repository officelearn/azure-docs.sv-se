---
title: Ställ in fakturerings- eller varningar för Azure-prenumerationer | Microsoft Docs
description: Beskriver hur du kan ställa in aviseringar på fakturan Azure så att du kan undvika fakturering överraskningar.
keywords: avisering för kredit, fakturering avisering
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
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: 981cb1153e0268e6572207f8d2401edb23485863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607811"
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Ställ in fakturerings- eller varningar för Microsoft Azure-prenumerationer
Om du är kontoadministratören för en Azure-prenumeration kan du använda fakturering aviseringstjänsten Azure för att skapa anpassade fakturaaviseringar som hjälper dig övervaka och hantera fakturerings-aktivitet för Azure-konton.

Tjänsten är i förhandsvisning, så du måste aktivera det på sidan funktioner för förhandsgranskning.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="set-the-alert-threshold-and-email-recipients"></a>Ange signalmottagare tröskelvärde och e-post
1. Besök [sidan förhandsgranskningsfunktioner](https://account.windowsazure.com/PreviewFeatures) och aktivera **fakturering aviseringstjänsten**.

1. När du har fått e-postbekräftelse som faktureringen är aktiverad för din prenumeration besöker [prenumerationssidan](https://account.windowsazure.com/Subscriptions) i kontoportalen. Klicka på den prenumeration som du vill övervaka och klicka sedan på **aviseringar**.

    ![Skärmbild av prenumerationsvyn i Azure kontocenter med aviseringar markerat][Image1]

2. Klicka sedan på **Lägg till avisering** att skapa din första. Du kan ställa in totalt fem fakturaaviseringar per prenumeration, med ett annat tröskelvärde och upp till två e-postmottagare för varje avisering.

    ![Skärmbild av vyn aviseringar, där du kan lägga till avisering][Image2]

3. När du lägger till en avisering du ge det ett unikt namn väljer ett tröskelvärde för utgiftsgräns och välj e-postadresser som aviseringar skickas. När du ställer in tröskelvärdet, kan du välja antingen en **Billing Total** eller en **Penningkredit** från den **avisering för** lista. Fakturering Totalt skickas en avisering när prenumerationen utgifter överstiger tröskelvärdet. För en penningkredit skickas en avisering när penningkrediten sjunker under gränsen. Penningkrediten gäller vanligtvis prenumerationer kostnadsfri utvärderingsversion och Visual Studio.

    ![Skärmbild av vyn avisering tillägg, där du kan konfigurera mottagare][Image3]

Azure stöder en e-postadress men Kontrollera inte att e-postadressen fungerar, så dubbelkolla för skrivfel.

## <a name="check-on-your-alerts"></a>Kontrollera på aviseringar
När du har skapat aviseringar Account Center visas dem och hur många fler kan du ställa in. För varje avisering måste se du datum och tid som det har skickats, oavsett om det är en avisering om Billing Total eller kredit och den gräns som du konfigurerar. Formatet för datum och tid är 24-timmarsformat universaltid samordna (UTC) och datum är åååå-mm-dd-formatet. Klicka på plustecknet för en avisering i listan om du vill redigera den eller klicka på Papperskorgen för att ta bort den.

## <a name="delete-alerts-or-email-addresses-from-the-azure-billing-alert-service"></a>Ta bort aviseringar eller e-postadresser från fakturering aviseringstjänsten Azure
Om du behöver ta bort all information från tjänsten, uppdatera e-postadressen på filen eller ta bort aviseringen helt.

   ![Skärmbild av vyn avisering borttagning, där du kan ta bort personlig information][Image4]

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Fakturering aviseringar för kunder med Enterprise-avtal (EA)
EA prenumerationer stöds inte av den här tjänsten, EA kunder kan i stället få aviseringar för varje avdelning under en registrering genom att ange utgifter kvoter. Se [avdelning utgifter kvoter](https://ea.azure.com/helpdocs/departmentSpendingQuotas) i EA-portalen för att komma igång.

## <a name="learn-more-about-azure-cost-management"></a>Lär dig mer om hantering av Azure kostnad
- Beräkna kostnader med hjälp av den [prisnivå Kalkylatorn](https://azure.microsoft.com/pricing/calculator/), [totalkostnad för ägarskap Kalkylatorn](https://aka.ms/azure-tco-calculator), och när du lägger till en tjänst.
- [Granska din användning och kostnader regelbundet i Azure-portalen](billing-getting-started.md#costs).
- Aktivera [Azure Advisor kostnad rekommendationer](../advisor/advisor-cost-recommendations.md).

Läs mer i [Azure kostnad management vägledning](billing-getting-started.md).

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
[Image4]: ./media/azure-billing-set-up-alerts/AlertsDeleteScreen1.PNG
