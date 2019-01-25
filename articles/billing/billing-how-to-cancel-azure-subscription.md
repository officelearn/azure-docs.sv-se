---
title: Avbryt din Azure-prenumeration | Microsoft Docs
description: Beskriver hur du avbryta din Azure-prenumeration, t.ex. kostnadsfri utvärderingsprenumeration
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/23/2019
ms.author: banders
ms.openlocfilehash: b0d5dd5fb29c8915e2c660b8647cb0148bce1399
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901521"
---
# <a name="cancel-your-subscription-for-azure"></a>Avbryta din prenumeration för Azure

Du kan avbryta din Azure-prenumeration som den [kontoadministratör](billing-subscription-transfer.md#whoisaa). Efter att du avbryter prenumerationen upphör din åtkomst till Azure-tjänster och resurser.

Innan du avbryter din prenumeration:

* Säkerhetskopiera dina data. Till exempel om du lagrar data i Azure storage och SQL, hämta en kopia. Om du har en virtuell dator kan du spara en avbildning av den lokalt.
* Stänga av dina tjänster. Gå till den [resurser sidan i hanteringsportalen](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), och **stoppa** någon kör virtuella datorer, program eller andra tjänster.
* Överväg att migrera dina data. Se [flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

Om du avbryter ett betalt supportavtal för Azure faktureras du fortfarande för resten av prenumerationsperioden. Mer information finns i [supportavtal](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Avbryt prenumeration med hjälp av Azure-portalen

1. Välj din prenumeration från den [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Välj den prenumeration som du vill avbryta.
3. Välj **översikt**, och välj sedan **Avbryt prenumeration**.

    ![Skärmbild som visar knappen Avbryt](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Följ anvisningarna och slutför annullering.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Vad händer när jag säga upp min prenumeration?

När du avbryter stoppas faktureringen omedelbart. Dock kan det ta upp till 10 minuter för detta ska visas i portalen.

Efter det inaktiveras dina tjänster. Det innebär att dina virtuella datorer är frigjord tillfälliga IP-adresser frigörs och lagring är skrivskyddad.

Om du avbryter en faktureringsperiod, kan vi skicka den slutgiltiga fakturan på din vanliga fakturadatum när den har löpt ut. 

Vi väntar du 90 dagar innan dina data tas bort permanent om du behöver åtkomst till den eller du ändrar dig. Vi debiterar inte du för att bevara data. Mer information finns i [Microsoft Trust Center – hur vi hanterar dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Återaktivera prenumeration

Om du avbryter prenumerationen med användningsbaserad betalning av misstag kan du [återaktivera den i Kontocentret](billing-subscription-become-disable.md).

Om prenumerationen inte är betala per användning, kontakta supporten inom 90 dagar efter uppsägning att återaktivera din prenumeration.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
