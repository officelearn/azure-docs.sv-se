---
title: Säkerhetsrekommendationer för Azure Key Vault
description: Säkerhetsrekommendationer för Azure Key Vault. Genom att implementera den här vägledningen kan du uppfylla dina säkerhetsskyldigheter enligt beskrivningen i vår modell för delat ansvar
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: d18dca2d7c44ac9db5ebabc5cc10f2f0b3f2c069
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546361"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Säkerhetsrekommendationer för Azure Key Vault

Den här artikeln innehåller säkerhetsrekommendationer för Azure Key Vault. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhetsskyldigheter enligt beskrivningen i vår modell för delat ansvar. Mer information om vad Microsoft gör för att uppfylla tjänsteleverantörens ansvar finns i [Delat ansvar för molnbaserad databehandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Några av rekommendationerna i den här artikeln kan övervakas automatiskt av Azure Security Center. Azure Security Center är den första försvarslinjen för att skydda dina resurser i Azure. Den analyserar regelbundet säkerhetstillståndet för dina Azure-resurser för att identifiera potentiella säkerhetsproblem. Den ger dig sedan rekommendationer om hur du ska hantera dem.

- Mer information om Azure Security Center-rekommendationer finns [i Säkerhetsrekommendationer i Azure Security Center](../security-center/security-center-recommendations.md).
- Information om Azure Security Center finns i [Vad är Azure Security Center?](../security-center/security-center-intro.md)

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
|Aktivera mjuk borttagning | [Med mjuk borttagning](key-vault-ovw-soft-delete.md) kan du återställa borttagna valv och valvobjekt |  - |
| Begränsa åtkomsten till arkivdata  | Följ principen om lägsta behörighet och begränsa vilka medlemmar i organisationen som har åtkomst till arkivdata |  - |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
| Begränsa antalet användare med deltagaråtkomst | Om en användare har behörighet för deltagare till ett nyckelvalvhanteringsplan kan användaren ge sig själva åtkomst till dataplanet genom att ange en åtkomstprincip för Nyckelvalv. Du bör ha en noggrann kontroll över vem som har deltagarrollåtkomst till dina nyckelvalv. Se till att endast de med behov av åtkomst behöriga personer kan komma åt och hantera dina valv. Du kan läsa [Säker åtkomst till ett nyckelvalv](key-vault-secure-your-key-vault.md) | - |

## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
 Diagnostikloggar i Key Vault ska vara aktiverade | Aktivera loggar och behåll dem upp till ett år. På så sätt kan du återskapa aktivitetsspår i undersökningssyfte när en säkerhetsincident inträffar eller ditt nätverk äventyras. | [Ja](../security-center/security-center-identity-access.md) |
| Begränsa vem som kan komma åt dina Azure Key-valvloggar | [Key Vault loggar](key-vault-logging.md) spara information om de aktiviteter som utförs på ditt valv såsom skapande eller borttagning av valv, nycklar, hemligheter och kan användas under en undersökning |  - |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer | Security Center |
|-|----|--|
|Begränsa nätverksexponeringen | Nätverksåtkomst bör begränsas till de virtuella nätverk som används av lösningar som kräver åtkomst till valvet. Granska information om [slutpunkter för virtuella nätverkstjänster för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Nästa steg

Kontrollera med programleverantören om det finns ytterligare säkerhetskrav. Mer information om hur du utvecklar säkra program finns i [Säker utvecklingsdokumentation](../security/fundamentals/abstract-develop-secure-apps.md).
