---
title: Säkra B2B-meddelanden med certifikat – Azure Logic Apps | Microsoft Docs
description: Lägg till certifikat för säker B2B-meddelanden i Azure Logic Apps med Enterprise-Integrationspaketet
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 5ae69d365a183f7d2a219d853241e73c1e27212b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054399"
---
# <a name="secure-b2b-messages-with-certificates"></a>Säkra B2B-meddelanden med certifikat

När du behöver B2B-kommunikation som konfidentiell, kan du skydda B2B-kommunikation för ditt företag integrationsprogram, särskilt logikappar, genom att lägga till certifikat i ditt integrationskonto. Certifikat är digitalt dokument som kontrollera identiteter för deltagarna i elektronisk kommunikation och hjälper dig att skydda kommunikation på följande sätt:

* Kryptera meddelandeinnehåll.
* Signera meddelanden. 

Du kan använda dessa certifikat i dina appar för enterprise-integration:

* [Offentliga certifikat](https://en.wikipedia.org/wiki/Public_key_certificate), som du måste köpa från en offentligt internet [certifikatutfärdare (CA)](https://en.wikipedia.org/wiki/Certificate_authority) men som inte behöver några nycklar. 

* Privata certifikat eller [ *självsignerade certifikat*](https://en.wikipedia.org/wiki/Self-signed_certificate), som du skapar och skicka dig själv men kräver även privata nycklar. 

## <a name="upload-a-public-certificate"></a>Ladda upp ett offentligt certifikat

Du använder en *offentligt certifikat* i logic apps som har B2B-funktioner måste du först överför certifikatet till ditt integrationskonto. När du har definierat egenskaperna i den [avtal](logic-apps-enterprise-integration-agreements.md) att du skapar kan certifikatet användas för att skydda dina B2B-meddelanden.

1. Logga in på [Azure Portal](https://portal.azure.com). Välj på Azure-huvudmenyn **alla resurser**. Ange namnet på ditt integrering i sökrutan och markera integrationskontot som du vill.

   ![Hitta och välj ditt integrationskonto](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Under **komponenter**, Välj den **certifikat** panelen.

   ![Välj ”certifikat”](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Under **certifikat**, Välj **Lägg till**. Under **Lägg till certifikat**, ange den här informationen för ditt certifikat. När du är klar väljer **OK**.

   | Egenskap  | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | <*certifikatnamn*> | Namn på certifikat som är ”publicCert” i det här exemplet | 
   | **Certifikattyp** | Offentligt | Din certifikattyp |
   | **Certifikat** | <*certifikat-filnamn*> | För att hitta och markera den certifikatfil du vill ladda upp, Välj mappikonen bredvid den **certifikat** box. |
   ||||

   ![Välj ”Lägg till”, ange information om certifikat](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   När Azure verifierar ditt val, Azure laddar upp certifikatet.

   ![Azure visar nytt certifikat](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Ladda upp ett privat certifikat

Du använder en *privata certifikat* i logic apps som har B2B-funktioner måste du först överför certifikatet till ditt integrationskonto. Du måste också ha en privat nyckel som du först lägga till [Azure Key Vault](../key-vault/key-vault-get-started.md). 

När du har definierat egenskaperna i den [avtal](logic-apps-enterprise-integration-agreements.md) att du skapar kan certifikatet användas för att skydda dina B2B-meddelanden.

> [!NOTE]
> Privata certifikat, se till att du lägger till ett motsvarande offentliga certifikat som visas i den [AS2-avtal](logic-apps-enterprise-integration-as2.md) **skicka och ta emot** inställningar för signering och kryptering av meddelanden.

1. [Lägg till din privata nyckel till Azure Key Vault](../key-vault/key-vault-get-started.md#add) och ger en **nyckelnamn**.
   
2. Auktorisera Azure Logic Apps för att utföra åtgärder på Azure Key Vault. Använda PowerShell-kommando för att bevilja åtkomst till Logic Apps-tjänstobjektet [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), till exempel:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Logga in på [Azure Portal](https://portal.azure.com). Välj på Azure-huvudmenyn **alla resurser**. Ange namnet på ditt integrering i sökrutan och markera integrationskontot som du vill.

   ![Hitta ditt integrationskonto](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Under **komponenter**, Välj den **certifikat** panelen.  

   ![Välj panelen certifikat](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Under **certifikat**, Välj **Lägg till**. Under **Lägg till certifikat**, ange den här informationen för ditt certifikat. När du är klar väljer **OK**.

   | Egenskap  | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | <*certifikatnamn*> | Namn på certifikat som är ”privateCert” i det här exemplet | 
   | **Certifikattyp** | Privat | Din certifikattyp |
   | **Certifikat** | <*certifikat-filnamn*> | För att hitta och markera den certifikatfil du vill ladda upp, Välj mappikonen bredvid den **certifikat** box. | 
   | **Resursgrupp** | <*Integration-konto-resursgrupp*> | Ditt integrationskonto resursgrupp, som är ”MyResourceGroup” i det här exemplet | 
   | **Key Vault** | <*Key-vault-namn*> | Namn på din Azure key vault |
   | **Nyckelnamn** | <*nyckel-name*> | Namn på din nyckel |
   ||||

   ![Välj ”Lägg till”, ange information om certifikat](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   När Azure verifierar ditt val, Azure laddar upp certifikatet.

   ![Azure visar nytt certifikat](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Nästa steg

* [Skapa en B2B-avtal](logic-apps-enterprise-integration-agreements.md)
