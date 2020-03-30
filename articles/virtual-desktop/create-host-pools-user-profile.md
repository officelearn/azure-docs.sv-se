---
title: Windows Virtual Desktop FSLogix-profilbehållareresurs - Azure
description: Konfigurera en FSLogix-profilbehållare för en värdpool för Windows Virtual Desktop med hjälp av en filresursbaserad filresurs med en virtuell datorbaserad filresurs.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250925"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Skapa en profilcontainer för en värdpool med hjälp av en filresurs

Tjänsten Windows Virtual Desktop erbjuder FSLogix-profilbehållare som den rekommenderade användarprofillösningen. Vi rekommenderar inte att du använder UPD-lösningen (User Profile Disk), som kommer att vara inaktuell i framtida versioner av Windows Virtual Desktop.

I den här artikeln får du veta hur du konfigurerar en FSLogix-profilbehållare för en värdpool med hjälp av en filresursbaserad filresurs för virtuella datorer. Mer dokumentation för FSLogix finns på [FSLogix-webbplatsen](https://docs.fslogix.com/).

>[!NOTE]
>Om du letar efter jämförelsematerial om de olika lagringsalternativen för FSLogix-profilbehållare på Azure läser du [Lagringsalternativ för FSLogix-profilbehållare](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Skapa en ny virtuell dator som fungerar som en filresurs

När du skapar den virtuella datorn ska du se till att placera den på antingen samma virtuella nätverk som virtuella värdpooldatorer eller i ett virtuellt nätverk som har anslutning till virtuella värdpooldatorer. Du kan skapa en virtuell dator på flera sätt:

- [Skapa en virtuell dator från en Azure Gallery-avbildning](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Skapa en virtuell dator från en hanterad avbildning](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Skapa en virtuell dator från en ohanterad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

När du har skapat den virtuella datorn ansluter du den till domänen genom att göra följande:

1. [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Starta **Kontrollpanelen** på den virtuella datorn och välj **System**.
3. Välj **Datornamn,** välj **Ändra inställningar**och välj sedan **Ändra...**
4. Välj **Domän** och ange sedan Active Directory-domänen i det virtuella nätverket.
5. Autentisera med ett domänkonto som har behörighet att ansluta datorer för domänkoppling.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Förbereda den virtuella datorn så att den fungerar som en filresurs för användarprofiler

Följande är allmänna instruktioner om hur du förbereder en virtuell dator för att fungera som en filresurs för användarprofiler:

1. Lägg till Active Directory-användare i Windows Virtual Desktop i en [Active Directory-säkerhetsgrupp](/windows/security/identity-protection/access-control/active-directory-security-groups/). Den här säkerhetsgruppen används för att autentisera Windows Virtual Desktop-användare till den virtuella datorn för filresurs som du just skapade.
2. [Anslut till den virtuella datorn för filresurs](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Skapa en mapp på **C-enheten** på den virtuella datorn för filresurs som ska användas som profilresurs.
4. Högerklicka på den nya mappen, välj **Egenskaper**, välj **Delning**och välj sedan **Avancerad delning...**.
5. Välj **Dela den här mappen,** välj **Behörigheter...** och välj sedan **Lägg till...**.
6. Sök efter den säkerhetsgrupp som du har lagt till windows virtual desktop-användare i och kontrollera att gruppen har **fullständig kontroll**.
7. När du har lagt till säkerhetsgruppen högerklickar du på mappen, väljer **Egenskaper**, väljer **Delning**och kopierar sedan den **nätverkssökväg** som ska användas för senare.

Mer information om behörigheter finns i [FSLogix-dokumentationen](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurera FSLogix-profilbehållaren

Så här konfigurerar du de virtuella datorerna med FSLogix-programvaran och gör följande på varje dator som är registrerad i värdpoolen:

1. [Anslut till den virtuella datorn](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) med de autentiseringsuppgifter du angav när du skapade den virtuella datorn.
2. Starta en webbläsare och navigera till [denna länk](https://go.microsoft.com/fwlink/?linkid=2084562) för att ladda ner FSLogix agent.
3. Navigera till \\ \\\\Win32 \\ \\Release eller\\X64 Release i ZIP-filen och kör **FSLogixAppsSetup** för att installera FSLogix-agenten.  Mer information om hur du installerar FSLogix finns i [Hämta och installera FSLogix](/fslogix/install-ht/).
4. Navigera till **Program Files** > **FSLogix** > **Apps** för att bekräfta agenten installerad.
5. På startmenyn kör du **RegEdit** som administratör. Navigera till **\\dator HKEY_LOCAL_MACHINE\\\\programvara FSLogix**.
6. Skapa en nyckel med namnet **Profiler**.
7. Skapa följande värden för profilnyckeln:

| Namn                | Typ               | Data/värde                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | Värde med flera strängar | "Nätverkssökväg för filresurs"     |

>[!IMPORTANT]
>För att skydda din Windows Virtual Desktop-miljö i Azure rekommenderar vi att du inte öppnar inkommande port 3389 på dina virtuella datorer. Windows Virtual Desktop kräver inte en öppen inkommande port 3389 för att användare ska komma åt värdpoolens virtuella datorer. Om du måste öppna port 3389 för felsökning rekommenderar vi att du använder [ny vm-åtkomst](../security-center/security-center-just-in-time.md).
