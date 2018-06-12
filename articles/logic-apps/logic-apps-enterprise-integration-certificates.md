---
title: Säkra B2B-meddelanden med certifikat i Azure Logic Apps | Microsoft Docs
description: Lägg till certifikat för säker B2B-meddelanden med Enterprise-Integrationspaket
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 7ba76a15792fe40b2a628b030f06930641d197a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299342"
---
# <a name="secure-b2b-messages-with-certificates"></a>Säkra B2B-meddelanden med certifikat

Ibland behöver konfidentiell B2B-kommunikation. Du kan lägga till certifikat till ditt konto för integrering för att hjälpa dig att skydda B2B-kommunikation för ditt företag integrationsappar, särskilt logic apps. Certifikat är digitalt dokument som bekräftar identiteten för deltagarna i elektronisk kommunikation.
Certifikat för att skydda kommunikationen på följande sätt:

* Kryptera meddelandeinnehåll
* Signera meddelanden  

Du kan använda dessa certifikat i dina enterprise integration-appar:

* Offentliga certifikat, som måste köpas från en certifikatutfärdare (CA).
* Privata certifikat kan du utfärda själv. Dessa certifikat kallas ibland självsignerade certifikat.

## <a name="upload-a-public-certificate"></a>Ladda upp ett offentligt certifikat

Att använda en *certifikat med offentlig* i logikappar som har B2B-funktioner, måste du först överföra certifikatet till ditt konto för integrering. När du har definierat egenskaperna i den [avtal](logic-apps-enterprise-integration-agreements.md) att du skapar, att certifikatet är tillgängligt för att skydda dina B2B-meddelanden.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. På Azure huvudmenyn, Välj **alla tjänster**. I sökrutan anger du ”integration” och välj sedan **integrationskonton**.

   ![Hitta integration-konto](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. Under **Integrationskonton**, väljer du det integration konto där du vill lägga till certifikatet.

   ![Välj integration kontot som du vill lägga till certifikatet](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. Välj den **certifikat** panelen.  

   ![Välj ”certifikat”](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. Under **certifikat**, Välj **Lägg till**.

   ![Välj ”Lägg till”](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. Under **Lägg till certifikat**, ange detaljer för certifikatet.
   
   1. Ange ditt certifikat **namn**. Certifikattyp, Välj **offentliga**.

   2. På höger sida av den **certifikat** väljer du mappikonen. 
   Hitta och markera den certifikatfil som du vill överföra. 
   När du är klar väljer **OK**.

      ![Överför offentliga certifikat](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure filöverföringar ditt certifikat när du verifierar ditt val.

   ![Finns det nya certifikatet](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>Ladda upp ett privat certifikat

Att använda en *privat certifikat* i logikappar som har B2B-funktioner, måste du först överföra certifikatet till ditt konto för integrering. Du måste också ha en privat nyckel som du först lägga till [Azure Key Vault](../key-vault/key-vault-get-started.md). 

När du har definierat egenskaperna i den [avtal](logic-apps-enterprise-integration-agreements.md) att du skapar, att certifikatet är tillgängligt för att skydda dina B2B-meddelanden.

> [!NOTE]
> För privata certifikat, se till att du lägger till ett motsvarande offentliga certifikat ska visas i den [AS2-avtal](logic-apps-enterprise-integration-as2.md) skicka och ta emot inställningar för signering och kryptering av meddelanden.

1. [Lägg till din privata nyckel till Azure Key Vault](../key-vault/key-vault-get-started.md#add) och ger en **nyckelnamn**.
   
2. Auktorisera Azure Logikappar att utföra åtgärder på Azure Key Vault. Använd PowerShell-kommando för att bevilja åtkomst till Logic Apps tjänstens huvudnamn [Set AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), till exempel:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Logga in på [Azure Portal](https://portal.azure.com).

4. På Azure huvudmenyn, Välj **alla tjänster**. I sökrutan anger du ”integration” och välj sedan **integrationskonton**.

   ![Hitta integration-konto](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. Under **Integrationskonton**, väljer du det integration konto där du vill lägga till certifikatet.

6. Välj den **certifikat** panelen.  

   ![Välj panelen certifikat](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. Under **certifikat**, Välj **Lägg till**.   

   ![Välj knappen Lägg till](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. Under **Lägg till certifikat**, ange detaljer för certifikatet.
   
   1. Ange ditt certifikat **namn**. Certifikattyp, Välj **privata**.

   2. På höger sida av den **certifikat** väljer du mappikonen. 
   Hitta och markera den certifikatfil som du vill överföra. 
   Markera också den **resursgruppen**, **Nyckelvalvet**, och **nyckelnamn**. 
   När du är klar väljer **OK**.

      ![Lägg till certifikat](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure filöverföringar ditt certifikat när du verifierar ditt val.

   ![Finns det nya certifikatet](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa en B2B-avtal](logic-apps-enterprise-integration-agreements.md)
