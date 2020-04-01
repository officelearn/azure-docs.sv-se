---
title: Ta emot och svara på viktiga meddelande om nyckelvalv med Azure Event Grid
description: Lär dig hur du integrerar Key Vault med Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 5eaf4cf702e56df932a61ab277dff6b34d97854d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78185037"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Ta emot och svara på viktiga meddelande om nyckelvalv med Azure Event Grid (förhandsversion)

Azure Key Vault-integrering med Azure Event Grid (för närvarande i förhandsversion) aktiverar användaravisering när statusen för en hemlighet som lagras i ett nyckelvalv har ändrats. En översikt över den här funktionen finns i [Övervaka nyckelvalv med händelserutnät](event-grid-overview.md).

Den här guiden beskriver hur du får Key Vault-meddelanden via Event Grid och hur du svarar på statusändringar via Azure Automation.

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.
- Ett nyckelvalv i din Azure-prenumeration. Du kan snabbt skapa ett nytt nyckelvalv genom att följa stegen i [Ange och hämta en hemlighet från Azure Key Vault med Azure CLI](quick-create-cli.md).

## <a name="concepts"></a>Begrepp

Event Grid är en eventing-tjänst för molnet. Genom att följa stegen i den här guiden prenumererar du på händelser för Key Vault och dirigerar händelser till Automation. När en av hemligheterna i nyckelvalvet håller på att upphöra att gälla meddelas Event Grid om statusändringen och gör ett HTTP-INLÄGG till slutpunkten. En webbkrok utlöser sedan en automationskörning av ett PowerShell-skript.

![HTTP POST-flödesschema](media/image1.png)

## <a name="create-an-automation-account"></a>Skapa ett Automation-konto

Skapa ett Automation-konto via [Azure-portalen:](https://portal.azure.com)

1.  Gå till portal.azure.com och logga in på din prenumeration.

1.  Ange **Automation-konton**i sökrutan .

1.  Välj **Automation-konton**under avsnittet **Tjänster** i listrutan i sökfältet .

1.  Välj **Lägg till**.

    ![Fönstret Automationskonton](media/image2.png)

1.  Ange nödvändig information i fönstret **Lägg till automationskonto** och välj sedan **Skapa**.

## <a name="create-a-runbook"></a>Skapa en runbook

När ditt Automation-konto är klart skapar du en runbook.

![Skapa ett runbook-användargränssnitt](media/image3.png)

1.  Välj det Automation-konto som du just skapade.

1.  Välj **Runbooks** under **Process Automation**.

1.  Välj **Skapa en runbook**.

1.  Namnge runbooken och välj **PowerShell** som runbooktyp.

1.  Markera runbooken som du skapade och välj sedan knappen **Redigera.**

1.  Ange följande kod (för testning) och välj knappen **Publicera.** Den här åtgärden returnerar resultatet av den mottagna POST-begäran.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Användargränssnitt för publicera runbook](media/image4.png)

## <a name="create-a-webhook"></a>Skapa en webhook

Skapa en webhook för att utlösa din nyskapade runbook.

1.  Välj **Webhooks** i avsnittet **Resurser** i den runbook som du just publicerade.

1.  Välj **Lägg till Webhook**.

    ![Knappen Lägg till Webhook](media/image5.png)

1.  Välj **Skapa ny Webhook**.

1. Namnge webhooken, ange ett utgångsdatum och kopiera webbadressen.

    > [!IMPORTANT] 
    > Du kan inte visa webbadressen när du har skapat den. Se till att du sparar en kopia på en säker plats där du kan komma åt den för resten av den här guiden.

1. Välj **Parametrar och kör inställningar** och välj sedan **OK**. Ange inga parametrar. Detta aktiverar knappen **Skapa.**

1. Välj **OK** och välj sedan **Skapa**.

    ![Skapa nytt webbkrokgränssnitt](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

Skapa en Event Grid-prenumeration via [Azure-portalen](https://portal.azure.com).

1.  Gå till nyckelvalvet och välj fliken **Händelser.** Om du inte kan se den kontrollerar du att du använder [förhandsgranskningsversionen av portalen](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![Fliken Händelser i Azure-portalen](media/image7.png)

1.  Välj knappen **Händelseprenumeration.**

1.  Skapa ett beskrivande namn för prenumerationen.

1.  Välj **schema för händelserutnät**.

1.  **Ämnesresurs** bör vara det nyckelvalv som du vill övervaka för statusändringar.

1.  För **Filter till händelsetyper**lämnar du alla alternativ markerade (**9 markerade**).

1.  Som **Typ av slutpunkt** väljer du **Webhook**.

1.  Välj **Välj en slutpunkt**. I det nya sammanhangsfönstret klistrar du in webhook-URL:en från steget [Skapa en webhook](#create-a-webhook) i fältet **Prenumerantslutpunkt.**

1.  Välj **Bekräfta markering** i kontextfönstret.

1.  Välj **Skapa**.

    ![Skapa händelseprenumeration](media/image8.png)

## <a name="test-and-verify"></a>Testa och verifiera

Kontrollera att din Event Grid-prenumeration är korrekt konfigurerad. Det här testet förutsätter att du prenumererar på meddelandet "Hemlig ny version skapad" i [prenumerationen Skapa ett eventrutnät](#create-an-event-grid-subscription)och att du har de behörigheter som krävs för att skapa en ny version av en hemlighet i ett nyckelvalv.

![Testa config av Event Grid-prenumeration](media/image9.png)

![Fönstret Skapa en hemlighet](media/image10.png)

1.  Gå till ditt nyckelvalv på Azure-portalen.

1.  Skapa en ny hemlighet. För testningsändamål anger du utgångsdatumet till nästa dag.

1.  På fliken **Händelser** i nyckelvalvet väljer du den Prenumeration i Event Grid som du skapade.

1.  Kontrollera om en händelse har fångats under **Mått.** Två händelser förväntas: SecretNewVersion och SecretNearExpiry. Dessa händelser verifierar att Event Grid har fångat statusändringen av hemligheten i nyckelvalvet.

    ![Fönstret Mätvärden: kontrollera om det finns infångade händelser](media/image11.png)

1.  Gå till ditt Automation-konto.

1.  Välj fliken **Runbooks** och välj sedan den runbook som du skapade.

1.  Välj fliken **Webhooks** och bekräfta att tidsstämpeln "senast utlöst" är inom 60 sekunder från när du skapade den nya hemligheten. Detta resultat bekräftar att Event Grid gjorde ett INLÄGG till webhook med händelseinformation om statusändringen i ditt nyckelvalv och att webhooken utlöstes.

    ![Fliken Webhooks, senast utlöst tidsstämpel](media/image12.png)

1. Gå tillbaka till din runbook och välj fliken **Översikt.**

1. Titta på listan **Senaste jobb.** Du bör se att ett jobb har skapats och att statusen är klar. Detta bekräftar att webhooken utlöste runbooken för att börja köra skriptet.

    ![Webhook Senaste jobb lista](media/image13.png)

1. Välj det senaste jobbet och titta på POST-begäran som skickades från Event Grid till webhook. Undersök JSON och se till att parametrarna för nyckelvalvet och händelsetypen är korrekta. Om parametern "händelsetyp" i JSON-objektet matchar den händelse som inträffade i nyckelvalvet (i det här exemplet Microsoft.KeyVault.SecretNearExpiry) lyckades testet.

## <a name="troubleshooting"></a>Felsökning

### <a name="you-cant-create-an-event-subscription"></a>Du kan inte skapa en händelseprenumeration

Registrera om Event Grid och nyckelvalvsleverantören i dina Azure-prenumerationsresursleverantörer. Se [Azure-resursleverantörer och -typer](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Nästa steg

Grattis! Om du har följt alla dessa steg korrekt är du nu redo att programmässigt svara på statusändringar av hemligheter som lagrats i nyckelvalvet.

Om du har använt ett avsökningsbaserat system för att söka efter statusändringar av hemligheter i dina nyckelvalv kan du nu börja använda den här meddelandefunktionen. Du kan också ersätta testskriptet i runbooken med kod för att programmässigt förnya dina hemligheter när de håller på att upphöra att gälla.

Läs mer:


- Översikt: [Övervaka nyckelvalv med Azure Event Grid (förhandsversion)](event-grid-overview.md)
- Så här får du [e-post när en nyckelvalvshemlighet ändras](event-grid-logicapps.md)
- [Azure Event Grid-händelseschema för Azure Key Vault (förhandsversion)](../event-grid/event-schema-key-vault.md)
- [Översikt över Azure Key Vault](key-vault-overview.md)
- [Översikt för Azure Event Grid](../event-grid/overview.md)
- [Översikt över Azure Automation](../automation/index.yml)
