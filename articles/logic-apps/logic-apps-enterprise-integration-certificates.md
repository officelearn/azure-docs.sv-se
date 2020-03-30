---
title: Säkra B2B-meddelanden med certifikat
description: Lägga till certifikat som hjälper till att skydda B2B-meddelanden i Azure Logic Apps med Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: c1b48ae8191e2e5313d9037c791eca73c8a55691
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191387"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>Förbättra säkerheten för B2B-meddelanden med hjälp av certifikat

När du behöver hålla B2B-kommunikationen konfidentiell kan du öka säkerheten för B2B-kommunikation i dina företagsintegrationsappar, särskilt logikappar, genom att lägga till certifikat i ditt integrationskonto. Certifikat är digitala dokument som kontrollerar identiteterna för deltagarna i elektronisk kommunikation och hjälper dig att säkra kommunikationen på följande sätt:

* Kryptera meddelandeinnehåll.
* Signera meddelanden digitalt.

Du kan använda dessa certifikat i företagets integrationsappar:

* [Offentliga certifikat](https://en.wikipedia.org/wiki/Public_key_certificate), som du måste köpa från en offentlig [internetcertifikatutfärdare (CA)](https://en.wikipedia.org/wiki/Certificate_authority) men inte kräver några nycklar. 

* Privata certifikat eller [*självsignerade certifikat*](https://en.wikipedia.org/wiki/Self-signed_certificate), som du skapar och utfärdar själv men också kräver privata nycklar. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Ladda upp ett offentligt certifikat

Om du vill använda ett *offentligt certifikat* i logikappar som har B2B-funktioner måste du först överföra certifikatet till ditt integrationskonto. När du har definierat egenskaperna i de [avtal](logic-apps-enterprise-integration-agreements.md) som du skapar är certifikatet tillgängligt för att skydda dina B2B-meddelanden.

1. Logga in på [Azure-portalen](https://portal.azure.com). På huvudmenyn i Azure väljer du **Alla resurser**. I sökrutan anger du namnet på ditt integrationskonto och väljer sedan önskat integrationskonto.

   ![Hitta och välj ditt integrationskonto](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Under **Komponenter**väljer du panelen **Certifikat.**

   ![Välj "Certifikat"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Under **Certifikat**väljer du **Lägg till**. Ange dessa uppgifter för certifikatet under **Lägg till certifikat.** När du är klar väljer du **OK**.

   | Egenskap | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | <*certifikatnamn*> | Certifikatets namn, som är "publicCert" i det här exemplet | 
   | **Typ av certifikat** | Offentlig | Certifikatets typ |
   | **Certifikat** | <*namn på certifikat-fil*> | Om du vill söka efter och markera den certifikatfil som du vill överföra väljer du mappikonen bredvid rutan **Certifikat.** |
   ||||

   ![Välj "Lägg till", ange certifikatinformation](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   När Azure har validerat ditt val laddar Azure upp ditt certifikat.

   ![Azure visar nytt certifikat](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Ladda upp ett privat certifikat

Om du vill använda ett *privat certifikat* i logikappar som har B2B-funktioner måste du först överföra certifikatet till ditt integrationskonto. Du måste också ha en privat nyckel som du först lägger till i [Azure Key Vault](../key-vault/key-vault-get-started.md). 

När du har definierat egenskaperna i de [avtal](logic-apps-enterprise-integration-agreements.md) som du skapar är certifikatet tillgängligt för att skydda dina B2B-meddelanden.

> [!NOTE]
> För privata certifikat kontrollerar du att du lägger till ett motsvarande offentligt certifikat som visas i [AS2-avtalets](logic-apps-enterprise-integration-as2.md) inställningar för **skicka och ta emot** för signering och kryptering av meddelanden.

1. [Lägg till din privata nyckel i Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) och ange ett **nyckelnamn**.
   
2. Auktorisera Azure Logic Apps för att utföra åtgärder på Azure Key Vault. Om du vill bevilja åtkomst till logic apps-tjänstens huvudnamn använder du kommandot PowerShell, [Set-AzKeyVaultAccessPolicy,](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)till exempel:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Logga in på [Azure-portalen](https://portal.azure.com). På huvudmenyn i Azure väljer du **Alla resurser**. I sökrutan anger du namnet på ditt integrationskonto och väljer sedan önskat integrationskonto.

   ![Hitta ditt integrationskonto](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Under **Komponenter**väljer du panelen **Certifikat.**  

   ![Välj panelen Certifikat](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Under **Certifikat**väljer du **Lägg till**. Ange dessa uppgifter för certifikatet under **Lägg till certifikat.** När du är klar väljer du **OK**.

   | Egenskap | Värde | Beskrivning | 
   |----------|-------|-------------|
   | **Namn** | <*certifikatnamn*> | Ditt certifikats namn, som är "privateCert" i det här exemplet | 
   | **Typ av certifikat** | Privat | Certifikatets typ |
   | **Certifikat** | <*namn på certifikat-fil*> | Om du vill söka efter och markera den certifikatfil som du vill överföra väljer du mappikonen bredvid rutan **Certifikat.** När du använder ett nyckelvalv för den privata nyckeln blir den uppladdade filen det offentliga certifikatet. | 
   | **Resursgrupp** | <*integration-konto-resurs-grupp*> | Ditt integrationskontos resursgrupp, som är "MyResourceGroup" i det här exemplet | 
   | **Key Vault** | <*nyckel-valv-namn*> | Namnet på Ditt Azure-nyckelvalv |
   | **Nyckelnamn** | <*nyckelnamn*> | Nyckelns namn |
   ||||

   ![Välj "Lägg till", ange certifikatinformation](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   När Azure har validerat ditt val laddar Azure upp ditt certifikat.

   ![Azure visar nytt certifikat](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Nästa steg

* [Skapa ett B2B-avtal](logic-apps-enterprise-integration-agreements.md)
