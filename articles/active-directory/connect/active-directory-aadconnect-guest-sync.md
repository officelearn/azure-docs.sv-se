---
title: Synkronisera användarkonton för gäst-användare som använder e-post för inloggning till Azure | Microsoft Docs
description: Den här artikeln förklarar hur du synkroniserar gästen användarkonton som använder ett alternativt ID för att logga in till program.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2f2b3514d30b3c728832ff074f6657a7c0b2710c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590879"
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>Synkronisera gäst-användarkonton med e-post för inloggningen (förhandsgranskning)

>[!NOTE]
> Den här funktionen är för närvarande i förhandsversion.

Följande scenario adressen situation där du kan ha externa användare i din lokala AD-miljö, till exempel partners, som använder en alternativ inloggningsmetod.

I föregående exempel Nina Morin fungerar för Fabrikam och hon har följande e-postadress: nmorin@fabrikam.com. Nina är en partner med Contoso hon behöver åtkomst till vissa program som har Contoso. Contoso har skapat ett konto för Nina och har dirigeras Nina du använder sin e-postadress för att logga in till program.

Det här scenariot har arbetat utmärkt för sina lokala program. Men nu Contoso flyttar dessa program till molnet och vill ha samma upplevelse för sina partner.

Det här scenariot löser den här situationen.


## <a name="pre-requisites-and-assumptions"></a>Krav och förutsättningar
Det här avsnittet innehåller en lista över förutsättningar och antaganden som du behöver känna till innan du försöker ställa in det här scenariot.

### <a name="pre-requisites"></a>Förutsättningar
- Global administratörsautentiseringsuppgifter för att konfigurera Azure AD Connect, verifiera domäner och konfigurera Domäninställningar för federation
- Azure AD Connect version 1.1.524.0 eller högre
- Verifierad domän att ange moln-UPN för externa användare (exempel: bmcontoso.com).
- Federationstjänsten att autentisera dina externa användare. Om du använder AD FS, måste den vara 2012 R2 eller senare
- MSOL PowerShell v1.1 är installerat på en dator för att verifiera inställningarna för federation. Mer information finns i [Azure ActiveDirectory (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).


### <a name="assumptions"></a>Antaganden 
- Azure AD Connect har redan ställts in och har installerats. Information om hur du installerar Azure AD Connect finns [Azure AD Connect och federation](active-directory-aadconnectfed-whatis.md).
Det här dokumentet blir följande antaganden:
- du har en federationstjänst som ställer in och att den korrekt autentisering av användare.
- externa användare kan autentisera med de externa e-postadresser.
- - Använda ett alternativt ID för inloggning har ställts in och konfigurerats. Användare kan autentiseras med deras alternativa-ID. Mer information om hur du skapar ett alternativt ID med AD FS finns [konfigurera alternativt inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

## <a name="task-1--prepare-the-environment"></a>Uppgift 1: Förbered miljön
Följande uppgift är av en information så att du är redo att börja synkronisera dina externa konton så att de kan logga in med ett alternativ i till exempel e-postattributet.

Definiera objekten i tabellen nedan innan du går vidare till den andra aktiviteten.

![Arkitektur](media/active-directory-aadconnect-guest-sync/guest2.png)

|Miljö aspekt|Vad är den användas för?|Implementering i din miljö|
|-----|-----|-----|
|Molnet UPN-attribut|Det attribut som fyller på UPN-namnet för externa användarobjekt i molnet. UPN-suffixet för externa konton måste vara den som angetts i kraven. Detta är verifierad domän.|* Exempel: UserPrincipalName (nmorin@bmcontoso.com)|
|Logga In adressen|Attributet som externa användare kommer att ange när du loggar in. Det här attributet måste ha en e-postadress och i de flesta fall det sammanfaller med den externa användaren verkliga e-postadress.|* Exempel: e (nmorin@fabrikam.com)|
|Azure AD Connect begränsade Filter|Filter som tillåter riktad externa identiteter omfång Synkroniseringsregler definieras senare i den här guiden. Vanliga sätt att scope är: en fördefinierad Organisationsenhet i organisationen, vissa en namngivningskonvention, en specifik domän och så vidare.|* Exempel: Organisationsenheten innehåller externa|
|Azure AD-klient|Namnet på Azure AD-klient som det visas till Azure AD Connect.|Exempel: contoso.onmicrosoft.com|

Följande skärmbild har tre rutor som beskrivs.
- Den **Externals** OU som används i Azure AD Connect omfång filtret och platsen för externa användare.
- Den **e** attribut som används av externa användare för att logga in.
- Den **userPrincipalName** attribut, som är verifierad domän som den lokala miljön är federerat med.

![Användare](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>Uppgift 2: Konfigurera Azure AD Connect
När du har informationen ovan definierade kan gå vi vidare till att konfigurera Azure AD Connect-synkroniseringsreglerna. Använda redigeraren för Azure AD Connect synch-regler för att du ställer in reglerna. Mer information om redigeraren finns [deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

### <a name="how-to-configure-the-synchronization-rule"></a>Så här konfigurerar du synkroniseringsregeln
Använd följande procedur för att konfigurera Azure AD Connect.

1. Öppna Redigeraren för Azure AD Connect-synkronisering regeln genom att gå till **Start - Azure AD Connect - synkronisering regler editor**.
2. På den **Synchronization regler Editor** kontrollerar riktningen är **inkommande** och till höger klickar du på **Lägg till ny regel**.
3. På den **beskrivning** konfigurerar du följande och klickar på **nästa**.
    - **Namnet** -ange ett namn för regeln 
    - **Det anslutna systemet:** -lokal AD-miljö
    - **Objekttyp för anslutna System:** -användare
    - **Typ av Metaversumobjekt:** -person
    - **Länktypen:** -delta
    - **Prioritet:** – 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. På den **Omfångsfiltret** klickar du på **Lägg till grupp**.
5. Använd-listrutor för att konfigurera filtret. Ange följande och klicka på **nästa**. Den här åtgärden skapar ett filter som gäller endast objekt som finns i den externa Organisationsenheten.
    - **Attributet** -dn
    - **Operatorn** -innehåller
    - **Värdet** -Externals
 
 ![Filter](media/active-directory-aadconnect-guest-sync/guest4.png)

6. På den **ansluta regler** klickar du på **nästa**.
7. På den **transformationer** klickar du på **omvandlingen Lägg**. Ange följande information:
    - **ExchangeRate för FlowType** - konstant
    - **Rikta attributet** -userType
    - **Källan** - gäst
8. På den **transformationer** klickar du på **omvandlingen Lägg**. Ange följande information:
    - **ExchangeRate för FlowType** – direkt
    - **Rikta attributet** -onPremisesUserPrincipalName
    - **Källan** -e-post
9. På den **transformationer** klickar du på **omvandlingen Lägg**. Ange följande information:
    - **ExchangeRate för FlowType** – direkt
    - **Rikta attributet** -userPrincipalName
    - **Källan** -userPrincipalName ![omvandling](media/active-directory-aadconnect-guest-sync/guest5.png)
10. Klicka på **Lägg till**. 
11. På den **Synchronization regler Editor** kontrollerar riktningen är **utgående** och till höger klickar du på **Lägg till ny regel**.
12. På den **beskrivning** sidan Konfigurera följande och klickar på **nästa**.
    - **Namnet** -ange ett namn för regeln 
    - **Det anslutna systemet:** -Azure AD-klient
    - **Objekttyp för anslutna System:** -användare
    - **Typ av Metaversumobjekt:** -person
    - **Länka typen** -delta
    - **Prioritet:** – 90
    - 
![Regel](media/active-directory-aadconnect-guest-sync/guest6.png)

13. På den **Scoping filter** klickar du på **nästa**.
14. På den **ansluta regler** klickar du på **nästa**.
15. På den **transformationer** klickar du på **omvandlingen Lägg**.  Ange följande information:
    - **ExchangeRate för FlowType** – direkt
    - **Rikta attributet** -userType
    - **Källan** -userType
9. På den **transformationer** klickar du på **omvandlingen Lägg**. Ange följande information:
    - **ExchangeRate för FlowType** – direkt
    - **Rikta attributet** -onPremisesUserPrincipalName
    - **Källan** -onPremisesUserPrincipalName ![omvandling](media/active-directory-aadconnect-guest-sync/guest7.png)
10. Klicka på **Lägg till**. 
11. När du har konfigurerat dessa regler måste du köra en fullständig synkronisering. Använd PowerShell för att starta en fullständig synkronisering. När synkroniseringen är klar kan du fortsätta till nästa steg.

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>Uppgift 3: Federation
Följande uppgift är ett informationsmeddelande på några saker som du behöver ha på plats för scenariot ska fungera.

Du kan kontrollera inställningarna för federation med Azure med hjälp av Azure AD PowerShell. Det här dokumentet använder v1.1 MSOL PowerShell. Du kan installera den här versionen [här](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).

### <a name="verify-the-federation-settings"></a>Verifiera inställningarna för federation
Använd följande procedur för att verifiera inställningarna för federation.
1. Öppna Windows PowerShell och ansluta till Azure AD med hjälp av följande kommando:
``` powershell
      Connect-MSOLservice
```
2.  Ange autentiseringsuppgifterna som global administratör
3.  Nu ska du ange följande kommando:
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   Observera federation information som ska returneras. Observera den **ActiveLogonUri** är URL-Adressen för federationsservern.

  ![Federation](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>Kontrollera alternativt inloggnings-ID
Det här dokumentet använder AD FS som identitetsprovider (Idp). Om du använder en annan Idp kan de här stegen mycket.

1. Öppna Windows PowerShell och Skriv följande kommando:
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. Du bör se informationen om AD FS.  Observera den **AlternateLoginID** och **LookupForests**.

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>Uppgift 4: testa
För att verifiera att detta ska fungera korrekt måste du logga in på en slutpunkt som har konfigurerats för att autentisera med Idp. Om du vill testa detta vi distribuera en webbplats i Azure och använder följande url: contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>Kontrollera att du kan logga in med Alternativt ID
1. Logga in till slutpunkten.</br>
![slutpunkt](media/active-directory-aadconnect-guest-sync/guest10.png)
1. Ange ditt användarnamn och du ska omdirigeras till sidan för federation.
![Federation](media/active-directory-aadconnect-guest-sync/guest11.png)
1. Ange dina autentiseringsuppgifter.
2. Du bör nu vara loggat in.
![Lyckades](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>Nästa steg
- [Egenskaper för en användare för Azure Active Directory B2B-samarbete](../../active-directory/b2b/user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [Konfigurera alternativt inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Versionshistorik för Azure AD Connect](active-directory-aadconnect-version-history.md)
