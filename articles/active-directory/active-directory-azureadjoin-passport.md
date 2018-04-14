---
title: Autentisera identiteter utan lösenord via Windows Hello för företag och Azure AD | Microsoft Docs
description: Innehåller en översikt över Windows Hello för företag och ytterligare information om hur du distribuerar Windows Hello för företag.
services: active-directory
documentationcenter: ''
author: femila
manager: mtillman
editor: ''
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 22534cc5f7f2de235bc1f1212c63ea227083c5a4
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2018
---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>Autentisera identiteter utan lösenord via Windows Hello för företag
De aktuella metoderna för autentisering med bara lösenord är inte tillräckliga för att skydda användare. Användare återanvända och glömt lösenord. Lösenord är breachable, phishable lätt sprickor och fjärråtkomstlösning. De får också svårt att komma ihåg och lätt att attacker som ”[skicka hash](https://technet.microsoft.com/dn785092.aspx)”.

## <a name="about-windows-hello-for-business"></a>Om Windows Hello för företag
Windows Hello för företag är en privata och offentliga nyckel eller certifikatbaserad autentisering strategi för organisationer och konsumenter utöver lösenord. Den här typen av autentisering är beroende av nyckelpar autentiseringsuppgifter kan ersätta ett lösenord som är motståndskraftiga mot överträdelser, stöld och nätfiske.

 Windows Hello för företag, kan användaren autentiseras till ett Microsoft-konto, ett Windows Server Active Directory-konto, ett konto med Microsoft Azure Active Directory (AD Azure) eller en icke-Microsoft-tjänst som stöder snabb identitet Online (FIDO) autentisering. Windows Hello för företag har ställts in på användarens enhet efter en inledande tvåstegsverifiering under Windows Hello för företag-registrering, och användaren anger en gest som kan vara Windows Hello eller en PIN-kod. Användaren anger gest för att verifiera sin identitet. Windows använder sedan Windows Hello för företag att autentisera användaren och hjälpa dem att komma åt skyddade resurser och tjänster.

Den privata nyckeln görs tillgänglig enbart via en ”användargest” som en PIN-kod, biometrik eller en fjärransluten enhet som ett smartkort som användaren använder för att logga in på enheten. Den här informationen är kopplad till ett certifikat eller ett asymmetriskt nyckelpar. Den privata nyckeln är maskinvara som godkänd av om enheten har ett TPM-chip Trusted Platform Module (TPM). Den privata nyckeln lämnar aldrig enheten.

Den offentliga nyckeln är registrerad med Azure Active Directory och Windows Server Active Directory (för lokala). Validera att användaren genom att mappa den offentliga nyckeln för användaren till den privata nyckeln identitetsleverantörer (IDPs) och ange inloggningsinformation via en tid lösenord (OTP), PhoneFactor eller en annan notification mekanism.

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>Varför företag bör anta Windows Hello för företag
Genom att aktivera Windows Hello för företag, kan företag göra sina resurser som ännu säkrare genom att:

* Konfigurera Windows Hello för företag med ett maskinvaru-önskade alternativ. Det innebär att nycklar genereras på TPM 1.2 eller TPM 2.0 när det är tillgängligt. När TPM inte är tillgänglig genererar programvara nyckeln.
* Definiera komplexiteten och längden på PIN-kod, och om Hello användning är aktiverad i din organisation.
* Konfigurera Windows Hello för företag som stöd för smartkort-liknande scenarier med hjälp av certifikatbaserad förtroende.

## <a name="how-windows-hello-for-business-works"></a>Hur Windows Hello för företag fungerar
1. Nycklar genereras på maskinvara med TPM- eller programvara. Många enheter har en inbyggd TPM-chip säkrar maskinvara genom att integrera krypteringsnycklar i enheter. TPM 1.2 eller TPM 2.0 genererar nycklar eller certifikat som skapas från de genererade nycklarna.
2. TPM intygar nycklarna maskinvara gränsen.
3. En enda unlock-gest låser enheten. Den här gest ger åtkomst till flera resurser om enheten är ansluten till domänen eller Azure AD-anslutna.

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>Hur den här Windows Hello för företag livscykel fungerar
![Windows Hello för företag livscykel](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Föregående diagram illustrerar privata och offentliga nyckelpar och validering av identitetsleverantören. Var och en av de här stegen beskrivs här:

1. Användaren visar sin identitet via flera inbyggda språkverktyg metoder (gester, fysiska smartkort, multifaktorautentisering) och skickar informationen till en identitet Provider (IDP) som Azure Active Directory eller lokala Active Directory.
2. Enheten sedan skapar nyckeln, intygar nyckeln, tar den offentliga delen av den här nyckeln, bifogar med station instruktioner, loggar in och skickar det till IDP att registrera nyckeln.
3. Så snart IDP registrerar den offentliga delen av nyckeln, anropar IDP enheten att logga in med den privata delen av nyckeln.
4. IDP sedan validerar och utfärdar autentiseringstoken som gör att användaren och Enhetsåtkomst till skyddade resurser. IDPs kan skriva plattformsoberoende appar eller använda webbläsarstöd (via JavaScript/Webcrypto API: er) för att skapa och använda Windows Hello för företag autentiseringsuppgifterna för sina användare.

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>Distributionskrav för Windows Hello för företag
### <a name="at-the-enterprise-level"></a>Om företaget
* Företaget har en Azure-prenumeration.

### <a name="at-the-user-level"></a>På användarnivå
* Användarens dator kör Windows 10 Professional och Enterprise.

Detaljerade anvisningar för distribution, se [aktivera Windows Hello för företag i organisationen](active-directory-azureadjoin-passport-deployment.md).

## <a name="additional-information"></a>Ytterligare information
* [Windows 10 för företaget: Sätt att använda enheter för arbete](active-directory-azureadjoin-windows10-devices-overview.md)
* [Utöka molnkapaciteten till Windows 10-enheter via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Läs mer om användningsscenarier för Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ansluta domänanslutna enheter till miljöer med Azure AD och Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurera Azure AD Join](active-directory-azureadjoin-setup.md)

