---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 6cb0ef848bdeab35c971ebd1a0b14eca1dfe3001
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523900"
---
#### <a name="applicationstabapplications"></a>[Program](#tab/applications/)

1. På sidan **registrerad app** -översikt väljer du **Inställningar**.
1. Under **API-åtkomst**väljer du **nödvändiga behörigheter**.
1. Välj **Windows-Azure Active Directory**.
1. Under **program behörigheter**väljer du **läsa och skriva katalog data**.
1. Välj **Spara**.
1. Välj **bevilja behörigheter**och välj sedan **Ja**. Det kan ta några minuter innan behörigheterna är fullständigt spridda.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Under **Hantera**, Välj **API-behörigheter**.
1. Under **konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Välj **Azure Active Directory graf**.
1. Välj **Programbehörigheter**.
1. Expandera **katalogen** och markera sedan kryss rutan **Directory. readwrite. all** .
1. Välj **Lägg till behörigheter**. Vänta några minuter innan du fortsätter till nästa steg.
1. Välj **bevilja administrativt godkännande för (ditt klient namn)** .
1. Välj ett klient administratörs konto.
1. Välj **Acceptera**.
1. Välj **Uppdatera**och verifiera sedan att "beviljat..." visas under **status**. Det kan ta några minuter innan behörigheterna har spridits.