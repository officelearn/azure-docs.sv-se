---
title: Skydda B2B-meddelanden med certifikat – Azure Logic Apps | Microsoft Docs
description: Lägg till certifikat för att skydda B2B-meddelanden i Azure Logic Apps med Enterprise-integrationspaket
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
ms.openlocfilehash: be3dbc386b0ac89a85bc1719081df944523c28ce
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997825"
---
# <a name="secure-b2b-messages-with-certificates"></a>Skydda B2B-meddelanden med certifikat

När du behöver hålla B2B-kommunikation konfidentiellt kan du skydda B2B-kommunikationen för dina företags integrerings program, särskilt Logic Apps, genom att lägga till certifikat till ditt integrations konto. Certifikat är digitala dokument som kontrollerar identiteter för deltagarna i elektronisk kommunikation och hjälper dig att säkra kommunikationen på följande sätt:

* Kryptera meddelande innehåll.
* Signera meddelanden digitalt. 

Du kan använda dessa certifikat i dina företags integrerings appar:

* [Offentliga certifikat](https://en.wikipedia.org/wiki/Public_key_certificate), som du måste köpa från en offentlig Internet [certifikat utfärdare (ca)](https://en.wikipedia.org/wiki/Certificate_authority) , men som inte kräver några nycklar. 

* Privata certifikat eller [*självsignerade certifikat*](https://en.wikipedia.org/wiki/Self-signed_certificate), som du skapar och utfärdar själv, men som även kräver privata nycklar. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Ladda upp ett offentligt certifikat

Om du vill använda ett *offentligt certifikat* i Logi Kap par som har B2B-funktioner måste du först ladda upp certifikatet till ditt integrations konto. När du har definierat egenskaperna i de [avtal](logic-apps-enterprise-integration-agreements.md) som du skapar är certifikatet tillgängligt för att hjälpa dig att skydda dina B2B-meddelanden.

1. Logga in på [Azure Portal](https://portal.azure.com). På huvud menyn i Azure väljer du **alla resurser**. I sökrutan anger du integrations kontots namn och väljer sedan det integrations konto som du vill använda.

   ![Hitta och välj ditt integrations konto](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Under **komponenter**väljer du panelen **certifikat** .

   ![Välj certifikat](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Under **certifikat**väljer du **Lägg till**. Under **Lägg till certifikat**, ange den här informationen för ditt certifikat. När du är klar väljer du **OK**.

   | Egenskap | Value | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | <*certificate-name*> | Certifikatets namn, som är "publicCert" i det här exemplet | 
   | **Certifikat typ** | Offentligt | Ditt certifikats typ |
   | **Certifikat** | <*certificate-file-name*> | Om du vill söka efter och välja den certifikat fil som du vill överföra väljer du mappikonen bredvid rutan **certifikat** . |
   ||||

   ![Välj "Lägg till", ange certifikat information](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   När Azure har verifierat ditt val laddar Azure upp ditt certifikat.

   ![Azure visar nytt certifikat](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Ladda upp ett privat certifikat

Om du vill använda ett *privat certifikat* i Logi Kap par som har B2B-funktioner måste du först ladda upp certifikatet till ditt integrations konto. Du måste också ha en privat nyckel som du först lägger till i [Azure Key Vault](../key-vault/key-vault-get-started.md). 

När du har definierat egenskaperna i de [avtal](logic-apps-enterprise-integration-agreements.md) som du skapar är certifikatet tillgängligt för att hjälpa dig att skydda dina B2B-meddelanden.

> [!NOTE]
> För privata certifikat, se till att du lägger till ett motsvarande offentligt certifikat som visas i [AS2](logic-apps-enterprise-integration-as2.md) -avtalets inställningar för **skicka och ta emot** för signering och kryptering av meddelanden.

1. [Lägg till din privata nyckel i Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) och ange ett **nyckel namn**.
   
2. Auktorisera Azure Logic Apps att utföra åtgärder på Azure Key Vault. Om du vill bevilja åtkomst till Logic Apps tjänstens huvud namn använder du PowerShell [-kommandot Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), till exempel:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Logga in på [Azure Portal](https://portal.azure.com). På huvud menyn i Azure väljer du **alla resurser**. I sökrutan anger du integrations kontots namn och väljer sedan det integrations konto som du vill använda.

   ![Hitta ditt integrations konto](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Under **komponenter**väljer du panelen **certifikat** .  

   ![Välj panelen certifikat](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Under **certifikat**väljer du **Lägg till**. Under **Lägg till certifikat**, ange den här informationen för ditt certifikat. När du är klar väljer du **OK**.

   | Egenskap | Value | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | <*certificate-name*> | Certifikatets namn, som är "privateCert" i det här exemplet | 
   | **Certifikat typ** | Privat | Ditt certifikats typ |
   | **Certifikat** | <*certificate-file-name*> | Om du vill söka efter och välja den certifikat fil som du vill överföra väljer du mappikonen bredvid rutan **certifikat** . När du använder ett nyckel valv för den privata nyckeln är den överförda filen det offentliga certifikatet. | 
   | **Resursgrupp** | <*integration-account-resource-group*> | Integrations kontots resurs grupp, som är "MyResourceGroup" i det här exemplet | 
   | **Key Vault** | <*Key-valv-namn*> | Ditt Azure Key Vault-namn |
   | **Nyckel namn** | <*nyckel namn*> | Nyckelns namn |
   ||||

   ![Välj "Lägg till", ange certifikat information](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   När Azure har verifierat ditt val laddar Azure upp ditt certifikat.

   ![Azure visar nytt certifikat](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Nästa steg

* [Skapa ett B2B-avtal](logic-apps-enterprise-integration-agreements.md)
