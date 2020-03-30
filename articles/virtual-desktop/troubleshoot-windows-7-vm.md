---
title: Windows 7 virtuella datorer Windows Virtual Desktop - Azure
description: Så här löser du problem för virtuella Datorer med Windows 7 (VMs) i en Windows Virtual Desktop-miljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127388"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Felsöka en virtuell Windows 7-dator i Windows Virtual Desktop

Använd den här artikeln för att felsöka problem som du har när du konfigurerar Windows Virtual Desktop-sessionen är värd för virtuella datorer .

## <a name="known-issues"></a>Kända problem

Windows 7 på virtuella skrivbord i Windows stöder inte följande funktioner:

- Virtualiserade program (RemoteApps)
- Omdirigering av tidszon
- Automatisk DPI-skalning

Windows Virtual Desktop kan bara virtualisera fullständiga skrivbord för Windows 7.

Automatisk DPI-skalning stöds inte, men du kan manuellt ändra upplösningen på den virtuella datorn genom att högerklicka på ikonen i klienten för fjärrskrivbord och välja **Upplösning**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Fel: Det går inte att komma åt användargruppen för fjärrskrivbord

Om Windows Virtual Desktop inte hittar dig eller användarnas autentiseringsuppgifter i gruppen Fjärrskrivbordsanvändare kan något av följande felmeddelanden visas:

- "Den här användaren är inte medlem i gruppen Fjärrskrivbordsanvändare"
- "Du måste ha behörighet att logga in via Fjärrskrivbordstjänster"

Lös det här felet genom att lägga till användaren i gruppen Användare av fjärrskrivbord:

1. Öppna Azure Portal.
2. Välj den virtuella datorn som du såg felmeddelandet på.
3. Välj **Kör ett kommando**.
4. Kör följande kommando `<username>` med namnet på den användare som du vill lägga till:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```