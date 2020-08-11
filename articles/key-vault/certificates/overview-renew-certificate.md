---
title: Om förnyelse av Azure Key Vault certifikat
description: Om förnyelse av Azure Key Vault certifikat
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 41085ee629189c32c1bc7196f23805c9c48d154a
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056286"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>Om förnyelse av Azure Key Vault certifikat

Med Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat för nätverket och aktivera säker kommunikation för program. Mer allmän information om certifikat finns i [Azure Key Vault certifikat](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

Om du har ett kort levde certifikat eller ökar frekvensen för certifikat rotation begränsas angripare omfattning för skada.

## <a name="certificate-expiration-notifications"></a>Meddelanden om förfallo datum för certifikat
Börja med att lägga till en certifikat kontakt till din Key Vault för att bli informerad när certifikaten snart upphör att gälla (t. ex. Använd PowerShell [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)) den andra, konfigurera när du vill få ett meddelande om att certifikatet upphör att gälla. Följ anvisningarna [nedan](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate) om du vill konfigurera livs längds åtgärd

Det finns tre kategorier för att skapa certifikat i Key Vault. I den här guiden får du hjälp att förstå hur förnyelse av certifikat kan uppnås.
-   Certifikat som skapats med integrerad CA (DigiCert eller GlobalSign)
-   Certifikat som skapats med icke-integrerad certifikat utfärdare
-   Självsignerade certifikat

## <a name="renewal-of-integrated-ca-certificate"></a>Förnyelse av integrerat CA-certifikat 
Goda nyheter! Azure Key Vaults tar hand om ett slut punkt till slut punkts underhåll av certifikat som utfärdas av betrodda Microsoft-ca: er, t. ex. DigiCert och GlobalSign. Lär dig hur du [integrerar en betrodd certifikat utfärdare med Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority).

## <a name="renewal-of-non-integrated-ca-certificate"></a>Förnyelse av icke-integrerade CA-certifikat 
Azure Key Vault ger användarna möjlighet att importera certifikat från alla CA: er så att användarna kan integrera med flera Azure-resurser och göra distributionen enklare. Om du oroar dig för att förlora spårningen av ditt certifikat kommer att bli förfallen eller sämre än att ditt certifikat redan har gått ut. sedan kan Key Vault hjälpa dig att hålla dig uppdaterad. För icke-integrerade CA-certifikat tillåter Key Vault att användare konfigurerar snart utgångna e-postaviseringar. Dessa meddelanden kan också ställas in för flera användare.

För att förnya ett certifikat är det viktigt att förstå att ett Azure Key Vault-certifikat är ett versions objekt. Om den aktuella versionen upphör att gälla måste du skapa en ny version. Varje ny version är konceptuellt ett nytt certifikat som består av en nyckel och en blob som samverkar med nyckeln till en identitet. När du använder en icke-partner certifikat utfärdare kommer nyckel valvet att generera ett nyckel värde par och returnera CSR.

**Steg att följa i Azure Portal:-**
1.  Öppna det certifikat som du vill förnya.
2.  Välj **+ knappen ny version** på certifikat skärmen.
3.  Välj **certifikat åtgärd**
4.  Välj **Hämta CSR**. Då hämtas en. CSR-fil på din lokala enhet.
5.  Ta CSR till ditt val av CA för att signera begäran
6.  Ta tillbaka den signerade begäran och välj **sammanfoga CSR** på samma certifikat åtgärds skärm.

> [!NOTE]
> Det är viktigt att slå samman den signerade kund service representanten med samma CSR-begäran som skapades, annars stämmer inte nyckeln.

Stegen påminner om hur du skapar ett nytt certifikat och har dokumenterat mer information [här]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renewal-of-self-signed-certificate"></a>Förnyat självsignerat certifikat

Goda nyheter igen! Azure Key Vaults tar också hand om automatisk förnyelse av självsignerade certifikat för sina användare. Läs mer [här](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)om du vill veta mer om hur du ändrar utgivnings principen och uppdaterar certifikatets attribut för livs längds åtgärder.

### <a name="troubleshoot"></a>Felsöka
Om certifikatet som har utfärdats är inaktiverat i Azure Portal kan du gå vidare och Visa certifikat åtgärden för att visa fel meddelandet för det certifikatet.

### <a name="frequently-asked-questions"></a>Vanliga frågor och svar
Kommer taggarna att replikeras efter automatisk förnyelse av certifikatet?
Nej, Taggar replikeras inte om inte användaren manuellt kopierar taggarna.

### <a name="see-also"></a>Se även
*   [Integrera nyckelvalv med DigiCert-certifikatutfärdare](how-to-integrate-certificate-authority.md)
*   [Självstudie: Konfigurera automatisk rotation av certifikat i Key Vault](tutorial-rotate-certificates.md)
