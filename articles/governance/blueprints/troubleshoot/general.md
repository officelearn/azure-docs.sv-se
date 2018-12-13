---
title: Felsöka vanliga fel
description: Lär dig hur du felsöker problem skapa och tilldela skisser
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 40668fed2fcc2a04e39fa3a4d7e8e8923c75ae05
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315539"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Felsöka med hjälp av Azure skisser

Du kanske stöter på fel när du skapar eller tilldelar skisser. Den här artikeln beskrivs olika fel som kan uppstå och hur du löser dem.

## <a name="finding-error-details"></a>Hitta information om fel

Många fel blir resultatet av att tilldela en skiss till ett omfång. När en uppgift misslyckas, innehåller information om den misslyckade distributionen i skissen. Den här informationen anger problemet så att det kan åtgärdas och nästa distributionen är klar.

1. Klicka på **Alla tjänster** och sök efter och välj **Princip** i den vänstra fönsterrutan. Klicka på **Skisser** på sidan **Princip**.

1. Välj **tilldelade skisser** från sidan till vänster och Använd sökningen för att filtrera skissen tilldelningar för att hitta den misslyckade tilldelningen. Du kan också sortera tabellen med tilldelningar efter den **Etableringsstatus** kolumn att se alla misslyckade tilldelningar grupperas tillsammans.

1. Vänsterklicka på skissen med den _misslyckades_ status eller högerklicka och välj **visa tilldelningsinformation**.

1. En röd banderoll varning att tilldelningen misslyckades är högst upp på sidan för tilldelning av skissen. Klicka någonstans på banderollen för att få mer information.

Det är vanligt att felet kan orsakas av en artefakt och inte skissen som helhet. Om en artefakt skapar ett Key Vault och Azure Policy förhindrar skapande av Key Vault, misslyckas hela tilldelningen.

## <a name="general-errors"></a>Allmänt fel

### <a name="policy-violation"></a>Scenario: Principöverträdelse

#### <a name="issue"></a>Problem

Malldistributionen misslyckades på grund av principöverträdelse.

#### <a name="cause"></a>Orsak

En princip orsaka en konflikt med distributionen för en rad orsaker:

- Resursen håller på att skapas är begränsad av principen (vanligtvis SKU eller plats-begränsningar)
- Distributionen kan du ställa fält som konfigurerats av principen (vanliga med taggar)

#### <a name="resolution"></a>Lösning

Ändra skissen så att det inte står i konflikt med principerna i felinformationen. Om den här ändringen inte är möjligt, är ett alternativ att ha omfånget för principtilldelningen ändras så skissen inte längre är i konflikt med principen.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

- Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.