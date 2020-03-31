---
title: ClaimsSchema - Azure Active Directory B2C | Microsoft-dokument
description: Ange elementet ClaimsSchema i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4c3b3318e941723ec333597c7e4b3e48710152d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78397786"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Elementet **ClaimsSchema** definierar de anspråkstyper som kan refereras som en del av principen. Anspråksschema är den plats där du deklarerar dina anspråk. Ett anspråk kan vara förnamn, efternamn, visningsnamn, telefonnummer med mera. ClaimsSchema-elementet innehåller en lista över **ClaimType-element.** **Elementet ClaimType** innehåller **ID-attributet,** som är anspråksnamnet.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType (Påståttstyp)

**Elementet ClaimType** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för anspråkstypen. Andra element kan använda den här identifieraren i principen. |

**Elementet ClaimType** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Titeln som visas för användare på olika skärmar. Värdet kan [lokaliseras](localization.md). |
| DataType | 1:1 | Typ av fordran. |
| DefaultPartnerClaimTypes | 0:1 | De anspråkstyper för partner som ska användas för ett angivet protokoll. Värdet kan skrivas över i **partnerclaimtypen** som anges i **elementen InputClaim** eller **OutputClaim.** Använd det här elementet om du vill ange standardnamnet för ett protokoll.  |
| Mask | 0:1 | En valfri sträng med maskeringstecken som kan användas när anspråket visas. Telefonnumret 324-232-4343 kan till exempel maskeras som XXX-XXX-4343. |
| UserHelpText | 0:1 | En beskrivning av anspråkstypen som kan vara till hjälp för användarna att förstå dess syfte. Värdet kan [lokaliseras](localization.md). |
| UserInputType | 0:1 | Den typ av indatakontroll som ska vara tillgänglig för användaren när du manuellt anger anspråksdata för anspråkstypen. Se de indatatyper för användaren som definierats senare på den här sidan. |
| AdminHelpText | 0:1 | En beskrivning av anspråkstypen som kan vara till hjälp för administratörer att förstå dess syfte. |
| Begränsning | 0:1 | Värdebegränsningarna för det här anspråket, till exempel ett reguljärt uttryck (Regex) eller en lista med godtagbara värden. Värdet kan [lokaliseras](localization.md). |
PredikatValidationReference| 0:1 | En referens till ett **predicateValidationsInput-element.** Med **elementen PredikatvalidationReference** kan du utföra en valideringsprocess för att säkerställa att endast korrekt formade data anges. Mer information finns i [Predikat](predicates.md). |



### <a name="datatype"></a>DataType

**DataType-elementet** stöder följande värden:

| Typ | Beskrivning |
| ------- | ----------- |
|boolean|Representerar ett`true` booleskt (eller) `false`värde.|
|date| Representerar ett ögonblick i tiden, vanligtvis uttryckt som ett datum för en dag. Värdet av datumet följer ISO 8601 konvention.|
|Datetime|Representerar ett ögonblick i tiden, vanligtvis uttryckt som ett datum och tid på dagen. Värdet av datumet följer ISO 8601 konvention.|
|varaktighet|Representerar ett tidsintervall i år, månader, dagar, timmar, minuter och sekunder. Formatet är `PnYnMnDTnHnMnS`, `P` där anger positivt `N` eller för negativt värde. `nY`är antalet år följt av `Y`en bokstavlig . `nMo`är antalet månader följt av `Mo`en bokstavlig . `nD`är antalet dagar följt av `D`en bokstavlig . Exempel: `P21Y` representerar 21 år. `P1Y2Mo`ett år och två månader. `P1Y2Mo5D`representerar ett år, två månader och fem dagar.  `P1Y2M5DT8H5M620S`representerar ett år, två månader, fem dagar, åtta timmar, fem minuter och tjugo sekunder.  |
|phoneNumber|Representerar ett telefonnummer. |
|int| Representerar tal mellan -2 147 483 648 och 2 147 483 647|
|long| Representerar tal mellan -9 223 372 036 854 775 808 till 9 223 372 036 854 775 807 |
|sträng| Representerar text som en sekvens av UTF-16-kodenheter.|
|strängInsamling|Representerar en `string`samling .|
|användareIdentitet| Representerar en användaridentitet.|
|användareIdentityKollection|Representerar en `userIdentity`samling .|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**DefaultPartnerClaimTypes** kan innehålla följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Protokoll | 1:n | Lista över protokoll med standardtypen för partneranspråk. |

**Protokollelementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Namn | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C. Möjliga värden är: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| PartnerClaimType | Ja | Namnet på anspråkstypen som ska användas. |

I följande exempel, när Identity Experience Framework interagerar med en SAML2-identitetsprovider `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`eller förlitande part-program, mappas **efternamnet** `family_name`till , med OpenIdConnect och OAuth2, anspråket mappas till .

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Därför avger JWT-token som utfärdats av Azure AD `family_name` B2C i stället för ClaimType-efternamn . **surname**

```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mask

**Maskelementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| `Type` | Ja | Typ av anspråksmask. Möjliga `Simple` värden: `Regex`eller . Värdet `Simple` anger att en enkel textmask används på den inledande delen av ett stränganspråk. Värdet `Regex` anger att ett reguljärt uttryck används på stränganspråket som helhet.  Om `Regex` värdet anges måste ett valfritt attribut också definieras med det reguljära uttryck som ska användas. |
| `Regex` | Inga | Om **`Type`** är `Regex`inställt på anger du det reguljära uttryck som ska användas.

I följande exempel konfigureras ett **PhoneNumber-anspråk** med masken: `Simple`

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Identity Experience Framework återger telefonnumret medan du döljer de sex första siffrorna:

![Telefonnummer anspråk visas i webbläsaren med första sex siffror maskerad av Xs](./media/claimsschema/mask.png)

I följande exempel konfigureras ett **AlternateEmail-anspråk** med masken: `Regex`

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Identity Experience Framework återger bara den första bokstaven i e-postadressen och e-postdomännamnet:

![E-postanspråk som visas i webbläsaren med tecken maskerade av asterisker](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Begränsning

**Begränsningselementet** kan innehålla följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| MergeBehavior | Inga | Den metod som används för att sammanfoga uppräkningsvärden med en ClaimType i en överordnad princip med samma identifierare. Använd det här attributet när du skriver över ett anspråk som anges i basprincipen. Möjliga `Append`värden: `Prepend`, `ReplaceAll`, eller . Värdet `Append` är en samling data som ska läggas till i slutet av den samling som anges i den överordnade principen. Värdet `Prepend` är en samling data som ska läggas till före den samling som anges i den överordnade principen. Värdet `ReplaceAll` är en samling data som anges i den överordnade principen och som ska ignoreras. |

Elementet **Begränsning** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Uppräkning | 1:n | De tillgängliga alternativen i användargränssnittet som användaren kan välja för ett anspråk, till exempel ett värde i en listruta. |
| Mönster | 1:1 | Det reguljära uttrycket som ska användas. |

#### <a name="enumeration"></a>Uppräkning

**Uppräkningselementet** definierar tillgängliga alternativ för användaren att välja för ett anspråk i `CheckboxMultiSelect`användargränssnittet, till exempel ett värde i en , `DropdownSingleSelect`, eller `RadioSingleSelect`. Du kan också definiera och lokalisera tillgängliga alternativ med [elementet LocalizedCollections.](localization.md#localizedcollections) Om du vill slå upp ett objekt från en **anspråksuppräkningssamling** använder du [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) claims transformation.

**Uppräkningselementet** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Text | Ja | Den visningssträng som visas för användaren i användargränssnittet för det här alternativet. |
|Värde | Ja | Anspråksvärdet som är associerat med att välja det här alternativet. |
| VäljByDefault | Inga | Anger om det här alternativet ska väljas som standard i användargränssnittet. Möjliga värden: Sant eller Falskt. |

I följande exempel konfigureras ett anspråk på en `New York`stadslistgarderingslista med ett standardvärde inställt på: **city**

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Listruta med standardvärde inställt på New York:

![Listkontrollen som återges i webbläsaren och visar standardvärdet](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Mönster

Elementet **Mönster** kan innehålla följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| RegelbundnaExpression | Ja | Det reguljära uttryck som anspråk av den här typen måste matcha för att vara giltigt. |
| Hjälptext | Inga | Ett felmeddelande för användare om kontrollen av reguljära uttryck misslyckas. |

I följande exempel konfigureras ett **e-postanspråk** med validering av inmatning av reguljära uttryck och hjälptext:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Identity Experience Framework återger e-postadressen med validering av e-postformat:

![TextBox som visar felmeddelande som utlöses av regex-begränsning](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C stöder en mängd olika indatatyper för användare, till exempel en textruta, ett lösenord och en listruta som kan användas när anspråksdata för anspråkstypen anges manuellt. Du måste ange **UserInputType** när du samlar in information från användaren med hjälp av en [självpåförd teknisk profil](self-asserted-technical-profile.md) och [visningskontroller](display-controls.md).

**Elementet UserInputType** tillgängliga användarindatatyper:

| UserInputType | ClaimType som stöds | Beskrivning |
| --------- | -------- | ----------- |
|KryssrutaMultiSelect| `string` |Nedrullningsbara flervalsruta. Anspråksvärdet representeras i en kommaavgränsare sträng av de valda värdena. |
|DatumTidsdropdown | `date`, `dateTime` |Rullgardinsmenyn för att välja en dag, månad och ett år. |
|Rullgardinsmenyn |`string` |En enda listruta. Anspråksvärdet är det valda värdet.|
|E-postbox | `string` |Fältet E-postinmatning. |
|Stycke | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Ett fält som bara visar text i en stycketagg. |
|lösenord | `string` |Textruta för lösenord.|
|RadiosingleSelect |`string` | Insamling av alternativknappar. Anspråksvärdet är det valda värdet.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Skrivskyddad textruta. |
|TextBox |`boolean`, `int`, `string` |Textruta med en rad. |


#### <a name="textbox"></a>TextBox

**TextBox-användarens** inmatningstyp används för att tillhandahålla en textruta med en rad.

![TextBox som visar egenskaper som anges i anspråkstyp](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>E-postbox

Indatatypen **e-postbox** används för att tillhandahålla ett grundläggande e-postinmatningsfält.

![E-postbox som visar egenskaper som anges i anspråkstyp](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>lösenord

Inmatningstypen **Lösenordsanvändare** används för att registrera ett lösenord som anges av användaren.

![Använda anspråkstyp med lösenord](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DatumTidsdropdown

Användarens inmatningstyp **dateTimeDropdown** används för att tillhandahålla en uppsättning listruppgångar för att välja en dag, månad och ett år. Du kan använda predikat- och predikatvalidationselement för att styra lägsta och högsta datumvärden. Mer information finns i avsnittet **Konfigurera ett datumintervall** [i predikat och predikatvalidationer](predicates.md).

![Använda anspråkstyp med datetimedropdown](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadiosingleSelect

Indatatypen **RadioSingLeSelect** används för att tillhandahålla en samling alternativknappar som gör att användaren kan välja ett alternativ.

![Använda anspråkstyp med radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>Rullgardinsmenyn

**ListdownSingleSelect-indatatypen** för användaren används för att tillhandahålla en listruta som gör att användaren kan välja ett alternativ.

![Använda anspråkstyp med dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>KryssrutaMultiSelect

Indatatypen **CheckboxMultiSelect** används för att tillhandahålla en samling kryssrutor som gör att användaren kan välja flera alternativ.

![Använda anspråkstyp med kryssrutamultiselect](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Readonly

Indatatypen **Readonly** används för att tillhandahålla ett läsinställfält för att visa anspråk och värde.

![Använda anspråkstyp med readonly](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Stycke

Indatatypen **Stycke** används för att ange ett fält som bara visar text i en stycketagg.  Till exempel &lt;&gt;p&lt;text&gt;/p . En **styckeanvändares** indatatyp `OutputClaim` av självförsäkrad `Required` `false` teknisk profil måste ange attributet (standard).

![Använda anspråkstyp med stycke](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
