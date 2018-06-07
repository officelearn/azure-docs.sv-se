---
title: Azure AD Connect sync tjänstens funktioner och konfiguration | Microsoft Docs
description: Beskriver funktioner för tjänsten på klientsidan för Azure AD Connect-synkroniseringstjänsten.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2dbe14f6c7f8f9f1dcfaf733dc884df5adf919aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593179"
---
# <a name="azure-ad-connect-sync-service-features"></a>Azure AD Connect sync-tjänsten-funktioner
Synkroniseringsfunktionen av Azure AD Connect har två komponenter:

* Lokala komponenten **Azure AD Connect-synkronisering**, även kallat **Synkroniseringsmotorn**.
* Tjänsten som finns i Azure AD så kallade **Azure AD Connect-synkroniseringstjänsten**

Det här avsnittet beskrivs hur följande funktioner i den **Azure AD Connect-synkroniseringstjänsten** fungerar och hur du kan konfigurera dem med hjälp av Windows PowerShell.

De här inställningarna är konfigurerade med den [Azure Active Directory-modulen för Windows PowerShell](https://aka.ms/aadposh). Hämta och installera det separat från Azure AD Connect. De cmdlets som beskrivs i det här avsnittet har introducerats i den [2016 mars-versionen (build 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Om du inte har de cmdlets som beskrivs i det här avsnittet eller om de inte ger samma resultat, se till att du kör den senaste versionen.

Om du vill se konfigurationen i Azure AD-katalogen kör `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures resultat](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Många av dessa inställningar kan bara ändras av Azure AD Connect.

Följande inställningar kan konfigureras med `Set-MsolDirSyncFeature`:

| DirSyncFeature | Kommentar |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Gör att objekt som ska delta i userPrincipalName utöver primära SMTP-adress. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Gör att Synkroniseringsmotorn att uppdatera attributet userPrincipalName för hanterade/licensierade (ofedererad) användare. |

När du har aktiverat en funktion kan inaktiveras det inte igen.

> [!NOTE]
> Från 24 augusti 2016 funktionen *duplicera attribut återhämtning* är aktiverad som standard för nya Azure AD-kataloger. Den här funktionen kommer även distribuerat och aktiverad på kataloger som skapats före detta datum. Du får ett e-postmeddelande när din katalog kommer att få den här funktionen aktiverad.
> 
> 

Följande inställningar konfigureras med Azure AD Connect och kan inte ändras av `Set-MsolDirSyncFeature`:

| DirSyncFeature | Kommentar |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Aktivera tillbakaskrivning av enheter](active-directory-aadconnect-feature-device-writeback.md) |
| DirectoryExtensions |[Azure AD Connect-synkronisering: katalogtillägg](active-directory-aadconnectsync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Tillåter ett attribut som ska placeras i karantän när det är en dubblett av ett annat objekt i stället misslyckas hela objektet under exporten. |
| PasswordSync |[Implementera hash Lösenordssynkronisering med Azure AD Connect-synkronisering](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |
| UnifiedGroupWriteback |[Förhandsversion: Tillbakaskrivning av grupp](active-directory-aadconnect-feature-preview.md#group-writeback) |
| UserWriteback |Stöds inte för närvarande. |

## <a name="duplicate-attribute-resiliency"></a>Duplicerat attribut återhämtning
I stället för misslyckas att etablera objekt med dubblerade UPN-namn / proxyAddresses, duplicerade attributet ”i karantän” och ett tillfälligt värde är tilldelad. När konflikten löses ändras tillfälliga UPN automatiskt till rätt värde. Mer information finns i [identitet synkronisering och dubblett attributet återhämtning](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName mjuka matchning
När den här funktionen är aktiverad, mjuk-matcha är aktiverad för UPN förutom den [primära SMTP-adress](https://support.microsoft.com/kb/2641663), som alltid är aktiverat. Soft-matcha används för att matcha befintliga molnanvändare i Azure AD med lokala användare.

Om du behöver matcha lokala AD-konton med befintliga konton som skapats i molnet och du använder inte Exchange Online och sedan den här funktionen är användbart. I det här scenariot kan du vanligtvis inte en orsak till att ange SMTP-attributet i molnet.

Den här funktionen är på skapas som standard för nya Azure AD-kataloger. Du kan se om den här funktionen är aktiverad för du genom att köra:  

```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Om den här funktionen inte har aktiverats för din Azure AD-katalog, kan du aktivera det genom att köra:  

```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synkronisera userPrincipalName uppdateringar
Tidigare uppdateringar för attributet UserPrincipalName med synkroniseringstjänsten från lokala har blockerats, om bägge dessa villkor är uppfyllda:

* Användaren hanteras (ofedererad).
* Användaren har inte tilldelats en licens.

Mer information finns i [användarnamnen i Office 365, Azure eller Intune matchar inte lokal UPN eller alternativ inloggnings-ID](https://support.microsoft.com/kb/2523192).

Den här funktionen aktiveras kan Synkroniseringsmotorn att uppdatera userPrincipalName när den har ändrats lokalt och du använder hash-synkronisering. Den här funktionen stöds inte om du använder federation.

Den här funktionen är på skapas som standard för nya Azure AD-kataloger. Du kan se om den här funktionen är aktiverad för du genom att köra:  

```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Om den här funktionen inte har aktiverats för din Azure AD-katalog, kan du aktivera det genom att köra:  

```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

När den här funktionen, befintliga userPrincipalName värden förblir-är. På Nästa ändring av userPrincipalName attributet lokal uppdaterar normal Deltasynkronisering på användare UPN.  

## <a name="see-also"></a>Se också
* [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

