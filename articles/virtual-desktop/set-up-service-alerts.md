---
title: Konfigurera tjänstaviseringar för Windows Virtual Desktop - Azure
description: Konfigurera Azure Service Health för att ta emot tjänstmeddelanden för Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2834ba924fa9c29d955c38fbaeb45ab23e5c4e9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127708"
---
# <a name="tutorial-set-up-service-alerts"></a>Självstudiekurs: Ställ in tjänstaviseringar

Du kan använda Azure Service Health för att övervaka serviceproblem och hälsorekommendationer för Windows Virtual Desktop. Azure Service Health kan meddela dig med olika typer av aviseringar (till exempel e-post eller SMS), hjälpa dig att förstå effekten av ett problem och hålla dig uppdaterad när problemet löser sig. Azure Service Health kan också hjälpa dig att minska driftstopp och förbereda för planerat underhåll och ändringar som kan påverka tillgängligheten för dina resurser.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa och konfigurera tjänstaviseringar.

Mer information om Azure Service Health finns i [Azure Health Documentation](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Krav

- [Självstudiekurs: Skapa en klient i Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
- [Självstudiekurs: Skapa tjänsthuvudnamn och rolltilldelningar med PowerShell](create-service-principal-role-powershell.md)
- [Självstudiekurs: Skapa en värdpool med Azure Marketplace](create-host-pools-azure-marketplace.md)

## <a name="create-service-alerts"></a>Skapa tjänstaviseringar

I det här avsnittet visas hur du konfigurerar Azure Service Health och hur du konfigurerar meddelanden som du kan komma åt på Azure-portalen. Du kan ställa in olika typer av aviseringar och schemalägga dem så att de meddelas i tid.

### <a name="recommended-service-alerts"></a>Rekommenderade serviceaviseringar

Vi rekommenderar att du skapar tjänstaviseringar för följande hälsohändelsetyper:

- **Serviceproblem:** Få meddelanden om större problem som påverkar anslutningen för dina användare med tjänsten eller med möjlighet att hantera din Windows Virtual Desktop-klient.
- **Hälsorådgivning:** Få meddelanden som kräver din uppmärksamhet. Följande är några exempel på den här typen av meddelande:
    - Virtuella datorer (VMs) inte korrekt konfigurerade som öppen port 3389
    - Utfasning av funktionalitet

### <a name="configure-service-alerts"></a>Konfigurera tjänstaviseringar

Så här konfigurerar du tjänstaviseringar:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Tjänsthälsa.**
3. Använd instruktionerna i [Skapa aktivitetsloggaviseringar på tjänstaviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) för att ställa in aviseringar och meddelanden.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du konfigurerar och använder Azure Service Health för att övervaka serviceproblem och hälsorekommendationer för Windows Virtual Desktop. Om du vill veta mer om hur du loggar in på Windows Virtual Desktop fortsätter du till hur du ansluter till Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Ansluta till fjärrskrivbordsklienten i Windows 7 och Windows 10](./connect-windows-7-and-10.md)
