---
title: Azure enkel inloggning i SAML-protokollet | Microsoft Docs
description: "Den här artikeln beskriver den enda Sign-Out SAML-protokoll i Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: 45e4705f53d80b5fe852c484b5e64d18a8e24f09
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# Enkel utloggning SAML-protokoll
Azure Active Directory (AD Azure) stöder SAML 2.0 web webbläsare enskild utloggning profil. För enstaka utloggning ska fungera korrekt, den **LogoutURL** för programmet explicit måste registreras med Azure AD under registreringen av program. Azure AD används LogoutURL för att omdirigera användare när de har loggat.

Det här diagrammet visar arbetsflödet för en enskild utloggning process som Azure AD.

![Enkel inloggning i arbetsflödet](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## LogoutRequest
Cloud service skickar en `LogoutRequest` meddelande till Azure AD för att ange att en session har avslutats. Följande utdrag visar ett exempel på en `LogoutRequest` element.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### LogoutRequest
Den `LogoutRequest` element som skickas till Azure AD kräver följande attribut:

* `ID`: Det identifierar utloggning begäran. Värdet för `ID` får inte inledas med en siffra. Det vanliga sättet är att lägga till **id** till strängrepresentation av en GUID.
* `Version`: Ange värdet för elementet för **2.0**. Det här värdet är obligatoriskt.
* `IssueInstant`: Det här är en `DateTime` sträng med ett värde för samordna Universal Time (UTC) och [fram och åter format (”o”)](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD förväntas ett värde av den här typen, men använda inte den.

### Utfärdaren
Den `Issuer` element i en `LogoutRequest` måste exakt matcha en av de **ServicePrincipalNames** i Molntjänsten i Azure AD. Normalt är inställningen i **App-ID URI** som anges under programmet registreringen.

### NameID
Värdet för den `NameID` elementet måste matcha den `NameID` för användaren loggas ut.

## LogoutResponse
Azure AD skickar en `LogoutResponse` som svar på en `LogoutRequest` element. Följande utdrag visar ett exempel på en `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### LogoutResponse
Azure AD anger den `ID`, `Version` och `IssueInstant` värdena i den `LogoutResponse` element. Den anger också den `InResponseTo` element med värdet för den `ID` attribut för den `LogoutRequest` som förvärvas svaret.

### Utfärdaren
Azure AD anger det här värdet till `https://login.microsoftonline.com/<TenantIdGUID>/` där <TenantIdGUID> är klient-ID för Azure AD-klient.

Utvärdera värdet för den `Issuer` element, Använd värdet för den **App-ID URI** angav under registreringen av program.

### Status
Azure AD-använder den `StatusCode` element i den `Status` element att ange lyckad eller misslyckad utloggning. När det utloggning försöket misslyckas den `StatusCode` element kan också innehålla anpassade felmeddelanden.