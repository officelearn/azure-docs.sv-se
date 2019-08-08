---
title: FSLogix profil behållare och Azure Files i Windows Virtual Desktop – Azure
description: I den här artikeln beskrivs FSLogix profil behållare i Windows Virtual Desktop och Azure Files.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: 13f80b42ef6dfd0c70067a3ecf5f3e172527a63f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846528"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profilcontainrar och Azure-filer

I förhands gransknings tjänsten för Windows Virtual Desktop rekommenderas FSLogix profil behållare som en användar profil lösning. FSLogix är utformad för centrala profiler i fjärrdatorer, till exempel Windows Virtual Desktop. Den lagrar en fullständig användar profil i en enda behållare. Vid inloggningen är den här behållaren dynamiskt kopplad till dator miljön med inbyggd virtuell hård disk (VHD) och virtuell Hyper-V-hårddisk (VHDX) som stöds. Användar profilen är omedelbart tillgänglig och visas i systemet precis som en inbyggd användar profil.

I den här artikeln beskriver vi FSLogix profil behållare som används med Azure Files. Informationen finns i kontexten för det virtuella Windows-skrivbordet, som har [annonser ATS den 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Användarprofiler

En användar profil innehåller data element om en individ, inklusive konfigurations information som Skriv bords inställningar, beständiga nätverks anslutningar och program inställningar. Som standard skapar Windows en lokal användar profil som är nära integrerad med operativ systemet.

En fjärran sluten användar profil innehåller en partition mellan användar data och operativ systemet. Det gör att operativ systemet kan ersättas eller ändras utan att påverka användar data. I RDSH (Remote Desktop Session Host) och VDI (Virtual Desktop Infrastructure) kan operativ systemet ersättas av följande orsaker:

- En uppgradering av operativ systemet
- Ersätta en befintlig virtuell dator (VM)
- En användare som ingår i en mellanliggande RDSH-eller VDI-miljö (icke-beständig)

Microsoft-produkter samarbetar med flera tekniker för fjärran vändare, inklusive dessa tekniker:
- Centrala användar profiler (RUP)
- Användar profil diskar (UPD)
- Enterprise State roaming (ESR)

UPD och RUP är de mest använda teknikerna för användar profiler i RDSH-och VHD-miljöer (Virtual Hard Disk).

### <a name="challenges-with-previous-user-profile-technologies"></a>Utmaningar med tidigare användar profil tekniker

Befintliga och äldre Microsoft-lösningar för användar profiler följde med olika utmaningar. Ingen tidigare lösning hanterade alla användar profil behov som medföljer en RDSH-eller VDI-miljö. UPD kan till exempel inte hantera stora OST-filer och RUP har inte kvar moderna inställningar.

#### <a name="functionality"></a>Funktioner

I följande tabell visas fördelarna och begränsningarna för tidigare användar profil tekniker.

| Teknik | Moderna inställningar | Win32-inställningar | OS-inställningar | Användardata | Stöds på Server-SKU: n | Server dels lagring på Azure | Server dels lagring lokalt | Versions stöd | Efterföljande inloggnings tid |Anteckningar|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Användar profil diskar (UPD)** | Ja | Ja | Ja | Ja | Ja | Nej | Ja | Win 7 + | Ja | |
| **Central användar profil (RUP), underhålls läge** | Nej | Ja | Ja | Ja | Ja| Nej | Ja | Win 7 + | Nej | |
| **Enterprise State Roaming (ESR)** | Ja | Nej | Ja | Nej | Se kommentarer | Ja | Nej | Win 10 | Nej | Funktioner på Server-SKU men saknar stöd för användar gränssnitt |
| **User Experience Virtualization (UE-V)** | Ja | Ja | Ja | Nej | Ja | Nej | Ja | Win 7 + | Nej |  |
| **OneDrive-molnappar** | Nej | Nej | Nej | Ja | Se kommentarer | Se kommentarer  | Se kommentarer | Win 10-RS3 | Nej | Inte testat på Server-SKU. Backend-lagring på Azure är beroende av Sync-klienten. Backend-lokal kräver en sync-klient. |

#### <a name="performance"></a>Prestanda

UPD kräver [Lagringsdirigering (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) för att uppfylla prestanda kraven. UPD använder SMB-protokoll (Server Message Block). Profilen kopieras till den virtuella dator där användaren loggas. UPD med S2D var lösningen för RDS-teamet som rekommenderas för Windows Virtual Desktop under för hands versionen av tjänsten.  

#### <a name="cost"></a>Kostnad

Även om S2D-kluster uppnår nödvändiga prestanda, är kostnaden dyra för företags kunder, men särskilt dyra för små och medel stora företags (SMB)-kunder. För den här lösningen betalar företag för lagrings diskar, tillsammans med kostnaden för de virtuella datorer som använder diskarna för en resurs.

#### <a name="administrative-overhead"></a>Administrativa kostnader

S2D-kluster kräver ett operativ system som korrigeras, uppdateras och bevaras i säkert tillstånd. De här processerna och komplexiteten vid installation av S2D-haveri beredskap gör S2D endast möjligt för företag med en dedikerad IT-personal.

## <a name="fslogix-profile-containers"></a>FSLogix profil behållare

Den 19 november 2018 har [Microsoft förvärvat FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix hanterar flera utmaningar för profil behållare. Nyckel mellan dem är:

- **Historik** [FSLogix profil behållare](https://fslogix.com/products/profile-containers) är höga prestanda och löser prestanda problem som har historiskt blockerat cachelagrat Exchange-läge.
- **OneDrive:** Utan FSLogix profil behållare stöds inte OneDrive för företag i icke-permanent RDSH-eller VDI-miljöer. [Bästa praxis för OneDrive för företag och FSLogix](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) beskriver hur de interagerar. Mer information finns i [använda Sync-klienten på virtuella skriv bord](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Ytterligare mappar:** FSLogix ger möjlighet att utöka användar profiler för att inkludera ytterligare mappar.

Eftersom förvärvs tjänsten ersatte befintliga användar profil lösningar, t. ex. UPD, med FSLogix profil behållare.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Files integrering med Azure Active Directory Domain Service

FSLogix profil behållares prestanda och funktioner utnyttjar molnet. Den 7 augusti 2019 Microsoft Azure filer som är allmänt tillgängliga för [Azure Files autentisering med Azure Active Directory Domain Service (AD DS)](/articles/storage/files/storage-files-active-directory-overview.md). Genom att adressera både kostnads-och administrations kostnader är Azure Files med Azure AD DS-autentisering en Premium-lösning för användar profiler i Windows Virtual Desktop-tjänsten.

## <a name="best-practices-for-windows-virtual-desktop"></a>Metod tips för virtuella Windows-datorer

Virtuella Windows-datorer ger fullständig kontroll över storlek, typ och antal virtuella datorer som används av kunderna. Mer information finns i [Vad är för hands versionen av Windows Virtual Desktop?](overview.md).

För att se till att din Windows Virtual Desktop-miljö följer bästa praxis:

- Azure Files lagrings kontot måste finnas i samma region som de virtuella datorerna i sessionen.
- Azure Files behörigheter ska matcha behörigheter som beskrivs i [behållare för krav – profiler](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Varje adresspool måste vara inbyggd av samma typ och storlek som den virtuella datorn baserat på samma huvud avbildning.
- Varje virtuell dator i poolen för värdar måste finnas i samma resurs grupp för att hantera, skala och uppdatera.
- För optimala prestanda bör lagrings lösningen och behållaren för FSLogix-profilen finnas på samma plats i data centret.
- Lagrings kontot som innehåller huvud avbildningen måste finnas i samma region och i den prenumeration där de virtuella datorerna har allokerats.

## <a name="next-steps"></a>Nästa steg

Använd följande guider för att skapa en Windows-miljö för virtuella skriv bord.

- Om du vill börja skapa en lösning för Skriv bords virtualisering läser du [skapa en klient i Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
- Information om hur du skapar en adresspool i din Windows-klient för virtuella datorer finns i [skapa en adresspool med Azure Marketplace](create-host-pools-azure-marketplace.md).
- Information om hur du konfigurerar fullständigt hanterade fil resurser i molnet finns i [konfigurera Azure Files-resurs](/articles/storage/files/storage-files-active-directory-enable.md).
- Information om hur du konfigurerar FSLogix profil behållare finns i [Konfigurera en användar profil resurs för en värd-pool](create-host-pools-user-profile.md).
- Information om hur du tilldelar användare till en adresspool finns i [Hantera app-grupper för Windows Virtual Desktop](manage-app-groups.md).
- För att få åtkomst till dina Windows-resurser för virtuella skriv bord från en webbläsare, se [Anslut till Windows Virtual Desktop](connect-web.md).
