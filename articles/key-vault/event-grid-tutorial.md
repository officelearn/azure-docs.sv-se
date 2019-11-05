---
title: Ta emot och svara på meddelanden om nyckel valv med Azure Event Grid
description: Lär dig hur du integrerar Key Vault med Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b24da4d988554da240baba2984df44ff4744aaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464100"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Gör så här: ta emot och svara på meddelanden om nyckel valv med Azure Event Grid (för hands version)

Key Vault integration med Azure Event Grid, som för närvarande finns i för hands version, gör det möjligt för användare att meddelas när statusen för en hemlighet som lagras i Key Vault har ändrats. En översikt över funktionen finns i [övervaknings Key Vault med Azure Event Grid](event-grid-overview.md).

I den här guiden visas hur du tar emot Key Vault-meddelanden via Azure Event Grid och hur du svarar på status ändringar med Azure Automation.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Ett nyckel valv i din Azure-prenumeration. Du kan snabbt skapa ett nytt nyckel valv genom att följa stegen i [Ange och hämta en hemlighet från Azure Key Vault med hjälp av Azure CLI](quick-create-cli.md)

## <a name="concepts"></a>Koncept

Azure Event Grid är en händelsetjänst för molnet. I den här guiden ska du prenumerera på händelser för Key Vault och dirigera händelser till Azure Automation. När en av hemligheterna i nyckel valvet håller på att gå ut får Event Grid ett meddelande om status ändringen och gör ett HTTP-inlägg till slut punkten. En webbhook utlöser sedan en Azure Automation körning av PowerShell-skript.

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

Skapa ett Azure Automation konto via [Azure Portal](https://portal.azure.com).

1.  Gå till portal.azure.com och logga in på din prenumeration.

1.  I rutan Sök skriver du i "Automation-konton".

1.  Under avsnittet tjänster i list rutan från Sök fältet väljer du Automation-konton.

1.  Klicka på Add (Lägg till).

    ![](media/image2.png)

1.  Fyll i den information som krävs på bladet "Lägg till Automation-konto" och välj "skapa".

## <a name="create-a-runbook"></a>Skapa en Runbook

När Azure Automation kontot är klart skapar du en Runbook.

![](media/image3.png)

1.  Välj det Automation-konto som du nyss skapade.

1.  Välj "Runbooks" under avsnittet process automatisering.

1.  Klicka på "skapa en Runbook".

1.  Namnge din Runbook och välj PowerShell som Runbook-typ.

1.  Klicka på den Runbook som du skapade och välj knappen Redigera.

1.  Ange följande kod (i test syfte) och klicka på knappen "publicera". Resultatet av POST-begäran har tagits emot.

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

![](media/image4.png)

## <a name="create-a-webhook"></a>Skapa en webhook

Nu ska du skapa en webhook för att utlösa din nyligen skapade Runbook.

1.  Välj "Webhooks" från avsnittet resurser i den Runbook som du nyss publicerat.

1.  Klicka på Lägg till webhook.

    ![](media/image5.png)

1.  Välj "Skapa ny webhook".

1. Namnge webhooken, ange ett förfallo datum och **Kopiera URL:** en.

    > [!IMPORTANT] 
    > Du kan inte Visa URL: en när du har skapat den. Se till att spara en kopia av en säker plats där du kan komma åt den för resten av den här guiden.

1. Klicka på parametrar och kör inställningar och välj OK. Ange inga parametrar. Då aktive ras knappen "skapa".

1. Välj "OK" och välj "skapa".

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Skapa en Event Grid-prenumeration

Skapa en Event Grid-prenumeration via [Azure Portal](https://portal.azure.com).

1.  Öppna Azure Portal med hjälp av följande länk: https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  Gå till ditt nyckel valv och välj fliken "händelser". Om du inte kan se fliken händelser kontrollerar du att du använder för [hands versionen av portalen](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![](media/image7.png)

1.  Klicka på knappen "+ händelse prenumeration".

1.  Skapa ett beskrivande namn för prenumerationen.

1.  Välj Event Grid schema.

1.  "Ämnes resurs" ska vara det nyckel valv som du vill övervaka för status ändringar.

1.  För "filtrera till händelse typer" lämnar du alla markerade ("9 markerade").

1.  För "slut punkts typ" väljer du webhook.

1.  Välj "Välj en slut punkt". I fönstret ny kontext klistrar du in webhook-URL: en från [skapa en webhook](#create-a-webhook) -åtgärd i fältet "prenumerantens slut punkt".

1.  Välj "Bekräfta val" i kontext fönstret.

1.  Välj "skapa".

    ![](media/image8.png)

## <a name="test-and-verify"></a>Testa och verifiera

Kontrol lera att din Event Grid-prenumeration har kon figurer ATS.  Det här testet förutsätter att du har prenumererat på meddelandet "hemlig ny version skapad" i [prenumerationen skapa en Event Grid](#create-an-event-grid-subscription)och att du har de behörigheter som krävs för att skapa en ny version av en hemlighet i ett nyckel valv.

![](media/image9.png)

![](media/image10.png)

1.  Gå till ditt nyckel valv på Azure Portal

1.  Skapa en ny hemlighet. I test syfte ställer du in förfallo datum till nästa dag.

1.  Gå till fliken händelser i ditt nyckel valv.

1.  Välj den Event Grid-prenumeration som du har skapat.

1.  Se om en händelse har registrerats under mått. Två händelser förväntas: SecretNewVersion och SecretNearExpiry. Detta validerar att event Grid har infångat status ändringen för hemligheten i ditt nyckel valv.

    ![](media/image11.png)

1.  Gå till ditt Azure Automation-konto.

1.  Välj fliken Runbooks och välj den Runbook som du skapade.

1.  Välj fliken "Webhooks" och bekräfta att tidsstämpeln "senast utlöst" är inom 60 sekunder när du skapade den nya hemligheten.  Detta bekräftar att Event Grid gjort ett inlägg i webhooken med händelse information om status ändringen i ditt nyckel valv och webhooken utlöstes.

    ![](media/image12.png)

1. Gå tillbaka till din Runbook och välj fliken "Översikt".

1. Titta på listan senaste jobb. Du bör se att ett jobb har skapats och att statusen är slutförd.  Detta bekräftar att webhooken utlöste runbooken för att börja köra skriptet.

    ![](media/image13.png)

1. Välj det senaste jobbet och titta på POST-begäran som skickades från Event Grid till webhooken. Undersök JSON och se till att parametrarna för nyckel valvet och händelse typen är korrekta. Om parametern "händelse typ" i JSON-objektet matchar händelsen som ingick i nyckel valvet (i det här exemplet Microsoft. Key Vault. SecretNearExpiry) lyckades testet.

## <a name="troubleshooting"></a>Felsökning

### <a name="unable-to-create-event-subscription"></a>Det gick inte att skapa händelse prenumeration

Registrera Event Grid-och Key Vault-providern i Azure-prenumerationens resurs leverantörer. Se [leverantörer och typer av Azure-resurser](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Nästa steg

Grattis! Om du har följt alla steg ovan är du nu redo för att program mässigt svara på status ändringar för hemligheter som lagras i ditt nyckel valv.

Om du har använt ett avsöknings system för att leta efter status ändringar för hemligheter i dina nyckel valv, migrera till att använda den här meddelande funktionen. Du kan också ersätta test skriptet i din Runbook med kod för att program mässigt förnya dina hemligheter när de håller på att gå ut.

Läs mer:

- [Översikt över Azure Key Vault](key-vault-overview.md)
- [Översikt över Azure Event Grid](../event-grid/overview.md)
- [Övervaka Key Vault med Azure Event Grid (förhands granskning)](event-grid-overview.md)
- [Azure Event Grid händelse schema för Azure Key Vault (förhands granskning)](../event-grid/event-schema-key-vault.md)
- [Översikt över Azure Automation](../automation/index.yml)
