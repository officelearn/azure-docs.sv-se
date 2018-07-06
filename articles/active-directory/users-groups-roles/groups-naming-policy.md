---
title: Namn på grupprincipinställningar för Office 365-grupper i Azure Active Directory (förhandsversion) | Microsoft Docs
description: Hur du ställer in förfallodatum för Office 365-grupper i Azure Active Directory (förhandsversion)
services: active-directory
documentationcenter: ''
author: curtand
manager: michael.tillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/21/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: c973245b6c8ee3557f9bf410544f9f698b2733c5
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37872526"
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory-preview"></a>Framtvinga en namnprincip för Office 365-grupper i Azure Active Directory (förhandsversion)

Konfigurera en grupp namngivningspolicy för dina klienter i Azure Active Directory (Azure AD) för att tillämpa konsekventa namngivningskonventioner för Office 365-grupper som skapats eller redigerats av dina användare. Exempelvis kunde du använda namnprincip för att kommunicera funktionen för en grupp, medlemskap, geografiskt område eller vem som skapade gruppen. Du kan också använda namnprincip för att kategorisera grupper i adressboken. Du kan använda för att blockera specifika ord används i namn och alias.

> [!IMPORTANT]
> Använder förhandsversionen av Office 365 grupper namnger principen kräver Azure Active Directory Premium P1-licenser eller Azure AD Basic EDU licenser för enskilda användare som är medlem i en eller flera grupper för Office 365.

Namngivning principen tillämpas på skapar eller redigerar grupper som skapats i arbetsbelastningar (till exempel Outlook, Microsoft Teams, SharePoint, Exchange eller Planner). Den tillämpas på både namn och gruppalias. Om du ställer in din namnprincip i Azure AD och du har en befintlig grupp för Exchange namngivningspolicy, tillämpas Azure AD som namnger principen.

## <a name="naming-policy-features"></a>Namngivning av policy-funktioner
Du kan tillämpa namnprincip för Office 365-grupper på två olika sätt:

-   **Prefix-suffix namnprincip** du kan definiera prefix eller suffix som sedan läggs automatiskt till att tillämpa en namngivningskonvention på dina grupper (till exempel i gruppnamnet ”GRP\_JAPAN\_min grupp\_ Ingenjörskonst ”, GRP\_JAPAN\_ är prefixet och \_tekniker är suffixet). 

-   **Anpassade spärrad ord** du kan ladda upp en uppsättning blockerade ord som är specifika för din organisation ska blockeras i grupper som skapats av användare (till exempel ”CEO, lönelistor, HR”).

### <a name="prefix-suffix-naming-policy"></a>Namnprincip för prefix-suffix

Den allmänna strukturen för namngivningskonventionen är Prefix [GroupName]-Suffix. Medan du kan definiera flera prefix och suffix, kan du bara ha en instans av [GroupName] i inställningen. Prefix eller suffix kan vara antingen fasta strängar eller användarattribut som \[avdelning\] som ersätts baserat på den användare som skapar gruppen. Det totala antalet tillåtna tecken för ditt prefix och suffix strängar som kombineras är 53 tecken. 

Prefix och suffix kan innehålla specialtecken som stöds i gruppnamn och ett gruppalias. Alla tecken i prefix eller suffix som inte stöds i gruppalias är fortfarande tillämpas i gruppnamnet, men tas bort från gruppalias. På grund av den här begränsningen kan det vara samma som de som tillämpas på gruppalias att prefix och suffix som tillämpas på gruppnamnet. 

#### <a name="fixed-strings"></a>Fast strängar

Du kan använda strängar för att göra det enklare att skanna och skilja grupper i den globala adresslistan och i vänstra navigeringsfönstret länkar för gruppen arbetsbelastningar. Vissa av de vanliga prefix är nyckelord som ”Grp\_namn ', '\#namn ','\_namn”

#### <a name="user-attributes"></a>Användarattribut

Du kan använda attribut som kan hjälpa dig och dina användare identifiera vilken avdelning, office eller geografiska region som gruppen har skapats. Exempel: Om du definierar din namnprincip som `PrefixSuffixNamingRequirement = “GRP [GroupName] [Department]”`, och `User’s department = Engineering`, och sedan en tvingande namn kan vara ”GRP min grupp teknik”. Stöd för Azure AD-attribut är \[avdelning\], \[företagets\], \[Office\], \[region/område\], \[CountryOrRegion \], \[Rubrik\]. Stöds inte användarattribut behandlas som fast strängar; till exempel ”\[postalCode\]”. Tilläggsattribut och anpassade attribut stöds inte.

Vi rekommenderar att du använder attribut som har värden som fylls för alla användare i din organisation och inte använda attribut som har långa värden.

### <a name="custom-blocked-words"></a>Anpassade spärrad ord

Ett blockerat ordlistan är en kommaavgränsad lista med fraser blockeras i namn och alias. Inga understräng sökningar utförs. En exakt matchning mellan gruppnamnet och en eller flera av de anpassa blockerade ord krävs för att utlösa ett fel. Understräng sökning utförs inte, så att användarna kan använda vanliga ord som ”Class” även om ”klassnamn' är ett blockerat ord.

Blockerat ord regler:
- Blockerade ord är inte skiftlägeskänsliga.
- När en användare anger ett blockerat ord som en del av ett gruppnamn, visas ett felmeddelande med det blockerade ordet.
- Det finns inga teckenbegränsningar på blockerade orden.
- Det finns en övre gräns på 5000 fraser som kan konfigureras i listan över blockerade orden. 

### <a name="administrator-override"></a>Åsidosättning av administratören

Utvalda administratörer kan undantas från dessa principer över alla arbetsbelastningar för gruppen och slutpunkter, så att de kan skapa grupper med hjälp av blockerade ord och med sina egna namngivningskonventioner. Följande är listan över administratörsroller som är undantagna från den princip för namngivning av grupper.

- Global administratör
- Nivå 1-Support för partner
- Nivå 2 partnersupport
- Användarkonto-administratör
- Katalogskrivare

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>Installera PowerShell-cmdlets för att konfigurera en namnprincip

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
Om du får en uppmaning om att få åtkomst till en ej betrodd lagringsplats skriver **Y**. Det kan ta några minuter för den nya modulen att installera.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Konfigurera gruppen namngivningspolicy för en klient med hjälp av Azure AD PowerShell

1. Öppna ett Windows PowerShell-fönster på datorn. Du kan öppna den utan utökad behörighet.

2. Kör följande kommandon för att förbereda för att köra cmdlets.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  I den **logga in på ditt konto** skärmen som öppnas, ange ditt administratörskonto och lösenord för att ansluta dig till din tjänst och välj **logga in**.

3. Följ stegen i [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](groups-settings-cmdlets.md) skapa gruppinställningar för den här klienten.

### <a name="view-the-current-settings"></a>Visa de aktuella inställningarna

1. Hämta den aktuella namnprincip om du vill visa de aktuella inställningarna.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Visa de aktuella gruppinställningarna för.
  
  ````
  $Setting.Values
  ````
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Ange namnprincip och anpassade spärrad ord

1. Ange grupp namnet prefix och suffix i Azure AD PowerShell.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Ange de anpassade blockerade ord som du vill begränsa. I följande exempel visas hur du kan lägga till egna anpassade ord.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Spara inställningarna för den nya principen för att gälla, som i följande exempel.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
Det var allt. Du har konfigurera principer för namngivning och har lagt till din blockerade ord.

## <a name="export-or-import-the-list-of-custom-blocked-words"></a>Exportera eller importera listan över anpassade spärrad ord

Mer information finns i artikeln [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](groups-settings-cmdlets.md).

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

## <a name="naming-policy-experiences-across-office-365-apps"></a>Namngivningspolicy resultat på alla Office 365-appar

När du har angett en gruppnamnsprincip i Azure AD, när en användare skapar en grupp i en Office 365-app, visas: 

* En förhandsgranskning av namnet enligt dina namnprincip (med prefix och suffix) så snart som användaren skriver i gruppnamnet
* Om användaren anger blockerade ord, visas de ett felmeddelande visas så att de kan ta bort blockerade orden.

Arbetsbelastning | Efterlevnad
----------- | -------------------------------
Azure Active Directory-portalerna | Azure AD-portalen och på åtkomstpanelen-portalen visar namn för naming principen tillämpas när användaren skriver ett gruppnamn när du skapar eller redigerar en grupp. När en användare anger ett anpassat blockerat ord, visas ett felmeddelande med det blockerade ordet så att användaren kan ta bort den.
OWA (Outlook Web Access) | Outlook Web Access visar namnprincip tillämpas namn när användaren skriver ett gruppnamn eller gruppalias. När en användare anger ett anpassat blockerat ord, visas ett felmeddelande visas i Användargränssnittet tillsammans med det blockerade ordet så att användaren kan ta bort den.
Skrivbordet Outlook | Grupper som skapats i skrivbordet Outlook är kompatibla med namngivning principinställningarna. Outlook-skrivbordsappen ännu visar inte förhandsgranskning av tvingande gruppnamnet och returnerar inte de anpassa blockerat ord fel när användaren anger gruppnamnet. Dock namngivning principen tillämpas automatiskt när du skapar eller redigerar en grupp och användare finns i felmeddelanden om det finns anpassade spärrad ord i namn eller alias.
Microsoft Teams | Microsoft Teams visar den grupp som naming principnamn tillämpas när användaren anger ett namn på teamet. När en användare anger ett anpassat blockerat ord, visas ett felmeddelande visas tillsammans med det blockerade ordet så att användaren kan ta bort den.
SharePoint  |  SharePoint visar namnet på namngivning principen som tillämpas när användaren skriver en plats namn eller gruppen e-postadress. När en användare anger ett anpassat blockerat ord ett felmeddelande visas, tillsammans med det blockerade ordet så att användaren kan ta bort den.
Microsoft Stream | Microsoft Stream visar den grupp som naming principnamn tillämpas när användaren skriver ett namn eller en grupp e-postalias. När en användare anger ett anpassat blockerat ord, visas ett felmeddelande med det blockerade ordet så att användaren kan ta bort den.
Outlook-iOS och Android-App | Grupper som skapats i Outlook-appar är kompatibla med den konfigurerade namnprincip. Outlook-mobilappen visar inte ännu i förhandsversionen av namn för naming principen tillämpas och returnera inte anpassade blockerat ord fel när användaren anger namnet på. Men namnprincip tillämpas automatiskt när du klickar på Skapa/redigera och användarna se felmeddelanden om det finns anpassade spärrad ord i namn eller alias.
Mobilappen för grupper | Grupper som skapats i mobilappen grupper är kompatibla med namnprincip. Grupper mobilappen visar inte förhandsversionen av namnprincip och returnerar inte anpassade blockerat ord fel när användaren anger namnet på. Men namnprincip tillämpas automatiskt när du skapar eller redigerar en grupp och användare visas med lämpliga fel om det finns anpassade spärrad ord i namn eller alias.
Planner | Planner är kompatibel med namnprincip. Planner visar förhandsversionen av namngivning princip när du anger plannamnet. När en användare anger ett anpassat blockerat ord, visas ett felmeddelande när du skapar planen.
Dynamics 365 för kundengagemang | Dynamics 365 för kundengagemang är kompatibel med namnprincip. Dynamics 365 visar namn för naming principen tillämpas när användaren skriver ett namn eller en grupp e-postalias. När användaren anger en anpassad blockerat ord, visas ett felmeddelande med det blockerade ordet så att användaren kan ta bort den.
School Data Sync (SDS) | Grupper som skapats via SDS uppfylla namngivningspolicy, men namnprincip används inte automatiskt. SDS administratörerna att lägga till prefix och suffix klassnamn för vilka grupper behöver skapas och sedan överförs till SDS. Gruppen Skapa eller redigera skulle misslyckas annars.
Outlook Customer hanteraren för | Outlook Customer Manager är kompatibla med principen namngivning som automatiskt tillämpas på den grupp som skapats i Outlook Customer Manager. Om ett anpassat blockerat ord upptäcks kan skapas i OCM blockeras och användaren är blockerad från att använda appen OCM.
Klassrumsappen | Grupper som skapats i appen klassrum följer namnprincip, men namnprincip används inte automatiskt och namngivning princip förhandsgranskningen visas inte för användarna när du skriver in ett namn på klassrum. Användare måste ange namnet på tvingande klassrum med prefix och suffix. Om inte, klassrumsgruppen skapa eller redigera misslyckas med fel.
Power BI | Power BI-arbetsytor är kompatibla med namnprincip.    
Yammer | När en användare loggar in på Yammer med deras Azure Active Directory-konto skapar du en grupp eller redigerar ett gruppnamn, ska gruppnamnet efterleva namngivningspolicy. Detta gäller både till Office 365 anslutna grupperna och alla andra Yammer-grupper.<br>Om en ansluten Office 365-grupp har skapats innan namnprincip finns på plats, följer gruppnamnet inte automatiskt namngivningsprinciper. När en användare redigerar gruppnamnet, uppmanas de att lägga till prefix och suffix.
StaffHub  | StaffHub-team följer inte namnprincip, men den underliggande Office 365-gruppen har. StaffHub-Teamnamn gäller inte prefix och suffix och kontrollerar inte om anpassade spärrad orden. Men StaffHub gäller prefix och suffix och blockerat ord tas bort från den underliggande Office 365-gruppen.
Exchange PowerShell | Exchange PowerShell-cmdletar är kompatibla med namnprincip. Användarna får lämplig felmeddelanden med föreslagna prefix och suffix och för anpassade spärrad ord om de inte följer namnprincip i gruppnamn och gruppalias (mailNickname).
Azure Active Directory PowerShell-cmdlets | Azure Active Directory PowerShell-cmdletar är kompatibla med namngivningspolicy. Användarna får lämplig felmeddelanden med föreslagna prefix och suffix och för anpassade spärrad ord om de inte följer namngivningskonventionen i gruppnamn och gruppalias.
Administrationscenter för Exchange | Administrationscenter för Exchange är kompatibla med namngivningspolicy. Användarna får lämplig felmeddelanden med föreslagna prefix och suffix och för anpassade spärrad ord om de inte följer namngivningskonventionen i gruppnamn och gruppalias.
Administrationscenter för Office 365 | Administrationscenter för Office 365 är kompatibel med namngivningspolicy. När en användare skapar redigeringar gruppnamn, namngivning principen tillämpas automatiskt och får användarna lämpliga fel när de kommer in anpassade spärrad orden. Administrationscenter för Office 365 ännu visar inte en förhandsgranskning av namnprincip och returnera inte anpassade blockerat ord fel när användaren anger gruppnamnet.

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure AD-grupper.

* [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Princip för Office 365-grupper](groups-lifecycle.md)
* [Hantera inställningarna för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
