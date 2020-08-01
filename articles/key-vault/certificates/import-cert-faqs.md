---
title: Vanliga frågor och Azure Key Vault certifikat import
description: Vanliga frågor och Azure Key Vault certifikat import
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 493c77a8f875018627bfe3167e66addeaf65d089
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445781"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Vanliga frågor och Azure Key Vault certifikat import

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Hur kan jag importera ett certifikat i Azure Key Vault?

Importera certifikat – för import åtgärd accepterar Azure Key Vault två certifikat format PEM och PFX. Även om det finns PEM-filer med endast den offentliga delen, kräver Azure Key Vault och accepterar bara en PEM eller PFX på filmapp och med en privat nyckel. Följ [självstudien för att importera certifikat](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>När du har importerat lösenordsskyddat certifikat till nyckel valvet och sedan laddar ned det kan jag inte se det lösen ord som är associerat med certifikatet?
    
Det överförda skyddade certifikatet efter lagring i Key Vault sparar inte lösen ordet som är kopplat till det. Det krävs bara en gång under importåtgärden. Även om detta är ett avsiktligt koncept, kan du alltid få certifikatet som en hemlighet och konvertera från base64 till PFX och lägga till föregående lösen ord via [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Hur kan jag lösa "felaktigt parameter fel"? Vilka certifikatformat stöds för import i Key Vault?

När du importerar certifikatet måste du se till att nyckeln ingår i själva filen. Om du har en privat nyckel separat i ett annat format måste du kombinera nyckeln med certifikatet. Vissa certifikat utfärdare tillhandahåller certifikat i olika format, därför innan du importerar certifikatet, se till att de är antingen i. pem eller. PFX-format och att nyckeln som används är antingen RSA eller ECC. Se dessa för att granska [certifikat krav](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) och [krav på certifikat nycklar](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection).

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>Fel när certifikat importeras via portalen ("ett fel uppstod"). Hur kan jag undersöka ytterligare?
    
Om du vill visa mer beskrivande fel importerar du certifikat filen via [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) eller [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Hur kan jag lösa ' feltyp: åtkomst nekad eller användaren har inte behörighet att importera certifikat '?
    
Den här åtgärden kräver certifikat-/importbehörighet. Navigera till platsen där Key Vault finns. Du måste ge användaren lämpliga behörigheter under åtkomstprinciper. Gå till Key Vault> åtkomst principer > Lägg till åtkomst princip > Välj certifikat behörigheter (eller som du vill ha behörighet) > huvud > Sök efter och Lägg sedan till användarens e-post. [Läs mer om certifikat relaterade åtkomst principer](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Hur kan jag lösa ' feltyp: en konflikt när du skapar ett certifikat '?
    
Certifikatet ska vara unikt. Ett certifikat med samma namn kan vara i läget Soft-Deleted, och som enligt [sammansättning av ett certifikat](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) i Azure Key Vault, om det finns en annan nyckel eller hemlighet i Key Vault med samma namn som du försöker ange för certifikatet, kommer det att Miss läge och du måste antingen ta bort nyckeln eller hemligheten eller använda ett annat namn för ditt certifikat. [Visa borttaget certifikat](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Varför får jag feltypen tecken: tecken längden är för lång?
    
* Certifikatets ämnes namn längd har en tecken gräns på 200 tecken
* Certifikatets lösen ords längd har en tecken gräns på 200 tecken

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Kan jag importera ett utgånget certifikat i Azure Key Vault?
    
Nej, de upphörde PFX-certifikaten importeras inte till Azure Key Vault.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Hur kan jag konvertera mitt certifikat till rätt format?

Du kan be din certifikat utfärdare att tillhandahålla certifikatet i det format som behövs, även om det finns verktyg från tredje part som kan hjälpa dig att konvertera till rätt format, kommer Microsoft inte att kunna meddela ytterligare om hur du får certifikatet i det önskade formatet.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Kan jag importera certifikat från certifikat utfärdare som inte är partner?
Ja, du kan importera certifikat från alla certifikat utfärdare, men nyckel valvet kommer inte att kunna förnya dessa certifikat automatiskt. Du kan ställa in e-postaviseringar för att få meddelanden om att certifikatet upphör att gälla.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Om jag importerar ett certifikat från en partner certifikat utfärdare kommer funktionen för automatisk förnyelse fortfarande att fungera?
Ja, du måste se till att när du har laddat upp en autorotation i certifikatets utgivnings princip. Ändringarna kommer också att avspeglas tills nästa cykel eller certifikat version.

### <a name="unable-to-see-the-app-service-certificate-imported-to-key-vault"></a>Det går inte att se App Service Certificate som importer ATS till Key Vault? 
Om certifikatet har importer ATS kan du gå igenom bladet hemligheter.


## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault certifikat](/azure/key-vault/certificates/about-certificates)
