---
title: Konfigurera tjänst aviseringar för Windows Virtual Desktop – Azure
description: Konfigurera Azure Service Health för att ta emot tjänst meddelanden för virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
ms.openlocfilehash: b5fa5fc13c8ce0c98c04d2451f3a50e89b986c16
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676438"
---
# <a name="tutorial-set-up-service-alerts"></a>Självstudier: Konfigurera tjänstaviseringar

Du kan använda Azure Service Health för att övervaka tjänst problem och hälso rekommendationer för virtuella Windows-datorer. Azure Service Health kan meddela dig om olika typer av aviseringar (t. ex. e-post eller SMS), hjälpa dig att förstå resultatet av ett problem och hålla dig uppdaterad när problemet löses. Azure Service Health kan också hjälpa dig att minska stillestånds tiden och förbereda för planerat underhåll och ändringar som kan påverka tillgängligheten för dina resurser.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa och konfigurera tjänst aviseringar.

Mer information om Azure Service Health finns i [Azure Health-dokumentationen](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Förutsättningar

- [Självstudier: Skapa en klient i Windows Virtual Desktop @ no__t-0
- [Självstudier: Skapa tjänstens huvud namn och roll tilldelningar med PowerShell @ no__t-0
- [Självstudier: Skapa en värdbaserad pool med Azure Marketplace @ no__t-0

## <a name="create-service-alerts"></a>Skapa tjänst aviseringar

I det här avsnittet visas hur du konfigurerar Azure Service Health och hur du konfigurerar aviseringar som du kan komma åt på Azure Portal. Du kan ställa in olika typer av aviseringar och schemalägga dem för att meddela dig inom rimlig tid.

### <a name="recommended-service-alerts"></a>Rekommenderade tjänst aviseringar

Vi rekommenderar att du skapar tjänst aviseringar för följande typer av hälso händelser:

- **Tjänst problem:** Ta emot meddelanden om större problem som påverkar anslutningarna för dina användare med tjänsten eller med möjligheten att hantera din Windows-klient för virtuella skriv bord.
- **Hälso rådgivning:** Ta emot meddelanden som kräver din uppmärksamhet. Följande är några exempel på den här typen av avisering:
    - Virtual Machines (VM) har inte kon figurer ATS på ett säkert sätt som öppen port 3389
    - Utfasning av funktioner

### <a name="configure-service-alerts"></a>Konfigurera tjänst aviseringar

Konfigurera tjänst aviseringar:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj **service Health.**
3. Följ anvisningarna i [skapa aktivitets logg aviseringar på tjänst aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) för att konfigurera aviseringar och meddelanden.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du konfigurerar och använder Azure Service Health för att övervaka tjänst problem och hälso rekommendationer för virtuella Windows-datorer. Om du vill veta mer om hur du loggar in på det virtuella Windows-skrivbordet fortsätter du till instruktionen Anslut till Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Ansluta till fjärr skrivbords klienten på Windows 7 och Windows 10](./connect-windows-7-and-10.md)
