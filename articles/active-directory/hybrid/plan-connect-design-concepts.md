---
title: 'Azure AD Connect: Designbegrepp | Microsoft Docs'
description: Det här avsnittet beskrivs vissa områden för design av implementering
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: be145e89becc7c03ba705c29436c3572365ee4ff
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489044"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Designbegrepp
Syftet med det här dokumentet är att beskriva områden som måste betraktas under genomförandet utformningen av Azure AD Connect. Det här dokumentet är en djupdykning i vissa områden och dessa koncept beskrivs kortfattat i andra dokument.

## <a name="sourceanchor"></a>sourceAnchor
SourceAnchor-attribut har definierats som *ett attribut som inte kan ändras under livslängden för ett objekt*. Den identifierar ett objekt som de samma objekt lokalt och i Azure AD. Attributet är en förkortning **immutableId** och de två namn används utbytbara.

Ordet inte kan ändras, som är ”kan inte ändras”, är viktigt att det här dokumentet. Eftersom värdet för det här attributet inte kan ändras när den har ställts in, är det viktigt att välja en design som har stöd för ditt scenario.

Attributet används för följande scenarier:

* När en ny synkroniseringsserver-motorn bygger eller återskapas efter en katastrofåterställning, länkar det här attributet befintliga objekt i Azure AD med objekt på lokalt.
* Om du flyttar från en molnbaserad identitet till en synkroniserade identitetsmodellen kan det här attributet objekt till ”hård träff” befintliga objekt i Azure AD med lokala objekt.
* Om du använder federation och sedan det här attributet tillsammans med den **userPrincipalName** används i anspråket för att unikt identifiera en användare.

Det här avsnittet handlar bara om sourceAnchor relateras till användare. Samma regler gäller för alla typer av objekt, men det är endast för användare med det här problemet är vanligtvis ett problem.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Att välja en bra sourceAnchor-attribut
Attributvärdet måste följa följande regler:

* Färre än 60 tecken
  * Tecken som inte är a – z, A-Z eller 0-9 kodas och räknas som 3 tecken
* Inte innehålla specialtecken: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " \@ _
* Måste vara globalt unikt
* Måste vara en sträng, heltal eller binär
* Får inte baseras på användarens namn eftersom dessa kan ändras
* Bör inte vara skiftlägeskänslig och undvika värden som kan variera beroende på om
* Ska tilldelas när objektet har skapats

Om den valda sourceAnchor inte är av typen sträng, Azure AD Connect Base64Encode attributvärdet för att se till att några specialtecken visas. Om du använder en annan federationsserver än AD FS kan du kontrollera om din server kan också Base64Encode attributet.

Attributet sourceAnchor är skiftlägeskänsligt. Värdet ”JohnDoe” är inte samma som ”johndoe”. Men du bör inte ha två olika objekt med endast en skillnad i fallet.

Om du har en enda skog är på plats, sedan attributet som du bör använda **objectGUID**. Detta är också det attribut som används när du använder standardinställningarna i Azure AD Connect och det attribut som används av DirSync.

Om du har flera skogar och flytta inte användare mellan skogar och domäner, sedan **objectGUID** är ett lämpligt attribut som du använder även i det här fallet.

Om du flyttar användare mellan skogar och domäner, måste du hitta ett attribut som inte ändras eller kan flyttas med användare under flytten. En rekommenderad metod är att införa en syntetisk attribut. Ett attribut som kan innehålla något som ser ut som ett GUID skulle vara lämplig. När ett objekt skapas en ny GUID skapas och stämplats på användaren. En anpassad synkroniseringsregel kan skapas i motorn synkroniseringsservern för att skapa det här värdet baserat på den **objectGUID** och uppdatera det markerade attributet i ADDS. När du flyttar objektet måste du också kopiera innehållet i det här värdet.

En annan lösning är att välja ett befintligt attribut som du vet inte ändras. Vanliga attribut inkluderar **employeeID**. Om du överväger att ett attribut som innehåller bokstäver och kontrollera att det finns ingen risk fallet (versal eller gemen) kan ändra för attributets värde. Felaktigt attribut som inte ska användas inkludera dessa attribut med namnet på användaren. I en annullering eller äktenskapsskillnad förväntas namnet ändras, vilket inte är tillåtet för det här attributet. Det är också en orsak varför attribut som **userPrincipalName**, **e**, och **targetAddress** inte är även möjligt att välja i Azure AD Connect-installationen guiden. Dessa attribut även innehålla de ”\@” tecken som inte är tillåten i sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Ändra sourceAnchor-attribut
Värdet för attributet sourceAnchor kan inte ändras efter att objektet har skapats i Azure AD och identitet synkroniseras.

Därför gäller följande begränsningar för Azure AD Connect:

* SourceAnchor-attribut kan bara anges under installationen. Om du kör installationsguiden av är det här alternativet skrivskyddad. Om du vill ändra den här inställningen måste du avinstallera och installera om.
* Om du installerar en annan Azure AD Connect-servern, måste du välja samma sourceAnchor-attribut som tidigare använt. Om du tidigare har använt DirSync och flytta till Azure AD Connect, så du måste använda **objectGUID** eftersom det är det attribut som används av DirSync.
* Om värdet för sourceAnchor har ändrats efter har objektet exporterats till Azure AD och sedan Azure AD Connect sync genererar ett fel och tillåter inte några ytterligare ändringar på objektet innan problemet har åtgärdats och sourceAnchor ändras tillbaka i källan director y.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Med hjälp av ms-DS-ConsistencyGuid som sourceAnchor
Som standard, Azure AD Connect (version 1.1.486.0 och äldre) använder objectGUID som sourceAnchor-attribut. Systemgenererade är ObjectGUID. Du kan inte ange värdet när du skapar en lokal AD-objekt. Enligt beskrivningen i avsnittet [sourceAnchor](#sourceanchor), det finns scenarier där du måste ange sourceAnchor-värde. Om scenarierna som är tillämpliga för dig, måste du använda ett konfigurerbart AD-attribut (till exempel ms-DS-ConsistencyGuid) som sourceAnchor-attribut.

Azure AD Connect (version 1.1.524.0 och efter) nu underlättar användningen av ms-DS-ConsistencyGuid som sourceAnchor-attribut. När du använder den här funktionen konfigurerar Synkroniseringsregler till automatiskt i Azure AD Connect:

1. Använd ms-DS-ConsistencyGuid som sourceAnchor-attribut för användarobjekt. ObjectGUID används för andra objekttyper.

2. För alla angivna lokala AD-användare vars ms-DS-ConsistencyGuid-attributet inte är ifylld, Azure AD Connect-skrivningar objectGUID värdet tillbaka till attributet ms-DS-ConsistencyGuid i lokala Active Directory-objekt. När attributet ms-DS-ConsistencyGuid är ifylld, exporterar Azure AD Connect sedan objektet till Azure AD.

>[!NOTE]
> När en lokal AD-objekt har importerats till Azure AD Connect (som är kan importeras till AD-Anslutarplatsen och projiceras in i metaversum), du kan inte ändra dess sourceAnchor värde längre. Ange sourceAnchor-värdet för ett givet lokala AD objekt, konfigurera dess ms-DS-ConsistencyGuid attributet innan den har importerats till Azure AD Connect.

### <a name="permission-required"></a>Behörighet krävs
AD DS-kontot som används för att synkronisera med lokala Active Directory måste beviljas behörighet att skriva till attributet ms-DS-ConsistencyGuid i den lokala Active Directory för för den här funktionen ska fungera.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Så här aktiverar du funktionen ConsistencyGuid - nyinstallation
Du kan aktivera användningen av ConsistencyGuid som sourceAnchor under installationen av nya. Det här avsnittet beskriver både Express och anpassad installation i information.

  >[!NOTE]
  > Endast nyare versioner av Azure AD Connect (1.1.524.0 och efter) stöder användningen av ConsistencyGuid som sourceAnchor under installationen av nya.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Så här aktiverar du funktionen ConsistencyGuid
Funktionen kan för närvarande kan bara aktiveras under endast nya Azure AD Connect-installationen.

#### <a name="express-installation"></a>Snabbinstallation
När du installerar Azure AD Connect med Express-läge, avgör Azure AD Connect-guiden automatiskt det lämpligaste AD-attributet som ska användas som sourceAnchor-attribut med hjälp av följande logik:

* Azure AD Connect-guiden frågar först din Azure AD-klient för att hämta det AD-attribut som används som sourceAnchor-attribut i den tidigare Azure AD Connect-installationen (om sådan finns). Om den här informationen är tillgänglig använder samma AD-attribut i Azure AD Connect.

  >[!NOTE]
  > Endast nyare versioner av Azure AD Connect (1.1.524.0 och efter) spara information i Azure AD-klienten om sourceAnchor-attribut som används under installationen. Äldre versioner av Azure AD Connect inte.

* Om information om sourceAnchor-attribut används inte är tillgänglig kontrollerar i guiden status för attributet ms-DS-ConsistencyGuid i din lokala Active Directory. Om attributet inte är konfigurerat på valfritt objekt i katalogen, används ms-DS-ConsistencyGuid som sourceAnchor-attribut. Om attributet har konfigurerats på ett eller flera objekt i katalogen, avslutar guiden attributet som används av andra applikationer och är inte lämplig som sourceAnchor-attribut...

* I så fall guiden använder till med hjälp av objectGUID som sourceAnchor-attribut.

* När attributet sourceAnchor är valt lagras i guiden i Azure AD-klienten. Informationen som ska användas av framtida installation av Azure AD Connect.

När Express-installationen har slutförts, informerar guiden dig vilket attribut har valts som källfästpunktsattribut.

![Guiden informerar AD-attribut som sourceAnchor har valts](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Anpassad installation
När du installerar Azure AD Connect med anpassade läge, tillhandahåller Azure AD Connect-guiden två alternativ när du konfigurerar sourceAnchor-attribut:

![Anpassad installation – sourceAnchor-konfiguration](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Inställning | Beskrivning |
| --- | --- |
| Låt Azure hantera källfästpunkten | Välj det här alternativet om du vill att Azure AD ska hämta attributet. Om du väljer det här alternativet kan Azure AD Connect-guiden gäller samma [sourceAnchor-attribut val av logik som används under snabbinstallationen](#express-installation). Liknar snabbinstallationen, guiden informerar dig vilket attribut har valts som källfästpunktsattribut när anpassade installationen har slutförts. |
| Ett specifikt attribut | Välj det här alternativet om du vill ange ett befintligt AD-attribut som sourceAnchor-attribut. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Så här aktiverar du funktionen ConsistencyGuid - befintlig distribution
Om du har en befintlig Azure AD Connect-distribution som använder objectGUID som källfästpunktsattribut kan växla du den till ConsistencyGuid i stället.

>[!NOTE]
> Endast nyare versioner av Azure AD Connect (1.1.552.0 och efter) stöder byter från ObjectGuid till ConsistencyGuid som källfästpunktsattribut.

Växla från objectGUID till ConsistencyGuid som källfästpunktsattribut:

1. Starta Azure AD Connect-guiden och klicka på **konfigurera** att gå till skärmen uppgifter.

2. Välj den **konfigurera Källankare** uppgift alternativ och klickar på **nästa**.

   ![Aktivera ConsistencyGuid för befintliga distribution – steg 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Ange dina autentiseringsuppgifter för Azure AD-administratör och klicka på **nästa**.

4. Azure AD Connect-guiden analyserar tillståndet för attributet ms-DS-ConsistencyGuid i din lokala Active Directory. Om attributet inte är konfigurerat på valfritt objekt i katalogen, avslutar Azure AD Connect att inget annat program för närvarande använder attributet och är säkert att använda den som källfästpunktsattribut. Klicka på **nästa** att fortsätta.

   ![Aktivera ConsistencyGuid för befintliga distribution – steg 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. I den **redo att konfigurera** klickar du på **konfigurera** att göra konfigurationsändringen.

   ![Aktivera ConsistencyGuid för befintliga distribution – steg 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. När konfigurationen är klar visar guiden som ms-DS-ConsistencyGuid används nu som källfästpunktsattribut.

   ![Aktivera ConsistencyGuid för befintliga distribution – steg 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

För analys (steg 4), om attributet har konfigurerats på ett eller flera objekt i katalogen, avslutar guiden attributet som används av ett annat program och returnerar ett fel som visas i diagrammet nedan. Det här felet kan också inträffa om du redan har aktiverat funktionen ConsistencyGuid primära Azure AD Connect-servern och du försöker göra samma sak på testservern.

![Aktivera ConsistencyGuid för befintliga distribution – fel](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Om du är säker på att attributet inte är används av andra befintliga program kan du ignorera felet genom att starta om Azure AD Connect-guiden med den **/SkipLdapSearchcontact** angivna. Om du vill göra det kör du följande kommando i Kommandotolken:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Påverkan på AD FS eller tredje parts federation-konfiguration
Om du använder Azure AD Connect att hantera en lokal AD FS-distribution, Azure AD Connect uppdaterar anspråksreglerna för att använda samma AD-attribut som sourceAnchor automatiskt. Detta säkerställer att ImmutableID-anspråket som genererats av AD FS är konsekventa med sourceAnchor-värden som exporteras till Azure AD.

Om du hanterar AD FS utanför Azure AD Connect eller om du använder federationsservrar från tredje part för autentisering, måste du manuellt uppdatera anspråksreglerna för ImmutableID-anspråket är konsekvent med sourceAnchor-värden som exporteras till Azure AD, enligt beskrivningen i artikel avsnittet [ändra AD FS anspråksregler](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Guiden returnerar följande varning när installationen är klar:

![Tredjeparts-federation-konfiguration](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Lägga till nya kataloger till befintlig distribution
Anta att du har distribuerat Azure AD Connect med funktionen ConsistencyGuid aktiverad och nu du vill lägga till en annan katalog i distributionen. När du försöker lägga till katalogen kontrollerar Azure AD Connect-guiden status för attributet ms-DS-ConsistencyGuid i katalogen. Om attributet har konfigurerats på ett eller flera objekt i katalogen, avslutar guiden attributet som används av andra applikationer och returnerar ett fel som visas i diagrammet nedan. Om du är säker på att attributet inte är används av befintliga program som du behöver kontakta supporten för information om hur du ignorera felet.

![Lägga till nya kataloger till befintlig distribution](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD-inloggning
När du integrerar din lokala katalog med Azure AD, är det viktigt att förstå hur synkroniseringsinställningarna kan påverka hur användaren autentiseras. Azure AD använder userPrincipalName (UPN) för att autentisera användaren. När du synkroniserar dina användare, måste du välja attribut som ska användas för värdet för userPrincipalName noggrant.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Välja attributet för userPrincipalName
När du väljer attributet för att ange bör värdet för UPN som ska användas i Azure som Kontrollera

* Attributet värden motsvarar UPN-syntax (RFC 822), som är det ska ha formatet användarnamn\@domän
* Suffixet i värdena matchar till någon av de verifierade anpassade domänerna i Azure AD

I standardinställningarna är det antagna valet för attributet userPrincipalName. Om attributet userPrincipalName inte innehåller värdet du vill att användarna ska logga in på Azure och sedan måste du välja **Anpassad Installation**.

### <a name="custom-domain-state-and-upn"></a>Tillstånd för anpassad domän- och UPN
Det är viktigt att kontrollera att det finns en verifierad domän för UPN-suffixet.

John är en användare i contoso.com. Du vill att John att använda lokala UPN john\@contoso.com att logga in på Azure när du har synkroniserats användare till din Azure AD directory contoso.onmicrosoft.com. För att göra det, måste du lägga till och verifiera contoso.com som en anpassad domän i Azure AD innan du kan börja synkronisera användare. Om UPN-suffix John, till exempel contoso.com, inte matchar en verifierad domän i Azure AD, ersätter Azure AD UPN-suffixet med contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Icke-dirigerbara lokala domäner och UPN för Azure AD
Vissa organisationer har icke-dirigerbara domäner, t.ex. contoso.local eller enkla enkla domännamn utan toppdomän, exempelvis contoso. Det går inte att verifiera en icke-dirigerbara domän i Azure AD. Azure AD Connect kan synkronisera med endast en verifierad domän i Azure AD. När du skapar en Azure AD-katalog skapar en dirigerbar domän som blir standarddomän för din Azure AD-exempel: contoso.onmicrosoft.com. Därför blir den nödvändiga för att kontrollera andra dirigerbara domäner i ett sådant scenario om du inte vill synkronisera till standarddomänen onmicrosoft.com.

Läs [lägga till ett anpassat domännamn i Azure Active Directory](../active-directory-domains-add-azure-portal.md) för mer information om att lägga till och verifiera domäner.

Azure AD Connect identifierar om du kör i en miljö med icke-dirigerbara domäner och på rätt sätt skulle varnar dig från att gå vidare med standardinställningar. Om du arbetar i en icke-dirigerbara domän, är det troligt att UPN-namnet, användare, har icke-dirigerbara suffix för. Till exempel, om du kör under contoso.local, föreslår Azure AD Connect du om du vill använda anpassade inställningar i stället för med standardinställningar. Via anpassade inställningar kan du ange attributet som ska användas som UPN för att logga in på Azure, när användarna har synkroniserats till Azure AD.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
