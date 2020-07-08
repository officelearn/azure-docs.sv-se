---
title: Anslut Office 365-loggar till Azure Sentinel | Microsoft Docs
description: Lär dig att använda Office 365-loggen för att få information om pågående användar-och administrations aktiviteter i Exchange och SharePoint, inklusive OneDrive.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: 180b25f80bd27caea20b1c17cd84fda38c172e0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559346"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Anslut Office 365-loggar till Azure Sentinel

[Office 365](https://docs.microsoft.com/office/) -loggen ansluter till Azure Sentinel-information om pågående användar-och administrations aktiviteter i **Exchange** och **SharePoint** (inklusive **OneDrive**). Den här informationen innehåller information om åtgärder som fil hämtningar, åtkomst förfrågningar som skickats, ändringar av grupp händelser och post lådor, samt information om den användare som utförde åtgärderna. Genom att ansluta Office 365-loggar till Azure Sentinel kan du Visa och analysera dessa data i dina arbets böcker, fråga den för att skapa anpassade aviseringar och införliva den för att förbättra din gransknings process, vilket ger dig mer insikt i din Office 365-säkerhet.

## <a name="prerequisites"></a>Krav

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste vara global administratör eller säkerhets administratör för din klient.

- Din Office 365-distribution måste finnas på samma klient organisation som Azure Sentinel-arbetsytan.

> [!IMPORTANT]
> - För att anslutningen ska kunna komma åt data via API: t för hanterings aktivitet i Office 365 måste du ha **enhetlig gransknings loggning** aktive rad på din Office 365-distribution. Beroende på vilken typ av Office 365/Microsoft 365-licens du har, kan det bero på att den inte är aktive rad som standard. Besök [Office 365 Security och Compliance Center](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) för att kontrol lera statusen för enhetlig gransknings loggning enligt din licens typ.
> - Du kan också manuellt aktivera, inaktivera och kontrol lera den aktuella statusen för den enhetliga gransknings loggningen i Office 365. Instruktioner finns i [Aktivera eller inaktivera gransknings loggs ökning i Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Mer information finns i API- [referens för Office 365 Management Activity](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Som anges ovan och som du ser på anslutnings sidan under **data typer**stöder Azure Sentinel Office 365-anslutningen för närvarande endast inmatningen av gransknings loggar från Microsoft Exchange och SharePoint (inklusive OneDrive). Det finns dock vissa externa lösningar om du är intresse rad av att hämta [data från Team](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761) eller [andra Office-data](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) till Azure Sentinel. 

## <a name="enable-the-office-365-log-connector"></a>Aktivera Office 365-logg kopplingen

1. Välj **data kopplingar**på navigerings menyn i Azure Sentinel.

1. I listan **data anslutningar** klickar du på **Office 365**och sedan på knappen **Öppna kopplings sidan** längst ned till höger.

1. Under avsnittet med etiketten **konfiguration**markerar du kryss rutorna för de Office 365-aktivitets loggar som du vill ansluta till Azure Sentinel och klickar på **tillämpa ändringar**. 

   > [!NOTE]
   > Om du tidigare har anslutit flera klienter till Azure Sentinel, med en äldre version av Office 365-anslutningen som stöder detta, kommer du att kunna visa och ändra vilka loggar som du samlar in från varje klient. Du kommer inte att kunna lägga till fler klienter, men du kan ta bort tidigare tillagda klienter.

1. Om du vill fråga Office 365-loggdata i Log Analytics skriver `OfficeActivity` du på den första raden i frågefönstret.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Office 365 till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel, med [inbyggda](tutorial-detect-threats-built-in.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.

