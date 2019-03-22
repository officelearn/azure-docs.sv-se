---
title: Skapa en användare profil resurs för en värd-pool – Azure
description: Hur du ställer in en FSLogix profil-behållare för en pool för virtuella Windows-skrivbordet (förhandsversion) värden.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 9dfbda6e17cf954369fd6caa533ba9eef41fd451
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336022"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Skapa en användare profil resurs för en värd-pool

Tjänsten Windows virtuellt skrivbord (förhandsversion) erbjuder FSLogix profil behållare som rekommenderade användarens profil lösning. Vi rekommenderar inte lösningen användarens profil Disk (UPD) och upphör att gälla i framtida versioner av virtuella Windows-skrivbordet.

Det här avsnittet kommer berättar hur du ställer in en resurs FSLogix profil behållare för en värd-pool.

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

1. Ansluta session host-datorer till en [Active Directory-säkerhetsgrupp](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Denna säkerhetsgrupp används för att autentisera session värdar virtuella datorer till filresurs virtuella datorn som du nyss skapade.
2. [Ansluta till filen resurs virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Skapa en mapp på filen dela virtuell dator på den **C-enheten** som ska användas som resursen.
4. Högerklicka på den nya mappen, Välj **egenskaper**väljer **delning**och välj sedan **Avancerad delning...** .
5. Välj **dela den här mappen**väljer **behörigheter...** och välj sedan **Lägg till...** .
6. Söker du efter gruppen som du har lagt till de session virtuella datorerna och kontrollera att gruppen har **fullständig kontroll**.
7. När du lägger till säkerhetsgruppen, högerklicka på mappen, Välj **egenskaper**väljer **delning**, kopiera sedan ned den **nätverkssökvägen** ska användas för senare.

Bästa metoder för behörigheter finns i följande [FSLogix dokumentation](https://support.fslogix.com/index.php/forum-main/faqs/84-best-practices#120).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurera behållaren FSLogix profil

För att konfigurera de virtuella datorerna med FSLogix-programvara, gör du följande på varje dator som är registrerad till poolen värden:

1. [Ansluta till den virtuella datorn](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) med de autentiseringsuppgifter som du angav när du skapar den virtuella datorn.
2. Starta en webbläsare och gå till följande [länk](https://go.microsoft.com/fwlink/?linkid=2084562) att hämta FSLogix-agenten. Som en del av den offentliga förhandsversionen av virtuella Windows-skrivbordet får du en licensnyckel för att aktivera FSLogix programvaran. Nyckeln är LicenseKey.txt-filen som ingår i ZIP-filen FSLogix agent.
3. Installera agenten FSLogix.
4. Gå till **programfiler** > **FSLogix** > **appar** att bekräfta att agenten installerad.
5. Kör från start-menyn **RegEdit** som administratör. Gå till **datorn\\HKEY_LOCAL_MACHINE\\programvara\\FSLogix\\profiler**
6. Skapa följande värden:

| Namn                | Type               | Data/värde                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | Multisträngvärde | ”Nätverkssökväg för filresursen” |
| VolumeType          | String             | VHDX                              |
| SizeInMBs           | DWORD              | ”heltal för storleken på profilen”     |
| IsDynamic           | DWORD              | 1                                 |
| LockedRetryCount    | DWORD              | 1                                 |
| LockedRetryInterval | DWORD              | 0                                 |
