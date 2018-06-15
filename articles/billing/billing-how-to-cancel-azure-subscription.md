---
title: Avbryt din Azure-prenumeration | Microsoft Docs
description: Beskriver hur du avbryter prenumerationen Azure som kostnadsfri utvärderingsversion
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 604e2ee6d2ac7e30813f8b4cd02253407c2b1e3f
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067443"
---
# <a name="cancel-your-subscription-for-azure"></a>Avbryta din prenumeration på Azure

Du kan avbryta din Azure-prenumeration som den [kontoadministratör](billing-subscription-transfer.md#whoisaa). Efter att du avbryter prenumerationen avslutas din åtkomst till Azure-tjänster och resurser.

Innan du avbryter prenumerationen:

* Säkerhetskopiera dina data. Om du lagrar data i Azure storage eller SQL, hämta, till exempel en kopia. Om du har en virtuell dator kan du spara en avbildning av det lokalt.
* Stäng av dina tjänster. Gå till den [resurser sidan i hanteringsportalen](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), och **stoppa** någon med virtuella datorer, program eller andra tjänster.
* Bör du migrera dina data. Se [flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).

Om du avbryter en betald [Azure-supportplan](https://azure.microsoft.com/support/plans/), fortfarande debiteras du varje månad för resten av termen 6 månader.

## <a name="cancel-subscription-using-the-azure-portal"></a>Avbryt prenumeration i Azure Portal

1. Välj din prenumeration från den [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill avbryta och klicka på **avbryta prenumerationen**.

    ![Skärmbild som visar knappen Avbryt](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Följ anvisningarna och slutför annullering.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Vad händer när jag avsluta min prenumeration?

När du avbryter stoppas fakturering omedelbart. Dock kan det ta upp till 10 minuter för att visa på portalen.

Sedan kan har dina tjänster inaktiverats. Det innebär att dina virtuella datorer är frigöra frigörs tillfälliga IP-adresser och lagring är skrivskyddad.

Om du avbryter mitt i en faktureringsperiod skicka vi den slutliga fakturan på vanliga fakturadatum när perioden är slut. 

Vi väntar 90 dagar före permanent ta bort data om du behöver åtkomst till den eller du ändrar dig. Vi debitera inte dig för att behålla data. Läs mer i [Microsoft Trust Center - hur vi kan hantera dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Återaktivera prenumeration

Om du avbryter prenumerationen betalning per användning av misstag kan du [återaktivera i Accounts Center](billing-subscription-become-disable.md).

Om din prenumeration inte är betala per användning, kontakta support inom 90 dagar efter att återaktivera din prenumeration.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande har frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
