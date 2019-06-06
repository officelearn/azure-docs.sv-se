---
title: Avbryt din Azure-prenumeration | Microsoft Docs
description: Beskriver hur du avbryta din Azure-prenumeration, t.ex. kostnadsfri utvärderingsprenumeration
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 8f4279d9ac085cdd1ded0dfdda4fad9d3fe12fb8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480233"
---
# <a name="cancel-your-subscription-for-azure"></a>Avbryta din prenumeration för Azure

Endast en Azure-prenumeration [kontoadministratör](billing-subscription-transfer.md#whoisaa) kan avbryta en Azure-prenumeration. En administratör för Azure-prenumeration kan också [tilldela en annan användare som en prenumerationsadministratör](billing-add-change-azure-subscription-administrator.md#assign-a-user-as-an-administrator-of-a-subscription) så att de kan avbryta en prenumeration. Efter att du avbryter prenumerationen upphör din åtkomst till Azure-tjänster och resurser.

Innan du avbryter din prenumeration:

* Säkerhetskopiera dina data. Till exempel om du lagrar data i Azure storage och SQL, hämta en kopia. Om du har en virtuell dator kan du spara en avbildning av den lokalt.
* Stänga av dina tjänster. Gå till den [resurser sidan i hanteringsportalen](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources), och **stoppa** någon kör virtuella datorer, program eller andra tjänster.
* Överväg att migrera dina data. Se [flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).
* Du måste ta bort alla resurser och alla resursgrupper. Det är obligatoriskt att ta bort dem innan du kan avbryta en prenumeration. Varje resursgrupp måste tas bort separat. Vid borttagning av resursen, måste du bekräfta borttagningen genom att skriva resursgruppens namn.
* Om du har några anpassade roller som refererar till den här prenumerationen i `AssignableScopes`, bör du uppdatera dessa anpassade roller för att ta bort prenumerationen. Om du försöker uppdatera en anpassad roll efter att du avbryter en prenumeration kan du få ett felmeddelande. Mer information finns i [felsökning av problem med anpassade roller](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) och [anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md).

Om du avbryter ett betalt supportavtal för Azure faktureras du fortfarande för resten av prenumerationsperioden. Mer information finns i [supportavtal](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Avbryt prenumeration med hjälp av Azure-portalen

1. Välj din prenumeration från den [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Välj den prenumeration som du vill avbryta.
3. Välj **översikt**, och välj sedan **Avbryt prenumeration**.

    ![Skärmbild som visar knappen Avbryt](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Följ anvisningarna och slutför annullering.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Vad händer när jag säga upp min prenumeration?

Faktureringen stoppas omedelbart efter att du avbryter. Dock kan det ta upp till 10 minuter för detta ska visas i portalen. Om du avbryter en faktureringsperiod, kan vi skicka den slutgiltiga fakturan på din vanliga fakturadatum när den har löpt ut.

Efter att du avbryter inaktiveras dina tjänster. Det innebär att dina virtuella datorer är frigjord tillfälliga IP-adresser frigörs och lagring är skrivskyddad.

Vi väntar du 90 dagar innan dina data tas bort permanent om du behöver åtkomst till den eller du ändrar dig. Vi debiterar inte du för att bevara data. Mer information finns i [Microsoft Trust Center – hur vi hanterar dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Återaktivera prenumeration

Om du avbryter prenumerationen med användningsbaserad betalning av misstag kan du [återaktivera den i Kontocentret](billing-subscription-become-disable.md).

Om prenumerationen inte är betala per användning, kontakta supporten inom 90 dagar efter uppsägning att återaktivera din prenumeration.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
