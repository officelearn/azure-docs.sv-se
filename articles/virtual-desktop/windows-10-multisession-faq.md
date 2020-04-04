---
title: Vanliga frågor och svar om windows 10 företagssessioner – Azure
description: Vanliga frågor och metodtips för hur du använder Windows 10 Enterprise-flersessioner för Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7ea99e2ee8e2882c211ee17acec70222dc058a8
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637087"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Vanliga frågor och svar om flera sessioner för Windows 10 Enterprise

I den här artikeln besvaras vanliga frågor och metodtips för flersessioner med Windows 10 Enterprise.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Vad är Windows 10 Enterprise multi-session?

Windows 10 Enterprise multi-session, tidigare känd som Windows 10 Enterprise for Virtual Desktops (EVD), är en ny värd för fjärrskrivbordssession som tillåter flera samtidiga interaktiva sessioner. Tidigare kunde bara Windows Server göra detta. Den här funktionen ger användarna en välbekant Windows 10-upplevelse medan IT kan dra nytta av kostnadsfördelarna med flerasessioner och använda befintlig Windows-licensiering per användare i stället för klientåtkomstlicenser för fjärrskrivbordstjänster (CALs). Mer information om licenser och priser finns i [Priser för Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Hur många användare kan samtidigt ha en interaktiv session på Windows 10 Enterprise multi-session?

Hur många interaktiva sessioner som kan vara aktiva samtidigt förlitar sig på systemets maskinvaruresurser (vCPU, minne, disk och vGPU), hur användarna använder sina appar när de är inloggade på en session och hur tung systemets arbetsbelastning är. Vi föreslår att du validerar systemets prestanda för att förstå hur många användare du kan ha på Windows 10 Enterprise multi-session. Mer information finns i [Windows Priser för virtuellt skrivbord](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Varför rapporterar mitt program Windows 10 Enterprise multi-session som ett serveroperativsystem?

Windows 10 Enterprise multi-session är en virtuell utgåva av Windows 10 Enterprise. En av skillnaderna är att det här operativsystemet (OS) rapporterar [att ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) har värdet 3, samma värde som Windows Server. Den här egenskapen håller operativsystemet kompatibelt med befintliga RDSH-hanteringsverktyg, RDSH-multisessionsmedvetna program och mestadels systemprestandaoptimeringar på låg nivå för RDSH-miljöer. Vissa programinstallatörer kan blockera installation på Windows 10 multisession beroende på om de upptäcker att ProductType är inställt på Klient. Om appen inte installeras kontaktar du programleverantören för en uppdaterad version. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Kan jag köra Windows 10 Enterprise multi-session lokalt?

Windows 10 Enterprise multi-session kan inte köras i lokala produktionsmiljöer eftersom det är optimerat för Windows Virtual Desktop-tjänsten för Azure. Det är emot licensavtalet att köra Windows 10 Enterprise multi-session utanför Azure för produktionsändamål. Windows 10 Enterprise multi-session aktiveras inte mot lokala Key Management Services (KMS).
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Hur anpassar jag windows 10 Enterprise-flersessionsavbildningen för min organisation?

Du kan starta en virtuell dator (VM) i Azure med Windows 10 Windows 10 Enterprise multi-session och anpassa den genom att installera LOB-program, sysprep/generalize och sedan skapa en avbildning med Azure-portalen.  
 
För att komma igång, skapa en virtuell dator i Azure med Windows 10 Enterprise multi-session. I stället för att starta den virtuella datorn i Azure kan du hämta den virtuella hårddisken direkt. Därefter kan du använda den virtuella hårddisk som du hämtade för att skapa en ny virtuell dator med generation 1 på en Windows 10-dator med Hyper-V aktiverat.

Anpassa avbildningen efter dina behov genom att installera LOB-program och systemprep avbildningen. När du är klar med anpassningen laddar du upp avbildningen till Azure med den virtuella hårddisken inuti. Därefter hämtar du Windows Virtual Desktop från Azure Marketplace och använder det för att distribuera en ny värdpool med den anpassade avbildningen.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Hur hanterar jag Windows 10 Enterprise multi-session efter distributionen?

Du kan använda alla konfigurationsverktyg som stöds, men vi rekommenderar Configuration Manager version 1906 eftersom den stöder flersessions med Windows 10 Enterprise. Vi arbetar för närvarande med Microsoft Intune-support.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Kan Windows 10 Enterprise multi-session vara Azure Active Directory (AD)-anslutna?

Windows 10 Enterprise multi-session stöds för närvarande för att vara hybrid Azure AD-anslutna. När Windows 10 Enterprise multi-session är domänansluten använder du det befintliga grupprincipobjektet för att aktivera Azure AD-registrering. Mer information finns i [Planera implementeringen av azure Active Directory-hybridanslutning](../active-directory/devices/hybrid-azuread-join-plan.md).
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Var hittar jag windows 10 Enterprise multi-session bild?

Windows 10 Enterprise multi-session finns i Azure-galleriet. Du hittar den genom att navigera till Azure-portalen och söka efter windows 10 Enterprise for Virtual Desktops-versionen. En avbildning som är integrerad med Office Pro Plus finns i Azure-portalen och söker efter Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Vilken windows 10 Enterprise-avbildning för flera sessionser kan jag använda?

Azure-galleriet har flera versioner, inklusive Windows 10 Enterprise multi-session, version 1809 och Windows 10 Enterprise multi-session, version 1903. Vi rekommenderar att du använder den senaste versionen för förbättrad prestanda och tillförlitlighet.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Vilka Windows 10 Enterprise-flersessionsversioner stöds?

Windows 10 Enterprise multi-session, versioner 1809 och senare stöds och är tillgängliga i Azure-galleriet. Dessa versioner följer samma supportlivscykelpolicy som Windows 10 Enterprise, vilket innebär att vårversionen stöds i 18 månader och höstversionen i 30 månader.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Vilken profilhanteringslösning ska jag använda för windows 10 Enterprise-flersession?

Vi rekommenderar att du använder FSLogix-profilbehållare när du konfigurerar Windows 10 Enterprise i icke-beständiga miljöer eller andra scenarier som behöver en centralt lagrad profil. FSLogix ser till att användarprofilen är tillgänglig och uppdaterad för varje användarsession. Vi rekommenderar också att du använder din FSLogix-profilbehållare för att lagra en användarprofil i valfri SMB-resurs med lämpliga behörigheter, men du kan lagra användarprofiler i Azure-sidblobblagring om det behövs. Windows Virtual Desktop-användare kan använda FSLogix utan extra kostnad.
 
Mer information om hur du konfigurerar en FSLogix-profilbehållare finns i [Konfigurera FSLogix-profilbehållaren](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Vilken licens behöver jag för att komma åt Windows 10 Enterprise multi-session?

En fullständig lista över tillämpliga licenser finns i [Windows Virtual Desktop-priser](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Varför försvinner mina appar när jag har loggat ut?

Detta beror på att du använder Windows 10 Enterprise multi-session med en profilhanteringslösning som FSLogix. Administratörs- eller profillösningen konfigurerade systemet för att ta bort användarprofiler när användare loggar ut. Den här konfigurationen innebär att när systemet tar bort din användarprofil när du har loggat ut tas även alla appar som du installerade under sessionen bort. Om du vill behålla de appar du har installerat måste du be administratören att etablera dessa appar för alla användare i windows virtual desktop-miljö.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Hur ser jag till att appar inte försvinner när användare loggar ut?

De flesta virtualiserade miljöer är konfigurerade som standard för att förhindra att användare installerar ytterligare appar till sina profiler. Om du vill vara säker på att en app inte försvinner när användaren loggar ut från Windows Virtual Desktop måste du etablera appen för alla användarprofiler i din miljö. Mer information om hur du etablerar appar finns i följande resurser:

- [Publicera inbyggda appar i Windows Virtual Desktop](publish-apps.md)
- [Kommandoradsalternativ för DISM-apppaket](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Tillägg-AppxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Hur ser jag till att användarna inte hämtar och installerar appar från Microsoft Store?

Du kan inaktivera Microsoft Store-appen för att se till att användarna inte laddar ned extra appar utöver de appar som du redan har etablerat för dem.

Så här inaktiverar du Store-appen:

1. Skapa en ny grupprincip.
2. Välj**Administrativa mallar för** >  **datorkonfiguration** > **Windows-komponenter**.
3. Välj **Store**.
4. Välj **Store-program**.
5. Välj **Inaktiverat**och välj sedan **OK**.
6. Välj **Använd**.
 
## <a name="next-steps"></a>Nästa steg

Mer information om Windows Virtual Desktop och Windows 10 Enterprise multi-session:

- Läs [dokumentationen för förhandsversionen av Windows Virtual Desktop](overview.md)
- Besök vår [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Konfigurera distributionen av Windows Virtual Desktop med [självstudierna för Virtuellt skrivbord i Windows](tenant-setup-azure-active-directory.md)
