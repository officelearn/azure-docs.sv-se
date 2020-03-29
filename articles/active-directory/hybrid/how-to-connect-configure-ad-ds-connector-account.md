---
title: 'Azure AD Connect: Konfigurera behörigheter för AD DS-anslutningskonto | Microsoft-dokument'
description: I det här dokumentet beskrivs hur AD DS Connector-kontot konfigureras med den nya ADSyncConfig PowerShell-modulen
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
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72515813"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Konfigurera behörigheter för AD DS-anslutningskonto 

PowerShell-modulen [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) introducerades med build 1.1.880.0 (släpptes i augusti 2018) som innehåller en samling cmdlets som hjälper dig att konfigurera rätt Active Directory-behörigheter för din Azure AD Connect-distribution. 

## <a name="overview"></a>Översikt 
Följande PowerShell-cmdlets kan användas för att konfigurera Active Directory-behörigheter för AD DS Connector-kontot för varje funktion som du väljer att aktivera i Azure AD Connect. För att förhindra problem bör du förbereda Active Directory-behörigheter i förväg när du vill installera Azure AD Connect med ett anpassat domänkonto för att ansluta till din skog. Den här ADSyncConfig-modulen kan också användas för att konfigurera behörigheter när Azure AD Connect har distribuerats.

![översikt över annons ds-konto](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

För Azure AD Connect Express-installation skapas ett automatiskt genererat konto (MSOL_nnnnnnnnnn) i Active Directory med alla nödvändiga behörigheter, så du behöver inte använda den här ADSyncConfig-modulen om du inte har blockerat behörigheter arv på organisationsenheter eller på specifika Active Directory-objekt som du vill synkronisera med Azure AD. 
 
### <a name="permissions-summary"></a>Sammanfattning av behörigheter 
I följande tabell finns en sammanfattning av de behörigheter som krävs för AD-objekt: 

| Funktion | Behörigheter |
| --- | --- |
| ms-DS-Konsekvensguid-funktionen |Läs- och skrivbehörigheter till attributet ms-DS-ConsistencyGuid som dokumenteras i [Designkoncept - Använda ms-DS-ConsistencyGuid som sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synkronisering av lösenordsh hash |<li>Replikera katalogändringar</li>  <li>Replikera katalog ändrar alla |
| Distribution av Exchange-hybrid |Läs- och skrivbehörigheter till attributen som dokumenteras i [Exchange hybridåterskrivning](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) för användare, grupper och kontakter. |
| Gemensam mapp för Exchange Mail |Läs behörigheter till attributen som dokumenteras i [Exchange Mails gemensamma mapp](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) för gemensamma mappar. | 
| Tillbakaskrivning av lösenord |Läs- och skrivbehörigheter till attributen som dokumenteras i [Komma igång med lösenordshantering](../authentication/howto-sspr-writeback.md) för användare. |
| Tillbakaskrivning av enheter |Läs- och skrivbehörigheter till enhetsobjekt och behållare som dokumenterats i [enhetens tillbakaskrivning](how-to-connect-device-writeback.md). |
| Tillbakaskrivning av grupp |Läsa, skapa, uppdatera och ta bort gruppobjekt för synkroniserade **Office 365-grupper**.  Mer information finns i [Gruppåterskrivning](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Använda ADSyncConfig PowerShell-modulen 
ADSyncConfig-modulen kräver [RSAT (Remote Server Administration Tools) för AD DS](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) eftersom det är beroende av AD DS PowerShell-modulen och -verktygen. Om du vill installera RSAT för AD DS öppnar du ett Windows PowerShell-fönster med "Kör som administratör" och kör: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Konfigurera](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>Du kan också kopiera filen **C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** till en domänkontrollant som redan har RSAT för AD DS installerat och använda den här PowerShell-modulen därifrån.

För att börja använda ADSyncConfig måste du läsa in modulen i ett Windows PowerShell-fönster: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

För att kontrollera alla cmdlets som ingår i denna modul kan du skriva:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Markera](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Varje cmdlet har samma parametrar för att mata in AD DS Connector-kontot och en AdminSDHolder-växel. Om du vill ange ditt AD DS Connector-konto kan du ange kontonamn och domän, eller bara kontot Distinguished Name (DN),

Exempel:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

Eller;

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Se till `<ADAccountName>`att `<ADDomainName>` `<ADAccountDN>` ersätta och med rätt värden för din miljö.

Om du inte vill ändra behörigheter för behållaren AdminSDHolder `-SkipAdminSdHolders`använder du växeln . 

Som standard försöker alla cmdlets för ange behörigheter för AD DS för roten för varje domän i skogen, vilket innebär att användaren som kör PowerShell-sessionen kräver domänadministratörsrättigheter för varje domän i skogen.  På grund av det här kravet rekommenderas att du använder en företagsadministratör från skogsroten. Om din Azure AD Connect-distribution har flera AD DS-kopplingar måste den köra samma cmdlet på varje skog som har en AD DS-anslutning. 

Du kan också ange behörigheter för ett visst organisationsenhets- eller AD DS-objekt med hjälp av parametern `-ADobjectDN` följt av DN för målobjektet där du vill ange behörigheter. När du använder ett mål ADobjectDN anger cmdlet endast behörigheter för det här objektet och inte på domänroten eller AdminSDHolder-behållaren. Den här parametern kan vara användbar när du har vissa US- eller AD DS-objekt som har behörighetsarv inaktiverat (se Hitta AD DS-objekt med behörighetsarv inaktiverat) 

Undantag från dessa vanliga `Set-ADSyncRestrictedPermissions` parametrar är cmdlet som används för att ange behörigheter `Set-ADSyncPasswordHashSyncPermissions` för AD DS Connector-kontot själv, och cmdlet eftersom de behörigheter som krävs `-ObjectDN` `-SkipAdminSdHolders` för Password Hash Sync endast anges vid domänroten, därför innehåller den här cmdleten inte parametrarna eller.

### <a name="determine-your-ad-ds-connector-account"></a>Ta reda på ditt AD DS Connector-konto 
Om Azure AD Connect redan är installerat och du vill kontrollera vad som används av AZURE AD Connect kan du köra cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Hitta AD DS-objekt med behörighetsarv inaktiverat 
Om du vill kontrollera om det finns något AD DS-objekt med behörighetsarv inaktiverat kan du köra: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Som standard söker den här cmdleten bara efter OUs med inaktiverat arv, men du kan ange andra AD DS-objektklasser i `-ObjectClass` parametern eller använda '*' för alla objektklasser, enligt följande: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Visa AD DS-behörigheter för ett objekt 
Du kan använda cmdleten nedan för att visa listan över behörigheter som för närvarande anges för ett Active Directory-objekt genom att ange dess DistinguishedName: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Konfigurera behörigheter för AD DS Connector 
 
### <a name="configure-basic-read-only-permissions"></a>Konfigurera grundläggande skrivskyddade behörigheter 
Om du vill ange grundläggande skrivskyddade behörigheter för AD DS Connector-kontot när du inte använder någon Azure AD Connect-funktion kör du: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


eller; 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Den här cmdleten anger följande behörigheter: 
 

|Typ |Namn |Åtkomst |Gäller för| 
|-----|-----|-----|-----|
|Tillåt |AD DS-anslutningskonto |Läs alla egenskaper |Underordnade enhetsobjekt| 
|Tillåt |AD DS-anslutningskonto|Läs alla egenskaper |Underordnade InetOrgPerson-objekt| 
|Tillåt |AD DS-anslutningskonto |Läs alla egenskaper |Underordnade datorobjekt| 
|Tillåt |AD DS-anslutningskonto |Läs alla egenskaper |Underordnade främmande Säkerhetsprincipobjekt| 
|Tillåt |AD DS-anslutningskonto |Läs alla egenskaper |Underordnade gruppobjekt| 
|Tillåt |AD DS-anslutningskonto |Läs alla egenskaper |Underordnade användarobjekt| 
|Tillåt |AD DS-anslutningskonto |Läs alla egenskaper |Underordnade kontaktobjekt| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Konfigurera behörigheter för MS-DS-Konsekvens-Guid 
Om du vill ange behörigheter för AD DS Connector-kontot när du använder attributet ms-Ds-Consistency-Guid som källankare (aka "Låt Azure hantera källankaret för mig") kör du: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

eller; 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Den här cmdleten anger följande behörigheter: 

|Typ |Namn |Åtkomst |Gäller för|
|-----|-----|-----|-----| 
|Tillåt|AD DS-anslutningskonto|Egenskapen Read/Write|Underordnade användarobjekt|

### <a name="permissions-for-password-hash-synchronization"></a>Behörigheter för synkronisering av lösenord hash-synkronisering 
Om du vill ange behörigheter för AD DS Connector-kontot när du använder synkronisering av lösenord hash kör du: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


eller; 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Den här cmdleten anger följande behörigheter: 

|Typ |Namn |Åtkomst |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutningskonto |Replikera katalogändringar |Endast det här objektet (domänrot)| 
|Tillåt |AD DS-anslutningskonto |Replikera katalogen ändrar alla |Endast det här objektet (domänrot)| 
  
### <a name="permissions-for-password-writeback"></a>Behörigheter för återställning av lösenord 
Om du vill ange behörigheter för AD DS Connector-kontot när du använder tillbakaskrivning av lösenord kör du: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


eller;

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Den här cmdleten anger följande behörigheter: 

|Typ |Namn |Åtkomst |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutningskonto |Återställ lösenord |Underordnade användarobjekt| 
|Tillåt |AD DS-anslutningskonto |Skriva egendom lockoutTime |Underordnade användarobjekt| 
|Tillåt |AD DS-anslutningskonto |Skriv egenskap pwdLastSet |Underordnade användarobjekt| 

### <a name="permissions-for-group-writeback"></a>Behörigheter för gruppåterskrivning 
Om du vill ange behörigheter för AD DS Connector-kontot när du använder gruppåterskrivning kör du: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
eller; 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Den här cmdleten anger följande behörigheter: 

|Typ |Namn |Åtkomst |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutningskonto |Allmän läsning/skrivning |Alla attribut för objekttypsgrupp och underobjekt| 
|Tillåt |AD DS-anslutningskonto |Skapa/ta bort underordnade objekt |Alla attribut för objekttypsgrupp och underobjekt| 
|Tillåt |AD DS-anslutningskonto |Ta bort/ta bort trädobjekt|Alla attribut för objekttypsgrupp och underobjekt|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Behörigheter för Exchange Hybrid-distribution 
Om du vill ange behörigheter för AD DS Connector-kontot när du använder Exchange Hybrid-distribution kör du: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


eller; 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Den här cmdleten anger följande behörigheter:  
 

|Typ |Namn |Åtkomst |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutningskonto |Läs/skriv alla egenskaper |Underordnade användarobjekt| 
|Tillåt |AD DS-anslutningskonto |Läs/skriv alla egenskaper |Underordnade InetOrgPerson-objekt| 
|Tillåt |AD DS-anslutningskonto |Läs/skriv alla egenskaper |Underordnade gruppobjekt| 
|Tillåt |AD DS-anslutningskonto |Läs/skriv alla egenskaper |Underordnade kontaktobjekt| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Behörigheter för gemensamma exchange-e-postmappar (förhandsversion) 
Om du vill ange behörigheter för AD DS Connector-kontot när du använder funktionen Gemensamma Exchange Mail-mappar kör du: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


eller; 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Den här cmdleten anger följande behörigheter: 

|Typ |Namn |Åtkomst |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |AD DS-anslutningskonto |Läs alla egenskaper |Underordnade PublicFolder-objekt| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Begränsa behörigheter för AD DS-anslutningskontot 
Det här PowerShell-skriptet drar åt behörigheterna för AD Connector-kontot som anges som en parameter. Att skärpa behörigheterna omfattar följande steg: 

- Inaktivera arv på det angivna objektet 
- Ta bort alla åtkomstkontrollistor på det specifika objektet, utom ASU som är specifika för SELF eftersom vi vill behålla standardbehörigheterna intakta när det gäller SELF. 
 
  Parametern -ADConnectorAccountDN är AD-kontot vars behörigheter måste skärpas. Detta är vanligtvis MSOL_nnnnnnnnnnnn domänkonto som är konfigurerat i AD DS-anslutningen (se Bestäm ditt AD DS Connector-konto). Parametern -Autentiseringsuppgifter är nödvändig för att ange det administratörskonto som har de behörigheter som krävs för att begränsa Active Directory-behörigheter för mål-AD-objektet. Detta är vanligtvis företags- eller domänadministratören.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Till exempel: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Den här cmdleten anger följande behörigheter: 

|Typ |Namn |Åtkomst |Gäller för|
|-----|-----|-----|-----| 
|Tillåt |SYSTEM |Fullständig kontroll |Detta objekt 
|Tillåt |Företagsadministratörer |Fullständig kontroll |Detta objekt 
|Tillåt |Domänadministratörer |Fullständig kontroll |Detta objekt 
|Tillåt |Administratörer |Fullständig kontroll |Detta objekt 
|Tillåt |Domänkontrollanter för företag |Lista innehåll |Detta objekt 
|Tillåt |Domänkontrollanter för företag |Läs alla egenskaper |Detta objekt 
|Tillåt |Domänkontrollanter för företag |Läs behörigheter |Detta objekt 
|Tillåt |Autentiserade användare |Lista innehåll |Detta objekt 
|Tillåt |Autentiserade användare |Läs alla egenskaper |Detta objekt 
|Tillåt |Autentiserade användare |Läs behörigheter |Detta objekt 

## <a name="next-steps"></a>Efterföljande moment
- [Azure AD Connect: Konton och behörigheter](reference-connect-accounts-permissions.md)
- [Snabb installation](how-to-connect-install-express.md)
- [Anpassad installation](how-to-connect-install-custom.md)
- [Referens för ADSyncConfig](reference-connect-adsyncconfig.md)

