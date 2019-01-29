---
title: Definiera en teknisk verifieringsprofil i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en Azure Active Directory-tekniska profilen i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 220a95f1bc95a8866a459eb878047e7f47920bd1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175102"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk verifieringsprofil i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En teknisk verifieringsprofil är en vanlig tekniska profilen från alla protokoll, till exempel [Azure Active Directory](active-directory-technical-profile.md) eller en [REST API](restful-technical-profile.md). Den tekniska profilen verifieringen returnerar utdataanspråk eller returnerar felmeddelandet HTTP 409 (konflikt Svarets statuskod), med följande innehåll:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Anspråk som returneras från en teknisk verifieringsprofil läggs till i uppsättningen anspråk. Du kan använda dessa anspråk i nästa Teknisk verifiering profilerna.

Tekniska profiler för verifiering utförs i den ordning som de visas i den **ValidationTechnicalProfiles** element. Du kan konfigurera i en profil för tekniska om körningen av alla efterföljande verifiering tekniska profiler ska fortsätta om teknisk verifieringsprofil genererar ett fel eller lyckas.

En validering tekniska profilen kan köras villkorligt baserat på villkor som definierats i den **ValidationTechnicalProfile** element. Du kan exempelvis kontrollera om specifika anspråksproviderförtroendet finns, eller om ett anspråk är lika med eller inte med det angivna värdet.

En självkontrollerad tekniska profilen kan definiera en verifiering tekniska profilen som ska användas för att verifiera några eller alla dess utdataanspråk. Alla inkommande anspråk för den refererade tekniska profilen måste visas i utdataanspråk på den refererande verifiering tekniska profilen.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Den **ValidationTechnicalProfiles** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | En teknisk profil som ska användas för att verifiera några eller alla utgående anspråk på den refererande tekniska profilen. |

Den **ValidationTechnicalProfile** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Tjänsten | Ja | En identifierare för en tekniska profilen som redan har definierats i principen eller överordnad principen. |
|ContinueOnError|Nej| Som anger om verifiering av alla efterföljande verifiering tekniska profiler ska fortsätta om den här tekniska profilen validaiton genererar ett fel. Möjliga värden: `true` eller `false` (standard bearbetning av ytterligare verifiering profiler stoppas och ett fel returneras). |
|ContinueOnSuccess | Nej | Som anger om verifiering av alla efterföljande verifiering profiler ska fortsätta om den här tekniska profilen verifiering lyckas. Möjliga värden: `true` eller `false`. Standardvärdet är `true`, vilket innebär att bearbetningen av ytterligare verifiering profiler kommer att fortsätta. |

Den **ValidationTechnicalProfile** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Villkoren | 0:1 | En lista över villkor som måste uppfyllas att köra verifieringen tekniska profilen. |

Den **Förhandsvillkoret** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Type | Ja | Typ av kontroll eller fråga för att utföra för villkor. Antingen `ClaimsExist` anges för att se till att åtgärder ska utföras om de angivna anspråk finns i användarens aktuella anspråksuppsättningen, eller `ClaimEquals` har angetts som åtgärder ska utföras om det angivna anspråket finns och dess värde är lika med den angivet värde. |
| ExecuteActionsIf | Ja | Anger om åtgärderna som i villkor som ska utföras om testet är true eller false. |

Den **Förhandsvillkoret** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Value | 1:n | De data som används av kontrollen. Om den här kontrollen är `ClaimsExist`, det här fältet anger en ClaimTypeReferenceId att fråga efter. Om typ av kontroll är `ClaimEquals`, det här fältet anger en ClaimTypeReferenceId att fråga efter. Även om ett annat värde element innehåller värdet som ska kontrolleras.|
| Åtgärd | 1:1 | Den åtgärd som ska vidtas om kontrollen Förhandsvillkoret inom en orkestreringssteget är sant. Värdet för den **åtgärd** är inställd på `SkipThisValidationTechnicalProfile`. Anger att den tekniska profilen med tillhörande inte ska köras. |

### <a name="example"></a>Exempel

Följande exempel använder dessa tekniska profiler för verifiering:

1. Den första verifiering tekniska profilen kontrollerar användarens autentiseringsuppgifter och fortsätter inte om ett fel inträffar, till exempel ogiltigt användarnamn eller felaktigt lösenord.
2. I nästa verifiering tekniska profil, kan inte köras om userType-anspråk inte finns, eller om värdet för userType är `Partner`. Den tekniska profilen verifieringen försöker läsa användarprofilen från databasen för interna kunder och fortsätta om ett fel inträffar, till exempel REST API-tjänsten är inte tillgänglig eller ett internt fel.
3. Den senaste verifiering tekniska profilen, kan inte köras om userType-anspråket inte fanns, eller om värdet för userType är `Customer`. Den tekniska profilen verifieringen försöker läsa användarprofilen från interna partner-databasen och fortsätter om ett fel inträffar, till exempel REST API-tjänsten är inte tillgänglig eller ett internt fel.

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
