---
title: Definiera en teknisk verifieringsprofil i en anpassad princip
titleSuffix: Azure AD B2C
description: Validera anspråk med hjälp av en teknisk valideringsprofil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1eaf159149bb353b1cf0474aad5bc233decddc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481576"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk verifieringsprofil i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En teknisk valideringsprofil är en vanlig teknisk profil från alla protokoll, till exempel [Azure Active Directory](active-directory-technical-profile.md) eller ett REST [API](restful-technical-profile.md). Den tekniska profilen för validering returnerar utdataanspråk eller returnerar 4xx HTTP-statuskod med följande data. Mer information finns i [returnera felmeddelande](restful-technical-profile.md#returning-error-message)

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Omfattningen av utdataanspråken för en teknisk profil för validering är begränsad till den [självpåstägda tekniska profil](self-asserted-technical-profile.md) som anropar den tekniska profilen för validering och dess tekniska profiler för validering. Om du vill använda utdataanspråken i nästa orchestration-steg lägger du till utdataanspråken i den självpåsterade tekniska profilen som anropar den tekniska profilen för validering.

Valideringstekniska profiler körs i den ordning som de visas i elementet **ValidationTechnicalProfiles.** Du kan i en teknisk valideringsprofil konfigurera om körningen av efterföljande valideringstekniska profiler ska fortsätta om den tekniska profilen för valideringen höjer ett fel eller lyckas.

En teknisk valideringsprofil kan utföras villkorligt baserat på förutsättningar som definierats i elementet **ValidationTechnicalProfile.** Du kan till exempel kontrollera om det finns ett visst anspråk eller om ett anspråk är lika med eller inte det angivna värdet.

En självpåstående teknisk profil kan definiera en teknisk valideringsprofil som ska användas för att validera vissa eller alla dess utdataanspråk. Alla indataanspråk för den refererade tekniska profilen måste finnas i utdataanspråken för den tekniska referensvaliderns tekniska profil.

> [!NOTE]
> Endast självsäkra tekniska profiler kan använda tekniska valideringsprofiler. Om du behöver validera utdataanspråk från icke-självpåtterade tekniska profiler kan du överväga att använda ytterligare ett orchestration-steg i användarfärden för att hantera den tekniska profilen som ansvarar för valideringen.

## <a name="validationtechnicalprofiles"></a>ValideringTekniskaProfiler

Elementet **ValidationTechnicalProfiles** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| ValideringTekniskprofil | 1:n | En teknisk profil som ska användas för att validera vissa eller alla utdataanspråk för den refererande tekniska profilen. |

Elementet **ValidationTechnicalProfile** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ReferenceId | Ja | En identifierare för en teknisk profil som redan har definierats i principen eller den överordnade principen. |
|ContinueOnError (på andra sätt)|Inga| Ange om validering av efterföljande valideringstekniska profiler bör fortsätta om den här tekniska profilen för validering ger upphov till ett fel. Möjliga `true` värden: `false` eller (standard, bearbetning av ytterligare valideringsprofiler stoppas och ett fel returneras). |
|ContinueOnSuccess | Inga | Ange om valideringen av efterföljande valideringsprofiler ska fortsätta om den här tekniska profilen för validering lyckas. Möjliga `true` värden: `false`eller . Standard är `true`, vilket innebär att bearbetningen av ytterligare valideringsprofiler kommer att fortsätta. |

Elementet **ValidationTechnicalProfile** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Förutsättningar | 0:1 | En lista över förutsättningar som måste vara uppfyllda för att den tekniska profilen för validering ska kunna köras. |

Elementet **Förutsättning** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| `Type` | Ja | Den typ av check eller fråga som ska utföras för förutsättningen. Antingen `ClaimsExist` anges för att säkerställa att åtgärder ska utföras om de angivna anspråken finns `ClaimEquals` i användarens aktuella anspråksuppsättning, eller anges att åtgärderna ska utföras om det angivna anspråket finns och dess värde är lika med det angivna värdet. |
| `ExecuteActionsIf` | Ja | Anger om åtgärderna i förutsättningarna ska utföras om testet är sant eller falskt. |

Elementet **Förutsättning** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Värde | 1:n | De data som används av kontrollen. Om typen av den `ClaimsExist`här kontrollen är anger det här fältet en ClaimTypeReferenceId att fråga efter. Om typen av `ClaimEquals`kontroll är anger det här fältet en ClaimTypeReferenceId att fråga efter. Ett annat värdeelement innehåller det värde som ska kontrolleras.|
| Åtgärd | 1:1 | Den åtgärd som ska vidtas om förutsättningskontrollen inom ett orchestration-steg är sann. Värdet för **åtgärden** är `SkipThisValidationTechnicalProfile`inställt på . Anger att den associerade validerande tekniska profilen inte ska köras. |

### <a name="example"></a>Exempel

I det här exemplet används dessa tekniska verifieringsprofiler:

1. Den första tekniska profilen för validering kontrollerar användarautentiseringsuppgifter och fortsätter inte om ett fel inträffar, till exempel ogiltigt användarnamn eller felaktigt lösenord.
2. Nästa tekniska profil för validering körs inte om userType-anspråket inte finns eller `Partner`om värdet för userType är . Den tekniska profilen för validering försöker läsa användarprofilen från den interna kunddatabasen och fortsätta om ett fel uppstår, till exempel REST API-tjänsten inte tillgänglig eller något internt fel.
3. Den senaste tekniska profilen för validering, körs inte om userType-anspråket inte har `Customer`funnits, eller om värdet för userType är . Den tekniska profilen för validering försöker läsa användarprofilen från den interna partnerdatabasen och fortsätter om ett fel inträffar, till exempel REST API-tjänsten inte tillgänglig eller något internt fel.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
