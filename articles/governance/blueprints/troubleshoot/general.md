---
title: Felsöka med hjälp av Azure skisser
description: Lär dig hur du felsöker problem skapa och tilldela skisser
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dd1163ece225c2e9a9b082f5e8364f34b06a10ae
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982298"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Felsöka med hjälp av Azure skisser

Kan det uppstå fel när du skapar eller tilldelar skisser. Den här artikeln beskrivs olika fel som kan uppstå och hur du löser dem.

## <a name="finding-error-details"></a>Hitta information om fel

Många fel blir resultatet av att tilldela en skiss till ett omfång. När en uppgift misslyckas tillhandahåller skiss information om den misslyckade distributionen. Den här informationen visar problemet så att det kan åtgärdas och efterföljande distributionen ska lyckas.

1. Starta tjänsten Azure skisser i Azure portal genom att klicka på **alla tjänster** och söka efter och välja **princip** i den vänstra rutan. På den **princip** klickar du på **skisser**.

1. Välj **tilldelade skisser** från sidan till vänster och Använd sökningen för att filtrera skissen tilldelningar för att hitta den misslyckade tilldelningen. Du kan också sortera tabellen med tilldelningar efter den **Etableringsstatus** kolumn att se alla misslyckade tilldelningar grupperas tillsammans.

1. Vänsterklicka på skissen med den _misslyckades_ status eller högerklicka och välj **visa tilldelningsinformation**.

1. Överst i skissen är tilldelning en röd banderoll varning att tilldelningen misslyckades. Klicka någonstans på banderollen för att få mer information.

Det är vanligt att felet kan orsakas av en artefakt som ingår i skissen och inte skissen som helhet. Till exempel om skissen innehåller en artefakt för att skapa ett Key Vault, men skapa en Key Vault förhindras av Azure Policy, hela tilldelningen kommer att misslyckas.

## <a name="general-errors"></a>Allmänt fel

### <a name="policy-violation"></a>Scenario: Policyöverträdelse

#### <a name="issue"></a>Problem

Malldistributionen misslyckades på grund av principöverträdelse.

#### <a name="cause"></a>Orsak

En princip orsaka en konflikt med distributionen för en rad orsaker:

- Resursen håller på att skapas är begränsad av principen (vanligtvis SKU eller plats-begränsningar)
- Distributionen kan du ställa fält som konfigurerats av principen (vanliga med taggar)

#### <a name="resolution"></a>Lösning

Justera skissen för att inte vara i konflikt med principer som visas i information om felet. Om det inte är möjligt, är ett alternativ att ha omfånget för principtilldelningen ändras så skissen inte längre är i konflikt med principen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

- Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.