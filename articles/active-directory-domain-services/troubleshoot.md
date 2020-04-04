---
title: Felsökning av Azure Active Directory Domain Services | Microsoft Dokument"
description: Lär dig hur du felsöker vanliga fel när du skapar eller hanterar Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 84efe294533186fdcf2e0a3356a7d6b01eccaf5f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654396"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Vanliga fel och felsökningssteg för Azure Active Directory Domain Services

Som en central del av identitet och autentisering för program har Azure Active Directory Domain Services (Azure AD DS) ibland problem. Om du stöter på problem finns det några vanliga felmeddelanden och tillhörande felsökningssteg som hjälper dig att få igång saker igen. Du kan när som helst också [öppna en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

Den här artikeln innehåller felsökningssteg för vanliga problem i Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Du kan inte aktivera Azure AD Domain Services för din Azure AD-katalog

Om du har problem med att aktivera Azure AD DS läser du följande vanliga fel och steg för att lösa dem:

| **Exempel på felmeddelande** | **Upplösning** |
| --- |:--- |
| *Namnet aaddscontoso.com används redan i det här nätverket. Ange ett namn som inte används.* |[Domännamnskonflikt i det virtuella nätverket](troubleshoot.md#domain-name-conflict) |
| *Domäntjänster kunde inte aktiveras i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet till programmet "Azure AD Domain Services Sync". Ta bort programmet "Azure AD Domain Services Sync" och försök sedan aktivera Domäntjänster för din Azure AD-klientorganisation.* |[Domain Services har inte tillräcklig behörighet för Azure AD Domain Services Sync-programmet](troubleshoot.md#inadequate-permissions) |
| *Domäntjänster kunde inte aktiveras i den här Azure AD-klienten. Domain Services-programmet i din Azure AD-klient har inte de behörigheter som krävs för att aktivera Domain Services. Ta bort programmet med programidentifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan aktivera Domäntjänster för din Azure AD-klientorganisation.* |[Domain Services-programmet är inte korrekt konfigurerat i din Azure AD-klientorganisation](troubleshoot.md#invalid-configuration) |
| *Domäntjänster kunde inte aktiveras i den här Azure AD-klienten. Microsoft Azure AD-programmet är inaktiverat i din Azure AD-klientorganisation. Aktivera programmet med programidentifieraren 0000002-0000-0000-c000-00000000000 och försök sedan aktivera Domäntjänster för din Azure AD-klientorganisation.* |[Microsoft Graph-programmet är inaktiverat i din Azure AD-klientorganisation](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt om domännamn

**Felmeddelande**

*Namnet aaddscontoso.com används redan i det här nätverket. Ange ett namn som inte används.*

**Upplösning**

Kontrollera att du inte har en befintlig AD DS-miljö med samma domännamn på samma eller ett peer-virtuellt nätverk. Du kan till exempel ha en AD DS-domän som heter *aaddscontoso.com* som körs på virtuella Azure-datorer. NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶r en Azure AD DS-hanterad domän med samma domännamn *aaddscontoso.com* i det virtuella nätverket misslyckas den begärda åtgärden.

Det här felet beror på namnkonflikter för domännamnet i det virtuella nätverket. En DNS-sökning kontrollerar om en befintlig AD DS-miljö svarar på det begärda domännamnet. LÃ¶s problemet genom att anse ett annat namn fÃ¶r att konfigurera din Azure AD DS-hanterade domän eller avetablering av den befintliga AD DS-domänen och sedan fÃ¶r att aktivera Azure AD DS.

### <a name="inadequate-permissions"></a>Otillräckliga tillstånd

**Felmeddelande**

*Domäntjänster kunde inte aktiveras i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet till programmet "Azure AD Domain Services Sync". Ta bort programmet "Azure AD Domain Services Sync" och försök sedan aktivera Domäntjänster för din Azure AD-klientorganisation.*

**Upplösning**

Kontrollera om det finns ett program med namnet *Azure AD Domain Services Sync* i din Azure AD-katalog. Om det här programmet finns, ta bort det och försök sedan igen för att aktivera Azure AD DS. Så här söker du efter ett befintligt program och tar bort det om det behövs:

1. I Azure-portalen väljer du **Azure Active Directory** på menyn för vänster navigering.
1. Välj **Företagsprogram**. Välj *Alla program* på den nedrullningsbara menyn **Programtyp** och välj sedan **Använd**.
1. Ange Synkronisering av *Azure AD Domain Services*i sökrutan . Om programmet finns markerar du det och väljer **Ta bort**.
1. När du har tagit bort programmet försöker du aktivera Azure AD DS igen.

### <a name="invalid-configuration"></a>Ogiltig konfiguration

**Felmeddelande**

*Domäntjänster kunde inte aktiveras i den här Azure AD-klienten. Domain Services-programmet i din Azure AD-klient har inte de behörigheter som krävs för att aktivera Domain Services. Ta bort programmet med programidentifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan aktivera Domäntjänster för din Azure AD-klientorganisation.*

**Upplösning**

Kontrollera om du har ett befintligt program med namnet *AzureActiveDirectoryDomainControllerServices* med en programidentifierare av *d87dcbc6-a371-462e-88e3-28ad15ec4e64* i din Azure AD-katalog. Om det här programmet finns tar du bort det och försöker sedan igen för att aktivera Azure AD DS.

Använd följande PowerShell-skript för att söka efter en befintlig programinstans och ta bort den om det behövs:

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

**Felmeddelande**

*Domäntjänster kunde inte aktiveras i den här Azure AD-klienten. Microsoft Azure AD-programmet är inaktiverat i din Azure AD-klientorganisation. Aktivera programmet med programidentifieraren 0000002-0000-0000-c000-00000000000 och försök sedan aktivera Domäntjänster för din Azure AD-klientorganisation.*

**Upplösning**

Kontrollera om du har inaktiverat ett program med identifieraren *0000002-0000-0000-c000-00000000000000*. Det här programmet är Microsoft Azure AD-programmet och ger Graph API-åtkomst till din Azure AD-klientorganisation. För att synkronisera din Azure AD-klient måste det här programmet vara aktiverat.

Så här kontrollerar du status för det här programmet och aktiverar det om det behövs:

1. I Azure-portalen väljer du **Azure Active Directory** på menyn för vänster navigering.
1. Välj **Företagsprogram**. Välj *Alla program* på den nedrullningsbara menyn **Programtyp** och välj sedan **Använd**.
1. Skriv *in 00000002-0000-0000-c000-00000000000*i sökrutan. Markera programmet och välj sedan **Egenskaper**.
1. Om **Aktiverad för användare att logga in** är inställt på *Nej*anger du värdet till *Ja*och väljer sedan **Spara**.
1. När du har aktiverat programmet försöker du aktivera Azure AD DS igen.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Användarna kan inte logga in på den hanterade domänen för Azure AD Domain Services

Om en eller flera användare i din Azure AD-klient inte kan logga in på den hanterade Azure AD DS-domänen slutför du följande felsökningssteg:

* **Autentiseringsformat** - Prova att använda UPN-formatet för att ange autentiseringsuppgifter, till exempel `dee@aaddscontoso.onmicrosoft.com`. UPN-formatet är det rekommenderade sättet att ange autentiseringsuppgifter i Azure AD DS. Kontrollera att upn-funktionen är korrekt konfigurerad i Azure AD.

    *SAMAccountName* för ditt konto, till exempel *AADDSCONTOSO\driley* kan skapas automatiskt om det finns flera användare med samma UPN-prefix i din klientorganisation eller om ditt UPN-prefix är alltför långt. Därför kan *SAMAccountName-formatet* för ditt konto skilja sig från vad du förväntar dig eller använder i din lokala domän.

* **Lösenordssynkronisering** – Kontrollera att du har aktiverat lösenordssynkronisering för [användare som endast är molnet eller][cloud-only-passwords] [hybridmiljöer med Azure AD Connect][hybrid-phs].
    * **Hybridsynkronerade konton:** Om de berörda användarkontona synkroniseras från en lokal katalog kontrollerar du följande områden:
    
      * Du har distribuerat eller uppdaterat till den [senaste rekommenderade versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
      * Du har konfigurerat Azure AD Connect för att [utföra en fullständig synkronisering][hybrid-phs].
      * Beroende på storleken på din katalog kan det ta ett tag innan användarkonton och autentiseringsuppgifterna är tillgängliga i Azure AD DS. Se till att du väntar tillräckligt länge innan du försöker autentisera mot den hanterade domänen.
      * Om problemet kvarstår efter att du har verifierat föregående steg kan du prova att starta om *Microsoft Azure AD Sync Service*. Öppna en kommandotolk från Azure AD Connect-servern och kör följande kommandon:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Molnkonton**: Om det berörda användarkontot är ett användarkonto endast för molnet kontrollerar du att [användaren har ändrat sitt lösenord när du har aktiverat Azure AD DS][cloud-only-passwords]. Den här lösenordsåterställningen gör att nödvändiga autentiseringsuppgifter för Azure AD Domain Services genereras.

* **Kontrollera att användarkontot är aktivt**: Som standard gör fem ogiltiga lösenordsförsök inom 2 minuter på den hanterade domänen att ett användarkonto är utelåst i 30 minuter. Användaren kan inte logga in medan kontot är utelåst. Efter 30 minuter låses användarkontot upp automatiskt.
  * Ogiltiga lösenordsförsök på azure AD DS-hanterade domänen låser inte användarkontot i Azure AD. Användarkontot är endast utelåst inom den hanterade domänen. Kontrollera användarkontostatusen i *ADAC (Active Directory Administrative Console)* med hjälp av [den virtuella hanterings-datorn][management-vm], inte i Azure AD.
  * Du kan också [konfigurera detaljerade lösenordsprinciper][password-policy] för att ändra standardgränsen för utelåsning och varaktighet.

* **Externa konton** – Kontrollera att det berörda användarkontot inte är ett externt konto i Azure AD-klienten. Exempel på externa konton `dee@live.com` är Microsoft-konton som eller användarkonton från en extern Azure AD-katalog. Azure AD DS lagrar inte autentiseringsuppgifter för externa användarkonton så att de inte kan logga in på den hanterade domänen.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Det finns en eller flera aviseringar på din hanterade domän

Om det finns aktiva aviseringar på Azure AD DS-hanterade domänen kan det förhindra att autentiseringsprocessen fungerar korrekt.

Kontrollera [hälsostatus för en Azure AD DS-hanterad domän][check-health]om det finns några aktiva aviseringar. Om några aviseringar visas [felsöker och löser du dem][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Användare som tas bort från Azure AD-klienten tas inte bort från den hanterade domänen

Azure AD skyddar mot oavsiktlig borttagning av användarobjekt. När du tar bort ett användarkonto från en Azure AD-klient flyttas motsvarande användarobjekt till papperskorgen. När den här borttagningsåtgärden synkroniseras med din Azure AD DS-hanterade domän markeras motsvarande användarkonto som inaktiverat. Den här funktionen hjälper dig att återställa, eller göra det bortreklarerat, användarkontot.

Användarkontot förblir inaktiverat i Azure AD DS-hanterad domän, även om du återskapar ett användarkonto med samma UPN i Azure AD-katalogen. Om du vill ta bort användarkontot från azure AD DS-hanterad domän måste du med våld ta bort det från Azure AD-klienten.

Om du vill ta bort ett användarkonto helt från en Azure AD DS-hanterad domän tar du bort användaren `-RemoveFromRecycleBin` permanent från din Azure AD-klient med hjälp av cmdleten [Remove-MsolUser][Remove-MsolUser] PowerShell med parametern.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en Azure-supportbegäran för][azure-support] ytterligare felsökningshjälp.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
