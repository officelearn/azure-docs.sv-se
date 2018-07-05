---
title: Språkanpassning i anpassade principer för Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du använder lokalisera innehållet i anpassade principer för flera språk.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6269ac65e5db20521346d5312bcbadd0905c36e2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440572"
---
# <a name="language-customization-in-custom-policies"></a>Språkanpassning i anpassade principer

> [!NOTE]
> Den här funktionen är i offentlig förhandsversion.
> 

I anpassade principer fungerar språkanpassning på samma sätt som i inbyggda principer.  Se inbyggt [dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) som beskriver bete sig hur ett språk som ska väljas utifrån parametrar och inställningar för webbläsaren.

## <a name="enable-supported-languages"></a>Aktivera stöd för språk
Om användargränssnittet språk angavs inte och användarens webbläsare begär ett av dessa språk, visas språk som stöds för användaren.  

Språk som stöds är definierade i `<BuildingBlocks>` i följande format:

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

Fungerar på samma sätt som i inbyggda principer standardspråk och språk som stöds.

## <a name="enable-custom-language-strings"></a>Aktivera anpassat språk strängar

Skapa anpassat språk strängar kräver två steg:
1. Redigera den `<ContentDefinition>` för sidan för att ange ett resurs-ID för önskat språk
2. Skapa den `<LocalizedResources>` med motsvarande ID: N i din `<BuildingBlocks>`

Tänk på att du kan placera en `<ContentDefinition>` och `<BuildingBlock>` i både din fil eller förlitande principfilen beroende på om du vill att ändringarna ska finnas i dina ärvande principer eller inte.

### <a name="edit-the-contentdefinition-for-the-page"></a>Redigera ContentDefinition för sidan

För varje sida du vill lokalisera, du kan ange i den `<ContentDefinition>` vilka språkresurser efter varje språkkod.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

I det här exemplet läggs franska (fr) och anpassade strängar för engelska (en) till sidan enhetliga registrering eller inloggning.  Den `LocalizedResourcesReferenceId` för varje `LocalizedResourcesReference` är samma som deras nationella inställningar, men du kan använda valfri sträng som-ID  För varje språk och sidan kombination, måste du skapa en motsvarande `<LocalizedResources>` enligt följande.


### <a name="create-the-localizedresources"></a>Skapa LocalizedResources

Åsidosättningar finns i din `<BuildingBlocks>` och det finns en `<LocalizedResources>` för varje sida och språk som du har angett i den `<ContentDefinition>` för varje sida.  Varje åsidosättning har angetts som en `<LocalizedString>` exempelvis i följande exempel:

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

Det finns fyra typer av strängen elementen på sidan:

**ClaimsProvider** -etiketter för dina Identitetsproviders (Facebook, Google, Azuread osv.) **ClaimType** -etiketter för dina attribut och deras motsvarande hjälptext eller fältet verifieringsfel **UxElement** – andra sträng elementen på sidan som är det som standard, till exempel knappar, länkar eller text **ErrorMessage** -formuläret felmeddelanden för verifiering

Se till att den `StringId`s matchar för sidan att du använder dessa åsidosättningar som på annat sätt har blockerat Principverifiering vid överföring.  