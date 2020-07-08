---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82900368"
---
1. Logga in på [Amazon Developer-konsolen](https://developer.amazon.com/dashboard) med dina Amazon-kontoautentiseringsuppgifter.
1. Om du inte redan har gjort det klickar du på **Registrera**, följer utvecklings registrerings stegen och godkänner principen.
1. Välj **Logga in med Amazon**på instrument panelen.
1. Välj **skapa en ny säkerhets profil**.
1. Ange ett **namn på en säkerhets profil**, en **Beskrivning av säkerhets profil**och URL för sekretess **meddelande**, till exempel URL: en för `https://www.contoso.com/privacy` Sekretess meddelandet är en sida som du hanterar som ger sekretess information till användarna. Klicka sedan på **Spara**.
1. I avsnittet **Logga in med Amazon-konfigurationer** väljer du **det säkerhets profil namn** som du har skapat, klickar på ikonen **Hantera** och väljer **webb inställningar**.
1. I avsnittet **webb inställningar** kopierar du värdena för **klient-ID**. Välj **Visa hemlighet** för att hämta klient hemligheten och kopiera den sedan. Du behöver båda dessa för att konfigurera ett Amazon-konto som en identitets leverantör i din klient organisation. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.
1. I avsnittet **webb inställningar** väljer du **Redigera**. I **tillåtna ursprung** och **tillåtna retur webb adresser**anger du lämpliga URL: er (anges ovan). 
1. Klicka på **Spara**.
