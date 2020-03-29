---
title: Azure Single Sign Out SAML Protocol | Microsoft-dokument
description: I den här artikeln beskrivs SAML-protokollet för enkel utskrivning i Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701373"
---
# <a name="single-sign-out-saml-protocol"></a>SAML-protokoll för enkel ut logga ut

Azure Active Directory (Azure AD) stöder saml 2.0-webbläsarens enda utskrivningsprofil. För att enstaka utloggning ska fungera korrekt måste **LogoutURL** för programmet uttryckligen registreras med Azure AD under programregistrering. Azure AD använder LogoutURL för att omdirigera användare när de har loggat ut.

Följande diagram visar arbetsflödet för Azure AD-processen för enkel ut signering.

![Arbetsflöde för engångssignering av Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Molntjänsten skickar `LogoutRequest` ett meddelande till Azure AD för att ange att en session har avslutats. Följande utdrag visar `LogoutRequest` ett exempelelement.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Elementet `LogoutRequest` som skickas till Azure AD kräver följande attribut:

* `ID`- Detta identifierar utanmälningsbegäran. Värdet av `ID` bör inte börja med ett tal. Den typiska metoden är att lägga till **id till** strängrepresentationen av ett GUID.
* `Version`- Ange värdet för det här elementet till **2.0**. Det här värdet är obligatoriskt.
* `IssueInstant`- Detta `DateTime` är en sträng med ett UTC-värde (Coordinate Universal Time) och [tur och retur-format ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD förväntar sig ett värde av den här typen, men verkställer det inte.

### <a name="issuer"></a>Utfärdare
Elementet `Issuer` i `LogoutRequest` ett måste exakt matcha ett av **ServicePrincipalNames** i molntjänsten i Azure AD. Detta är vanligtvis inställt på **app-ID URI** som anges under programregistreringen.

### <a name="nameid"></a>NameID
Värdet för `NameID` elementet måste `NameID` exakt matcha användarens värde som loggas ut.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD `LogoutResponse` skickar ett `LogoutRequest` svar på ett element. Följande utdrag visar `LogoutResponse`ett exempel .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD `ID`anger `Version` `IssueInstant` och värden `LogoutResponse` i elementet. Det anger `InResponseTo` också elementet till `ID` värdet `LogoutRequest` av attributet för det som framkallade svaret.

### <a name="issuer"></a>Utfärdare
Azure AD anger `https://login.microsoftonline.com/<TenantIdGUID>/` det \<här värdet till var TenantIdGUID> är klient-ID för Azure AD-klienten.

Om du vill `Issuer` utvärdera elementets värde använder du värdet för **app-ID URI** som anges vid programregistrering.

### <a name="status"></a>Status
Azure AD `StatusCode` använder elementet i elementet `Status` för att ange att ut signeringen har misslyckats eller misslyckades. När ut logga ut-försöket misslyckas kan elementet `StatusCode` också innehålla anpassade felmeddelanden.
