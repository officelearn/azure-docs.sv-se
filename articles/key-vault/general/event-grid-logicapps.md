---
title: E-post när Key Vault-status för de hemliga ändringarna
description: Guide för att använda Logic Apps för att svara på Key Vault hemligheter ändringar
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 53e8586486d9a9ebf870de350d5607f58977c0f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423280"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Använd Logic Apps för att få e-post om statusändringar av nyckelvalvshemligheter

I den här guiden får du lära dig hur du svarar på Azure Key Vault-händelser som tas emot via [Azure Event Grid](../../event-grid/index.yml) med hjälp av Azure Logic [Apps](../../logic-apps/index.yml). I slutet har du en Azure logic app inställd för att skicka ett e-postmeddelande varje gång en hemlighet skapas i Azure Key Vault.

En översikt över integreringen av Azure Key Vault/Azure Event Grid finns i [Övervaka nyckelvalv med Azure Event Grid (förhandsversion).](event-grid-overview.md)

## <a name="prerequisites"></a>Krav

- Ett e-postkonto från alla e-postleverantörer som stöds av Azure Logic Apps (till exempel Office 365 Outlook). Det här e-postkontot används för att skicka händelsemeddelandena. En fullständig lista över Logic App-anslutningsprogram som stöds finns i [Översikt över anslutningsappar](/connectors)
- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.
- Ett nyckelvalv i din Azure-prenumeration. Du kan snabbt skapa ett nytt nyckelvalv genom att följa stegen i [Ange och hämta en hemlighet från Azure Key Vault med Azure CLI](../secrets/quick-create-cli.md).

## <a name="create-a-logic-app-via-event-grid"></a>Skapa en logikapp via händelserutnät

Skapa först Logic App med händelserutnätshanterare och prenumerera på Azure Key Vault "SecretNewVersionCreated"-händelser.

Så här skapar du en Azure Event Grid-prenumeration:

1. Gå till nyckelvalvet i Azure-portalen, välj **Händelser > Komma igång** och klicka på **Logikappar**

    
    ![Key Vault - evenemang sida](../media/eventgrid-logicapps-kvsubs.png)

1. På **Logic Apps Designer** validera anslutningen och klicka på **Fortsätt** 
 
    ![Logic App Designer - anslutning](../media/eventgrid-logicappdesigner1.png)

1. Gör följande på skärmen **När en resurshändelse inträffar:**
    - Lämna **prenumerations-** och **resursnamn** som standard.
    - Välj **Microsoft.KeyVault.vaults** för **resurstypen**.
    - Välj **Microsoft.KeyVault.SecretNewVersionSkaperad** för **händelsetypsobjekt - 1**.

    ![Logic App Designer - händelsehanterare](../media/eventgrid-logicappdesigner2.png)

1. Välj **+ Nytt steg** Detta öppnar ett fönster för att välja en åtgärd.
1. Sök efter **E-post**. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Den här självstudien använder **Office 365 Outlook**. Stegen för andra e-postleverantörer liknar dessa.
1. Välj åtgärden **Skicka ett e-postmeddelande (V2).**

   ![Logic App Designer - lägg till e-post](../media/eventgrid-logicappdesigner3.png)

1. Skapa din e-postmall:
    - **Till:** Ange e-postadressen för att få e-postmeddelandena. I den här självstudiekursen använder du ett e-postkonto som du kan komma åt för testning.
    - **Ämne** och **Brödtext**: Skriv e-postmeddelandets text. Välj JSON-egenskaper från valverktyget för att ta med dynamiskt innehåll baserat på händelsedata. Du kan hämta data för `@{triggerBody()?['Data']}`händelsen med .

    Din e-postmall kan se ut så här.

    ![Logic App Designer - lägg till e-post](../media/eventgrid-logicappdesigner4.png)

8. Klicka på **Spara som**.
9. Ange ett **namn** för den nya logikappen och klicka på **Skapa**.
    
    ![Logic App Designer - lägg till e-post](../media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Testa och verifiera

1.  Gå till nyckelvalvet på Azure-portalen och välj **Händelser > Event-prenumerationer**.  Kontrollera att en ny prenumeration har skapats
    
    ![Logic App Designer - lägg till e-post](../media/eventgrid-logicapps-kvnewsubs.png)

1.  Gå till nyckelvalvet, välj **Hemligheter**och välj **+ Generera/importera**. Skapa en ny hemlighet för testningsändamål namnger nyckeln och behåller de återstående parametrarna i standardinställningarna.

    ![Key Vault - Skapa hemliga](../media/eventgrid-logicapps-kv-create-secret.png)

1. På skärmen **Skapa ett hemligt** anger du vilket namn som helst, vilket värde som helst och väljer **Skapa**.

När hemligheten skapas tas ett e-postmeddelande emot på de konfigurerade adresserna.

## <a name="next-steps"></a>Nästa steg

- Översikt: [Övervaka nyckelvalv med Azure Event Grid (förhandsversion)](event-grid-overview.md)
- Så [här: Dirigera nyckelvalvsmeddelanden till Azure Automation](event-grid-tutorial.md).
- [Azure Event Grid-händelseschema för Azure Key Vault (förhandsversion)](../../event-grid/event-schema-key-vault.md)
- Läs mer om [Azure Event Grid](../../event-grid/index.yml).
- Läs mer om [Logic Apps-funktionen i Azure App Service](../../logic-apps/index.yml).
