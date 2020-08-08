---
title: Vad är nytt i det virtuella Windows-skrivbordet? – Azure
description: Nya funktioner och produkt uppdateringar för virtuella Windows-datorer.
author: Heidilohr
ms.topic: overview
ms.date: 08/07/2020
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.openlocfilehash: 8663fc3f016fadcd4f4c99acd800cd0ccf8844f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003530"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Vad är nytt i det virtuella Windows-skrivbordet?

Uppdateringar av virtuella Windows-datorer regelbundet. I den här artikeln får du lära dig att:

- De senaste uppdateringarna
- Nya funktioner
- Förbättringar av befintliga funktioner
- Felkorrigeringar

Den här artikeln uppdateras varje månad. Se till att kontrol lera igen ofta för att hålla dig uppdaterad med nya uppdateringar.

## <a name="july-2020"></a>Juli 2020  

Juli var när Windows Virtual Desktop med Azure Resource Management-integrering blev allmänt tillgängligt.

Detta är vad som har ändrats med den nya versionen: 

- "Hösten 2019-versionen" kallas nu "Windows Virtual Desktop (klassisk)" medan "våren 2020 release" nu är bara "Windows Virtual Desktop". Mer information finns i [det här blogg inlägget](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Om du vill veta mer om nya funktioner kan du kolla i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Uppdatering av autoskalning Tool

Den senaste versionen av verktyget autoskalning som fanns i för hands versionen är nu allmänt tillgänglig. Det här verktyget använder ett Azure Automation-konto och Azure Logic-appen för att automatiskt stänga och starta om sessionens virtuella datorer (VM) i en adresspool, vilket minskar infrastruktur kostnaderna. Läs mer i [Scale session-värdar med hjälp av Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure Portal

Nu kan du göra följande saker med Azure Portal i det virtuella Windows-Skriv bordet: 

- Tilldela användare direkt till personliga värdar för fjärrskrivbordssessioner  
- Ändra validerings miljö inställningen för värdbaserade pooler 

### <a name="diagnostics"></a>Diagnostik

Vi har släppt några nya fördefinierade frågor för Log Analytics arbets ytan. Om du vill komma åt frågorna går du till **loggar** och under **kategori**väljer du **virtuellt Windows-skrivbord**. Läs mer i [använda Log Analytics för funktionen diagnostik](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Uppdatering för fjärr skrivbords klient för Android

[Fjärr skrivbords klienten för Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) stöder nu Windows virtuella Skriv bords anslutningar. Från och med version 10.0.7 innehåller Android-klienten ett nytt gränssnitt för bättre användar upplevelse. Klienten integreras också med Microsoft Authenticator på Android-enheter för att aktivera villkorlig åtkomst när du prenumererar på Windows Virtual Desktop-arbetsytor.  

Den tidigare versionen av fjärr skrivbords klienten kallas nu "fjärr skrivbord 8". Alla befintliga anslutningar som du har i den tidigare versionen av klienten överförs sömlöst till den nya klienten. Den nya klienten har skrivits om till samma underliggande RDP Core-motor som iOS-och macOS-klienter, och snabbare lansera nya funktioner på alla plattformar. 

### <a name="teams-update"></a>Uppdatering av team

Vi har gjort förbättringar av Microsoft Teams för virtuella Windows-datorer. Det viktigaste är att Windows Virtual Desktop stöder ljud-och video optimering för Windows Desktop-klienten. Omdirigering förbättrar svars tiden genom att skapa direkta sökvägar mellan användare när de använder ljud eller video i samtal och möten. Mindre avstånd innebär färre hopp, vilket gör att anropen ser ut och ljuden blir mjukare. Läs mer i [använda team på Windows Virtual Desktop](teams-on-wvd.md).

## <a name="june-2020"></a>Juni 2020

Förra månaden introducerade vi Windows Virtual Desktop med Azure Resource Manager-integrering i för hands versionen. Den här uppdateringen innehåller många spännande nya funktioner som vi älskar att berätta om. Här är what's nya för den här versionen av det virtuella Windows-skrivbordet.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop är nu integrerat med Azure Resource Manager

Windows Virtual Desktop är nu integrerat i Azure Resource Manager. I den senaste uppdateringen Azure Resource Manager alla virtuella Windows-skrivbordet-objekt. Den här uppdateringen är också integrerad med rollbaserad åtkomst kontroll i Azure (Azure RBAC). Se [Vad är Azure Resource Manager?](../azure-resource-manager/management/overview.md) om du vill veta mer.

Den här ändringen gör du:

- Det virtuella Windows-skrivbordet är nu integrerat med Azure Portal. Det innebär att du kan hantera allt direkt i portalen, inga PowerShell-, webbappar eller verktyg från tredje part krävs. Kom igång genom att titta på vår självstudie i [skapa en adresspool med Azure Portal](create-host-pools-azure-marketplace.md).

- Innan den här uppdateringen kan du bara publicera RemoteApp-och skriv bord till enskilda användare. Med Azure Resource Manager kan du nu publicera resurser i Azure Active Directory grupper.

- Den tidigare versionen av Windows Virtual Desktop hade fyra inbyggda administratörs roller som du kan tilldela till en klient eller adresspool. Dessa roller finns nu i [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md). Du kan tillämpa dessa roller på alla virtuella Windows-datorer Azure Resource Manager objekt, vilket gör att du kan ha en fullständig, omfattande Delegerings modell.

- I den här uppdateringen behöver du inte längre köra Azure Marketplace eller GitHub-mallen upprepade gånger för att expandera en adresspool. Allt du behöver för att expandera en adresspool är att gå till din värd-pool i Azure Portal och välja **+ Lägg** till för att distribuera ytterligare värdar för sessioner.

- Distribution av värd bassäng är nu fullständigt integrerat med [galleriet för Azures delade avbildningar](../virtual-machines/windows/shared-image-galleries.md). Delade avbildnings galleriet är en separat Azure-tjänst som lagrar definitioner av virtuella dator avbildningar, inklusive avbildnings versioner. Du kan också använda global replikering för att kopiera och skicka dina avbildningar till andra Azure-regioner för lokal distribution.

- Övervakning av funktioner som används för att utföras via PowerShell eller diagnostik-webbappen har nu flyttats till Log Analytics i Azure Portal. Nu har du två alternativ för att visualisera dina rapporter. Du kan köra Kusto-frågor och använda arbets böcker för att skapa visuella rapporter.

- Du behöver inte längre slutföra Azure Active Directory (Azure AD) medgivande för att använda Windows Virtual Desktop. I den här uppdateringen autentiserar Azure AD-klienten på din Azure-prenumeration användarna och tillhandahåller Azure RBAC-kontroller för dina administratörer.


### <a name="powershell-support"></a>Stöd för PowerShell

Vi har lagt till nya AzWvd-cmdletar i modulen Azure PowerShell AZ med den här uppdateringen. Den här nya modulen stöds i PowerShell Core, som körs på .NET Core.

Installera modulen genom att följa anvisningarna i [Konfigurera PowerShell-modulen för Windows Virtual Desktop](powershell-module.md).

Du kan också se en lista över tillgängliga kommandon i [AzWvd PowerShell-referensen](/powershell/module/az.desktopvirtualization/?view=azps-4.2.0#desktopvirtualization).

Mer information om de nya funktionerna finns i [vårt blogg inlägg](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Ytterligare gatewayer

Vi har lagt till ett nytt Gateway-kluster i Sydafrika för att minska anslutnings fördröjningen.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams på Windows Virtual Desktop (för hands version)

Vi har gjort några förbättringar av Microsoft Teams för Windows Virtual Desktop. Det viktigaste är att Windows Virtual Desktop har nu stöd för ljud-och visuell omdirigering för samtal. Omdirigering förbättrar svars tiden genom att skapa direkta sökvägar mellan användare när de anropar med hjälp av ljud eller video. Mindre avstånd innebär färre hopp, vilket gör att anropen ser ut och ljuden blir mjukare.

Mer information finns i [vårt blogg inlägg](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om framtida planer i [Microsoft 365 översikt över virtuella Windows-datorer](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).

Ta en titt på de här artiklarna om du vill veta mer om uppdateringar för våra klienter för virtuella Windows-datorer och Fjärrskrivbordstjänster:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Webb](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)
