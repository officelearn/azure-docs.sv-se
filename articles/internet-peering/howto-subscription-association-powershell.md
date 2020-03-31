---
title: Associera peer-ASN till Azure-prenumeration med PowerShell
titleSuffix: Azure
description: Associera peer-ASN till Azure-prenumeration med PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908991"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Associera peer-ASN till Azure-prenumeration med PowerShell

Innan du skickar en peering-begäran bör du först associera din ASN med Azure-prenumeration med hjälp av stegen nedan.

Om du vill kan du slutföra den här guiden med hjälp av [portalen](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Skapa PeerASN för att associera din ASN med Azure-prenumeration

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Registrera dig för peering-resursprovider
Registrera dig för peering-resursprovider i din prenumeration med kommandot nedan. Om du inte kör detta är Azure-resurser som krävs för att konfigurera peering inte tillgängliga.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Du kan kontrollera registreringsstatus med hjälp av kommandona nedan:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Vänta *tills RegistrationState* har vänt "Registrerad" innan du fortsätter. Det kan ta 5 till 30 minuter efter att du har utfört kommandot.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Uppdatera peer-informationen som är associerad med den här prenumerationen

Nedan finns ett exempel för att uppdatera peer-information.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Namnet motsvarar resursnamn och kan vara vad du vill. -peerName motsvarar dock företagets namn och måste vara så nära din PeeringDB-profil som möjligt. Observera att värdet för -peerName endast stöder tecken a-z, A-Ö och blanksteg.

En prenumeration kan ha flera ASN.A subscription can have multiple ASN. Uppdatera peering-informationen för varje ASN. Se till att "namn" är unikt för varje ASN.

Peer-datorer förväntas ha en fullständig och uppdaterad profil på [PeeringDB](https://www.peeringdb.com). Vi använder denna information under registreringen för att validera peer-uppgifter såsom NOC-information, teknisk kontaktinformation och deras närvaro på peering-anläggningar etc.

Observera att i stället **för {subscriptionId}** i utdata ovan visas faktiskt prenumerations-ID.

## <a name="view-status-of-a-peerasn"></a>Visa status för ett PeerASN

Sök efter ASN-valideringstillstånd med kommandot nedan:

```powershell
Get-AzPeerAsn
```

Nedan följer ett exempel på svar:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Vänta tills ValidationState har vänt "Godkänd" innan du skickar en peering-begäran. Det kan ta upp till 12 timmar för detta godkännande.

## <a name="modify-peerasn"></a>Ändra PeerAsn
Du kan ändra NOC-kontaktinformation när som helst.

Nedan är ett exempel:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Ta bort PeerAsn
Det går inte att ta bort ett PeerASN.Topvisa en PeerASN stöds inte för närvarande. Om du behöver ta bort PeerASN kontaktar du [Microsoft peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en Direct-peering](howto-direct-powershell.md)
* [Konvertera en äldre Direct-peering till en Azure-resurs](howto-legacy-direct-powershell.md)
* [Skapa eller ändra Exchange-peering](howto-exchange-powershell.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns på [vanliga frågor om internet peering](faqs.md)
