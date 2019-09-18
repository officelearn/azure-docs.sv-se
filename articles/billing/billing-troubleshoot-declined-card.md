---
title: Felsöka ett nekat kort vid Azure-registreringen
description: Lös problem med nekade kreditkort vid Azure-registreringen i Azure-portalen eller kontocentret.
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "69657065"
---
# <a name="troubleshoot-a-declined-card-at-azure-sign-up"></a>Felsöka ett nekat kort vid Azure-registreringen

I den här artikeln får du hjälp att lösa problem där ett kreditkort nekas vid Azure-registreringen i Azure-portalen eller i Azure-kontocentret. Innan du börjar felsöka problemet ska du kontrollera följande:

- Se till att du har angett rätt information i Azure-kontoprofilen, som e-post, adress och telefonnummer.
- Kontrollera att kreditkortsuppgifterna stämmer.
- Kontrollera att du inte redan har ett Microsoft-konto där du använder samma uppgifter.
- Du kan inte använda betalkort

## <a name="issues"></a>Problem

Här är en lista med vanliga problem som kan göra att ett kreditkort nekas vid Azure-registreringen.

### <a name="the-credit-card-provider-is-not-accepted-for-your-country"></a>Utgivaren av kreditkortet är inte godkänd för ditt land

När du väljer ett kort visar Azure de kortalternativ som är giltiga i landet du väljer. Kontakta din bank eller kortutfärdare och kontrollera att kreditkortet är aktiverat för internationella transaktioner. Mer information om vilka länder och valutor som stöds finns i [Vanliga frågor och svar om köp i Azure](https://azure.microsoft.com/pricing/faq/).

>[!Note]
>Kreditkort från American Express kan för närvarande inte användas som betalningsmedel i Indien. Vi vet inte när det här kommer att ändras.

### <a name="youre-using-a-virtual-or-prepaid-card"></a>Du använder ett virtuellt eller förbetalt kort 

Virtuella eller förbetalda kredit- eller betalkort kan inte användas som betalningsmedel för Azure-prenumerationer.

### <a name="your-credit-information-is-inaccurate-or-incomplete"></a>Din kreditinformation är felaktig eller ofullständig 

Det namn, den adress och den CVV-kod du anger måste exakt matcha det som står på kortet.

### <a name="the-card-is-inactive-or-blocked"></a>Kortet är inaktivt eller blockerat 

Kontakta din bank och se till att kortet är aktivt.

Du kanske har andra problem med registreringen 

Mer information om att felsöka problem med Azure-registreringar finns i följande artikel i kunskapsbasen: 

[Du kan inte registrera dig för Azure i Azure-portalen eller Azure-kontocentret](billing-troubleshoot-azure-sign-up.md)

### <a name="you-represent-a-business-that-doesnt-want-to-pay-by-card"></a>Du representerar ett företag som inte vill betala med kreditkort 

Om du representerar ett företag kan du betala för Azure-prenumerationen med checkar eller banköverföringar. När du har konfigurerat kontot för betalning via faktura kan du inte byta till något annat betalningssätt, såvida du inte har ett Microsoft-kundavtal och registrerade dig för Azure på Azure-webbplatsen.

Mer information om att betala via faktura finns i [Skicka en förfrågan om att betala Azure-prenumerationen via faktura](billing-how-to-pay-by-invoice.md).

### <a name="your-credit-card-information-is-outdated"></a>Kreditkortsinformationen är inaktuell 

Information om hur du hanterar din kortinformation, som att ändra eller ta bort ett kort, finns i [Lägg till, uppdatera eller ta bort ett kreditkort för Azure](billing-how-to-change-credit-card.md).

## <a name="additional-help-resources"></a>Fler hjälpresurser

Här är några fler felsökningsartiklar som gäller fakturering och prenumerationer i Azure

- [Registreringsproblem](billing-troubleshoot-azure-sign-up.md)
- [Problem med prenumerationsinloggning](billing-troubleshoot-sign-in-issue.md)
- [Inga prenumerationer hittades](billing-no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](index.md)
