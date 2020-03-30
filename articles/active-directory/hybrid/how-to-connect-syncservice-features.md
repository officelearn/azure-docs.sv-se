---
title: Azure AD Connect-synkroniseringstjänstfunktioner och konfiguration | Microsoft-dokument
description: Beskriver tjänstsidans funktioner för Azure AD Connect-synkroniseringstjänsten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5486a8d8bd4c295f49e0ab847daf45d0fcab47ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300544"
---
# <a name="azure-ad-connect-sync-service-features"></a>Tjänstfunktioner för Azure AD Connect-synkronisering

Synkroniseringsfunktionen i Azure AD Connect har två komponenter:

* Den lokala komponenten **Azure AD Connect-synkronisering**, även kallad **synkroniseringsmotor**.
* Tjänsten som finns i Azure AD kallas även **Azure AD Connect-synkroniseringstjänst**

I det här avsnittet beskrivs hur följande funktioner i **synkroniseringstjänsten Azure AD Connect** fungerar och hur du kan konfigurera dem med Windows PowerShell.

De här inställningarna konfigureras av [Azure Active Directory Module för Windows PowerShell](https://aka.ms/aadposh). Hämta och installera den separat från Azure AD Connect. Cmdlets som dokumenteras i det här avsnittet introducerades i [2016 marsversionen (version 9031.1).](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1) Om du inte har cmdlets dokumenteras i det här avsnittet eller de inte ger samma resultat, se till att du kör den senaste versionen.

Om du vill se konfigurationen `Get-MsolDirSyncFeatures`i din Azure AD-katalog kör du .  
![Get-MsolDirSyncFeatures resultat](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Många av dessa inställningar kan bara ändras av Azure AD Connect.

Följande inställningar kan konfigureras `Set-MsolDirSyncFeature`av:

| DirSyncFeature (dirsyncfeature) | Kommentar |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Tillåter objekt att gå med på userPrincipalName utöver den primära SMTP-adressen. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Gör att synkroniseringsmotorn kan uppdatera attributet userPrincipalName för hanterade/licensierade (icke-federerade) användare. |

När du har aktiverat en funktion kan den inte inaktiveras igen.

> [!NOTE]
> Från och med den 24 augusti 2016 aktiveras funktionen *Duplicera attributåtersåtersåtare* som standard för nya Azure AD-kataloger. Den här funktionen kommer också att distribueras och aktiveras på kataloger som skapats före detta datum. Du får ett e-postmeddelande när katalogen är på väg att få den här funktionen aktiverad.
> 
> 

Följande inställningar konfigureras av Azure AD Connect `Set-MsolDirSyncFeature`och kan inte ändras av:

| DirSyncFeature (dirsyncfeature) | Kommentar |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Aktivera tillbakaskrivning av enheter](how-to-connect-device-writeback.md) |
| KatalogUtuttensions |[Synkronisering av Azure AD Connect: Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAdressEriliens<br/>DupliceraUPNResiliens](#duplicate-attribute-resiliency) |Tillåter att ett attribut sätts i karantän när det är en dubblett av ett annat objekt i stället för att misslyckas hela objektet under export. |
| Hash-synkronisering av lösenord |[Implementera synkronisering av lösenordsh hash-synkronisering med Azure AD Connect-synkronisering](how-to-connect-password-hash-synchronization.md) |
|Direktautentisering|[Användarinloggning med Azure Active Directory-direktautentisering](how-to-connect-pta.md)|
| Enhetlig gruppSkrivåter |[Förhandsgranskning: Gruppåterskrivning](how-to-connect-preview.md#group-writeback) |
| AnvändareSkriva tillbaka |Stöds inte för närvarande. |

## <a name="duplicate-attribute-resiliency"></a>Återhämtning för dubblettattribut

I stället för att inte etablera objekt med dubbla UPNs /proxyAddresses är attributet duplicerat "karantän" och ett tillfälligt värde tilldelas. När konflikten är löst ändras det tillfälliga UPN till rätt värde automatiskt. Mer information finns i [Identitetssynkronisering och dubblettattributåterslutning](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Soft matchning för UserPrincipalName

När den här funktionen är aktiverad aktiveras mjukmatchning för UPN utöver den [primära SMTP-adressen](https://support.microsoft.com/kb/2641663), som alltid är aktiverad. Soft-match används för att matcha befintliga molnanvändare i Azure AD med lokala användare.

Om du behöver matcha lokala AD-konton med befintliga konton som skapats i molnet och du inte använder Exchange Online är den här funktionen användbar. I det här fallet har du i allmänhet inte en anledning att ange SMTP-attributet i molnet.

Den här funktionen är aktiverad som standard för nyligen skapade Azure AD-kataloger. Du kan se om den här funktionen är aktiverad för dig genom att köra:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Om den här funktionen inte är aktiverad för din Azure AD-katalog kan du aktivera den genom att köra:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synkronisera uppdateringar för userPrincipalName

Historiskt sett har uppdateringar av attributet UserPrincipalName med synkroniseringstjänsten från lokala spärrats, såvida inte båda dessa villkor är sanna:

* Användaren hanteras (icke-federerade).
* Användaren har inte tilldelats någon licens.

Mer information finns [i Användarnamn i Office 365, Azure eller Intune matchar inte det lokala UPN- eller alternativa inloggnings-ID:t](https://support.microsoft.com/kb/2523192).

Om du aktiverar den här funktionen kan synkroniseringsmotorn uppdatera användarenPrincipalName när den ändras lokalt och du använder synkronisering av lösenord hash- eller direktautentisering.

Den här funktionen är aktiverad som standard för nyligen skapade Azure AD-kataloger. Du kan se om den här funktionen är aktiverad för dig genom att köra:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Om den här funktionen inte är aktiverad för din Azure AD-katalog kan du aktivera den genom att köra:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

När du har aktiverat den här funktionen förblir befintliga userPrincipalName-värden som de är. Vid nästa ändring av attributet userPrincipalName lokalt uppdateras UPN-uppdateringen av UPN.  

## <a name="see-also"></a>Se även

* [Synkronisering av Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
