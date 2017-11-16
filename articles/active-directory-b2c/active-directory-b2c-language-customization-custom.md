---
title: Azure Active Directory B2C:Language anpassningen i anpassade principer | Microsoft Docs
description: "Lär dig hur du använder localize innehållet i anpassade principer för flera språk"
services: active-directory-b2c
documentationcenter: 
author: sammak
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 11/13/2017
ms.author: sama
ms.openlocfilehash: 4ed9791d6590e3982a1bc79b96f8592995bc315c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
#<a name="language-customization-in-custom-policies"></a>Anpassning av språk i anpassade principer

> [!NOTE]
> Den här funktionen är tillgänglig som förhandsversion.
> 

Anpassade principer fungerar språk anpassning desamma som för inbyggda principer.  Finns inbyggt [dokumentationen](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) som beskriver beteende i hur ett språk har valts baserat på parametrar och inställningar för webbläsaren.

##<a name="enable-supported-languages"></a>Aktivera stöd för språk
Om ui-språk angavs inte och användarens webbläsare uppmanas du att ange något av dessa språk, visas de språk som stöds för användaren.  

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

##<a name="enable-custom-language-strings"></a>Aktivera anpassad språk strängar

Skapa anpassade språk strängar kräver två steg:
1. Redigera den `<ContentDefinition>` för sidan för att ange en resurs-ID för önskat språk
2. Skapa den `<LocalizedResources>` med motsvarande ID: N i din`<BuildingBlocks>`

Tänk på att du kan placera en `<ContentDefinition>` och `<BuildingBlock>` i både din tilläggsfilen eller förlitande principfilen beroende på om du vill att ändringarna ska vara i ärvande principer eller inte.

###<a name="edit-the-contentdefinition-for-the-page"></a>Redigera ContentDefinition för sidan

För varje sida du vill lokalisera, du kan ange i den `<ContentDefinition>` vilka språkresurser efter varje språkkod.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

I det här exemplet läggs till sidan Unified registrering eller inloggning franska (fr) och anpassade strängar för engelska (en).  Den `LocalizedResourcesReferenceId` för varje `LocalizedResourcesReference` är samma som deras språk, men du kan använda valfri sträng som ID.  För varje kombination av språk och sidan, måste du skapa en motsvarande `<LocalizedResources>` visas i följande.


###<a name="create-the-localizedresources"></a>Skapa LocalizedResources

Din åsidosättningar finns i din `<BuildingBlocks>` och det finns en `<LocalizedResources>` för varje sida och språk som du har angett i den `<ContentDefinition>` för varje sida.  Varje åsidosättning som har angetts som en `<LocalizedString>` till exempel i följande exempel:

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

**ClaimsProvider** -etiketter för din identitetsleverantörer (Facebook, Google, Azure AD osv.) **ClaimType** -etiketter för din attribut och deras motsvarande hjälptext eller fältet valideringsfel **UxElement** - annan sträng elementen på sidan som är det som standard, till exempel knappar eller länkar text **ErrorMessage** -formuläret Validering felmeddelanden

Se till att den `StringId`s matchar för sidan som du använder dessa åsidosättningar annars har blockerat Principverifiering vid överföring.  