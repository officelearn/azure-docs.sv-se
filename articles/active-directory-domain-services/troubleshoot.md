---
title: Azure Active Directory Domain Services fel sökning | Microsoft Docs
description: Lär dig hur du felsöker vanliga fel när du skapar eller hanterar Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 3cb57fae2b1c67ece321a294e56612f49358405a
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612728"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Vanliga fel och fel söknings steg för Azure Active Directory Domain Services

Som en central del av identitet och autentisering för program har Azure Active Directory Domain Services (Azure AD DS) ibland problem. Om du stöter på problem finns det några vanliga fel meddelanden och associerade fel söknings steg som hjälper dig att komma igång igen. Du kan när som helst [öppna en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

Den här artikeln innehåller fel söknings steg för vanliga problem i Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Du kan inte aktivera Azure AD Domain Services för din Azure AD-katalog

Om du har problem med att aktivera Azure AD DS kan du läsa följande vanliga fel och steg för att lösa dem:

| **Exempel på fel meddelande** | **Upplösning** |
| --- |:--- |
| *Namnet addscontoso.com används redan i det här nätverket. Ange ett namn som inte används.* |[Domän namns konflikt i det virtuella nätverket](troubleshoot.md#domain-name-conflict) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet för programmet som heter "Azure AD Domain Services Sync". Ta bort programmet som heter "Azure AD Domain Services Sync" och försök sedan Aktivera domän tjänster för Azure AD-klienten.* |[Domän tjänster har inte tillräcklig behörighet för Azure AD Domain Services Sync-programmet](troubleshoot.md#inadequate-permissions) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Domän tjänst programmet i din Azure AD-klient har inte de behörigheter som krävs för att aktivera domän tjänster. Ta bort programmet med program identifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan att aktivera Domain Services för din Azure AD-klient.* |[Domän tjänst programmet har inte kon figurer ATS korrekt i din Azure AD-klient](troubleshoot.md#invalid-configuration) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Microsoft Azure AD programmet är inaktiverat i din Azure AD-klient. Aktivera programmet med program identifieraren 00000002-0000-0000-C000-000000000000 och försök sedan aktivera Domain Services för din Azure AD-klient.* |[Microsoft Graph programmet är inaktiverat i din Azure AD-klient](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Domän namns konflikt

**Fel meddelande**

*Namnet aaddscontoso.com används redan i det här nätverket. Ange ett namn som inte används.*

**Upplösning**

Kontrol lera att du inte har en befintlig AD DS-miljö med samma domän namn på samma eller ett peer-kopplat virtuellt nätverk. Du kan till exempel ha en AD DS-domän med namnet *aaddscontoso.com* som körs på virtuella Azure-datorer. När du försöker aktivera en Azure AD DS-hanterad domän med samma domän namn *aaddscontoso.com* i det virtuella nätverket, Miss lyckas den begärda åtgärden.

Det här felet beror på namn konflikter för domän namnet i det virtuella nätverket. En DNS-sökning kontrollerar om en befintlig AD DS-miljö svarar på det begärda domän namnet. Lös problemet genom att använda ett annat namn för att konfigurera din Azure AD DS-hanterade domän eller avetablera den befintliga AD DS-domänen och försök sedan igen för att aktivera Azure AD DS.

### <a name="inadequate-permissions"></a>Otillräckliga behörigheter

**Fel meddelande**

*Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet för programmet som heter "Azure AD Domain Services Sync". Ta bort programmet som heter "Azure AD Domain Services Sync" och försök sedan Aktivera domän tjänster för Azure AD-klienten.*

**Upplösning**

Kontrol lera om det finns ett program med namnet *Azure AD Domain Services Sync* i Azure AD-katalogen. Om det här programmet finns tar du bort det och försöker sedan aktivera Azure AD DS igen. För att söka efter ett befintligt program och ta bort det vid behov, utför följande steg:

1. I Azure Portal väljer du **Azure Active Directory** i navigerings menyn till vänster.
1. Välj **företags program**. Välj *alla program* på list menyn **program typ** och välj sedan **Använd**.
1. I rutan Sök anger *Azure AD Domain Services Sync*. Om programmet finns markerar du det och väljer **ta bort**.
1. När du har tagit bort programmet försöker du aktivera Azure AD DS igen.

### <a name="invalid-configuration"></a>Ogiltig konfiguration

**Fel meddelande**

*Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Domän tjänst programmet i din Azure AD-klient har inte de behörigheter som krävs för att aktivera domän tjänster. Ta bort programmet med program identifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan att aktivera Domain Services för din Azure AD-klient.*

**Upplösning**

Kontrol lera att du har ett befintligt program med namnet *AzureActiveDirectoryDomainControllerServices* med ett program-ID för *D87DCBC6-A371-462E-88E3-28AD15EC4E64* i Azure AD-katalogen. Om det här programmet finns tar du bort det och försöker sedan igen för att aktivera Azure AD DS.

Använd följande PowerShell-skript för att söka efter en befintlig program instans och ta bort den om det behövs:

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph inaktiverat

**Fel meddelande**

*Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Microsoft Azure AD programmet är inaktiverat i din Azure AD-klient. Aktivera programmet med program identifieraren 00000002-0000-0000-C000-000000000000 och försök sedan aktivera Domain Services för din Azure AD-klient.*

**Upplösning**

Kontrol lera om du har inaktiverat ett program med identifieraren *00000002-0000-0000-C000-000000000000*. Det här programmet är Microsoft Azure AD programmet och ger Graph API åtkomst till din Azure AD-klient. Detta program måste vara aktiverat för att du ska kunna synkronisera din Azure AD-klient.

För att kontrol lera status för det här programmet och aktivera det vid behov, utför följande steg:

1. I Azure Portal väljer du **Azure Active Directory** i navigerings menyn till vänster.
1. Välj **företags program**. Välj *alla program* på list menyn **program typ** och välj sedan **Använd**.
1. I rutan Sök anger du *00000002-0000-0000-C000-00000000000*. Välj programmet och välj sedan **Egenskaper**.
1. Om **aktive rad för användare till inloggning** har värdet *Nej*anger du värdet *Ja*och väljer sedan **Spara**.
1. När du har aktiverat programmet försöker du aktivera Azure AD DS igen.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Användarna kan inte logga in på den hanterade domänen för Azure AD Domain Services

Om en eller flera användare i din Azure AD-klient inte kan logga in på den hanterade domänen i Azure AD DS, slutför du följande fel söknings steg:

* **Format för autentiseringsuppgifter** – försök använda UPN-formatet för att ange autentiseringsuppgifter, till exempel `dee@aaddscontoso.onmicrosoft.com`. UPN-formatet är det rekommenderade sättet att ange autentiseringsuppgifter i Azure AD DS. Kontrol lera att detta UPN är korrekt konfigurerat i Azure AD.

    *SAMAccountName* för ditt konto, till exempel *AADDSCONTOSO\driley* , kan skapas automatiskt om det finns flera användare med samma UPN-prefix i din klient organisation eller om ditt UPN-prefix är för långt. Därför kan *sAMAccountName* -formatet för ditt konto skilja sig från vad du förväntar dig eller använder i din lokala domän.

* **Lösenordssynkronisering – kontrol** lera att du har aktiverat Lösenordssynkronisering för [enbart moln användare][cloud-only-passwords] eller för [hybrid miljöer som använder Azure AD Connect][hybrid-phs].
    * **Hybrid-synkroniserade konton:** Om de berörda användar kontona synkroniseras från en lokal katalog kontrollerar du följande områden:
    
      * Du har distribuerat eller uppdaterat till, den [senaste rekommenderade versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
      * Du har konfigurerat Azure AD Connect att [utföra en fullständig synkronisering][hybrid-phs].
      * Beroende på katalogens storlek kan det ta en stund innan användar konton och inloggnings-hashar är tillgängliga i Azure AD DS. Se till att du väntar tillräckligt länge innan du försöker autentisera mot den hanterade domänen.
      * Om problemet kvarstår när du har verifierat föregående steg kan du försöka starta om *tjänsten Microsoft Azure AD Sync*. Från din [virtuella hanterings dator][management-vm]öppnar du en kommando tolk och kör följande kommandon:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Endast moln konton**: om det berörda användar kontot är ett användar konto med endast ett moln kontrollerar [du att användaren har ändrat sitt lösen ord när du har aktiverat Azure AD DS][cloud-only-passwords]. Den här lösen ords återställningen gör att hashar av autentiseringsuppgifter som krävs för Azure AD Domain Services genereras.

* **Kontrol lera att användar kontot är aktivt**: fem ogiltiga lösen ords försök inom 2 minuter på den hanterade domänen gör att ett användar konto blir utelåst i 30 minuter. Användaren kan inte logga in när kontot är utelåst. Efter 30 minuter låses användar kontot automatiskt upp.
  * Ogiltiga lösen ords försök på den hanterade Azure AD DS-domänen låser inte användar kontot i Azure AD. Användar kontot är bara utelåst i den hanterade domänen. Kontrol lera status för användar kontot i *Active Directory administrations konsolen (ADAC)* med den [virtuella hanterings datorn][management-vm], inte i Azure AD.
  * Du kan också [Konfigurera detaljerade lösen ords principer][password-policy] för att ändra standard utelåsnings tröskeln och varaktighet.

* **Externa konton** – kontrol lera att det berörda användar kontot inte är ett externt konto i Azure AD-klienten. Exempel på externa konton är Microsoft-konton som `dee@live.com` eller användar konton från en extern Azure AD-katalog. Azure AD DS lagrar inte autentiseringsuppgifter för externa användar konton så att de inte kan logga in på den hanterade domänen.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Det finns en eller flera aviseringar på din hanterade domän

Om det finns aktiva aviseringar på den hanterade domänen i Azure AD DS kan det leda till att autentiseringsprocessen fungerar som den ska.

Om du vill se om det finns några aktiva aviseringar [kontrollerar du hälso tillståndet för en hanterad Azure AD DS-domän][check-health]. Om några aviseringar visas kan du [Felsöka och lösa dem][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Användare som tas bort från din Azure AD-klient tas inte bort från din hanterade domän

Azure AD skyddar mot oavsiktlig borttagning av användar objekt. När du tar bort ett användar konto från en Azure AD-klient flyttas motsvarande användar objekt till pappers korgen. När den här borttagnings åtgärden synkroniseras med din Azure AD DS-hanterade domän markeras motsvarande användar konto som inaktiverat. Med den här funktionen kan du återställa eller ångra borttagning av användar kontot.

Användar kontot förblir i inaktiverat tillstånd i den hanterade domänen i Azure AD DS, även om du återskapar ett användar konto med samma UPN i Azure AD-katalogen. Om du vill ta bort användar kontot från den hanterade domänen i Azure AD DS måste du framtvinga borttagning av den från Azure AD-klienten.

Om du vill ta bort ett användar konto fullständigt från en Azure AD DS-hanterad domän, tar du bort användaren permanent från din Azure AD-klient med hjälp av cmdleten [Remove-MsolUser][Remove-MsolUser] med parametern `-RemoveFromRecycleBin`.

## <a name="next-steps"></a>Nästa steg

Om du fortsätter att ha problem [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
