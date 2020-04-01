---
title: Anpassa appens användargränssnitt med en anpassad princip
titleSuffix: Azure AD B2C
description: Lär dig mer om hur du anpassar ett användargränssnitt med hjälp av en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e50d6d0623e87dfa68a7cc9744c3f595ff0179c6
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396372"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Anpassa användargränssnittet för ditt program med hjälp av en anpassad princip i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Genom att slutföra stegen i den här artikeln skapar du en anpassad princip för registrering och inloggning med ditt varumärke och utseende. Med Azure Active Directory B2C (Azure AD B2C) får du nästan full kontroll över HTML- och CSS-innehållet som presenteras för användare. När du använder en anpassad princip konfigurerar du anpassning av användargränssnittet i XML i stället för att använda kontroller i Azure-portalen.

## <a name="prerequisites"></a>Krav

Slutför stegen i [Komma igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Ändra tilläggsfilen

Om du vill konfigurera anpassning av användargränssnittet **kopierar du ContentDefinition** och dess underordnade element från basfilen till tilläggsfilen.

1. Öppna principfilens basfil. Till exempel <em> `SocialAndLocalAccounts/` </em>. Den här basfilen är en av de principfiler som ingår i det anpassade startpaketet för principen, som du borde ha fått i förutsättningen, [Kom igång med anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Sök efter och kopiera hela innehållet i **ContentDefinitions-elementet.**
1. Öppna tilläggsfilen. Till exempel *TrustFrameworkExtensions.xml*. Sök efter elementet **BuildingBlocks.** Om elementet inte finns lägger du till det.
1. Klistra in hela innehållet i **contentdefinitions-elementet** som du kopierade som underordnad i elementet **BuildingBlocks.**
1. Sök efter **ContentDefinition-elementet** som finns `Id="api.signuporsignin"` i den XML som du kopierade.
1. Ändra värdet **för LoadUri** till URL:en för HTML-filen som du laddade upp till lagring. Till exempel `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Din anpassade princip ska se ut som följande kodavsnitt:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Spara tilläggsfilen.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Ladda upp och testa din uppdaterade anpassade policy

#### <a name="51-upload-the-custom-policy"></a>5.1 Ladda upp den anpassade principen

1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Sök efter och välj **Azure AD B2C**.
1. Under **Principer**väljer du **Identity Experience Framework**.
1. Välj **Ladda upp anpassad princip**.
1. Ladda upp tilläggsfilen som du tidigare har ändrat.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Testa den anpassade principen med hjälp av **Kör nu**

1. Välj den princip som du har laddat upp och välj sedan **Kör nu**.
1. Du bör kunna registrera dig med hjälp av en e-postadress.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurera dynamiskt anpassat sidinnehåll URI

Genom att använda anpassade Azure AD B2C-principer kan du skicka en parameter i URL-sökvägen eller en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram. Parametern kan vara valfri [anspråksmatchningsmatchning](claim-resolver-overview.md), till exempel program-ID, `campaignId`språk-ID eller anpassad frågesträngparameter, till exempel .

### <a name="sending-query-string-parameters"></a>Skicka frågesträngparametrar

Om du vill skicka frågesträngparametrar `ContentDefinitionParameters` lägger du till ett element som visas nedan i [principen för förlitande part.](relyingparty.md)

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

Ändra värdet på `LoadUri` till `https://<app_name>.azurewebsites.net/home/unified`. Din anpassade `ContentDefinition` princip ska se ut som följande kodavsnitt:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

När Azure AD B2C läser in sidan anropas webbserverns slutpunkt:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Dynamiskt sidinnehåll URI

Innehåll kan hämtas från olika platser baserat på de parametrar som används. I den CORS-aktiverade slutpunkten ställer du in en mappstruktur som värd för innehåll. Du kan till exempel ordna innehållet i följande struktur. *Rotmapp/mapp per språk/html-filer*. Din anpassade sid-URI kan till exempel se ut så här:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C skickar två bokstaven `fr` ISO-kod för språket, för franska:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Nästa steg

Mer information om gränssnittselement som kan anpassas finns i [referenshandboken för anpassning av användargränssnittet för användarflöden](customize-ui-overview.md).
