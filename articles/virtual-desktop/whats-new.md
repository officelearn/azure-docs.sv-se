---
title: Vad är nytt i det virtuella Windows-skrivbordet? – Azure
description: Nya funktioner och produkt uppdateringar för virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 06/15/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 383b59d10fa1de1b2ee0ea8b505c164577487255
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84974389"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Vad är nytt i det virtuella Windows-skrivbordet?

Uppdateringar av virtuella Windows-datorer regelbundet. I den här artikeln får du lära dig att:

- De senaste uppdateringarna
- Nya funktioner
- Förbättringar av befintliga funktioner
- Felkorrigeringar

Den här artikeln uppdateras varje månad. Se till att kontrol lera igen ofta för att hålla dig uppdaterad med nya uppdateringar.

## <a name="june-2020"></a>Juni 2020

Förra månaden introducerade vi Windows Virtual Desktop våren 2020 Update i för hands versionen. Den här uppdateringen innehåller många spännande nya funktioner som vi älskar att berätta om. Här är what's nya för våren 2020-uppdateringen.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager-preview"></a>Windows Virtual Desktop är nu integrerat med Azure Resource Manager (för hands version)

Windows Virtual Desktop är nu integrerat i Azure Resource Manager. I den senaste uppdateringen Azure Resource Manager alla virtuella Windows-skrivbordet-objekt. Den här uppdateringen är också integrerad med rollbaserad åtkomst kontroll (RBAC) i Azure. Se [Vad är Azure Resource Manager?](../azure-resource-manager/management/overview.md) om du vill veta mer.

Den här ändringen gör du:

- Det virtuella Windows-skrivbordet är nu integrerat med Azure Portal. Det innebär att du kan hantera allt direkt i portalen, inga PowerShell-, webbappar eller verktyg från tredje part krävs. Kom igång genom att titta på vår självstudie i [skapa en adresspool med Azure Portal](create-host-pools-azure-marketplace.md).

- Innan våren 2020-uppdateringen kan du bara publicera RemoteApp-och skriv bord till enskilda användare. Med Azure Resource Manager kan du nu publicera resurser i Azure Active Directory grupper.

- Den tidigare versionen av Windows Virtual Desktop hade fyra inbyggda administratörs roller som du kan tilldela till en klient eller adresspool. Dessa roller finns nu i [rollbaserad åtkomst kontroll](../role-based-access-control/overview.md)i Azure. Du kan tillämpa dessa roller på alla virtuella Windows-datorer Azure Resource Manager objekt, vilket gör att du kan ha en fullständig, omfattande Delegerings modell.

- I våren 2020-uppdateringen behöver du inte längre köra Azure Marketplace eller GitHub-mallen upprepade gånger för att expandera en adresspool. Allt du behöver för att expandera en adresspool är att gå till din värd-pool i Azure Portal och välja **+ Lägg** till för att distribuera ytterligare värdar för sessioner.

- Distribution av värd bassäng är nu fullständigt integrerat med [galleriet för Azures delade avbildningar](../virtual-machines/windows/shared-image-galleries.md). Delade avbildnings galleriet är en separat Azure-tjänst som lagrar definitioner av virtuella dator avbildningar, inklusive avbildnings versioner. Du kan också använda global replikering för att kopiera och skicka dina avbildningar till andra Azure-regioner för lokal distribution.

- Övervakning av funktioner som används för att utföras via PowerShell eller diagnostik-webbappen har nu flyttats till Log Analytics i Azure Portal. Nu har du två alternativ för att visualisera dina rapporter. Du kan köra Kusto-frågor och använda arbets böcker för att skapa visuella rapporter.

- Du behöver inte längre slutföra Azure Active Directory (Azure AD) medgivande för att använda Windows Virtual Desktop. I våren 2020-uppdateringen autentiserar Azure AD-klienten på din Azure-prenumeration användarna och tillhandahåller RBAC-kontroller för dina administratörer.


### <a name="powershell-support"></a>Stöd för PowerShell

Vi har lagt till nya AzWvd-cmdletar i modulen Azure PowerShell AZ med våren 2020-uppdateringen. Den här nya modulen stöds i PowerShell Core, som körs på .NET Core.

Installera modulen genom att följa anvisningarna i [Konfigurera PowerShell-modulen för Windows Virtual Desktop](powershell-module.md).

Du kan också se en lista över tillgängliga kommandon i [AzWvd PowerShell-referensen](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Mer information om de nya funktionerna finns i [vårt blogg inlägg](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="additional-gateways"></a>Ytterligare gatewayer

Vi har lagt till ett nytt Gateway-kluster i Sydafrika för att minska anslutnings fördröjningen.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams på Windows Virtual Desktop (för hands version)

Vi har gjort några förbättringar av Microsoft Teams för Windows Virtual Desktop. Det viktigaste är att Windows Virtual Desktop har nu stöd för ljud-och visuell omdirigering för samtal. Omdirigering förbättrar svars tiden genom att skapa direkta sökvägar mellan användare när de anropar med hjälp av ljud eller video. Mindre avstånd innebär färre hopp, vilket gör att anropen ser ut och ljuden blir mjukare.

Mer information finns i [vårt blogg inlägg](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Nästa steg

Ta en titt på de här artiklarna om du vill veta mer om uppdateringar för våra klienter för virtuella Windows-datorer och Fjärrskrivbordstjänster:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Webb](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
