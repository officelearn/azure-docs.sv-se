---
title: SAML-protokoll för enkel inloggning i Azure | Microsoft Docs
description: I den här artikeln beskrivs SAML-protokollet enkel utloggning i Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 95d3deff73ce357f012b15a7fc1cfa3decdb4bda
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701373"
---
# <a name="single-sign-out-saml-protocol"></a>SAML-protokoll för enkel utloggning

Azure Active Directory (Azure AD) stöder en enkel inloggnings profil för SAML 2,0-webbläsare. För att enkel utloggning ska fungera korrekt måste **LogoutURL** för programmet uttryckligen registreras med Azure AD under program registreringen. Azure AD använder LogoutURL för att omdirigera användare när de har loggat ut.

Följande diagram visar arbets flödet för processen för enkel inloggning i Azure AD.

![Arbets flöde för enkel inloggning i Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Moln tjänsten skickar ett `LogoutRequest` meddelande till Azure AD för att indikera att en session har avslut ATS. Följande utdrag visar ett exempel på ett `LogoutRequest`-element.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Det `LogoutRequest`-element som skickas till Azure AD kräver följande attribut:

* `ID` – detta identifierar utloggnings förfrågan. Värdet för `ID` får inte börja med en siffra. Den typiska metoden är att lägga till **ID** i sträng representationen av ett GUID.
* `Version` – ange värdet för det här elementet till **2,0**. Det här värdet är obligatoriskt.
* `IssueInstant` – det här är en `DateTime` sträng med ett UTC-värde (Coordination Universal Time) och tur och [RETUR-format ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD förväntar sig ett värde av den här typen, men tvingar inte det.

### <a name="issuer"></a>Utfärdare
`Issuer`-elementet i en `LogoutRequest` måste exakt matcha ett av **ServicePrincipalNames** i moln tjänsten i Azure AD. Detta är vanligt vis inställt på **app-ID-URI: n** som anges vid program registrering.

### <a name="nameid"></a>NameID
Värdet för `NameID`-elementet måste exakt matcha `NameID` för den användare som loggas ut.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD skickar ett `LogoutResponse` som svar på ett `LogoutRequest`-element. Följande utdrag visar ett exempel på `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD anger `ID`, `Version` och `IssueInstant` värden i `LogoutResponse`-elementet. Den anger också `InResponseTo`-elementet till värdet för attributet `ID` för `LogoutRequest` som har underutnyttjat svaret.

### <a name="issuer"></a>Utfärdare
Azure AD ställer in det här värdet på `https://login.microsoftonline.com/<TenantIdGUID>/` där \<TenantIdGUID > är klient-ID: t för Azure AD-klienten.

Om du vill utvärdera värdet för `Issuer`-elementet använder du värdet för **app-ID-URI: n** som angavs under program registreringen.

### <a name="status"></a>Status
Azure AD använder `StatusCode`-elementet i `Status`-elementet för att ange att utloggning lyckades eller misslyckades. När utloggnings försöket Miss lyckas kan `StatusCode`-elementet också innehålla anpassade fel meddelanden.
