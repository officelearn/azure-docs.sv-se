---
title: ClaimsSchema  - Azure Active Directory B2C | Microsoft Docs
description: Ange det ClaimsSchema elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a49553941b83e323f23f20b794a464f47ef31981
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849098"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den **ClaimsSchema** elementet definierar vilka anspråkstyper som kan referera till som en del av principen. Anspråk schemat är den plats där du deklarera dina anspråk. Ett anspråk kan vara förnamn, senaste namn, visningsnamn, telefonnummer och mer. ClaimsSchema-elementet innehåller listan över **ClaimType** element. Den **ClaimType** elementet innehåller den **Id** attribut, som är anspråkets namn. 

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

## <a name="claimtype"></a>ClaimType

Den **ClaimType** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för Anspråkstypen. Andra element kan använda den här identifieraren i principen. |

Den **ClaimType** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Rubriken som visas för användarna på olika skärmar. Värdet kan vara [lokaliserade](localization.md). |
| Datatyp | 0:1 | Typ av anspråket. Datatyperna för booleskt värde, datum, dateTime, int, long, string, stringCollection, alternativeSecurityIdCollection kan användas. |
| DefaultPartnerClaimTypes | 0:1 | Partner-standard anspråkstyper ska användas för ett angivet protokoll. Värdet kan åsidosättas i den **PartnerClaimType** anges i den **InputClaim** eller **OutputClaim** element. Använd det här elementet för att ange standardnamnet för ett protokoll.  |
| Mask | 0:1 | En valfri sträng med maskering av tecken som kan användas vid visning av anspråket. Till exempel ska phone nummer 324-232-4343 maskeras som XXX-XXX-4343. |
| UserHelpText | 0:1 | En beskrivning av Anspråkstypen som kan vara till hjälp för användarna att förstå dess syfte. Värdet kan vara [lokaliserade](localization.md). |
| UserInputType | 0:1 | Typ av kontrollen som ska vara tillgängligt för användaren när du manuellt anger informationen som anspråk för Anspråkstypen. Se indatatyper för användare som definieras senare i den här sidan. |
| Begränsning | 0:1 | Begränsningar för värdet för det här anspråket, till exempel ett reguljärt uttryck (Regex) eller en lista över godkända värden. Värdet kan vara [lokaliserade](localization.md). |
PredicateValidationReference| 0:1 | En referens till en **PredicateValidationsInput** element. Den **PredicateValidationReference** element kan du utföra en verifieringsprocessen för att säkerställa att endast korrekt strukturerad data har angetts. Mer information finns i [predikat](predicates.md). |

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

Den **DefaultPartnerClaimTypes** kan innehålla följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Protokoll | 0: n | Lista över protokoll med respektive standard-partner anspråk typnamn. |

Den **protokollet** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Namn | Ja | Namnet på ett giltigt protokoll som stöds av Azure AD B2C. Möjliga värden:  OAuth1, OAuth2, SAML2, OpenIdConnect, WsFed, or WsTrust. |
| PartnerClaimType | Ja | Anspråk typnamn som ska användas. |

I följande exempel, när den Identitetsramverk interagerar med en identitetsprovider för SAML2 eller förlitande partsprogram den **efternamn** anspråk har mappats till `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`, med OpenIdConnect och OAuth2 anspråket är mappad till `family_name`.

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

Därför JWT-token som utfärdas av Azure AD B2C genererar den `family_name` i stället för ClaimType **efternamn**.
 
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

Den **Mask** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Typ | Ja | Typ av anspråk masken. Möjliga värden: `Simple` eller `Regex`. Den `Simple` värdet anger att en enkel text mask tillämpas på den ledande delen av ett sträng-anspråk. Den `Regex` värdet anger att ett reguljärt uttryck används till sträng anspråk som helhet.  Om den `Regex` värde har angetts, ett valfritt attribut måste också ha definierats med reguljära uttrycket som ska användas. |
| Regex | Nej | Om **typ** är inställd på `Regex`, ange det reguljära uttrycket att använda.

I följande exempel konfigureras en **PhoneNumber** anspråk med den `Simple` mask:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>  
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Den Identitetsramverk återges telefonnumret utan att visa de första sex siffrorna:

![Med hjälp av Anspråkstypen med mask](./media/claimsschema/mask.png)

I följande exempel konfigureras en **AlternateEmail** anspråk med den `Regex` mask:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Den Identitetsramverk återges endast den första bokstaven i e-postadressen och e-domännamn:

![Med hjälp av Anspråkstypen med mask](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Begränsning

Den **begränsning** element kan innehålla följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| MergeBehavior | Nej | Den metod som används för att slå samman uppräkningsvärdena med en ClaimType i en överordnad-princip med samma identifierare. Använd det här attributet när du skriver över ett anspråk som angetts i basprincipen. Möjliga värden: `Append`, `Prepend`, eller `ReplaceAll`. Den `Append` värdet är en samling data som ska läggas till i slutet av den samling som anges i den överordnade principen. Den `Prepend` värdet är en samling data som ska läggas till innan den samling som anges i den överordnade principen. Den `ReplaceAll` värdet är en samling data som anges i den överordnade-princip som ska ignoreras. |

Den **begränsning** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Uppräkning | 1:n | De tillgängliga alternativen i användargränssnittet för att användaren väljer för ett anspråk, till exempel ett värde i en listruta. |
| Mönster | 1:1 | Det reguljära uttrycket att använda. |

### <a name="enumeration"></a>Uppräkning

Den **uppräkning** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Text | Ja | Visningssträngen som visas för användaren i användargränssnittet för det här alternativet. |
|Värde | Ja | Anspråkets värde som är associerad med det här alternativet. |
| SelectByDefault | Nej | Anger huruvida det här alternativet bör väljas som standard i Användargränssnittet. Möjliga värden: SANT eller FALSKT. |

I följande exempel konfigureras en **Stad** listrutan lista anspråk med ett standardvärde som angetts till `New York`:

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
Stad listruta med ett standardvärde som angetts till New York:

![Listruta för stad](./media/claimsschema/dropdownsingleselect.png)


### <a name="pattern"></a>Mönster

Den **mönstret** element kan innehålla följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Reguljärt uttryck | Ja | Det reguljära uttrycket som anspråk av den här typen måste matcha för att vara giltiga. |
| HelpText | Nej | Mönster eller reguljärt uttryck för denna begäran. |

I följande exempel konfigureras en **e-post** anspråket med det reguljära uttrycket verifiering och hjälp indatatext:

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

Den Identitetsramverk återges e-postadress-anspråk med verifiering av indata i e-format:

![Med hjälp av Anspråkstypen med mönster](./media/claimsschema/pattern.png)

## <a name="userinputtype"></a>UserInputType

Azure AD-B2C stöder en mängd olika indatatyper för användaren, till exempel en textruta, lösenord och listrutan lista som kan användas när du manuellt anger anspråksdata för Anspråkstypen. Du måste ange den **UserInputType** när du samlar in information från användaren med hjälp av en [lokal verifieringsvillkor tekniska profilen](self-asserted-technical-profile.md).

### <a name="textbox"></a>TextBox

Den **textrutan** typ av användarindata som används för att tillhandahålla en enradig textruta.

![Med hjälp av Anspråkstypen med textrutan](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

### <a name="emailbox"></a>EmailBox

Den **EmailBox** typ av användarindata som används för att tillhandahålla en grundläggande e-post indatafält.

![Med hjälp av Anspråkstypen med emailbox](./media/claimsschema/emailbox.png)

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

### <a name="password"></a>Lösenord

Den **lösenord** typ av användarindata som används för att registrera ett lösenord som anges av användaren.

![Med hjälp av Anspråkstypen med lösenord](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

### <a name="datetimedropdown"></a>DateTimeDropdown

Den **DateTimeDropdown** typ av användarindata som används för att tillhandahålla en uppsättning listrutor för att välja en dag, månad och år. Du kan använda predikat och PredicateValidations element för att kontrollera lägsta och högsta datumvärden. Mer information finns i den **konfigurera ett datumintervall** delen av [predikat och PredicateValidations](predicates.md).

![Med hjälp av Anspråkstypen med datetimedropdown](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

### <a name="radiosingleselect"></a>RadioSingleSelect

Den **RadioSingleSelect** typ av användarindata som används för att tillhandahålla en uppsättning alternativknappar som gör att användaren kan välja ett alternativ.

![Med hjälp av Anspråkstypen med radiodsingleselect](./media/claimsschema/radiosingleselect.png)

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

### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Den **DropdownSingleSelect** typ av användarindata som används för att tillhandahålla en listruta som gör att användaren kan välja ett alternativ.

![Med hjälp av Anspråkstypen med dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

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

### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

Den **CheckboxMultiSelect** typ av användarindata som används för att tillhandahålla en samling av kryssrutorna som gör att användaren kan välja flera alternativ.

![Med hjälp av Anspråkstypen med checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

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

### <a name="readonly"></a>skrivskyddad

Den **Readonly** typ av användarindata som används för att tillhandahålla ett skrivskyddat fält om du vill visa anspråk och värde.

![Med hjälp av Anspråkstypen med skrivskyddad](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


### <a name="paragraph"></a>Stycke

Den **punkt** typ av användarindata som används för att tillhandahålla ett fält som visar text endast i en styckestagg. Till exempel &lt;p&gt;text&lt;/p&gt;.

![Med hjälp av Anspråkstypen med punkt](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

Att visa en av de **uppräkning** värdena i en **responseMsg** anspråk, Använd `GetMappedValueFromLocalizedCollection` eller `CreateStringClaim` omvandling av anspråk. Mer information finns i [sträng Anspråksomvandlingar](string-transformations.md) 
