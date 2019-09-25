---
title: Säkerhets rekommendationer för Azure Key Vault
description: Säkerhets rekommendationer för Azure Key Vault. Genom att implementera den här vägledningen kan du uppfylla dina säkerhets skyldigheter enligt beskrivningen i vår delade ansvars modell
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/23/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 90965618b1d2cb126d56453d46a5bebc85319c0f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219641"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Säkerhets rekommendationer för Azure Key Vault

Den här artikeln innehåller säkerhets rekommendationer för Azure Key Vault. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhets skyldigheter enligt beskrivningen i vår delade ansvars modell. Mer information om vad Microsoft gör för att uppfylla ansvaret för service leverantörer finns i [delade ansvars områden för molnbaserad data behandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Några av rekommendationerna som ingår i den här artikeln kan övervakas automatiskt av Azure Security Center. Azure Security Center är den första försvars linjen i att skydda dina resurser i Azure. Den analyserar regelbundet säkerhets läget för dina Azure-resurser för att identifiera potentiella säkerhets risker. Sedan får du rekommendationer om hur du kan åtgärda dem.

- Mer information om Azure Security Center rekommendationer finns [i säkerhets rekommendationer i Azure Security Center](../security-center/security-center-recommendations.md).
- Mer information om Azure Security Center finns i [Azure Security Center?](../security-center/security-center-intro.md)

## <a name="recommendations"></a>Rekommendationer

| Category | Rekommendation | Kommentar | Security Center |
|-|-|----|--|
| Dataskydd |Aktivera mjuk borttagning | Med [mjuk borttagning](key-vault-ovw-soft-delete.md) kan du återställa borttagna valv och valv objekt |  - |
| Dataskydd | Begränsa åtkomsten till valv data  | Följ principen om minsta behörighet och begränsa vilka medlemmar i organisationen som har åtkomst till valv data |  - |
| Identitets- och åtkomsthantering | Begränsa antalet användare med deltagar åtkomst | Om en användare har deltagar behörighet till ett nyckel valv hanterings plan kan användaren ge sig själva åtkomst till data planet genom att ange en Key Vault åtkomst princip. Du bör ha närmare kontroll över vem som har deltagar rollen åtkomst till dina nyckel valv. Se till att endast de med ett behov av åtkomst behöriga personer kan komma åt och hantera dina valv. Du kan läsa [säker åtkomst till ett nyckel valv](key-vault-secure-your-key-vault.md) | - |
| Övervakning | Diagnostikloggar i Key Vault ska vara aktive rad | Aktivera loggar och behålla upp till ett år. På så sätt kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras. | [Ja](../security-center/security-center-identity-access.md) |
| Övervakning | Begränsa vem som har åtkomst till dina Azure Key Vault-loggar | [Key Vault loggar](key-vault-logging.md) sparar information om aktiviteterna som har utförts på ditt valv, till exempel skapande eller borttagning av valv, nycklar, hemligheter och kan användas under en undersökning |  - |
| Nätverk |Begränsa nätverks exponering | Nätverks åtkomsten bör begränsas till de virtuella nätverk som används av lösningar som kräver valv åtkomst. Granska information om [tjänst slut punkter för virtuella nätverk för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Nästa steg

Kontakta din programprovider för att se om det finns ytterligare säkerhets krav. Mer information om hur du utvecklar säkra program finns i [dokumentationen om säker utveckling](../security/fundamentals/abstract-develop-secure-apps.md).
