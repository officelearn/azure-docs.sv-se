---
title: 'Azure AD Connect: Designbegreppen | Microsoft Docs'
description: Det här avsnittet beskrivs vissa implementering design områden
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 0a648d0733d9d81cc0e586f5fa54dc8d75d2f6f0
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801940"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Designbegrepp
Syftet med det här dokumentet är att beskriva områden som måste betraktas under genomförandet designen av Azure AD Connect. Det här dokumentet är en djupdykning i vissa områden och dessa koncept beskrivs kortfattat i andra dokument.

## <a name="sourceanchor"></a>sourceAnchor
Attributet sourceAnchor definieras som *ett attribut som inte ändras under livslängden för ett objekt*. Den identifierar ett objekt som den samma objekt lokalt och i Azure AD. Attributet kallas även **immutableId** och de två namnen används utbytbara.

Ordet ändras, som är ”kan inte ändras”, är viktigt att det här dokumentet. Eftersom värdet för det här attributet inte kan ändras när den väl har angetts, är det viktigt att välja en design som har stöd för ditt scenario.

Attributet används för följande scenarier:

* När en ny synkroniseringsserver för motorn inbyggda eller återskapas efter en katastrofåterställning, länkar det här attributet befintliga objekt i Azure AD med objekt på lokalt.
* Om du flyttar från en molnbaserad identitet till en modell för synkroniserade identitet, kan det här attributet objekt ”hårda matchar” befintliga objekt i Azure AD med lokalt objekt.
* Om du använder federation och sedan det här attributet tillsammans med den **userPrincipalName** används för att unikt identifiera en användare i anspråket.

Det här avsnittet handlar bara om sourceAnchor relateras till användare. Samma regler gäller för alla typer av objekt, men endast för användare är det här problemet är vanligtvis ett problem.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Att välja en bra sourceAnchor attribut
Attributvärdet som måste följa följande regler:

* Färre än 60 tecken
  * Tecken som inte är a-z, A-Z eller 0-9 kodas och räknas som 3 tecken
* Inte innehåller ett specialtecken: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* Måste vara globalt unika
* Måste vara en sträng, heltal eller binär
* Bör inte väljas utifrån användarens namn eftersom dessa kan ändras
* Bör inte skiftlägeskänsliga och undvika värden som kan variera efter fall
* Ska tilldelas när objektet har skapats

Om den valda sourceAnchor inte är av typen string, Azure AD Connect Base64Encode attributvärdet så inga specialtecken visas. Om du använder en annan federationsserver än AD FS kan du kontrollera om servern kan också Base64Encode attributet.

Attributet sourceAnchor är skiftlägeskänsliga. Värdet ”AndersSvensson” är inte samma som ”AndersSvensson”. Men du bör inte ha två olika objekt med endast en avvikelse i fall.

Om du har en enda skog är lokalt, sedan attributet bör du använda **objectGUID**. Detta är också det attribut som används när du använder standardinställningar i Azure AD Connect och det attribut som används av DirSync.

Om du har flera skogar och flytta inte användare mellan skogar och domäner, sedan **objectGUID** är en bra attribut som ska användas även i det här fallet.

Om du flyttar användare mellan skogar och domäner, måste du hitta ett attribut som inte ändras eller kan flyttas med användarna under flytten. En rekommenderad metod är att införa en syntetisk attribut. Ett attribut som kan innehålla något som ser ut som ett GUID skulle vara lämplig. När objekt skapas en ny GUID skapas och stämplad på användaren. En anpassad synkroniseringsregel kan skapas i synkroniseringsserver för motorn för att skapa det här värdet baserat på de **objectGUID** och uppdatera det markerade attributet i lägger till. När du flyttar objektet, kontrollera att också kopiera innehållet i det här värdet.

En annan lösning är att välja ett befintligt attribut som du vet inte ändras. Vanliga attribut inkluderar **employeeID**. Om du anser att ett attribut som innehåller bokstäverna, kontrollera att det finns ingen risk fallet (versal eller gemen) kan ändra för attributets värde. Felaktigt attribut som inte ska användas med dessa attribut med namnet på användaren. I en annullering eller äktenskapsskillnad förväntas namnet ändra, vilket inte är tillåtet för det här attributet. Detta är också ett skäl till varför attribut som **userPrincipalName**, **e**, och **targetAddress** inte är även möjligt att välja i Azure AD Connect-installationen guiden. Dessa attribut även innehålla de ”@” tecken som inte tillåts i sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Ändra attributet sourceAnchor
Värdet för attributet sourceAnchor kan inte ändras efter att objektet har skapats i Azure AD och identitet synkroniseras.

Därför gäller följande begränsningar för Azure AD Connect:

* Attributet sourceAnchor kan endast anges under installationen. Om du kör installationsguiden av är det här alternativet skrivskyddad. Om du behöver ändra den här inställningen måste du avinstallera och installera.
* Om du har installerat en annan Azure AD Connect-server måste du välja samma sourceAnchor attribut som tidigare använt. Om du tidigare har använt DirSync och flyttar till Azure AD Connect och sedan måste du använda **objectGUID** eftersom det är det attribut som används av DirSync.
* Om värdet för sourceAnchor har ändrats efter har objektet exporterats till Azure AD och sedan Azure AD Connect-synkronisering genererar ett fel och tillåter inte några fler ändringar på objektet innan problemet har åtgärdats och sourceAnchor ändras tillbaka i källan director y.

## <a name="using-msds-consistencyguid-as-sourceanchor"></a>Med msDS-ConsistencyGuid som sourceAnchor
Som standard, Azure AD Connect (version 1.1.486.0 och äldre) använder objectGUID som attributet sourceAnchor. ObjectGUID är systemgenererad. Du kan inte ange värdet när du skapar lokala AD-objekt. Enligt beskrivningen i avsnittet [sourceAnchor](#sourceanchor), det finns scenarier där du måste ange sourceAnchor-värde. Om scenarier som är tillämpliga för dig, måste du använda en konfigurerbar AD-attributet (till exempel msDS-ConsistencyGuid) som attributet sourceAnchor.

Azure AD Connect (version 1.1.524.0 och efter) nu underlättar användningen av msDS-ConsistencyGuid som attributet sourceAnchor. När du använder den här funktionen konfigurerar Synkroniseringsregler till automatiskt i Azure AD Connect:

1. Använda msDS-ConsistencyGuid som attributet sourceAnchor för användarobjekt. ObjectGUID används för andra objekttyper.

2. För alla angivna lokala AD-användare vars attributet msDS-ConsistencyGuid inte är ifylld, Azure AD Connect-skrivningar objectGUID värdet tillbaka till attributet msDS-ConsistencyGuid i lokala Active Directory-objekt. När attributet msDS-ConsistencyGuid fylls exporterar Azure AD Connect sedan objektet till Azure AD.

>[!NOTE]
> När en lokal AD-objekt har importerats till Azure AD Connect (som, importeras till anslutningsplatsen AD och planerad i metaversum), du kan inte ändra dess sourceAnchor värde längre. Ange sourceAnchor-värdet för ett givet lokala AD objekt, konfigurera ett attributet msDS-ConsistencyGuid innan den har importerats till Azure AD Connect.

### <a name="permission-required"></a>Behörigheten som krävs
AD DS-konto som används för att synkronisera med lokala Active Directory måste beviljas behörighet att skriva till attributet msDS-ConsistencyGuid i lokala Active Directory för för den här funktionen ska fungera.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Så här aktiverar du funktionen ConsistencyGuid - ny installation
Du kan aktivera användningen av ConsistencyGuid som sourceAnchor vid nyinstallation. Det här avsnittet beskriver både snabb och anpassad installation i informationen.

  >[!NOTE]
  > Endast nyare versioner av Azure AD Connect (1.1.524.0 och efter) stöder användning av ConsistencyGuid som sourceAnchor under ny installation.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Så här aktiverar du funktionen ConsistencyGuid
Funktionen kan för närvarande kan endast aktiveras under endast nya Azure AD Connect-installationen.

#### <a name="express-installation"></a>Expressfunktion för Installation
När du installerar Azure AD Connect med Express-läge, bestämmer det lämpligaste AD-attributet som ska användas som attributet sourceAnchor med hjälp av följande logik automatiskt i Azure AD Connect-guiden:

* Azure AD Connect-guiden frågar först Azure AD-klienten för att hämta AD-attribut som används som attributet sourceAnchor i den tidigare Azure AD Connect-installationen (om sådan finns). Om denna information är tillgänglig använder Azure AD Connect samma AD-attribut.

  >[!NOTE]
  > Endast nyare versioner av Azure AD Connect (1.1.524.0 och efter) lagrar information i Azure AD-klienten om attributet sourceAnchor användas under installationen. Äldre versioner av Azure AD Connect inte.

* Om information om attributet sourceAnchor används inte är tillgänglig i guiden kontrollerar tillståndet för attributet msDS-ConsistencyGuid i din lokala Active Directory. Om attributet inte är konfigurerad på alla objekt i katalogen, använder guiden msDS-ConsistencyGuid som attributet sourceAnchor. Om attributet har konfigurerats på ett eller flera objekt i katalogen, avslutar guiden attributet används av andra program och är inte lämpligt som attributet sourceAnchor...

* I så fall guiden använder till med objectGUID som attributet sourceAnchor.

* När attributet sourceAnchor är valt lagras i guiden i Azure AD-klienten. Informationen kommer att användas av framtida installation av Azure AD Connect.

När Express-installationen är klar, informerar guiden vilket attribut har plockats som Källfästpunkten-attribut.

![Guiden informerar AD attributet sourceAnchor har valts](./media/active-directory-aadconnect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Anpassad installation
När du installerar Azure AD Connect med anpassade läge, innehåller Azure AD Connect-guiden två alternativ när du konfigurerar attributet sourceAnchor:

![Anpassad installation – sourceAnchor konfiguration](./media/active-directory-aadconnect-design-concepts/consistencyGuid-02.png)

| Inställning | Beskrivning |
| --- | --- |
| Låt Azure hantera källfästpunkten | Välj det här alternativet om du vill att Azure AD ska hämta attributet. Om du väljer det här alternativet, Azure AD Connect-guiden gäller samma [sourceAnchor attributet markeringen logik som används under snabbinstallationen](#express-installation). Liknar snabbinstallationen, guiden informerar vilket attribut har plockats som Källfästpunkten attribut när anpassade installationen är klar. |
| Ett specifikt attribut | Välj det här alternativet om du vill ange ett befintligt AD-attribut som sourceAnchor-attribut. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Så här aktiverar du funktionen ConsistencyGuid - befintlig distribution
Om du har en befintlig Azure AD Connect-distribution som använder objectGUID som attributet Källfästpunkten växla du den att använda ConsistencyGuid i stället.

>[!NOTE]
> Endast nyare versioner av Azure AD Connect (1.1.552.0 och efter) har stöd för växlar från ObjectGuid till ConsistencyGuid som Källfästpunkten-attribut.

Växla från objectGUID till ConsistencyGuid som Källfästpunkten-attribut:

1. Starta Azure AD Connect-guiden och klicka på **konfigurera** att gå till skärmen uppgifter.

2. Välj den **konfigurera Källfästpunkten** uppgift alternativ och klickar på **nästa**.

   ![Aktivera ConsistencyGuid för befintliga distribution – steg 2](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment01.png)

3. Ange dina autentiseringsuppgifter för Azure AD-administratör och klicka på **nästa**.

4. Azure AD Connect-guiden analyserar tillståndet för attributet msDS-ConsistencyGuid i din lokala Active Directory. Om attributet inte är konfigurerad på alla objekt i katalogen, slutsatsen att inget annat program använder attributet och är säkert att använda det som attributet Källfästpunkt i Azure AD Connect. Klicka på **nästa** att fortsätta.

   ![Aktivera ConsistencyGuid för befintliga distribution – steg 4](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment02.png)

5. I den **redo att konfigurera** klickar du på **konfigurera** att göra konfigurationsändringen.

   ![Aktivera ConsistencyGuid för befintliga distribution – steg 5](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment03.png)

6. När konfigurationen är klar visar guiden att msDS-ConsistencyGuid används som Källfästpunkten-attribut.

   ![Aktivera ConsistencyGuid för befintliga distribution – steg 6](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment04.png)

Under analysen (steg 4) om attributet har konfigurerats på ett eller flera objekt i katalogen, slutsatsen guiden attributet som används av ett annat program och returnerar ett fel som visas i diagrammet nedan. Det här felet kan också inträffa om du tidigare har aktiverat funktionen ConsistencyGuid primära Azure AD Connect-servern och du försöker göra på testservern.

![Aktivera ConsistencyGuid för befintliga distribution – fel](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Om du är säker på att attributet inte är används av andra befintliga program kan du ignorera felet genom att starta om Azure AD Connect-guiden med den **/SkipLdapSearchcontact** angivna. Det gör du genom att köra följande kommando i Kommandotolken:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Påverkan på AD FS eller tredje parts federation-konfiguration
Om du använder Azure AD Connect att hantera lokala AD FS-distribution, Azure AD Connect uppdateras automatiskt anspråksregler för att använda samma AD-attribut som sourceAnchor. Detta säkerställer att ImmutableID anspråket som genererats av AD FS är konsekventa med sourceAnchor värdena exporteras till Azure AD.

Om du hanterar AD FS utanför Azure AD Connect eller om du använder federationsservrar från tredje part för autentisering, måste du manuellt uppdatera anspråksregler för ImmutableID anspråk till att det stämmer överens med sourceAnchor värden exporteras till Azure AD som beskrivs i artikel avsnittet [ändra AD FS anspråksregler](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Guiden returnerar följande varning när installationen är klar:

![Tredjeparts-federation-konfiguration](./media/active-directory-aadconnect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Lägga till nya kataloger till befintlig distribution
Anta att du har distribuerat Azure AD Connect med funktionen ConsistencyGuid aktiverad och nu vill du lägga till en annan katalog i distributionen. När du försöker lägga till katalogen kontrollerar Azure AD Connect-guiden status för attributet mSDS-ConsistencyGuid i katalogen. Om attributet har konfigurerats på ett eller flera objekt i katalogen, avslutar guiden attributet används av andra program och returnerar ett fel som visas i diagrammet nedan. Om du är säker på att attributet inte är används av befintliga program, måste du kontakta Support för information om hur du kan ignorera felet.

![Lägga till nya kataloger till befintlig distribution](./media/active-directory-aadconnect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD-inloggning
När du integrerar din lokala katalog med Azure AD, är det viktigt att förstå hur synkroniseringsinställningarna kan påverka hur användaren autentiseras. Azure AD använder userPrincipalName (UPN) för att autentisera användaren. När du synkroniserar dina användare, måste du välja attribut som ska användas för userPrincipalName värdet noggrant.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Att välja attribut för userPrincipalName
När du väljer attribut för att ange bör värdet för UPN som ska användas i Azure som Kontrollera

* Attributvärden motsvarar UPN-syntaxen (RFC 822), som det ska vara i formatet username@domain
* Suffixet i värdena matchar en verifierad anpassade domäner i Azure AD

I standardinställningar är det antagna valet för attributet userPrincipalName. Om attributet userPrincipalName inte innehåller värdet du vill att användarna att logga in på Azure och du måste välja **Anpassad Installation**.

### <a name="custom-domain-state-and-upn"></a>Tillstånd för anpassad domän- och UPN
Det är viktigt att se till att det finns en verifierad domän för UPN-suffixet.

John är en användare i contoso.com. Du vill Johan använda lokala UPN john@contoso.com logga in till Azure när du har synkroniserat användare till din Azure AD directory contoso.onmicrosoft.com. Om du vill göra det, måste du lägga till och verifierar contoso.com som en anpassad domän i Azure AD innan du börjar synkronisera användare. Om UPN-suffixet John, till exempel contoso.com inte matchar en verifierad domän i Azure AD, ersätter Azure AD UPN-suffixet med contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Icke-dirigerbara lokala domäner och UPN för Azure AD
Vissa organisationer har icke-dirigerbara domäner, t.ex. contoso.local eller enkel etikett domäner som contoso. Det går inte att verifiera en icke-dirigerbara domän i Azure AD. Azure AD Connect kan synkronisera med endast en verifierad domän i Azure AD. När du skapar en Azure AD-katalog skapar en dirigerbara domän som blir standarddomän för din Azure AD, till exempel contoso.onmicrosoft.com. Därför blir det nödvändigt att kontrollera andra dirigerbara domänen i ett sådant scenario om du inte vill synkronisera till för standarddomänen onmicrosoft.com.

Läs [lägga till ett eget domännamn i Azure Active Directory](../active-directory-domains-add-azure-portal.md) för mer information om att lägga till och verifiera domäner.

Azure AD Connect identifierar om du kör i en icke-dirigerbara domänmiljö och korrekt skulle varnar dig från att gå vidare med standardinställningar. Om du arbetar i en icke-dirigerbara domän, är det troligt att UPN för användarna har icke-dirigerbara suffix för. Till exempel föreslår, om du kör under contoso.local, Azure AD Connect du om du vill använda anpassade inställningar i stället för med standardinställningar. Med anpassade inställningar kan du ange attributet som ska användas som UPN för att logga in på Azure när användarna har synkroniserats till Azure AD.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
