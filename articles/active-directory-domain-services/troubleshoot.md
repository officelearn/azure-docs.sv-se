---
title: 'Azure Active Directory Domain Services: Fel söknings guide | Microsoft Docs'
description: Fel söknings guide för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: acb001417b85b8ff45b2617e148e8b1961f3cbfa
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772979"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services – fel söknings guide
Den här artikeln innehåller fel söknings tips för problem som kan uppstå när du konfigurerar eller administrerar Azure Active Directory (AD) Domain Services.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Du kan inte aktivera Azure AD Domain Services för din Azure AD-katalog
Det här avsnittet hjälper dig att felsöka fel när du försöker aktivera Azure AD Domain Services för din katalog.

Välj de fel söknings steg som motsvarar det fel meddelande som du stöter på.

| **Fel meddelande** | **Lösning** |
| --- |:--- |
| *Namnet contoso100.com används redan i nätverket. Ange ett namn som inte används.* |[Domän namns konflikt i det virtuella nätverket](troubleshoot.md#domain-name-conflict) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet för programmet ”Azure AD Domain Services Sync”. Ta bort programmet ”Azure AD Domain Services Sync” och försök sedan aktivera Domain Services för din Azure AD-klient.* |[Domän tjänster har inte tillräcklig behörighet för Azure AD Domain Services Sync-programmet](troubleshoot.md#inadequate-permissions) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Domain Services i din Azure AD-klient har inte tillräcklig behörighet för att aktivera Domain Services. Ta bort programmet med programidentifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan aktivera Domain Services för din Azure AD-klient.* |[Domän tjänst programmet har inte kon figurer ATS korrekt i din klient organisation](troubleshoot.md#invalid-configuration) |
| *Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Microsoft Azure AD är inaktiverat i din Azure AD-klient. Aktivera programmet med programidentifieraren 00000002-0000-0000-c000-000000000000 och försök aktivera Domain Services för din Azure AD-klient.* |[Microsoft Graph programmet är inaktiverat i din Azure AD-klient](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Domän namns konflikt
**Fel meddelande:**

*Namnet contoso100.com används redan i nätverket. Ange ett namn som inte används.*

**Reparation**

Se till att du inte har en befintlig domän med samma domän namn tillgängligt i det virtuella nätverket. Anta exempelvis att det redan finns en domän som heter ”contoso.com” i det valda virtuella nätverket. Senare kan du försöka aktivera en Azure AD Domain Services hanterad domän med samma domän namn (det vill säga "contoso.com") på det virtuella nätverket. Det uppstår ett problem när du försöker aktivera Azure AD Domain Services.

Det här felet beror på namn konflikter för domän namnet på det virtuella nätverket. I den här situationen måste du använda ett annat namn för att ställa in din Azure AD Domain Services-hanterade domän. Du kan också avetablera den befintliga domänen och sedan aktivera Azure AD Domain Services.

### <a name="inadequate-permissions"></a>Otillräckliga behörigheter
**Fel meddelande:**

*Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Tjänsten har inte tillräcklig behörighet för programmet ”Azure AD Domain Services Sync”. Ta bort programmet ”Azure AD Domain Services Sync” och försök sedan aktivera Domain Services för din Azure AD-klient.*

**Reparation**

Kontrol lera om det finns ett program med namnet "Azure AD Domain Services Sync" i Azure AD-katalogen. Om det här programmet finns tar du bort det och aktiverar Azure AD Domain Services igen.

Utför följande steg för att söka efter närvaron av programmet och ta bort det om programmet finns:

1. Gå till avsnittet **program** i Azure AD-katalogen i [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Välj **alla program** i list rutan **Visa** . Välj **valfri** i list rutan **program status** . Välj **valfri** i list rutan **synlighet för program** .
3. Skriv **Azure AD Domain Services Sync** i sökrutan. Om programmet finns klickar du på det och klickar på knappen **ta bort** i verktygsfältet för att ta bort det.
4. När du har tagit bort programmet försöker du aktivera Azure AD Domain Services en gång till.

### <a name="invalid-configuration"></a>Ogiltig konfiguration
**Fel meddelande:**

*Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Domain Services i din Azure AD-klient har inte tillräcklig behörighet för att aktivera Domain Services. Ta bort programmet med programidentifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64 och försök sedan aktivera Domain Services för din Azure AD-klient.*

**Reparation**

Kontrol lera om du har ett program med namnet "AzureActiveDirectoryDomainControllerServices" (med program identifieraren d87dcbc6-a371-462e-88e3-28ad15ec4e64) i Azure AD-katalogen. Om det här programmet finns måste du ta bort det och sedan återaktivera Azure AD Domain Services.

Använd följande PowerShell-skript för att hitta programmet och ta bort det.

> [!NOTE]
> Det här skriptet använder **Azure AD PowerShell version 2** -cmdletar. En fullständig lista över alla tillgängliga cmdlets och för att hämta modulen finns i [referens dokumentationen för AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

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
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph inaktiverat
**Fel meddelande:**

Det gick inte att aktivera Domain Services i den här Azure AD-klienten. Programmet Microsoft Azure AD är inaktiverat i din Azure AD-klient. Aktivera programmet med program identifieraren 00000002-0000-0000-C000-000000000000 och försök sedan aktivera Domain Services för din Azure AD-klient.

**Reparation**

Kontrol lera om du har inaktiverat ett program med identifieraren 00000002-0000-0000-C000-000000000000. Det här programmet är Microsoft Azure AD programmet och ger Graph API åtkomst till din Azure AD-klient. Azure AD Domain Services behöver det här programmet vara aktiverat för att synkronisera din Azure AD-klient med din hanterade domän.

Du kan lösa det här felet genom att aktivera det här programmet och sedan försöka aktivera Domain Services för din Azure AD-klient.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Användarna kan inte logga in på den hanterade domänen för Azure AD Domain Services
Om en eller flera användare i din Azure AD-klient inte kan logga in på den nyligen skapade hanterade domänen utför du följande fel söknings steg:

* **Logga in med UPN-format:** Försök logga in med UPN-formatet (till exempel ”joeuser@contoso.com”) istället för formatet SAMAccountName format (”CONTOSO\joeuser”). SAMAccountName kan genereras automatiskt för användare vars UPN-prefix är över långt eller samma som en annan användare på den hanterade domänen. UPN-formatet garanterar att det är unikt i en Azure AD-klient.

> [!NOTE]
> Vi rekommenderar att du använder UPN-formatet för att logga in på den Azure AD Domain Services hanterade domänen.
>
>

* Kontrollera att du har [aktiverat lösenordssynkronisering](active-directory-ds-getting-started-password-sync.md) i enlighet med anvisningarna i guiden Komma igång.
* **Externa konton:** Kontrollera att det användarkonto som påverkas inte är ett externt konto i Azure AD-klienten. Exempel på externa konton är Microsoft-konton (till exempel 'joe@live.com') eller användar konton från en extern Azure AD-katalog. Eftersom Azure AD Domain Services saknar autentiseringsuppgifter för sådana användar konton kan de här användarna inte logga in på den hanterade domänen.
* **Synkroniserade konton:** Om de berörda användar kontona synkroniseras från en lokal katalog kontrollerar du att:

  * Du har distribuerat eller uppdaterat till den [senaste rekommenderade versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Du har konfigurerat Azure AD Connect att [utföra en fullständig synkronisering](active-directory-ds-getting-started-password-sync.md).
  * Beroende på katalogens storlek kan det ta en stund innan användar konton och inloggnings-hashar är tillgängliga i Azure AD Domain Services. Se till att du väntar tillräckligt länge innan du försöker autentisera igen.
  * Om problemet kvarstår när du har verifierat föregående steg kan du försöka starta om tjänsten Microsoft Azure AD Sync. Starta en kommando tolk från den synkroniserade datorn och kör följande kommandon:

    1. net stop "Microsoft Azure AD Sync"
    2. net start Microsoft Azure AD Sync
* **Endast moln konton**: Om det berörda användar kontot är ett moln användar konto, se till att användaren har ändrat sitt lösen ord när du har aktiverat Azure AD Domain Services. Det här steget gör att de autentiseringshashvärden som krävs för Azure AD Domain Services genereras.
* **Kontrol lera att användar kontot är aktivt**: Om ett användar konto är utelåst kan de inte logga in förrän deras konto är aktivt igen. Fem ogiltiga lösen ords försök inom 2 minuter på den hanterade domänen gör att ett användar konto blir utelåst i 30 minuter. Efter 30 minuter låses användar kontot automatiskt upp.
  * Ogiltiga lösen ords försök i den hanterade domänen låser inte användar kontot i Azure AD. Användar kontot är bara utelåst i din Azure AD Domain Services hanterade domän. Kontrol lera status för användar kontot med hjälp av Active Directory administrations konsolen (ADAC) för den hanterade Azure AD DS-domänen, inte i Azure AD.
  * Du kan också [Konfigurera detaljerade lösen ords principer som ändrar standard utelåsnings tröskeln och varaktighet](https://docs.microsoft.com/azure/active-directory-domain-services/password-policy).

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Det finns en eller flera aviseringar på din hanterade domän

Se hur du löser aviseringar på din hanterade domän genom att gå till artikeln [felsök aviseringar](troubleshoot-alerts.md) .

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Användare som tas bort från din Azure AD-klient tas inte bort från din hanterade domän
Azure AD skyddar dig mot oavsiktlig borttagning av användarobjekt. När du tar bort ett användarkonto från Azure AD-klienten flyttas motsvarande användarobjekt till papperskorgen. När den här borttagnings åtgärden synkroniseras till din hanterade domän, gör det att motsvarande användar konto markeras som inaktiverat. Den här funktionen hjälper dig att återställa eller ångra borttagning av användar kontot senare.

Användar kontot förblir i inaktiverat tillstånd i din hanterade domän, även om du återskapar ett användar konto med samma UPN i Azure AD-katalogen. Om du vill ta bort användar kontot från din hanterade domän måste du framtvinga borttagning av det från din Azure AD-klient.

Om du vill ta bort användar kontot fullständigt från din hanterade domän, tar du bort användaren permanent från din Azure AD-klient. Använd PowerShell-cmdleten `-RemoveFromRecycleBin` med alternativet, enligt beskrivningen i den här [MSDN-artikeln](/previous-versions/azure/dn194132(v=azure.100)). `Remove-MsolUser`


## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services produkt teamet för att [dela feedback eller för support](contact-us.md).
