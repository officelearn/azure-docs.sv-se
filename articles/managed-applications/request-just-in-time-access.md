---
title: Aktivera och begär just-in-time-åtkomst för Azure Managed Applications
description: Beskriver hur utgivare av Azure Managed Applications begär just-in-time-åtkomst till ett hanterat program.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481781"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Aktivera och begär just-in-time-åtkomst för Azure Managed Applications

Användare av det hanterade programmet vara ovilliga att ge permanent åtkomst till den hanterade resursgruppen. Som utgivare av ett manager-program, kanske du föredrar att kunderna ser exakt när du behöver åtkomst till de hanterade resurserna. Om du vill ge användare innehåller större kontroll över beviljar åtkomst till hanterade resurser, Azure Managed Applications en funktion som kallas just-in-time (JIT) åtkomst, vilket är en förhandsversion.

JIT-åtkomst till kan du begära utökad åtkomst till resurser i ett hanterat program för felsökning eller underhåll. Du har alltid skrivskyddad åtkomst till resurser, men du kan ha större åtkomst för en viss tidsperiod.

Arbetsflöde för att bevilja åtkomst är:

1. Du lägger till ett hanterat program på Marketplace och ange att JIT-åtkomst till är tillgängliga.

1. Under distributionen kan konsumenten JIT-åtkomst för den instansen av det hanterade programmet.

1. Konsumenten kan ändra inställningarna för JIT-åtkomst till efter distributionen.

1. Du kan skicka en begäran om åtkomst när du behöver felsöka eller uppdatera hanterade resurser.

1. Konsumenten godkänner din begäran.

Den här artikeln fokuserar på de åtgärder som utgivare vidta för att aktivera JIT-åtkomst och skicka begäran. Läs om hur du godkänner JIT förfrågningar i [godkänna just-in-time-åtkomst i Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Lägg till steg för JIT-åtkomst till Gränssnittet

CreateUiDefinition.json-filen är likadant som filen Användargränssnittet du skapar för permanent åtkomst, förutom måste du inkludera ett steg som låter användare aktivera JIT-åtkomst. Mer information om hur du publicerar ditt första hanterade program i Azure Marketplace finns [Azure Managed Applications i Marketplace](publish-marketplace-app.md).

För att stödja JIT-funktion för ditt erbjudande, lägger du till följande innehåll i filen CreateUiDefinition.json:

I ”steg”:

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
 
I ”utdata”:

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> JIT-åtkomst till genomgår förhandsgranskning. Schemat för JIT-konfiguration kan ändras i framtida iterationer.

## <a name="enable-jit-access"></a>Aktivera JIT-åtkomst

När du definierar ditt erbjudande i marketplace måste du kontrollera att du aktiverar JIT-åtkomst.

1. Logga in på den [Molnpartner publiceringsportalen](https://cloudpartner.azure.com).

1. Ange värden för att publicera det hanterade programmet i marketplace. Välj **Ja** för **aktivera JIT-åtkomst?**

   ![Aktivera just-in-time-åtkomst](./media/request-just-in-time-access/marketplace-enable.png)

Du har lagt till ett JIT-konfigurationssteg i Användargränssnittet och har aktiverat JIT-åtkomst till i marketplace-erbjudande. När kunder distribuerar det hanterade programmet, kan de [aktivera JIT-åtkomst för deras instans](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Begär åtkomst

När du behöver komma åt konsumentens hanterade resurser kan skicka du en begäran om en viss roll, tid och varaktighet. Konsumenten måste sedan godkänna begäran.

Så här skickar en begäran för JIT-åtkomst:

1. Välj **JIT-åtkomst till** för det hanterade programmet som du behöver komma åt.

1. Välj **berättigade roller**, och välj **aktivera** i kolumnen åtgärder för den roll som du vill använda.

   ![Aktivera begäran om åtkomst](./media/request-just-in-time-access/send-request.png)

1. På den **aktivera rollen** formuläret, Välj en starttid och varaktighet för din roll ska aktiveras. Välj **aktivera** att skicka din begäran.

   ![Aktivera åtkomst till](./media/request-just-in-time-access/activate-access.png) 

1. Visa meddelanden att se att den nya JIT-förfrågan har skickats till konsumenten.

   ![Avisering](./media/request-just-in-time-access/in-progress.png)

   Nu kan du måste vänta tills användaren till [godkänna din begäran](approve-just-in-time-access.md#approve-requests).

1. Om du vill visa status för alla JIT-begäranden för ett hanterat program, Välj **JIT-åtkomst till** och **begär historik**.

   ![Visa status](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Kända problem

Ägar-ID för det konto som begär åtkomst till JIT måste uttryckligen inkluderas i definitionen för hanterade programmet. Kontot kan inte bara inkluderas genom en grupp som har angetts i paketet. Den här begränsningen kommer att åtgärdas i en framtida version.

## <a name="next-steps"></a>Nästa steg

Läs om hur du godkänner begäranden om JIT-åtkomst i [godkänna just-in-time-åtkomst i Azure Managed Applications](approve-just-in-time-access.md).
