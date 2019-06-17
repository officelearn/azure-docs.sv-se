---
title: FSLogix profil behållare och Azure Files i Windows virtuellt skrivbord – Azure
description: Den här artikeln beskriver FSLogix profil behållare i virtuella Windows-skrivbordet och Azure-filer.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497524"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profilcontainrar och Azure-filer

Tjänsten Windows Virtual Desktop förhandsversion rekommenderar FSLogix profil behållare som en lösning för användarens profil. FSLogix har utformats för att flytta profiler i remote datormiljöer, till exempel virtuella Windows-skrivbordet. En fullständig profil lagras i en enskild behållare. Vid inloggning bifogas den här behållaren dynamiskt datormiljön med inbyggt stöd virtuella hårddisk (VHD) och Hyper-V Virtual Hard disk (VHDX). Användarprofilen är omedelbart tillgängligt och visas i systemet precis som en intern användarprofil.

I den här artikeln beskriver vi FSLogix profil behållare som används med Azure Files. Informationen är i samband med virtuella Windows-skrivbordet, som var [visats på 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Användarprofiler

En användarprofil innehåller dataelement om en individ, inklusive konfigurationsinformation som inställningarna för skrivbordet, beständiga nätverksanslutningar och programinställningar. Som standard skapar en lokal användarprofil som är nära integrerad med operativsystemet i Windows.

En fjärranvändarprofil innehåller en partition mellan användardata och operativsystemet. Det gör att ersättas eller ändrats utan att påverka användarens data. I Remote värd för fjärrskrivbordssession (RDSH) och infrastrukturer VDI (Virtual Desktop) ersättas operativsystemet av följande skäl:

- En uppgradering av operativsystemet
- En ersättning för en befintlig virtuell dator (VM)
- En användare som är en del av en pool (icke-beständiga) RDSH eller VDI-miljö

Microsoft-produkter fungerar med flera tekniker för fjärranvändare profiler, inklusive dessa tekniker:
- Centrala användarprofiler (RUP)
- Användarprofil-diskar (UPD)
- Företagsroaming (BS)

UPD och centrala användarprofiler är de mest använda teknikerna för användarprofiler i Remote värd för fjärrskrivbordssession (RDSH)- och virtuella hårddisk (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Utmaningar med föregående användarens profil tekniker

Befintliga och äldre Microsoft-lösningar för användarprofiler medföljde olika utmaningar. Ingen tidigare lösningen hanteras alla användarens profil behov som ingår i en VDI- eller RDSH-miljö. Till exempel UPD kan inte hantera stora OST-filer och centrala användarprofiler sparas inte moderna inställningar.

#### <a name="functionality"></a>Funktioner

I följande tabell visas fördelar och begränsningar av föregående användare profil tekniker.

| Teknologi | Moderna inställningar | Win32-inställningar | OS-inställningar | Användardata | Stöds på server SKU | Backend-lagringen i Azure | Backend-lagring på plats | Versionsstöd | Efterföljande inloggningar tid |Anteckningar|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Användarprofil-diskar (UPD)** | Ja | Ja | Ja | Ja | Ja | Nej | Ja | Win 7 + | Ja | |
| **Centrala användare profil (RUP), underhållsläge** | Nej | Ja | Ja | Ja | Ja| Nej | Ja | Win 7 + | Nej | |
| **Enterprise State Roaming (ESR)** | Ja | Nej | Ja | Nej | Se information | Ja | Nej | Win 10 | Nej | Funktioner på servern SKU men inget stödjande användargränssnitt |
| **Användarens upplevelse virtualisering (UE-V)** | Ja | Ja | Ja | Nej | Ja | Nej | Ja | Win 7 + | Nej |  |
| **OneDrive-filer för molnet** | Nej | Nej | Nej | Ja | Se information | Se information  | Se information | Win 10 RS3 | Nej | Inte testat på SKU-servern. Backend-lagringen i Azure är beroende av Synkroniseringsklienten. Backend-lagring på lokal måste en Synkroniseringsklienten. |

#### <a name="performance"></a>Prestanda

UPD kräver [Lagringsdirigering (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) att möta krav på prestanda. UPD använder Server Message Block (SMB) protokollet. Profilen kopieras till den virtuella datorn där användaren loggas. UPD med S2D var lösningen RDS-teamet rekommenderas för virtuella Windows-skrivbordet under förhandsversionen av tjänsten.  

#### <a name="cost"></a>Kostnad

När S2D-kluster uppnå nödvändiga prestanda, är kostnaden dyra för företagskunder, men särskilt dyra för små och medelstora företag (SMB). Företag betalar för premium-lagringsdiskar, tillsammans med kostnaden för de virtuella datorerna som använder diskarna för en resurs för den här lösningen.

#### <a name="administrative-overhead"></a>Administrativa kostnader

S2D-kluster kräver ett operativsystem som är uppdaterad, uppdateras och hanteras i ett säkert tillstånd. Dessa processer och komplexitet och hur du konfigurerar S2D haveriberedskap gör du S2D är möjligt endast för företag med en dedikerad IT-avdelning.

## <a name="fslogix-profile-containers"></a>FSLogix profil behållare

19 November 2018 [Microsoft har förvärvat FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix löser många profil behållare utmaningar. Nyckel mellan dem är:

- **Prestanda:** Den [FSLogix profil behållare](https://fslogix.com/products/profile-containers) är med höga prestanda och Lös prestandaproblem som tidigare har blockerat cachelagrat exchange-läge.
- **OneDrive:** Utan FSLogix profil behållare stöds OneDrive för företag inte i icke-beständiga RDSH eller VDI-miljöer. [OneDrive för företag och FSLogix metodtips](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) beskriver hur de samverkar. Mer information finns i [använda Synkroniseringsklienten på virtuella skrivbord](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Ytterligare mappar:** FSLogix ger möjlighet att utöka användarprofiler för att inkludera ytterligare mappar.

Sedan förvärv startade Microsoft ersätta befintliga användare profil lösningar, som UPD, med FSLogix profil behållare.

## <a name="azure-files-integration-with-azure-active-directory"></a>Integrering med Azure filer med Azure Active Directory

FSLogix profil behållare prestanda- och dra nytta av molnet. Microsoft Azure Files 24 september 2018 meddelade en offentlig förhandsversion av [Azure Files stöder Azure Active Directory-autentisering](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). Genom att hantera både kostnad och administrativa kostnader, är Azure Files med Azure Active Directory-autentisering en premium-lösning för användarprofiler i den nya virtuella skrivbordet i Windows-tjänsten.

## <a name="best-practices-for-windows-virtual-desktop"></a>Metodtips för virtuella Windows-skrivbordet

Virtuella Windows-skrivbordet erbjuder fullständig kontroll över storlek, typ och antal virtuella datorer som används av kunder. Mer information finns i [vad är förhandsversionen av Windows virtuella skrivbord?](https://docs.microsoft.com/azure/virtual-desktop/overview).

För att säkerställa att din virtuella Windows-skrivbordet följer miljö de bästa metoderna:

- Azure Files storage-konto måste vara i samma region som värd för fjärrskrivbordssession virtuella datorer.
- Azure filer-behörigheter måste matcha behörigheterna som beskrivs i [krav - profil behållare](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Varje värd pool måste byggas av samma typ och storlek på virtuell dator baserat på samma huvudavbildningen.
- Varje värd pool VM måste finnas i samma resursgrupp för enklare hantering, skalning och uppdaterar.
- För optimala prestanda lagringslösningen och FSLogix profil behållaren ska vara i samma data platsen för datacenter.
- Lagringskontot som innehåller huvudavbildningen måste vara i samma region och prenumeration där de virtuella datorerna etableras.

## <a name="next-steps"></a>Nästa steg

Använd följande instruktioner för att konfigurera en miljö för virtuella Windows-skrivbordet.

- Om du vill börja skapa din lösning för fjärrskrivbord, se [skapa en klient i virtuella Windows-skrivbordet](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- Om du vill skapa en pool för värden i din klient för virtuella Windows-skrivbordet, [skapa en värd-pool med Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- Du ställer in fullständigt hanterade filresurser i molnet, se [ställa in Azure Files-resurs](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- För att konfigurera FSLogix profil behållare, se [ställa in en användare profil resurs för en värd-pool](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Om du vill tilldela användare till en värd-pool, se [hantera app-grupper för virtuella Windows-skrivbordet](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- Du hittar dina virtuella Windows-skrivbordet resurser från en webbläsare i [Anslut till virtuella Windows-skrivbordet](https://docs.microsoft.com/azure/virtual-desktop/connect-web).
