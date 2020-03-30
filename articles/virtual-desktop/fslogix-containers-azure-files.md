---
title: Windows Virtual Desktop FSLogix-profilbehållare filer - Azure
description: I den här artikeln beskrivs FSLogix-profilbehållare i Windows Virtual Desktop- och Azure-filer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127874"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profilcontainrar och Azure-filer

Tjänsten Windows Virtual Desktop rekommenderar FSLogix-profilbehållare som en användarprofillösning. FSLogix är utformat för att flytta profiler i fjärrdatormiljöer, till exempel Windows Virtual Desktop. Den lagrar en komplett användarprofil i en enda behållare. Vid inloggningen är den här behållaren dynamiskt kopplad till datormiljön med inbyggd virtuell hårddisk (VHD) och Hyper-V Virtual Hard disk (VHDX). Användarprofilen är omedelbart tillgänglig och visas i systemet precis som en inbyggd användarprofil. I den här artikeln beskrivs hur FSLogix-profilbehållare som används med Azure Files fungerar i Windows Virtual Desktop.

>[!NOTE]
>Om du letar efter jämförelsematerial om de olika lagringsalternativen för FSLogix-profilbehållare på Azure läser du [Lagringsalternativ för FSLogix-profilbehållare](store-fslogix-profile.md).

## <a name="user-profiles"></a>Användarprofiler

En användarprofil innehåller dataelement om en individ, inklusive konfigurationsinformation som skrivbordsinställningar, beständiga nätverksanslutningar och programinställningar. Som standard skapas en lokal användarprofil som är tätt integrerad med operativsystemet.

En fjärranvändarprofil ger en partition mellan användardata och operativsystemet. Det gör att operativsystemet kan bytas ut eller ändras utan att påverka användardata. I RDSH-infrastruktur (Remote Desktop Session Host) och Virtual Desktop Infrastructures (VDI) kan operativsystemet ersättas av följande skäl:

- En uppgradering av operativsystemet
- En ersättning av en befintlig virtuell dator (VM)
- En användare som ingår i en poolad (icke-beständig) RDSH- eller VDI-miljö

Microsofts produkter arbetar med flera tekniker för fjärranvändarprofiler, inklusive följande tekniker:
- Centrala användarprofiler (RUP)
- Användarprofildiskar (UPD)
- Roaming i företagstillstånd (ESR)

UPD och RUP är de mest använda teknikerna för användarprofiler i RDSH-miljöer (Remote Desktop Session Host) och Virtual Hard Disk (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Utmaningar med tidigare användarprofilteknik

Befintliga och äldre Microsoft-lösningar för användarprofiler kom med olika utmaningar. Ingen tidigare lösning hanterade alla användarprofilbehov som följer med en RDSH- eller VDI-miljö. UPD kan till exempel inte hantera stora OST-filer och RUP finns inte med i moderna inställningar.

#### <a name="functionality"></a>Funktioner

Följande tabell visar fördelar och begränsningar för tidigare användarprofilteknik.

| Teknologi | Moderna inställningar | Win32-inställningar | Inställningar för operativsystem | Användardata | Stöds på servern SKU | Backend-lagring på Azure | Backend-lagring lokalt | Stöd för version | Efterföljande inloggningstid |Anteckningar|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Användarprofildiskar (UPD)** | Ja | Ja | Ja | Ja | Ja | Inga | Ja | Vinn 7+ | Ja | |
| **Rup (Central User Profile), underhållsläge** | Inga | Ja | Ja | Ja | Ja| Inga | Ja | Vinn 7+ | Inga | |
| **Roaming i företagsstater (ESR)** | Ja | Inga | Ja | Inga | Se anteckningar | Ja | Inga | Vinn 10 | Inga | Funktioner på servern SKU men inget stöd användargränssnitt |
| **Virtualisering av användarupplevelsen (UE-V)** | Ja | Ja | Ja | Inga | Ja | Inga | Ja | Vinn 7+ | Inga |  |
| **OneDrive-molnfiler** | Inga | Inga | Inga | Ja | Se anteckningar | Se anteckningar  | Se anteckningar | Vinn 10 RS3 | Inga | Inte testad på servern SKU. Backend-lagring på Azure beror på synkroniseringsklient. Backend-lagring på prem behöver en synkroniseringsklient. |

#### <a name="performance"></a>Prestanda

UPD kräver [Att lagringsutrymmen direkt (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) för att uppfylla prestandakrav. UPD använder SMB-protokoll (Server Message Block). Profilen kopieras till den virtuella datorn där användaren loggas. UPD med S2D är den lösning vi rekommenderar för Windows Virtual Desktop.  

#### <a name="cost"></a>Kostnad

S2D-kluster uppnår den prestanda som krävs, men kostnaden är dyr för företagskunder, men särskilt dyrt för små och medelstora företagskunder(SMB). För den här lösningen betalar företag för lagringsdiskar, tillsammans med kostnaden för de virtuella datorer som använder diskarna för en resurs.

#### <a name="administrative-overhead"></a>Högre administrativa kostnader

S2D-kluster kräver ett operativsystem som är korrigerat, uppdaterat och underhållet i ett säkert tillstånd. Dessa processer och komplexiteten i att inrätta S2D katastrofåterställning gör S2D möjligt endast för företag med en särskild IT-personal.

## <a name="fslogix-profile-containers"></a>FSLogix profilbehållare

Den 19 november 2018 [förvärvade Microsoft FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix hanterar många profilcontainerutmaningar. Viktiga bland dem är:

- **Prestanda:** [FSLogix-profilbehållarna](/fslogix/configure-profile-container-tutorial/) är högpresterande och löser prestandaproblem som historiskt har blockerat cachelagrat exchange-läge.
- **OneDrive:** Utan FSLogix-profilbehållare stöds inte OneDrive för företag i icke-beständiga RDSH- eller VDI-miljöer. [Metodtipsen för OneDrive för företag och FSLogix](/fslogix/overview/) beskriver hur de interagerar. Mer information finns i [Använda synkroniseringsklienten på virtuella skrivbord](/deployoffice/rds-onedrive-business-vdi/).
- **Ytterligare mappar:** FSLogix ger möjlighet att utöka användarprofiler till att omfatta ytterligare mappar.

Sedan förvärvet har Microsoft börjat ersätta befintliga lösningar för användarprofiler, som UPD, med FSLogix profilbehållare.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure-filintegrering med Azure Active Directory Domain Service

FSLogix profilbehållares prestanda och funktioner utnyttjar molnet. Den 7 augusti 2019 meddelade Microsoft Azure Files den allmänna tillgängligheten för [Azure Files-autentisering med Azure Active Directory Domain Service (AD DS)](../storage/files/storage-files-active-directory-overview.md). Genom att ta itu med både kostnads- och administrativa kostnader är Azure-filer med Azure AD DS-autentisering en premiumlösning för användarprofiler i Windows Virtual Desktop-tjänsten.

## <a name="best-practices-for-windows-virtual-desktop"></a>Metodtips för Virtuellt Windows-skrivbord

Windows Virtual Desktop ger full kontroll över storlek, typ och antal virtuella datorer som används av kunder. Mer information finns i [Vad är Windows Virtual Desktop?](overview.md).

Så här kontrollerar du att din Windows Virtual Desktop-miljö följer metodtips:

- Azure Files storage-konto måste finnas i samma region som virtuella sessionsvärdar.
- Azure Files-behörigheter ska matcha behörigheter som beskrivs i [Krav - Profilbehållare](/fslogix/fslogix-storage-config-ht).
- Varje värdpool måste byggas av samma typ och storlek VM baserat på samma huvudavbildning.
- Varje virtuell värdpool måste finnas i samma resursgrupp för att underlätta hantering, skalning och uppdatering.
- För bästa prestanda bör lagringslösningen och FSLogix-profilbehållaren vara på samma datacenterplats.
- Lagringskontot som innehåller huvudavbildningen måste finnas i samma region och prenumeration där de virtuella datorerna etableras.

## <a name="next-steps"></a>Nästa steg

Använd följande guider för att konfigurera en Windows Virtual Desktop-miljö.

- Mer om du vill börja bygga ut en virtualiseringslösning för skrivbordet finns [i Skapa en klient i Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
- Information om hur du skapar en värdpool i windows virtual desktop-klienten finns i [Skapa en värdpool med Azure Marketplace](create-host-pools-azure-marketplace.md).
- Information om hur du konfigurerar fullständigt hanterade filresurser i molnet finns i [Konfigurera Azure Files-resurs](/azure/storage/files/storage-files-active-directory-enable/).
- Om du vill konfigurera FSLogix-profilbehållare finns i [Skapa en profilbehållare för en värdpool med hjälp av en filresurs](create-host-pools-user-profile.md).
- Mer om du vill tilldela användare till en värdpool finns i [Hantera appgrupper för Windows Virtuella skrivbord](manage-app-groups.md).
- Information om hur du kommer åt windows virtuella skrivbordsresurser från en webbläsare finns i [Ansluta till Windows Virtual Desktop](connect-web.md).
