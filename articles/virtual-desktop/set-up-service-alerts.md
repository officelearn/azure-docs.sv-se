---
title: Konfigurera aviseringar för Windows virtuella skrivbordet – Azure
description: Hur du ställer in Azure Service Health att ta emot tjänstmeddelanden för virtuella Windows-skrivbordet.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: v-chjenk
ms.openlocfilehash: cae75f16da2cad453c74b7e6e9fb62dd789fe5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081373"
---
# <a name="tutorial-set-up-service-alerts"></a>Självstudier: Konfigurera aviseringar

Du kan använda Azure Service Health övervakar karaktär och hälsoråd för virtuella Windows-skrivbordet. Azure Service Health kan meddela dig med olika typer av aviseringar (till exempel e-post eller SMS) hjälper dig att förstå effekten av ett problem och hålla dig uppdaterad under löser problemet. Azure Service Health kan också hjälpa dig att minimera driftstopp och förbereda för planerat underhåll och ändringar som kan påverka tillgängligheten för dina resurser.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa och konfigurera aviseringar.

Mer information om Azure Service Health finns i [Health-dokumentation för Azure](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Självstudie: Skapa en klient i förhandsversion för virtuella skrivbord i Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Självstudie: Skapa tjänstens huvudnamn och rolltilldelningar med PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Självstudie: Skapa en värd-pool med Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

## <a name="create-service-alerts"></a>Skapa aviseringar

Det här avsnittet visar hur du konfigurerar Azure Service Health och hur du ställer in meddelanden, som du kan komma åt på Azure portal. Du kan ställa in olika typer av aviseringar och schemalägger dem att meddela dig i rätt tid.

### <a name="recommended-service-alerts"></a>Rekommenderade aviseringar

Vi rekommenderar att du skapar aviseringar för följande typer av hälsotillstånd händelse:

- **Tjänsten problem:** Ta emot meddelanden på allvarliga problem som påverkar anslutningen för dina användare med tjänsten eller möjlighet att hantera virtuella skrivbord i Windows-klienten.
- **Hälsostatus:** Ta emot meddelanden som kräver din uppmärksamhet. Här följer några exempel på den här typen av meddelande:
    - Virtuella datorer (VM), inte på ett säkert sätt konfigureras som öppnar port 3389
    - Utfasning av funktioner

### <a name="configure-service-alerts"></a>Konfigurera aviseringar

Konfigurera aviseringar:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj **Service Health.**
3. Följ instruktionerna i [skapa aviseringar för aktivitetsloggen för tjänstmeddelanden](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) att ställa in dina aviseringar och meddelanden.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att konfigurera och använda Azure Service Health för att övervaka karaktär och hälsoråd för virtuella Windows-skrivbordet. Mer information om hur du loggar in till virtuella Windows-skrivbordet, fortsätter du att ansluta till virtuella skrivbord instruktioner för Windows.

> [!div class="nextstepaction"]
> [Ansluta till fjärrskrivbord-klienten på Windows 7 och Windows 10](./connect-windows-7-and-10.md)
