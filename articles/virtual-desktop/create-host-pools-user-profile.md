---
title: Skapa en användare profil resurs för en förhandsversion för virtuella skrivbord i Windows-värd pool - Azure
description: Hur du ställer in en FSLogix profil-behållare för en pool för förhandsversion för virtuella skrivbord i Windows-värd.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 0cb4df099faad8ca482fd15cf0bb50504c1528ab
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276396"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Skapa en användarprofilresurs för en värdpool

Windows Virtual Desktop förhandsversionen av tjänsten erbjuder FSLogix profil behållare som rekommenderade användarens profil lösning. Vi rekommenderar inte med hjälp av lösningen användarens profil Disk (UPD), som är föråldrade i framtida versioner av virtuella Windows-skrivbordet.

Det här avsnittet kommer berättar hur du ställer in en resurs FSLogix profil behållare för en värd-pool. Allmän dokumentation om FSLogix finns i den [FSLogix plats](https://docs.fslogix.com/).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Skapa en ny virtuell dator som fungerar som en filresurs

När du skapar den virtuella datorn, måste du placera den på antingen samma virtuella nätverk som värd pool-datorer eller på ett virtuellt nätverk som är ansluten till de poolen virtuella datorerna. Du kan skapa en virtuell dator på flera olika sätt:

- [Skapa en virtuell dator från en avbildning i Azure-galleriet](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Skapa en virtuell dator från en hanterad avbildning](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Skapa en virtuell dator från en ohanterad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

När du har skapat den virtuella datorn, ansluter du den till domänen genom att göra följande:

1. [Ansluta till den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) med de autentiseringsuppgifter som du angav när du skapar den virtuella datorn.
2. På den virtuella datorn, startar **Kontrollpanelen** och välj **System**.
3. Välj **datornamn**väljer **ändra inställningarna för**, och välj sedan **ändringen...**
4. Välj **domän** och ange sedan Active Directory-domänen i det virtuella nätverket.
5. Autentisera med ett domänkonto som har behörighet att domänanslutning datorer.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Förbereda den virtuella datorn ska fungera som en filresurs för användarprofiler

Här följer allmänna instruktioner om hur du förbereder en virtuell dator att fungera som en filresurs för användarprofiler:

1. Lägga till Windows Virtual Desktop Active Directory-användare i en [Active Directory-säkerhetsgrupp](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Denna säkerhetsgrupp används för att autentisera virtuellt skrivbord i Windows-användare till filresurs virtuella datorn som du nyss skapade.
2. [Ansluta till filen resurs virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Skapa en mapp på filen dela virtuell dator på den **C-enheten** som ska användas som resursen.
4. Högerklicka på den nya mappen, Välj **egenskaper**väljer **delning**och välj sedan **Avancerad delning...** .
5. Välj **dela den här mappen**väljer **behörigheter...** och välj sedan **Lägg till...** .
6. Söker du efter gruppen som du har lagt till de virtuella Windows-skrivbordet och kontrollera att gruppen har **fullständig kontroll**.
7. När du lägger till säkerhetsgruppen, högerklicka på mappen, Välj **egenskaper**väljer **delning**, kopiera sedan ned den **nätverkssökvägen** ska användas för senare.

Mer information om behörigheter finns i den [FSLogix dokumentation](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurera behållaren FSLogix profil

För att konfigurera de virtuella datorerna med FSLogix-programvara, gör du följande på varje dator som är registrerad till poolen värden:

1. [Ansluta till den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) med de autentiseringsuppgifter som du angav när du skapar den virtuella datorn.
2. Starta en webbläsare och gå till [den här länken](https://go.microsoft.com/fwlink/?linkid=2084562) att hämta FSLogix-agenten. Som en del av den offentliga förhandsversionen av virtuella Windows-skrivbordet får du en licensnyckel för att aktivera FSLogix programvaran. Nyckeln är LicenseKey.txt-filen som ingår i ZIP-filen FSLogix agent.
3. Navigera till någon \\ \\Win32\\versionen eller \\ \\X64\\versionen i ZIP-filen och kör **FSLogixAppsSetup** att installera agenten FSLogix.
4. Gå till **programfiler** > **FSLogix** > **appar** att bekräfta att agenten installerad.
5. Kör från start-menyn **RegEdit** som administratör. Gå till **datorn\\HKEY_LOCAL_MACHINE\\programvara\\FSLogix**.
6. Skapa en nyckel med namnet **profiler**.
7. Skapa följande värden för nyckeln profiler:

| Namn                | Typ               | Data/värde                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | Multisträngvärde | ”Nätverkssökväg för filresursen”     |

>[!IMPORTANT]
>Om du vill att skydda din miljö för virtuella Windows-skrivbordet i Azure, rekommenderar vi du inte öppna inkommande port 3389 på dina virtuella datorer. Virtuella Windows-skrivbordet kräver inte en öppen inkommande port 3389 för användare att komma åt värden poolens virtuella datorer. Om du måste öppna port 3389 för felsökningsändamål kan vi rekommenderar att du använder [åtkomst till Virtuella just-in-time](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time).