---
title: Anpassa användar gränssnittet för din app med en anpassad princip
titleSuffix: Azure AD B2C
description: Läs mer om hur du anpassar ett användar gränssnitt med en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 340c9629af89bfacb85b37503743fc5770070ae3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990915"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Anpassa ditt programs användar gränssnitt med hjälp av en anpassad princip i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Genom att slutföra stegen i den här artikeln skapar du en anpassad princip för registrering och inloggning med ditt varumärke och utseende. Med Azure Active Directory B2C (Azure AD B2C) får du nästan fullständig kontroll över HTML-och CSS-innehållet som presenteras för användarna. När du använder en anpassad princip kan du konfigurera UI-anpassning i XML i stället för att använda kontroller i Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. ändra tilläggs filen

Om du vill konfigurera UI-anpassning kopierar du **ContentDefinition** och dess underordnade element från bas filen till tilläggs filen.

1. Öppna bas filen för din princip. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Den här bas filen är en av de principfiler som ingår i den anpassade principens start paket, som du borde ha skaffat i förutsättningen, [Kom igång med anpassade principer](./custom-policy-get-started.md).
1. Sök efter och kopiera hela innehållet i **ContentDefinitions** -elementet.
1. Öppna tilläggs filen. Till exempel *TrustFrameworkExtensions.xml*. Sök efter **BuildingBlocks** -elementet. Om elementet inte finns lägger du till det.
1. Klistra in hela innehållet i **ContentDefinitions** -elementet som du kopierade som ett underordnat objekt till **BuildingBlocks** -elementet.
1. Sök efter det **ContentDefinition** -element som innehåller `Id="api.signuporsignin"` i XML-filen som du kopierade.
1. Ändra värdet för **LoadUri** till URL: en för HTML-filen som du laddade upp till lagringen. Exempelvis `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Den anpassade principen bör se ut som följande kodfragment:

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

1. Spara tilläggs filen.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Ladda upp och testa din uppdaterade anpassade princip

#### <a name="51-upload-the-custom-policy"></a>5,1 Ladda upp den anpassade principen

1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Sök efter och välj **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**.
1. Välj **överför anpassad princip**.
1. Ladda upp tilläggs filen som du har ändrat tidigare.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 testa den anpassade principen med hjälp av **Kör nu**

1. Välj den princip som du överförde och välj sedan **Kör nu**.
1. Du bör kunna registrera dig med hjälp av en e-postadress.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurera innehålls-URI för dynamiskt anpassad sida

Genom att använda Azure AD B2C anpassade principer kan du skicka en parameter i URL-sökvägen eller en frågesträng. Genom att skicka parametern till HTML-slutpunkten kan du dynamiskt ändra sidinnehållet. Du kan till exempel ändra bakgrundsbilden på registrerings- eller inloggningssidan för Azure AD B2C baserat på en parameter som du skickar från ditt webb- eller mobilprogram. Parametern kan vara alla [anspråks lösare](claim-resolver-overview.md), till exempel program-ID, språk-ID eller anpassad frågesträngparametern, till exempel `campaignId` .

### <a name="sending-query-string-parameters"></a>Parametrar för frågesträng skickas

Om du vill skicka frågesträngs parametrar i [principen för förlitande part](relyingparty.md), lägger du till ett- `ContentDefinitionParameters` element som visas nedan.

```xml
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

Ändra värdet för till i din innehålls definition `LoadUri` `https://<app_name>.azurewebsites.net/home/unified` . Den anpassade principen `ContentDefinition` bör se ut som följande kodfragment:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

När Azure AD B2C läser in sidan, gör den ett anrop till din webb server slut punkt:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>URI för dynamiskt sid innehåll

Innehållet kan hämtas från olika platser baserat på de parametrar som används. I den CORS-aktiverade slut punkten ställer du in en mappstruktur som värd för innehåll. Du kan till exempel organisera innehållet i följande struktur. Rotmapp */mapp per språk/dina HTML-filer*. Din anpassade sid-URI kan till exempel se ut så här:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C skickar ISO-koden med två bokstäver för språket `fr` franska:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Nästa steg

Mer information om GRÄNSSNITTs element som kan anpassas finns i [referens guide för anpassning av användar flöden för användar flöden](customize-ui-overview.md).