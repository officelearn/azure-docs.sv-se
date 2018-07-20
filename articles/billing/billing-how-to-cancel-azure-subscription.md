---
title: Avbryt din Azure-prenumeration | Microsoft Docs
description: Beskriver hur du avbryta din Azure-prenumeration, t.ex. kostnadsfri utvärderingsprenumeration
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: genli
ms.openlocfilehash: 0bd0fca54ae5bf4292564a15adf38b7586768450
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145459"
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
1. Välj den prenumeration som du vill avbryta och klickar på **Avbryt prenumeration**.

    ![Skärmbild som visar knappen Avbryt](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Följ anvisningarna och slutför annullering.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Vad händer när jag säga upp min prenumeration?

När du avbryter stoppas faktureringen omedelbart. Dock kan det ta upp till 10 minuter för annullering show i portalen.

Efter det inaktiveras dina tjänster. Det innebär att dina virtuella datorer är frigjord tillfälliga IP-adresser frigörs och lagring är skrivskyddad.

Om du avbryter en faktureringsperiod, kan vi skicka den slutgiltiga fakturan på din vanliga fakturadatum när den har löpt ut. 

Vi väntar du 90 dagar innan dina data tas bort permanent om du behöver åtkomst till den eller du ändrar dig. Vi debiterar inte du för att bevara data. Mer information finns i [Microsoft Trust Center – hur vi hanterar dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Återaktivera prenumeration

Om du avbryter prenumerationen med användningsbaserad betalning av misstag kan du [återaktivera den i Kontocentret](billing-subscription-become-disable.md).

Om prenumerationen inte är betala per användning, kontakta supporten inom 90 dagar efter uppsägning att återaktivera din prenumeration.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
