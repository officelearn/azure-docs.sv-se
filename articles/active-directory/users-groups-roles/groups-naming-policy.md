---
title: Framtvinga gruppnamnprincip i Azure Active Directory | Microsoft-dokument
description: Konfigurera namngivningsprincipen för Office 365-grupper i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 141e83e21db18f21468113fd9927c2bdd2ed176d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497874"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Framtvinga en namngivningsprincip för Office 365-grupper i Azure Active Directory

Om du vill tillämpa konsekventa namngivningskonventioner för Office 365-grupper som skapats eller redigerats av användarna konfigurerar du en gruppnamngivningsprincip för dina klienter i Azure Active Directory (Azure AD). Du kan till exempel använda namngivningsprincipen för att kommunicera funktionen för en grupp, ett medlemskap, en geografisk region eller vem som skapade gruppen. Du kan också använda namngivningsprincipen för att kategorisera grupper i adressboken. Du kan använda principen för att blockera specifika ord från att användas i gruppnamn och alias.

> [!IMPORTANT]
> Om du använder Azure AD-namngivningsprincipen för Office 365-grupper krävs att du har men inte nödvändigtvis tilldela en Azure Active Directory Premium P1-licens eller Azure AD Basic EDU-licens för varje unik användare som är medlem i en eller flera Office 365-grupper.

Namngivningsprincipen tillämpas på att skapa eller redigera grupper som skapats över arbetsbelastningar (till exempel Outlook, Microsoft Teams, SharePoint, Exchange eller Planner). Den tillämpas på både gruppnamnet och gruppaliaset. Om du ställer in namngivningsprincipen i Azure AD och har en befintlig namngivningsprincip för Exchange-gruppen tillämpas namngivningsprincipen i Azure AD i din organisation.

När gruppnamngivningsprincipen har konfigurerats tillämpas principen på nya Office 365-grupper som skapats av slutanvändare. Namngivningsprincipen gäller inte för vissa katalogroller, till exempel Global administratör eller Användaradministratör (se nedan för en fullständig lista över roller som är undantagna från gruppnamngivningsprincipen). För befintliga Office 365-grupper gäller principen inte omedelbart vid konfigurationstillfället. När gruppägaren har redigerat gruppnamnet för dessa grupper tillämpas namngivningsprincipen.

## <a name="naming-policy-features"></a>Namngivningsprincipfunktioner

Du kan tillämpa namngivningsprincipen för grupper på två olika sätt:

- **Namngivningsprincipen prefix-suffix** Du kan definiera prefix eller suffix som sedan läggs till automatiskt för att tillämpa en namngivningskonvention\_för dina grupper\_\_ (till exempel i gruppnamnet "GRP \_\_JAPAN\_My Group Engineering", GRP JAPAN är prefixet och Engineering är suffixet). 

- **Anpassade blockerade ord** Du kan ladda upp en uppsättning blockerade ord som är specifika för din organisation för att blockeras i grupper som skapats av användare (till exempel "VD, Lön, HR").

### <a name="prefix-suffix-naming-policy"></a>Namngivningsprincipen prefix-suffix

Namnkonventionens allmänna struktur är "Prefix[GroupName]Suffix". Du kan definiera flera prefix och suffix, men du kan bara ha en instans av [GroupName] i inställningen. Prefixen eller suffixen kan vara antingen fasta strängar \[eller\] användarattribut, till exempel Avdelning som ersätts baserat på användaren som skapar gruppen. Det totala tillåtna antalet tecken för prefixet och suffixsträngarna tillsammans är 53 tecken. 

Prefix och suffix kan innehålla specialtecken som stöds i gruppnamn och gruppalias. Alla tecken i prefixet eller suffixet som inte stöds i gruppaliaset används fortfarande i gruppnamnet, men tas bort från gruppaliaset. På grund av den här begränsningen kan prefix och suffix som tillämpas på gruppnamnet skilja sig från de som tillämpas på gruppalias. 

#### <a name="fixed-strings"></a>Fasta strängar

Du kan använda strängar för att göra det enklare att söka igenom och differentiera grupper i den globala adresslistan och i de vänstra navigeringslänkarna för grupparbetsbelastningar. Några av de vanliga prefixen är\_nyckelord som\#"Grp\_Name", ' Name', ' Name'

#### <a name="user-attributes"></a>Användarattribut

Du kan använda attribut som kan hjälpa dig och dina användare att identifiera vilken avdelning, kontor eller geografisk region som gruppen skapades för. Om du till exempel definierar `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`namnprincipen som , och `User’s department = Engineering`kan ett påtvingat gruppnamn vara "GRP My Group Engineering". Azure AD-attribut \[som\] \[stöds\] \[är\] \[Avdelning, Företag\], \[Office\], \[\]StateOrProvince , CountryOrRegion , Title . Användarattribut som inte stöds behandlas som fasta strängar. till exempel\["\]postalCode ". Tilläggsattribut och anpassade attribut stöds inte.

Vi rekommenderar att du använder attribut som har värden ifyllda för alla användare i organisationen och inte använder attribut som har långa värden.

### <a name="custom-blocked-words"></a>Anpassade blockerade ord

En blockerad ordlista är en kommaavgränsad lista med fraser som ska blockeras i gruppnamn och alias. Inga substrängsökningar utförs. En exakt matchning mellan gruppnamnet och ett eller flera av de anpassade blockerade orden krävs för att utlösa ett fel. Understrängssökning utförs inte så att användarna kan använda vanliga ord som "Klass" även om "lass" är ett blockerat ord.

Blockerade ordlisteregler:

- Blockerade ord är inte skiftlägeskänsliga.
- När en användare anger ett blockerat ord som en del av ett gruppnamn visas ett felmeddelande med det blockerade ordet.
- Det finns inga teckenbegränsningar för blockerade ord.
- Det finns en övre gräns på 5000 fraser som kan konfigureras i listan blockerade ord. 

### <a name="roles-and-permissions"></a>Roller och behörigheter

För att konfigurera namngivningsprincipen krävs en av de efterföljande rollerna:
- Global administratör
- Gruppadministratör
- Användaradministratör

Valda administratörer kan undantas från dessa principer, över alla grupparbetsbelastningar och slutpunkter, så att de kan skapa grupper med blockerade ord och med sina egna namngivningskonventioner. Följande är listan över administratörsroller som är undantagna från gruppnamngivningsprincipen.

- Global administratör
- Support för partnernivå 1
- Support för partnernivå 2
- Användaradministratör
- Katalogförfattare

## <a name="configure-naming-policy-in-azure-portal"></a>Konfigurera namngivningsprincipen i Azure Portal

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett gruppadministratörskonto.
1. Välj **Grupper**och välj sedan **Namngivningsprincip** för att öppna sidan Namngivningsprincip.

    ![öppna sidan Namngivningsprincip i administrationscentret](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Visa eller redigera namngivningsprincipen för prefix-suffix

1. Välj **Gruppnamnsprincip**på sidan **Namngivningsprincip** .
1. Du kan visa eller redigera det aktuella prefixet eller suffixets namngivningsprinciper individuellt genom att välja de attribut eller strängar som du vill tillämpa som en del av namngivningsprincipen.
1. Om du vill ta bort ett prefix eller ett suffix från listan markerar du prefixet eller suffixet och väljer sedan **Ta bort**. Flera objekt kan tas bort samtidigt.
1. Spara ändringarna för att den nya principen ska träda i kraft genom att välja **Spara**.

### <a name="edit-custom-blocked-words"></a>Redigera anpassade blockerade ord

1. På sidan **Namngivningsprincip** väljer du **Blockerade ord**.

    ![redigera och ladda upp listan med blockerade ord för namngivningsprincip](./media/groups-naming-policy/blockedwords.png)

1. Visa eller redigera den aktuella listan med anpassade blockerade ord genom att välja **Hämta**.
1. Ladda upp den nya listan med anpassade blockerade ord genom att välja filikonen.
1. Spara ändringarna för att den nya principen ska träda i kraft genom att välja **Spara**.

## <a name="install-powershell-cmdlets"></a>Installera PowerShell-cmdletar

Se till att avinstallera äldre versioner av Azure Active Directory PowerShell för Graph-modulen för Module for Windows PowerShell och installera [Azure Active Directory PowerShell för Graph – offentlig förhandsversion 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) innan du kör PowerShell-kommandon.

1. Öppna Windows PowerShell-appen som administratör.
2. Avinstallera en eventuell tidigare version av AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Installera den senaste versionen av AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Om du uppmanas att komma åt en ej betrodd databas anger du **Y**. Det kan ta några minuter innan den nya modulen installeras.

## <a name="configure-naming-policy-in-powershell"></a>Konfigurera namngivningsprincipen i PowerShell

1. Öppna ett Windows PowerShell-fönster på datorn. Du kan öppna den utan förhöjda privilegier.

1. Kör följande kommandon för att förbereda körningen av cmdletarna.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   På sidan **Logga in på ditt konto** som öppnas anger du ditt administratörskonto och lösenord för att ansluta till tjänsten, och väljer **Logga in**.

1. Följ stegen i [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](groups-settings-cmdlets.md) för att skapa gruppinställningar för den här klientorganisationen.

### <a name="view-the-current-settings"></a>Visa de aktuella inställningarna

1. Hämta den aktuella namngivningsprincipen om du vill visa de aktuella inställningarna.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Visa de aktuella gruppinställningarna.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Ange namngivningsprincipen och anpassade blockerade ord

1. Ange gruppnamnsprefix och -suffix i Azure AD PowerShell. För att funktionen ska fungera korrekt måste [GroupName] inkluderas i inställningen.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Ange de anpassade blockerade ord du vill begränsa. I följande exempel visas hur du kan lägga till dina egna anpassade ord.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Spara inställningarna för den nya principen som ska börja gälla, till exempel i följande exempel.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Klart! Du har angett namngivningsprincipen och lagt till dina blockerade ord.

## <a name="export-or-import-custom-blocked-words"></a>Exportera eller importera anpassade blockerade ord

Mer information finns i artikeln [Azure Active Directory cmdlets för att konfigurera gruppinställningar](groups-settings-cmdlets.md).

Här är ett exempel på ett PowerShell-skript för att exportera flera blockerade ord:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Här är ett exempel på PowerShell-skript för att importera flera blockerade ord:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Ta bort namngivningsprincipen

### <a name="remove-the-naming-policy-using-azure-portal"></a>Ta bort namngivningsprincipen med Azure-portalen

1. På sidan **Namngivningsprincip** väljer du **Ta bort princip**.
1. När du har bekräftat borttagningen tas namngivningsprincipen bort, inklusive alla namngivningsprincipen prefix-suffix och eventuella anpassade blockerade ord.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Ta bort namngivningsprincipen med Azure AD PowerShell

1. Ta bort gruppnamnsprefix och -suffix i Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Ta bort de anpassade spärrade orden.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Spara inställningarna.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Erfarenhet av office 365-appar

När du har angett en gruppnamngivningsprincip i Azure AD, när en användare skapar en grupp i en Office 365-app, visas:

- En förhandsgranskning av namnet enligt namnprincipen (med prefix och suffix) så snart användaren skriver i gruppnamnet
- Om användaren anger blockerade ord visas ett felmeddelande så att de kan ta bort de blockerade orden.

Arbetsbelastning | Efterlevnad
----------- | -------------------------------
Azure Active Directory-portaler | Azure AD-portalen och Access Panel-portalen visar namnet på namngivningsprincipen när användaren skriver i ett gruppnamn när du skapar eller redigerar en grupp. När en användare anger ett anpassat blockerat ord visas ett felmeddelande med det blockerade ordet så att användaren kan ta bort det.
Outlook Web Access (OWA) | Outlook Web Access visar namnet på namngivningsprincipen när användaren skriver ett gruppnamn eller gruppalias. När en användare anger ett anpassat blockerat ord visas ett felmeddelande i användargränssnittet tillsammans med det blockerade ordet så att användaren kan ta bort det.
Outlook-skrivbordet | Grupper som skapas på Outlook-skrivbordet är kompatibla med namngivningsprincipinställningarna. Outlook-skrivbordsappen visar ännu inte förhandsgranskningen av det påtvingade gruppnamnet och returnerar inte de anpassade blockerade ordfelen när användaren anger gruppnamnet. Namngivningsprincipen tillämpas dock automatiskt när du skapar eller redigerar en grupp, och användarna ser felmeddelanden om det finns anpassade blockerade ord i gruppens namn eller alias.
Microsoft Teams | Microsoft Teams visar det tvingande namnet på gruppnamnen när användaren anger ett gruppnamn. När en användare anger ett anpassat blockerat ord visas ett felmeddelande tillsammans med det blockerade ordet så att användaren kan ta bort det.
SharePoint  |  SharePoint visar namnet på namngivningsprincipen när användaren skriver ett webbplatsnamn eller en grupp-e-postadress. När en användare anger ett anpassat blockerat ord visas ett felmeddelande tillsammans med det blockerade ordet så att användaren kan ta bort det.
Microsoft Stream | Microsoft Stream visar det tvingande namnet för gruppnamngivning när användaren skriver ett gruppnamn eller gruppe-postalias. När en användare anger ett anpassat blockerat ord visas ett felmeddelande med det blockerade ordet så att användaren kan ta bort det.
Outlook iOS och Android-app | Grupper som skapas i Outlook-appar är kompatibla med den konfigurerade namngivningsprincipen. Outlook-mobilappen visar ännu inte förhandsgranskningen av namnet på namngivningsprincipen och returnerar inte anpassade blockerade ordfel när användaren anger gruppnamnet. Namngivningsprincipen tillämpas dock automatiskt när du klickar på skapa/redigera och användarna ser felmeddelanden om det finns anpassade blockerade ord i gruppens namn eller alias.
Mobilapp för grupper | Grupper som skapas i mobilappen Grupper är kompatibla med namngivningsprincipen. Grupp mobilapp visar inte förhandsgranskningen av namngivningsprincipen och returnerar inte anpassade blockerade ordfel när användaren anger gruppnamnet. Men namngivningsprincipen tillämpas automatiskt när du skapar eller redigerar en grupp och användarna visas med lämpliga fel om det finns anpassade blockerade ord i gruppens namn eller alias.
Planner | Planner är kompatibel med namngivningsprincipen. Planner visar förhandsgranskningen av namngivningsprincipen när du anger planens namn. När en användare anger ett anpassat blockerat ord visas ett felmeddelande när planen skapas.
Dynamics 365 för Customer Engagement | Dynamics 365 for Customer Engagement är kompatibel med namngivningsprincipen. Dynamics 365 visar namnet på namngivningsprincipen när användaren skriver ett gruppnamn eller gruppe-postalias. När användaren anger ett anpassat blockerat ord visas ett felmeddelande med det blockerade ordet så att användaren kan ta bort det.
Synkronisering av skoldata (SDS) | Grupper som skapas via SDS följer namngivningsprincipen, men namngivningsprincipen tillämpas inte automatiskt. SDS-administratörer måste lägga till prefix och suffix till klassnamn för vilka grupper måste skapas och sedan överföras till SDS. Gruppskapa eller redigera skulle misslyckas annars.
Outlook Kundhanteraren (OCM) | Outlook Customer Manager är kompatibelt med namngivningsprincipen, som tillämpas automatiskt på den grupp som skapas i Outlook Customer Manager. Om ett anpassat blockerat ord identifieras blockeras gruppskapandet i OCM och användaren blockeras från att använda OCM-appen.
Appen Klassrum | Grupper som skapas i Classroom-appen följer namngivningsprincipen, men namngivningsprincipen tillämpas inte automatiskt och förhandsgranskningen av namngivningsprincipen visas inte för användarna när de anger ett klassrumsgruppnamn. Användare måste ange det påtvingade klassrummet gruppnamnet med prefix och suffix. Om inte, misslyckas klassrumsgruppens skapa eller redigera åtgärd med fel.
Power BI | Power BI-arbetsytor är kompatibla med namngivningsprincipen.    
Yammer | När en användare som är inloggad på Yammer med sitt Azure Active Directory-konto skapar en grupp eller redigerar ett gruppnamn, följer gruppnamnet namngivningsprincipen. Detta gäller både Office 365-anslutna grupper och alla andra Yammer-grupper.<br>Om en Ansluten Office 365-grupp skapades innan namngivningsprincipen finns på plats följer gruppnamnet inte automatiskt namngivningsprinciperna. När en användare redigerar gruppnamnet uppmanas de att lägga till prefixet och suffixet.
StaffHub  | StaffHub-team följer inte namngivningsprincipen, men den underliggande Office 365-gruppen gör det. StaffHub-teamets namn använder inte prefix och suffix och söker inte efter anpassade blockerade ord. Men StaffHub tillämpar prefix och suffix och tar bort blockerade ord från den underliggande Office 365-gruppen.
Växla PowerShell | Exchange PowerShell-cmdletar är kompatibla med namngivningsprincipen. Användarna får lämpliga felmeddelanden med föreslagna prefix och suffix och för anpassade blockerade ord om de inte följer namngivningsprincipen i gruppnamnet och gruppaliaset (mailNickname).
Azure Active Directory PowerShell-cmdlets | Azure Active Directory PowerShell-cmdletar är kompatibla med namngivningsprincipen. Användarna får lämpliga felmeddelanden med föreslagna prefix och suffix och för anpassade blockerade ord om de inte följer namngivningskonventionen i gruppnamn och gruppalias.
Administrationscenter för Exchange | Administrationscentret för Exchange är kompatibelt med namngivningsprincipen. Användarna får lämpliga felmeddelanden med föreslagna prefix och suffix och för anpassade blockerade ord om de inte följer namngivningskonventionen i gruppnamnet och gruppaliaset.
Administrationscenter för Microsoft 365 | Microsoft 365-administrationscentret är kompatibelt med namngivningsprincipen. När en användare skapar eller redigerar gruppnamn tillämpas namngivningsprincipen automatiskt och användarna får lämpliga fel när de anger anpassade blockerade ord. Administrationscentret för Microsoft 365 visar ännu inte en förhandsgranskning av namngivningsprincipen och returnerar inte anpassade blockerade ordfel när användaren anger gruppnamnet.

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om Azure AD-grupper.

- [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Förfalloprincip för Office 365-grupper](groups-lifecycle.md)
- [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera medlemmar i en grupp](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
