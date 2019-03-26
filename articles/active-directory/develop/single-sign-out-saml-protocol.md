---
title: Azure enkel utloggning SAML-protokoll | Microsoft Docs
description: Den här artikeln beskriver enkel utloggning SAML-protokoll i Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d4f9c69d6ddcbef96a88a0df10c3b0974cd5b74
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437092"
---
# <a name="single-sign-out-saml-protocol"></a>Enkel utloggning SAML-protokoll

Azure Active Directory (Azure AD) stöder SAML 2.0 web webbläsarprofilen för enkel utloggning. För enkel utloggning ska fungera korrekt, den **LogoutURL** för programmet explicit måste registreras med Azure AD under programregistrering. Azure AD använder LogoutURL för att omdirigera användare när de har loggat ut.

Följande diagram visar arbetsflödet om hur Azure AD enkel utloggning.

![Azure AD enkel utloggning arbetsflöde](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Cloud service skickar en `LogoutRequest` meddelande till Azure AD för att indikera att en session har avslutats. Följande utdrag visar ett exempel `LogoutRequest` element.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Den `LogoutRequest` skickas till Azure AD-elementet kräver följande attribut:

* `ID` -Detta identifierar utloggning begäran. Värdet för `ID` får inte inledas med en siffra. Den vanliga metoden är att lägga till **id** till strängrepresentation av ett GUID.
* `Version` -Ange värdet för det här elementet till **2.0**. Det här värdet är obligatoriskt.
* `IssueInstant` – Detta är en `DateTime` sträng med ett värde för samordna Universal Time (UTC) och [fram och åter format (”o”)](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD måste ha ett värde av den här typen, men använda inte den.

### <a name="issuer"></a>Utfärdare
Den `Issuer` element i en `LogoutRequest` måste exakt matcha en av de **ServicePrincipalNames** i Molntjänsten i Azure AD. Normalt detta är inställt på den **Appidentitets-URI** som har angetts under registreringen av program.

### <a name="nameid"></a>NameID
Värdet för den `NameID` elementet måste exakt matcha de `NameID` för den användare som loggas ut.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD skickar en `LogoutResponse` som svar på en `LogoutRequest` element. Följande utdrag visar ett exempel `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD-uppsättningar på `ID`, `Version` och `IssueInstant` värdena i den `LogoutResponse` element. I exemplet anges också den `InResponseTo` element till värdet för den `ID` attributet för den `LogoutRequest` som förvärvas svaret.

### <a name="issuer"></a>Utfärdare
Azure AD anger det här värdet till `https://login.microsoftonline.com/<TenantIdGUID>/` där \<TenantIdGUID > är klient-ID för Azure AD-klient.

Att utvärdera värdet för den `Issuer` element, Använd värdet för den **Appidentitets-URI** angavs under registreringen av program.

### <a name="status"></a>Status
Azure AD använder den `StatusCode` elementet i den `Status` element att ange lyckad eller misslyckad utloggning. När det utloggning försöket misslyckas, den `StatusCode` element kan också innehålla anpassade felmeddelanden.
