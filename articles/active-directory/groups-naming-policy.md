---
title: Grupprincipinställningar för namnet för Office 365-grupper i Azure Active Directory (förhandsversion) | Microsoft Docs
description: Hur du ställer in förfallodatum för Office 365-grupper i Azure Active Directory (förhandsgranskning)
services: active-directory
documentationcenter: ''
author: curtand
manager: michael.tillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/29/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: c21706a591d0e1aa00279edf7a5534ada95fd8c1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory-preview"></a>Tillämpa en princip för namngivning för Office 365-grupper i Azure Active Directory (förhandsgranskning)

Om du vill framtvinga konsekvent namnkonventionerna för Office 365-grupper som skapats eller redigerats av dina användare att skapa gruppen naming princip för dina klienter i Azure Active Directory (AD Azure). T.ex, du kan använda namngivning princip för att kommunicera funktionen för en grupp, medlemskap, geografiskt område eller vem som skapade gruppen. Du kan också använda namngivning princip för att kategorisera grupper i adressboken. Du kan använda principen för att blockera specifika ord används i gruppnamn och alias.

> [!IMPORTANT]
> Med förhandsversionen av Office 365-grupper Naming princip kräver Azure Active Directory Premium P1 licenser eller Azure AD Basic EDU licenser för enskilda användare som är medlem i en eller flera grupper för Office 365.

Namngivning principen gäller för att skapa eller redigera grupper som skapats över arbetsbelastningar (till exempel Outlook, Microsoft Teams, SharePoint, Exchange eller Planner). Den tillämpas på både namn och gruppalias. Om du ställer in namngivning principen i Azure AD och du har en befintlig grupp för Exchange naming princip, har Azure AD naming principen tillämpats.

## <a name="naming-policy-features"></a>Namnge principfunktioner
Du kan tillämpa namngivning princip för Office 365-grupper på två olika sätt:

-   **Prefixet suffix namngivning princip** kan du definiera prefix eller -suffix som sedan läggs till automatiskt tillämpa namngivningskonventioner på grupper (till exempel i gruppnamnet ”GRP\_JAPAN\_min grupp\_ Tekniker ”GRP\_JAPAN\_ är prefixet och \_tekniker är suffixet som). 

-   **Anpassad blockeras ord** du kan ladda upp en uppsättning blockerade ord som är specifika för din organisation ska blockeras i grupper som skapats av användare (till exempel ”CEO lön, HR”).

### <a name="prefix-suffix-naming-policy"></a>Prefixet suffix namngivning princip

Den allmänna strukturen för namnkonventionen är Prefix [gruppnamn]-Suffix. Du kan bara ha en instans av [gruppnamn] i inställningen medan du kan definiera flera prefix och suffix. Prefix eller suffix kan vara antingen fasta strängar eller användarattribut som \[avdelning\] som ersätts baserat på den användare som skapar gruppen. Det totala tillåtna antalet tecken för din prefix och suffix strängar som kombineras är 53 tecken. 

Prefix och suffix kan innehålla specialtecken som stöds i gruppnamn och gruppen. Alla tecken i prefix eller suffix som inte stöds i gruppen aliaset är fortfarande används i gruppnamnet, men tas bort från gruppalias. På grund av den här begränsningen kanske prefix och suffix som tillämpas på tillgänglighetsgruppens namn skiljer sig från de som används för gruppalias. 

#### <a name="fixed-strings"></a>Fasta strängar

Du kan använda strängar för att göra det enklare att skanna och särskilja grupper i den globala adresslistan och vänstra navigeringsfönstret länkar för gruppen arbetsbelastningar. Några av de vanliga prefix är nyckelord som ' Grp\_Name', '\#Name ','\_namn '

#### <a name="user-attributes"></a>Användarattribut

Du kan använda attribut som kan hjälpa dig och användarna identifiera vilken avdelning, office eller geografiska region som gruppen har skapats. Om du definierar din namngivning princip som till exempel `PrefixSuffixNamingRequirement = “GRP [GroupName] [Department]”`, och `User’s department = Engineering`, och sedan en tvingande gruppnamn kanske ”GRP min grupp teknik”. Stöd för Azure AD-attribut är \[avdelning\], \[företagets\], \[Office\], \[region/område\], \[CountryOrRegion \], \[Rubrik\]. Stöds inte användarattribut behandlas som fasta strängar; till exempel ”\[postnummer\]”. Tilläggsattribut och anpassade attribut stöds inte.

Vi rekommenderar att du använder attribut som har värden som fylls i för alla användare i din organisation och inte använda attribut som har långa värden.

### <a name="custom-blocked-words"></a>Anpassade blockerade ord

En lista med blockerade word är en kommaavgränsad lista med fraser blockeras i gruppnamn och alias. Inga understräng sökningar utförs. En exakt matchning mellan tillgänglighetsgruppens namn och en eller flera av de anpassa blockerade ord krävs för att utlösa ett fel. Är inte utföra understräng sökningen så att användarna kan använda vanliga ord som ”Class-även om 'klassnamn' är ett ord som är blockerade.

Blockerade word regler:
- Blockerade ord är inte skiftlägeskänsliga.
- När en användare anger ett blockerade ord som en del av ett gruppnamn, visas ett felmeddelande med de blockerade word.
- Det finns inga teckenbegränsningar blockerade ord med.
- Det finns en övre gräns på 5000 fraser som kan konfigureras i listan över blockerade ord. 

### <a name="administrator-override"></a>Åsidosättning av administratören

Valda administratörer kan undantas från dessa principer för alla arbetsbelastningar i gruppen och slutpunkter, så att de kan skapa grupper med hjälp av blockerade ord och med sina egna namnkonventioner. Följande är listan över administratörsroller undantas från gruppen namngivning av principen.

- Global administratör
- Support för partner nivå 1
- Partner nivå 2-stöd
- Kontoadministratör för användaren
- Katalogskrivare

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>Installera PowerShell-cmdlets för att konfigurera en princip för namngivning

Se till att avinstallera en äldre version av Azure Active Directory PowerShell för Graph-modulen för Windows PowerShell och installera [Azure Active Directory PowerShell för Graph - offentliga förhandsversionen 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) innan du kör den PowerShell-kommandon. 

1. Öppna appen Windows PowerShell som administratör.
2. Avinstallera alla tidigare versioner av AzureADPreview.
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. Installera den senaste versionen av AzureADPreview.
  
  ````
  Install-Module AzureADPreview
  ````
Om du får en uppmaning om att få åtkomst till en ej betrodd lagringsplats skriver **Y**. Det kan ta några minuter att installera nya modulen.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Konfigurera gruppen namngivningen princip för en klient med Azure AD PowerShell

1. Öppna Windows PowerShell-fönstret på datorn. Du kan öppna den utan utökad behörighet.

2. Kör följande kommandon för att förbereda för att köra cmdlets.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  I den **logga in på ditt konto** skärm som öppnas, ange ditt administratörskonto och lösenord för att ansluta till din tjänst och välj **logga in**.

3. Följ stegen i [Azure Active Directory-cmdlets för att konfigurera inställningarna för](active-directory-accessmanagement-groups-settings-cmdlets.md) att skapa gruppinställningar för den här innehavaren.

### <a name="view-the-current-settings"></a>Visa de aktuella inställningarna

1. Hämta den aktuella namngivning principen om du vill visa de aktuella inställningarna.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Visa de aktuella inställningarna.
  
  ````
  $Setting.Values
  ````
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Ange princip för namngivning och anpassade blockerade ord

1. Ange den grupp namnet prefix och suffix i Azure AD PowerShell.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Ange anpassade blockerade orden som du vill begränsa. I följande exempel visas hur du kan lägga till egna anpassade ord.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Spara inställningarna för den nya principen för att gälla, som i följande exempel.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
Det var allt. Du har ställa in namngivning principen och lagt till din blockerade ord.

## <a name="export-or-import-the-list-of-custom-blocked-words"></a>Exportera eller importera en lista över anpassade blockerade ord

Mer information finns i artikeln [Azure Active Directory-cmdlets för att konfigurera inställningarna för](active-directory-accessmanagement-groups-settings-cmdlets.md).

Här är ett exempel på ett PowerShell.skript för att exportera flera blockerade ord:

````
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
````

Här är ett exempel PowerShell-skript för att importera flera blockerade ord:

````
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
$Settings["EnableMSStandardBlockedWords"] = $True
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
````

## <a name="naming-policy-experiences-across-office-365-apps"></a>Namnge princip inträffar i Office 365-appar

När du ställer in en grupprincip för namngivning i Azure AD, när en användare skapar en grupp i en app för Office 365, se de: 

* En förhandsgranskning av namnet enligt din namngivning princip (med prefix och suffix) så snart som användaren skriver namnet på
* Om användaren anger blockerade ord, ser de felmeddelandet så att de kan ta bort de blockerade ord.

Arbetsbelastning | Efterlevnad
----------- | -------------------------------
Azure Active Directory-portaler | Azure AD-portalen och på åtkomstpanelen portalen visar namngivning tillämpas principnamnet när användaren anger ett namn på gruppen när du skapar eller redigerar en grupp. När en användare anger en anpassad blockerade word, visas ett felmeddelande med blockerade ordet så att användaren kan ta bort den.
OWA (Outlook Web Access) | Outlook Web Access visar namngivning principen tillämpas namn när användaren anger ett gruppnamn eller gruppen. När en användare anger en anpassad blockerade word, visas ett felmeddelande visas i Användargränssnittet tillsammans med de blockerade word så att användaren kan ta bort den.
Skrivbordet Outlook | Grupper som skapats i skrivbordet Outlook är kompatibla med namngivning principinställningar. Outlook-skrivbordsapp ännu Visa inte förhandsgranskning av tvingande gruppnamnet och returnerar inte anpassade blockerade word-fel när användaren anger gruppens namn. Dock namngivning principen tillämpas automatiskt när du skapar eller redigerar en grupp och användare se felmeddelanden om det finns anpassade blockerade ord i gruppnamn eller alias.
Microsoft Teams | Microsoft-Teams visar den grupp som namnges principnamn tillämpas när användaren anger ett namn på teamet. När en användare anger en anpassad blockerade word, visas ett felmeddelande visas tillsammans med de blockerade word så att användaren kan ta bort den.
SharePoint  |  SharePoint visar namngivning principen tillämpas när användaren skriver en plats namn eller grupp-e-postadress. När en användare anger en anpassad blockerade word och ett felmeddelande visas, tillsammans med de blockerade word så att användaren kan ta bort den.
Microsoft Stream | Microsoft Stream visar gruppen naming principnamn tillämpas när användaren anger ett gruppnamn eller grupp e-postalias. När en användare anger en anpassad blockerade word, visas ett felmeddelande med blockerade ordet så att användaren kan ta bort den.
Outlook-iOS och Android-App | Grupper som skapats i Outlook-appar är kompatibla med den konfigurerade namngivning principen. Outlook mobila app ännu Visa inte förhandsgranskning av namngivning tillämpas principnamnet och returnerar inte anpassade blockerade word-fel när användaren anger gruppens namn. Dock namngivning principen tillämpas automatiskt när du klickar på Redigera och användare se felmeddelanden om det finns anpassade blockerade ord i gruppnamn eller alias.
Mobilappen för grupper | Grupper som skapats i grupper mobila appar är kompatibla med principen för namngivning. Grupper mobilappen visar inte förhandsgranskning av principen för namngivning och returnerar inte anpassade blockerade word-fel när användaren anger gruppens namn. Men namngivning principen tillämpas automatiskt när du skapar eller redigerar en grupp och användare visas med lämpliga fel om det finns anpassade blockerade ord i gruppnamn eller alias.
Planner | Planner är kompatibel med principen för namngivning. Planner visar namngivning princip förhandsgranskningen när du lägger till namn för energischema. När en användare anger en anpassad blockerade word, visas ett felmeddelande när du skapar planen.
Dynamics 365 för kunden Engagement | Dynamics 365 för kunden Engagement är kompatibel med principen för namngivning. Dynamics 365 visar namngivning principen tillämpas när användaren anger ett gruppnamn eller grupp e-postalias. Om användaren anger en anpassad blockerade word, visas ett felmeddelande med blockerade ordet så att användaren kan ta bort den.
School datasynkronisering (SDS) | Grupper som skapats via SDS följa naming princip, men namngivning principen används inte automatiskt. SDS-administratörer har att lägga till prefix och suffix till klassnamn som grupper måste skapas och sedan överförs till SDS. Gruppen Skapa eller redigera misslyckas annars.
Outlook Customer Manager (OCM) | Outlook Customer Manager är kompatibel med principen namngivning som automatiskt tillämpas på gruppen som skapades i Outlook kunden Manager. Om en anpassad blockerade word upptäcks att skapa gruppen i OCM blockeras och användaren har blockerats från att använda appen OCM.
Klassrum app | Grupper som skapats i klassrummet appen uppfyller principen namngivning men namngivning principen används inte automatiskt, och namngivning princip förhandsgranskningen visas inte för användarna när du skriver in ett klassrum gruppnamn. Användarna måste ange namnet på tvingande klassrum med prefix och suffix. Om inte klassrumsgruppen skapa eller redigera misslyckas med fel.
Power BI | Power BI arbetsytor är kompatibla med principen för namngivning.    
Yammer | Yammer-anslutna grupperna inte framtvingar den konfigurerade namngivning principen. För organisationer med naming principen är aktiverad skapar Yammer äldre Yammer-grupper som inte är ansluten till Office 365 för grupper som inte överensstämmer med principen för namngivning.
StaffHub  | StaffHub team följer inte principen namngivning, men den underliggande Office 365-gruppen har. StaffHub Teamnamn gäller inte prefix och suffix och kontrollerar inte om anpassade blockerade ord. Men StaffHub gäller prefix och suffix och blockerade ord tas bort från den underliggande Office 365-gruppen.
Exchange PowerShell | Exchange PowerShell cmdlets är kompatibla med principen för namngivning. Användarna får felmeddelanden med föreslagna prefix och suffix och för anpassade blockerade ord om de inte följer principen namngivning i gruppnamn och gruppalias (mailNickname).
Azure Active Directory PowerShell-cmdlets | Azure Active Directory PowerShell-cmdlets är kompatibla med namngivning av principen. Användarna får felmeddelanden med föreslagna prefix och suffix och för anpassade blockerade ord om de inte följer namnkonventionen i gruppnamn och gruppen.
Administrationscentret för Exchange | Administrationscentret Exchange är kompatibel med namngivning av principen. Användarna får felmeddelanden med föreslagna prefix och suffix och för anpassade blockerade ord om de inte följer namnkonventionen i gruppnamn och gruppen.
Administrationscenter för Office 365 | Administrationscenter för Office 365 är kompatibel med namngivning av principen. När en användare skapar eller redigeringar gruppnamn, namngivning principen tillämpas automatiskt och användarna få lämplig fel när de ange anpassade blockerade ord. Office 365 Admin center ännu Visa inte en förhandsgranskning av principen för namngivning och returnerar inte anpassade blockerade word-fel när användaren anger gruppens namn.

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure AD-grupper.

* [Se befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Princip för Office 365-grupper](active-directory-groups-lifecycle-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](active-directory-groups-dynamic-membership-azure-portal.md)
