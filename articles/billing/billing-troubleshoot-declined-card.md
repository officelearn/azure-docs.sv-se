---
title: Felsök ett nekat kort vid Azure-registrering
description: Lös avvisat kredit kort vid Azure-registrering i Azure Portal eller konto Center.
author: v-miegge
manager: adpick
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: banders
ms.openlocfilehash: 730238d62e4ee4aad1807a4461c9b26ee1c8485d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657065"
---
# <a name="troubleshoot-a-declined-card-at-azure-sign-up"></a>Felsök ett nekat kort vid Azure-registrering

Den här artikeln hjälper dig att lösa problem där ett kredit kort nekas vid Azure-registrering i Azure Portal eller Azure konto Center. Innan du börjar felsöka problemet kontrollerar du följande:

- Kontrol lera att den information som du har angett för din profil för Azure-kontot, till exempel e-postadress, adress och telefonnummer, är korrekt.
- Kontrollera att kreditkortsuppgifterna stämmer.
- Kontrollera att du inte redan har ett Microsoft-konto där du använder samma uppgifter.
- Debiterings kort har inte accepterats

## <a name="issues"></a>Problem

Nedan visas vanliga problem som kan orsaka att ett kredit kort nekas vid Azure-registrering.

### <a name="the-credit-card-provider-is-not-accepted-for-your-country"></a>Kredit korts leverantören är inte godkänd för ditt land

När du väljer ett kort visar Azure de kort alternativ som är giltiga i det land som du väljer. Kontakta din bank eller kort utfärdare för att kontrol lera att ditt kredit kort har Aktiver ATS för internationella transaktioner. Mer information om länder och valutor som stöds finns i [vanliga frågor och svar om Azure-köp](https://azure.microsoft.com/pricing/faq/).

>[!Note]
>Amerikanska Express-kredit kort stöds för närvarande inte som ett betalnings instrument i Indien. Vi har ingen tids gräns för när det kan vara en godkänd betalnings form.

### <a name="youre-using-a-virtual-or-prepaid-card"></a>Du använder ett virtuellt eller förbetalt kort 

Virtuella eller förbetalda kredit-eller betalkort godkänns inte som betalning för Azure-prenumerationer.

### <a name="your-credit-information-is-inaccurate-or-incomplete"></a>Din kredit information är felaktig eller ofullständig 

Namn, adress och CVV kod som du anger måste matcha exakt det som skrivs ut på kortet.

### <a name="the-card-is-inactive-or-blocked"></a>Kortet är inaktivt eller blockerat 

Kontakta din bank för att se till att kortet är aktivt.

Du kanske har problem med att registrera dig 

Mer information om hur du felsöker problem med Azure-registrering finns i följande artikel i kunskaps basen: 

[Du kan inte registrera dig för Azure i Azure Portal eller Azure konto Center](billing-troubleshoot-azure-sign-up.md)

### <a name="you-represent-a-business-that-doesnt-want-to-pay-by-card"></a>Du representerar en verksamhet som inte vill betala per kort 

Om du representerar ett företag kan du använda faktura betalnings metoder, till exempel kontroller, över natten eller överförings överföringar för att betala för din Azure-prenumeration. När du har konfigurerat kontot för att betala med faktura kan du inte ändra till ett annat betalnings alternativ, om du inte har ett Microsoft-kundavtal och registrerat dig för Azure via Azure-webbplatsen.

Mer information om hur du betalar per faktura finns i [skicka en begäran om att betala Azure-prenumeration per faktura](billing-how-to-pay-by-invoice.md).

### <a name="your-credit-card-information-is-outdated"></a>Kredit korts informationen är inaktuell 

Information om hur du hanterar kort information, inklusive att ändra eller ta bort ett kort, finns i [lägga till, uppdatera eller ta bort en kredit för Azure](billing-how-to-change-credit-card.md).

## <a name="additional-help-resources"></a>Ytterligare hjälp resurser

Andra fel söknings artiklar för Azure-fakturering och-prenumerationer

- [Registreringsproblem](billing-troubleshoot-azure-sign-up.md)
- [Problem med prenumerations inloggning](billing-troubleshoot-sign-in-issue.md)
- [Inga prenumerationer hittades](billing-no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](index.md)
