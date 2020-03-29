---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77474114"
---
Frågor till behållaren faktureras på prisnivån för Azure-resursen `ApiKey`som används för .

Azure Cognitive Services-behållare är inte licensierade att köras utan att vara anslutna till slutpunkten för mätning/fakturering. Du måste alltid aktivera behållarna för att kunna kommunicera faktureringsinformation med faktureringsslutpunkten. Cognitive Services-behållare skickar inte kunddata, till exempel bilden eller texten som analyseras, till Microsoft.

### <a name="connect-to-azure"></a>Anslut till Azure

Behållaren behöver värdena för faktureringsargument för att kunna köras. Dessa värden gör att behållaren kan ansluta till slutpunkten för fakturering. Behållaren rapporterar användning ungefär var 10 till 15:e minut. Om behållaren inte ansluter till Azure inom det tillåtna tidsfönstret fortsätter behållaren att köras men betjänar inte frågor förrän faktureringsslutpunkten har återställts. Anslutningen görs 10 gånger med samma tidsintervall på 10 till 15 minuter. Om den inte kan ansluta till faktureringsslutpunkten inom de 10 försöken slutar behållaren att betjäna begäranden.

### <a name="billing-arguments"></a>Argument för fakturering

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` Kommandot <span class="docon docon-navigate-external x-hidden-focus"></span> </a> startar behållaren när alla tre av följande alternativ har giltiga värden:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckeln för Cognitive Services-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en API-nyckel för `Billing`den etablerade resurs som anges i . |
| `Billing` | Slutpunkten för cognitive services-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till slutpunkts-URI för en etablerad Azure-resurs.|
| `Eula` | Anger att du har accepterat licensen för behållaren.<br/>Värdet för det här alternativet måste anges så att **det accepteras**. |
