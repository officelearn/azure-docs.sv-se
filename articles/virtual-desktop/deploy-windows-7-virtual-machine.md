---
title: Distribuera Windows 7 virtuell dator Windows Virtual Desktop - Azure
description: Konfigurera och distribuera en virtuell Windows 7-dator på Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366691"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Distribuera en virtuell Windows 7-dator på Windows Virtual Desktop

Processen att distribuera en virtuell dator (VM) för Windows 7 på Windows Virtual Desktop skiljer sig något från för virtuella datorer som kör senare versioner av Windows. Den här guiden visar hur du distribuerar Windows 7.

## <a name="prerequisites"></a>Krav

Innan du börjar följer du instruktionerna i [Skapa en värdpool med PowerShell](create-host-pools-powershell.md) för att skapa en värdpool. Följ sedan instruktionerna i [Skapa värdpooler på Azure Marketplace](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) för att tilldela en eller flera användare till skrivbordsprogramgruppen.

## <a name="configure-a-windows-7-virtual-machine"></a>Konfigurera en virtuell Windows 7-dator

När du har gjort förutsättningarna är du redo att konfigurera din virtuella Windows 7-dator för distribution på Windows Virtual Desktop.

Så här konfigurerar du en virtuell Windows 7-dator på Windows Virtual Desktop:

1. Logga in på Azure-portalen och sök antingen efter Windows 7 Enterprise-avbildningen eller ladda upp din egen anpassade Windows 7 Enterprise-avbildning (x64).  
2. Distribuera en eller flera virtuella datorer med Windows 7 Enterprise som värdoperativsystem. Kontrollera att de virtuella datorerna tillåter RDP (Remote Desktop Protocol) (TCP/3389-porten).
3. Anslut till Windows 7 Enterprise-värden med hjälp av RDP och autentisera med de autentiseringsuppgifter du definierade när du konfigurerade distributionen. 
4. Lägg till kontot som du använde när du anslöt till värden med RDP till gruppen "Fjärrskrivbordsanvändare". Om du inte gör det kanske du inte kan ansluta till den virtuella datorn när du har anslutit den till Active Directory-domänen.
5. Gå till Windows Update på den virtuella datorn.
6. Installera alla Windows-uppdateringar i kategorin Viktigt.
7. Installera alla Windows-uppdateringar i kategorin Valfritt (exklusive språkpaket). Detta installerar uppdateringen För fjärrskrivbordsprotokoll 8.0[(KB2592687)](https://www.microsoft.com/download/details.aspx?id=35387)som du behöver för att slutföra dessa instruktioner.
8. Öppna redigeraren för lokala grupprinciper och navigera till**administrativa mallar för** >  **datorkonfiguration** > **Windows Components** > **Fjärrskrivbordssession** > **värd för** > **fjärrsession.**
9. Aktivera principen För fjärrskrivbordsprotokoll 8.0.
10. Gå med i den här virtuella datorn till Active Directory-domänen.
11. Starta om den virtuella datorn genom att köra följande kommando:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Följ instruktionerna [här](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) för att få en registreringstoken.
13. [Ladda ned Windows Virtual Desktop Agent för Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Ladda ned Windows Virtual Desktop Agent Manager för Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Öppna installationsprogrammet för Windows Virtual Desktop Agent och följ instruktionerna. När du uppmanas till det anger du registreringsnyckeln som du skapade i steg 12.
16. Öppna installationsprogrammet för Windows Virtual Desktop och följ instruktionerna.
17. Du kan också blockera TCP/3389-porten för att ta bort direkt åtkomst till fjärrskrivbordsprotokoll till den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Distributionen av Windows Virtual Desktop är nu klar att användas. [Hämta den senaste versionen av Windows Virtual Desktop-klienten](https://aka.ms/wvd/clients/windows) för att komma igång.

En lista över kända problem och felsökningsinstruktioner för Windows 7 på Virtuellt skrivbord i Windows finns i felsöka artikeln på [Felsöka virtuella Datorer i Windows 7 i Windows Virtual Desktop](troubleshoot-windows-7-vm.md).
