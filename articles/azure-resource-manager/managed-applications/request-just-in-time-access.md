---
title: Begära just-in-time-åtkomst
description: Beskriver hur utgivare av Azure Managed Applications begär just-in-Time-åtkomst till ett hanterat program.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651389"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Aktivera och begär just-in-Time-åtkomst för Azure Managed Applications

Konsumenter av det hanterade programmet kan vara ovilliga för att ge dig permanent åtkomst till den hanterade resurs gruppen. Som utgivare av ett hanterings program kanske du föredrar att konsumenter vet exakt när du behöver åtkomst till de hanterade resurserna. För att ge konsumenterna bättre kontroll över beviljandet av åtkomst till hanterade resurser, ger Azure Managed Applications en funktion som kallas JIT-åtkomst (just-in-Time), som för närvarande är en för hands version.

Med JIT-åtkomst kan du begära utökad åtkomst till ett hanterat programs resurser för fel sökning eller underhåll. Du har alltid skrivskyddad åtkomst till resurserna, men under en viss tids period kan du ha större åtkomst.

Arbets flödet för att bevilja åtkomst är:

1. Du lägger till ett hanterat program på Marketplace och anger att JIT-åtkomst är tillgänglig.

1. Under distributionen aktiverar konsumenten JIT-åtkomst för den instansen av det hanterade programmet.

1. Efter distributionen kan konsumenten ändra inställningarna för JIT-åtkomst.

1. Du skickar en begäran om åtkomst när du behöver felsöka eller uppdatera de hanterade resurserna.

1. Konsumenten godkänner din begäran.

Den här artikeln fokuserar på de åtgärder som utgivaren vidtar för att aktivera JIT-åtkomst och skicka begär Anden. Information om hur du godkänner JIT-begäranden finns i [godkänna just-in-Time-åtkomst i Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Lägg till JIT-åtkomst steg i användar gränssnittet

Din CreateUiDefinition.jsi filen är precis som den GRÄNSSNITTs fil som du skapar för permanent åtkomst, förutom att du måste inkludera ett steg som låter konsumenterna Aktivera JIT-åtkomst. Mer information om hur du publicerar ditt första hanterade program på Azure Marketplace finns i [Azure Managed Applications på Marketplace](publish-marketplace-app.md).

För att stödja JIT-funktioner för ditt erbjudande lägger du till följande innehåll i CreateUiDefinition.jspå filen:

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
 
I "utdata":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> JIT-åtkomst är i för hands version. Schemat för JIT-konfiguration kan ändras i framtida iterationer.

## <a name="enable-jit-access"></a>Aktivera JIT-åtkomst

När du definierar ditt erbjudande i Marketplace, se till att aktivera JIT-åtkomst.

1. Logga in på [moln partnerns publicerings Portal](https://cloudpartner.azure.com).

1. Ange värden för att publicera det hanterade programmet på Marketplace. Välj **Ja** för **Aktivera JIT-åtkomst?**

   ![Aktivera just-in-Time-åtkomst](./media/request-just-in-time-access/marketplace-enable.png)

Du har lagt till ett konfigurations steg för JIT i användar gränssnittet och har aktiverat JIT-åtkomst i Marketplace-erbjudandet. När konsumenter distribuerar ditt hanterade program kan de [Aktivera JIT-åtkomst för instansen](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Begär åtkomst

När du behöver åtkomst till konsumentens hanterade resurser skickar du en begäran om en speciell roll, tid och varaktighet. Konsumenten måste sedan godkänna begäran.

Skicka en begäran om JIT-åtkomst:

1. Välj **JIT-åtkomst** för det hanterade programmet som du behöver åtkomst till.

1. Välj **berättigade roller**och välj **Aktivera** i kolumnen åtgärd för den roll som du vill använda.

   ![Aktivera åtkomstbegäran](./media/request-just-in-time-access/send-request.png)

1. I formuläret **Aktivera roll** väljer du start tid och varaktighet för att din roll ska vara aktiv. Välj **Aktivera** för att skicka begäran.

   ![Aktivera åtkomst](./media/request-just-in-time-access/activate-access.png) 

1. Visa meddelanden för att se att den nya JIT-begäran har skickats till konsumenten.

   ![Meddelande](./media/request-just-in-time-access/in-progress.png)

   Nu måste du vänta tills konsumenten har [godkänt din begäran](approve-just-in-time-access.md#approve-requests).

1. Om du vill visa status för alla JIT-begäranden för ett hanterat program väljer du **JIT-åtkomst** och **begär ande historik**.

   ![Visa status](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Kända problem

Ägar-ID för det konto som begär JIT-åtkomst måste uttryckligen tas med i definitionen för hanterade program. Kontot får inte ingå i en grupp som har angetts i paketet. Den här begränsningen kommer att åtgärdas i en framtida version.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om att godkänna begär Anden om JIT-åtkomst, se [Godkänn just-in-Time-åtkomst i Azure Managed Applications](approve-just-in-time-access.md).
