---
title: Funktioner i tjänsten Azure AD Connect-synkronisering och konfiguration | Microsoft Docs
description: Beskriver funktionerna för tjänsten på klientsidan för Azure AD Connect-synkroniseringstjänsten.
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
ms.openlocfilehash: be67a6f287e2d6e77070928cbe12542857696011
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347554"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funktioner i tjänsten Azure AD Connect-synkronisering

Synkroniseringsfunktionen av Azure AD Connect har två komponenter:

* Lokala komponenten med namnet **Azure AD Connect-synkronisering**, kallas även **Synkroniseringsmotorn**.
* Tjänsten som finns i Azure AD så kallade **Azure AD Connect-synkroniseringstjänsten**

Det här avsnittet förklarar hur följande funktioner i den **Azure AD Connect-synkroniseringstjänsten** arbete och hur du kan konfigurera dem med hjälp av Windows PowerShell.

De här inställningarna är konfigurerade med den [Azure Active Directory-modulen för Windows PowerShell](https://aka.ms/aadposh). Ladda ned och installera den separat från Azure AD Connect. Cmdletarna som beskrivs i det här avsnittet har introducerats i den [mars 2016-versionen (build 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Om du inte har de cmdletar som beskrivs i det här avsnittet eller om de inte ge samma resultat, se till att du kör den senaste versionen.

Om du vill se konfigurationen i Azure AD-katalogen, kör `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures resultat](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Många av dessa inställningar kan bara ändras av Azure AD Connect.

Följande inställningar kan konfigureras med `Set-MsolDirSyncFeature`:

| DirSyncFeature | Kommentar |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Kan objekt att delta i userPrincipalName utöver primära SMTP-adress. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Kan Synkroniseringsmotorn att uppdatera attributet userPrincipalName för hanterade/licensierade (icke-federerade) användare. |

När du har aktiverat en funktion, kan det inte inaktiveras igen.

> [!NOTE]
> Från den 24 augusti 2016 funktionen *återhämtning av Duplicerat attribut* är aktiverad som standard för nya Azure AD-kataloger. Den här funktionen kommer även distribuerat och aktiverat på kataloger som skapats före detta datum. Du får ett e-postmeddelande när din katalog kommer att bli den här funktionen är aktiverad.
> 
> 

Följande inställningar konfigureras av Azure AD Connect och kan inte ändras av `Set-MsolDirSyncFeature`:

| DirSyncFeature | Kommentar |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Aktivera tillbakaskrivning av enheter](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect-synkronisering: Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Gör ett attribut som ska placeras i karantän när det är en dubblett av ett annat objekt i stället misslyckas hela objektet under exporten. |
| Hash-synkronisering av lösenord |[Implementera lösenordshashsynkronisering med Azure AD Connect-synkronisering](how-to-connect-password-hash-synchronization.md) |
|Direktautentisering|[Användarinloggning med Azure Active Directory-direktautentisering](how-to-connect-pta.md)|
| UnifiedGroupWriteback |[Förhandsversion: Tillbakaskrivning av grupp](how-to-connect-preview.md#group-writeback) |
| UserWriteback |Stöds för närvarande inte. |

## <a name="duplicate-attribute-resiliency"></a>Återhämtning av Duplicerat attribut

I stället för att etablera cachelagras inte objekt med dubbla UPN-namn / proxyAddresses, Duplicerat attribut är ”i karantän” och ett tillfälligt värde har tilldelats. När konflikten har lösts ändras tillfälliga UPN det korrekta värdet automatiskt. Mer information finns i [identitet identitetssynkronisering och duplicerad attributåterhämtning](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName mjuk matchning

När den här funktionen är aktiverad, aktiveras ungefärlig matchning för UPN utöver den [primär SMTP-adress](https://support.microsoft.com/kb/2641663), som alltid är aktiverat. Ungefärlig matchning används för att matcha befintliga molnanvändare i Azure AD med lokala användare.

Om du vill matcha en lokal AD-konton med befintliga konton som skapas i molnet och du använder inte Exchange Online och den här funktionen är användbar. I det här scenariot kan du vanligtvis inte en orsak till att ange SMTP-attributet i molnet.

Den här funktionen är på som standard för nyligen skapade Azure AD-kataloger. Du kan se om den här funktionen är aktiverad för dig genom att köra:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Om den här funktionen inte är aktiverad för Azure AD-katalogen, kan du aktivera det genom att köra:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synkronisera userPrincipalName uppdateringar

Historiskt sett uppdateringar till attributet UserPrincipalName med synkroniseringstjänsten från den lokala har blockerats, om inte båda dessa villkor är uppfyllda:

* Användaren är hanterad (icke-federerade).
* Användaren har inte tilldelats en licens.

Mer information finns i [användarnamn i Office 365, Azure eller Intune matchar inte den lokala UPN eller alternativa inloggnings-ID](https://support.microsoft.com/kb/2523192).

Den här funktionen aktiveras kan Synkroniseringsmotorn att uppdatera userPrincipalName när det är ändrade lokalt och du använder hash-synkronisering eller direktautentisering lösenordsautentisering. Den här funktionen stöds inte om du använder federation.

Den här funktionen är på som standard för nyligen skapade Azure AD-kataloger. Du kan se om den här funktionen är aktiverad för dig genom att köra:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Om den här funktionen inte är aktiverad för Azure AD-katalogen, kan du aktivera det genom att köra:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

När du har aktiverat den här funktionen, befintliga userPrincipalName värden förblir-är. På Nästa ändring av de userPrincipalName attributet lokalerna uppdaterar normala Deltasynkronisering på användare UPN.  

## <a name="see-also"></a>Se också

* [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
