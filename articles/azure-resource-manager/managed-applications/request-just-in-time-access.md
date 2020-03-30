---
title: Begära just-in-time-åtkomst
description: Beskriver hur utgivare av Azure Managed Applications begär just-in-time-åtkomst till ett hanterat program.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651389"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Aktivera och begära just-in-time-åtkomst för Azure Managed Applications

Konsumenter av ditt hanterade program kan vara ovilliga att ge dig permanent åtkomst till den hanterade resursgruppen. Som utgivare av ett förvaltarprogram kanske du föredrar att konsumenterna vet exakt när du behöver komma åt de hanterade resurserna. Azure Managed Applications ger en funktion som kallas JIT-åtkomst (just-in-time) som kallas jit-åtkomst (just-in-time) som för närvarande förhandsgranskas.

JIT-åtkomst gör att du kan begära förhöjd åtkomst till ett hanterat programs resurser för felsökning eller underhåll. Du har alltid skrivskyddad åtkomst till resurserna, men under en viss tidsperiod kan du få större åtkomst.

Arbetsflödet för att bevilja åtkomst är:

1. Du lägger till ett hanterat program på marketplace och anger att JIT-åtkomst är tillgänglig.

1. Under distributionen aktiverar konsumenten JIT-åtkomst för den instansen av det hanterade programmet.

1. Efter distributionen kan konsumenten ändra inställningarna för JIT-åtkomst.

1. Du skickar en begäran om åtkomst när du behöver felsöka eller uppdatera de hanterade resurserna.

1. Konsumenten godkänner din begäran.

Den här artikeln fokuserar på de åtgärder som utgivare vidtar för att aktivera åtkomst till jit-åtkomst och skicka begäranden. Mer information om hur du godkänner jit-åtkomstbegäranden finns [i Godkänna just-in-time-åtkomst i Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Lägg till åtkomststeg för JIT i användargränssnittet

Filen CreateUiDefinition.json är precis som den gränssnittsfil som du skapar för permanent åtkomst, förutom att du måste inkludera ett steg som gör att konsumenter kan aktivera JIT-åtkomst. Mer information om hur du publicerar ditt första hanterade programerbjudande på Azure Marketplace finns [i Azure Managed Applications på Marketplace](publish-marketplace-app.md).

Om du vill stödja JIT-funktioner för ditt erbjudande lägger du till följande innehåll i filen CreateUiDefinition.json:

I "steg":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
I "utgångar":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT-åtkomst är i förhandsversion. Schemat för JIT-konfigurationen kan ändras i framtida iterationer.

## <a name="enable-jit-access"></a>Aktivera JIT-åtkomst

När du definierar ditt erbjudande på marknaden, se till att du aktiverar JIT-åtkomst.

1. Logga in på [molnpartnerns publiceringsportal](https://cloudpartner.azure.com).

1. Ange värden för att publicera ditt hanterade program på marketplace. Välj **Ja** för **aktivera JIT Access?**

   ![Aktivera åtkomst just-in-time](./media/request-just-in-time-access/marketplace-enable.png)

Du har lagt till ett JIT-konfigurationssteg i användargränssnittet och har aktiverat JIT-åtkomst på marketplace-erbjudandet. När konsumenter distribuerar ditt hanterade program kan de [aktivera JIT-åtkomst för sin instans](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Begär åtkomst

När du behöver komma åt konsumentens hanterade resurser skickar du en begäran om en viss roll, tid och varaktighet. Konsumenten måste sedan godkänna begäran.

Så här skickar du en begäran om åtkomst till gemensamma utredningsgruppen:

1. Välj **JIT Access** för det hanterade programmet som du behöver komma åt.

1. Välj **Kvalificerade roller**och välj **Aktivera** i kolumnen ACTION för den roll du vill ha.

   ![Aktivera begäran om åtkomst](./media/request-just-in-time-access/send-request.png)

1. I formuläret **Aktivera roll** väljer du en starttid och varaktighet för att rollen ska vara aktiv. Välj **Aktivera** om du vill skicka begäran.

   ![Aktivera åtkomst](./media/request-just-in-time-access/activate-access.png) 

1. Visa meddelandena för att se att den nya JIT-begäran har skickats till konsumenten.

   ![Avisering](./media/request-just-in-time-access/in-progress.png)

   Nu måste du vänta på att konsumenten [ska godkänna din begäran.](approve-just-in-time-access.md#approve-requests)

1. Om du vill visa status för alla JIT-begäranden för ett hanterat program väljer du **JIT Access** och **Request History**.

   ![Visa status](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Kända problem

Huvud-ID:n för kontot som begär jit-åtkomst måste uttryckligen inkluderas i den hanterade programdefinitionen. Kontot kan inte bara inkluderas via en grupp som anges i paketet. Denna begränsning kommer att fastställas i en framtida version.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du godkänner begäranden om åtkomst till JIT finns [i Godkänna åtkomst i Azure Managed Applications](approve-just-in-time-access.md).
