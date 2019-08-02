---
title: Avbryta din Azure-prenumeration | Microsoft Docs
description: Beskriver hur du avbryter din Azure-prenumeration, t. ex. den kostnads fria prov prenumerationen
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
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666432"
---
# <a name="cancel-your-azure-subscription"></a>Avbryta din Azure-prenumeration

Du kan avbryta din Azure-prenumeration i Azure Portal om du inte längre behöver prenumerationen. 

Innan du avbryter din prenumeration:
* Säkerhetskopiera dina data. Om du till exempel lagrar data i Azure Storage eller SQL kan du hämta en kopia. Om du har en virtuell dator sparar du en avbildning av den lokalt.
* Stäng av tjänsterna. Gå till [sidan resurser i hanterings portalen](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)och **stoppa** alla virtuella datorer, program eller andra tjänster som körs.
* Överväg att migrera dina data. Se [Flytta resurser till ny resurs grupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md).
* Ta bort alla resurser och alla resurs grupper. Du måste ta bort dem innan du kan avbryta en prenumeration. Varje resurs grupp måste tas bort individuellt. När du tar bort en resurs grupp måste du bekräfta borttagningen genom att skriva resurs gruppens namn.
* Om du har anpassade roller som refererar till den här prenumerationen i `AssignableScopes`bör du uppdatera de anpassade rollerna för att ta bort prenumerationen. Om du försöker uppdatera en anpassad roll när du har avbrutit en prenumeration kan du få ett fel meddelande. Mer information finns i [Felsöka problem med anpassade roller](../role-based-access-control/troubleshooting.md#problems-with-custom-roles) och [anpassade roller för Azure-resurser](../role-based-access-control/custom-roles.md).

Om du avbryter ett betalt support avtal för Azure debiteras du för resten av prenumerations perioden. Mer information finns i support avtal för [Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Avbryt prenumerationen i Azure Portal

1. Välj din prenumeration på [sidan prenumerationer i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Välj den prenumeration som du vill avbryta.
3. Välj **Översikt**och välj sedan **Avbryt prenumeration**.
    ![Skärm bild som visar knappen Avbryt](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. Följ anvisningarna och avsluta annulleringen.


## <a name="who-can-cancel-a-subscription"></a>Vem kan avbryta en prenumeration?

I tabellen nedan beskrivs den behörighet som krävs för att avbryta en prenumeration.

|Prenumerationstyp     |Vem kan avbryta  |
|---------|---------|
|Prenumerationer som skapas när du registrerar dig för Azure via Azure-webbplatsen. Till exempel när du registrerar dig för ett [kostnads fritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/)kan du [använda ett konto med betala per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning eller som en [Visual Studio-prenumerant](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Konto administratör, ägare och deltagare för prenumerationen  |
|[Microsoft Enterprise-avtal](https://azure.microsoft.com/pricing/enterprise-agreement/) och [Enterprise dev/test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Konto ägare, ägare och deltagare för prenumerationen       |
|[Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/) och [Azure-plan för DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Ägare och deltagare för prenumerationen      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>Vad händer när jag har avbrutit min prenumeration?

När du har avbrutit stoppas faktureringen omedelbart. Det kan dock ta upp till 10 minuter innan annulleringen visas i portalen. Om du avbryter mitt i en fakturerings period skickar vi den sista fakturan till ditt vanliga faktura datum när perioden är slut.

När du har avbrutit inaktive ras tjänsterna. Det innebär att de virtuella datorerna är avallokerade, temporära IP-adresser frigörs och att lagringen är skrivskyddad.

Vi väntar 90 dagar innan data tas bort permanent om du behöver komma åt det eller om du ändrar dig. Vi debiterar dig inte för att spara data. Mer information finns i [Microsoft Trust Center – hur vi hanterar dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Återaktivera prenumeration

Om du avbryter prenumerationen med priser enligt principen betala per användning kan du [återaktivera den i konto Center](billing-subscription-become-disable.md).

Om prenumerationen inte är en prenumeration med taxan betala per användning kontaktar du support inom 90 dagar från det att du har avbrutit för att återaktivera din prenumeration.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).
