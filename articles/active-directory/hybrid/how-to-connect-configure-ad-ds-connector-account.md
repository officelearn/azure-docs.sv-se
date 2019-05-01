---
title: 'Azure AD Connect: Konfigurera behörigheterna för AD DS-anslutningen | Microsoft Docs'
description: Det här dokumentet beskriver hur du konfigurerar kontot för AD DS-anslutningen med den nya ADSyncConfig PowerShell-modulen
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff151ff8e14b5cf9602d4e7e2e9c6cb2118a8a65
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918507"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Konfigurera behörigheterna för AD DS-koppling 

PowerShell-modulen med namnet [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) introducerades i version 1.1.880.0 (släpptes i augusti 2018) som innehåller en uppsättning cmdletar för att hjälpa dig att konfigurera Active Directory-behörighet för din Azure AD Anslut distribution. 

## <a name="overview"></a>Översikt 
Följande PowerShell-cmdletar kan användas för att konfigurera Active Directory-behörigheter för AD DS-anslutningskontot för varje funktion som du väljer för att aktivera i Azure AD Connect. För att förhindra problem bör du förbereda Active Directory-behörigheter i förväg när du vill installera Azure AD Connect med ett konto för anpassad domän för att ansluta till din skog. Den här modulen ADSyncConfig kan också användas för att konfigurera behörigheter när Azure AD Connect har distribuerats.

![Översikt över ad ds-konto](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

För Azure AD Connect Express-installationen skapas en automatiskt genererad konto (MSOL_nnnnnnnnnn) i Active Directory med alla de nödvändiga behörigheterna, så inte behöver använda den här modulen ADSyncConfig om inte du har blockerat behörigheter arv på organisationsenheter eller specifika Active Directory-objekt som du vill synkronisera till Azure AD. 
 
### <a name="permissions-summary"></a>Sammanfattning av behörigheter 
Följande tabell innehåller en sammanfattning av de behörigheter som krävs för AD-objekt: 

| Funktion | Behörigheter |
| --- | --- |
| MS-DS-ConsistencyGuid funktion |Skrivbehörighet till attributet ms-DS-ConsistencyGuid som beskrivs i [designbegrepp – med ms-DS-ConsistencyGuid som sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Lösenordets hash-synkronisering |<li>Replikera katalogändringar</li>  <li>Replikera katalogen ändras alla |
| Exchange-hybridinstallation |Skrivbehörighet till de attribut som beskrivs i [tillbakaskrivning av Exchange-hybrid](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) för användare, grupper och kontakter. |
| Offentlig mapp för Exchange-e-post |Läsbehörighet till de attribut som beskrivs i [offentlig mapp för Exchange-e-post](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) för gemensamma mappar. | 
| Tillbakaskrivning av lösenord |Skrivbehörighet till de attribut som beskrivs i [komma igång med lösenordshantering](../authentication/howto-sspr-writeback.md) för användare. |
| Tillbakaskrivning av enheter |Skrivbehörighet till enhetsobjekt och behållare som beskrivs i [tillbakaskrivning av enhet](how-to-connect-device-writeback.md). |
| Tillbakaskrivning av grupp |Läsa, skapa, uppdatera och ta bort grupp objekt för synkroniserade **Office 365-grupper**.  Mer information finns i [tillbakaskrivning av grupp](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Med ADSyncConfig PowerShell-modulen 
Modulen ADSyncConfig kräver den [Remote verktyg för fjärrserveradministration (RSAT) för AD DS](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) eftersom den är beroende av AD DS PowerShell-modulen och verktyg. Om du vill installera RSAT för AD DS, öppna en Windows PowerShell-fönster med ”Kör som administratör” och kör: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurera](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Du kan också kopiera filen **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** till en domänkontrollant som redan har RSAT för AD DS installerad och använda PowerShell-modulen därifrån.

Om du vill börja använda ADSyncConfig som du behöver läsa in modulen i ett Windows PowerShell-fönster: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Så här kontrollerar alla de cmdletar som ingår i den här modulen som du kan skriva:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Markera](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Varje cmdlet har samma parametrar för att ange AD DS-Anslutningskontot och en AdminSDHolder-växel. Om du vill ange din AD DS-Anslutningskontot, du kan ange namnet på kontot och domän eller bara kontot unika namn (DN),

t.ex.:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Eller;

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Ersätt `<ADAccountName>`, `<ADDomainName>` och `<ADAccountDN>` med rätt värden för din miljö.

Om du inte vill ändra behörigheter för AdminSDHolder-behållaren kan använda växeln `-SkipAdminSdHolders`. 

Som standard försöker alla cmdletar för set-behörigheter för att ange behörigheter för AD DS i roten på varje domän i skogen, vilket innebär att användaren som kör PowerShell-sessionen kräver domänadministratörsbehörighet på varje domän i skogen.  Därför rekommenderar vi att du använder en företagsadministratör från skogens rot. Om distributionen av Azure AD Connect har flera AD DS-Anslutningsappar, kommer den att behöva köra samma cmdleten på varje skog som har en AD DS-koppling. 

Du kan också ange behörigheter för ett visst Organisationsenhet eller AD DS-objekt med hjälp av parametern `-ADobjectDN` följt av det unika namnet för målobjektet där du vill ange behörigheter. När du använder ett mål ADobjectDN, ange cmdleten behörigheter för det här objektet och inte på domänroten eller AdminSDHolder-behållare. Den här parametern kan vara användbart när du har vissa organisationsenheter eller AD DS-objekt som har ärvd behörighet har inaktiverat (se hitta AD DS-objekt med arv av behörigheter inaktiverad) 

Undantag för dessa vanliga parametrar är den `Set-ADSyncRestrictedPermissions` cmdlet som används för att ange behörighet i AD DS-Anslutningskontot, och `Set-ADSyncPasswordHashSyncPermissions` cmdlet eftersom de behörigheter som krävs för lösenordets Hash-synkronisering är endast att ställa in på domänroten därför Denna cmdlet inte innehåller den `-ObjectDN` eller `-SkipAdminSdHolders` parametrar.

### <a name="determine-your-ad-ds-connector-account"></a>Fastställa din AD DS-Anslutningsapp konto 
Om Azure AD Connect har redan installerats och du vill kontrollera vad är AD DS-Anslutningskontot används av Azure AD Connect, kan du köra cmdleten: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Leta upp AD DS-objekt med ärvd behörighet har inaktiverats 
Om du vill kontrollera om det finns någon AD DS-objekt med arv av behörigheter inaktiverad, kan du köra: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Som standard den här cmdleten endast söker efter organisationsenheter med inaktiverad arv, men du kan ange andra AD DS-objektklasser i `-ObjectClass` parametern eller Använd ”*” för alla objektklasser, enligt följande: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Visa AD DS-behörigheter för ett objekt 
Du kan använda cmdleten nedan för att visa listan över behörigheter som ställts in på Active Directory-objekt genom att ange dess DistinguishedName: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Konfigurera behörigheterna för AD DS-koppling 
 
### <a name="configure-basic-read-only-permissions"></a>Konfigurera grundläggande läsbehörigheter 
Om du vill ange grundläggande läsbehörigheter för AD DS-anslutningskontot när du inte använder en Azure AD Connect-funktion, kör du: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Eller; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Denna cmdlet kommer att ange följande behörigheter: 
 

|Typ |Namn |Access |Gäller| 
|-----|-----|-----|-----|
|Tillåt |Kontot för AD DS-koppling |Läsa alla egenskaper |Underordnade enhetsobjekt| 
|Tillåt |Kontot för AD DS-koppling|Läsa alla egenskaper |Underordnade InetOrgPerson-objekt| 
|Tillåt |Kontot för AD DS-koppling |Läsa alla egenskaper |Underordnade datorobjekt| 
|Tillåt |Kontot för AD DS-koppling |Läsa alla egenskaper |Underordnade foreignSecurityPrincipal objekt| 
|Tillåt |Kontot för AD DS-koppling |Läsa alla egenskaper |Underordnad grupp objekt| 
|Tillåt |Kontot för AD DS-koppling |Läsa alla egenskaper |Underordnade objekt| 
|Tillåt |Kontot för AD DS-koppling |Läsa alla egenskaper |Underordnade kontakt-objekt| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Configure MS-DS-Consistency-Guid Permissions 
Om du vill ange behörigheter för AD DS-anslutningskontot när du använder attributet ms-Ds-konsekvens-Guid som källfästpunktsattribut (även kallat ”Låt Azure hantera källfästpunkten åt mig”-alternativet), kör du: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

Eller; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Namn |Access |Gäller|
|-----|-----|-----|-----| 
|Tillåt|Kontot för AD DS-koppling|Läs/Skriv-egenskapen|Underordnade objekt|

### <a name="permissions-for-password-hash-synchronization"></a>Behörigheter för synkronisering av Lösenordshash 
Om du vill ange behörigheter för AD DS-anslutningskontot när du använder synkronisering av Lösenordshash, kör du: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


Eller; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Namn |Access |Gäller|
|-----|-----|-----|-----| 
|Tillåt |Kontot för AD DS-koppling |Replikera katalogändringar |Endast det här objektet (domänroten)| 
|Tillåt |Kontot för AD DS-koppling |Replikera ändringar av alla |Endast det här objektet (domänroten)| 
  
### <a name="permissions-for-password-writeback"></a>Behörigheter för tillbakaskrivning av lösenord 
Om du vill ange behörigheter för AD DS-anslutningskontot när du använder tillbakaskrivning av lösenord, kör du: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Eller;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Namn |Access |Gäller|
|-----|-----|-----|-----| 
|Tillåt |Kontot för AD DS-koppling |Återställ lösenord |Underordnade objekt| 
|Tillåt |Kontot för AD DS-koppling |Skriva egenskapen lockoutTime |Underordnade objekt| 
|Tillåt |Kontot för AD DS-koppling |Skriva egenskapen pwdLastSet |Underordnade objekt| 

### <a name="permissions-for-group-writeback"></a>Behörigheter för tillbakaskrivning av grupp 
Om du vill ange behörigheter för AD DS-anslutningskontot när du använder tillbakaskrivning av grupp, kör du: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
Eller; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Namn |Access |Gäller|
|-----|-----|-----|-----| 
|Tillåt |Kontot för AD DS-koppling |Allmän Läs/Skriv |Alla attribut för grupp av objekt och underobjekt| 
|Tillåt |Kontot för AD DS-koppling |Skapa/ta bort underordnade objekt |Alla attribut för grupp av objekt och underobjekt| 
|Tillåt |Kontot för AD DS-koppling |Ta bort/ta bort trädet objekt|Alla attribut för grupp av objekt och underobjekt|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Behörigheter för Exchange-Hybridinstallation 
Om du vill ange behörigheter för AD DS-anslutningskontot när du använder Exchange-hybridinstallation, kör du: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Eller; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Denna cmdlet kommer att ange följande behörigheter:  
 

|Typ |Namn |Access |Gäller|
|-----|-----|-----|-----| 
|Tillåt |Kontot för AD DS-koppling |Läs/Skriv alla egenskaper |Underordnade objekt| 
|Tillåt |Kontot för AD DS-koppling |Läs/Skriv alla egenskaper |Underordnade InetOrgPerson-objekt| 
|Tillåt |Kontot för AD DS-koppling |Läs/Skriv alla egenskaper |Underordnad grupp objekt| 
|Tillåt |Kontot för AD DS-koppling |Läs/Skriv alla egenskaper |Underordnade kontakt-objekt| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Behörigheter för gemensamma mappar för Exchange e-post (förhandsversion) 
Om du vill ange behörigheter för AD DS-anslutningskontot när du använder funktionen gemensamma mappar för Exchange-e-post, kör du: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


Eller; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Namn |Access |Gäller|
|-----|-----|-----|-----| 
|Tillåt |Kontot för AD DS-koppling |Läsa alla egenskaper |Underordnade PublicFolder objekt| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Begränsa behörigheten för AD DS-koppling konto 
Det här PowerShell-skriptet kommer öka behörigheterna för kontot AD-koppling som tillhandahålls som parameter. Skärpa behörigheter omfattar följande steg: 

- Inaktivera arv på det angivna-objektet 
- Ta bort alla åtkomstkontrollposter på specifika objekt, utom åtkomstkontrollposter som är specifika för SELF eftersom vi vill hålla standardbehörigheterna intakt när det gäller att själv. 
 
  Parametern - ADConnectorAccountDN är det AD-konto vars behörigheter behöver höjas. Detta är vanligtvis MSOL_nnnnnnnnnnnn domänkontot som har konfigurerats i AD DS-anslutning (Se ta reda på dina AD DS-Anslutningskontot). -Credential parametern är nödvändigt att ange det administratörskonto som har de behörigheter som krävs för att begränsa Active Directory-behörigheter i målobjektet AD. Detta är vanligtvis Enterprise eller domänadministratör.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Till exempel: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Denna cmdlet kommer att ange följande behörigheter: 

|Typ |Namn |Access |Gäller|
|-----|-----|-----|-----| 
|Tillåt |SYSTEM |Fullständig behörighet |Det här objektet 
|Tillåt |Företagsadministratörer |Fullständig behörighet |Det här objektet 
|Tillåt |Domänadministratörer |Fullständig behörighet |Det här objektet 
|Tillåt |Administratörer |Fullständig behörighet |Det här objektet 
|Tillåt |Företagets domänkontrollanter |Lista innehåll |Det här objektet 
|Tillåt |Företagets domänkontrollanter |Läsa alla egenskaper |Det här objektet 
|Tillåt |Företagets domänkontrollanter |Läsbehörighet |Det här objektet 
|Tillåt |Autentiserade användare |Lista innehåll |Det här objektet 
|Tillåt |Autentiserade användare |Läsa alla egenskaper |Det här objektet 
|Tillåt |Autentiserade användare |Läsbehörighet |Det här objektet 

## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect: Konton och behörigheter](reference-connect-accounts-permissions.md)
- [Snabbinstallation](how-to-connect-install-express.md)
- [Anpassad Installation](how-to-connect-install-custom.md)
- [ADSyncConfig referens](reference-connect-adsyncconfig.md)

