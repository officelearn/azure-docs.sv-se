---
title: "Ändra signaturhash-algoritm för förlitande part för Office 365 | Microsoft Docs"
description: "Den här sidan innehåller riktlinjer för att ändra SHA-algoritmen för federationsförtroende med Office 365"
keywords: "SHA1, SHA256, O365, federation, aadconnect, adfs, ADFS, ändra sha federationsförtroende förtroende för förlitande part"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: samueld
editor: 
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
ms.openlocfilehash: c581b1468630a9f28204592c936360b72f42f0d8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Ändra signaturhash-algoritm för Office 365 förtroende för förlitande part
## <a name="overview"></a>Översikt
Active Directory Federation Services (AD FS) loggar dess token till Microsoft Azure Active Directory så att de inte kan brytas. Denna signatur kan baseras på SHA1 eller SHA256. Azure Active Directory har nu stöd för token som signerats med ett SHA256-algoritmen och vi rekommenderar att du anger algoritmen tokensignering till SHA256 för den högsta säkerhetsnivån. Den här artikeln beskriver de steg som krävs för att ange algoritmen tokensignering säkrare SHA256 nivå.

>[!NOTE]
>Microsoft rekommenderar användning av SHA256 som algoritmen för att signera token som det är säkrare än SHA1 men SHA1 fortfarande är ett alternativ som stöds.

## <a name="change-the-token-signing-algorithm"></a>Ändra certifikat för tokensignering-algoritmen
När du har angett signaturalgoritm med någon av de två processerna nedan, loggar AD FS tokens för Office 365 förtroende för förlitande part med SHA256. Du behöver inte göra några extra konfigurationsändringar och den här ändringen påverkar inte din förmåga att få åtkomst till Office 365 eller andra Azure AD-program.

### <a name="ad-fs-management-console"></a>AD FS-hanteringskonsolen
1. Öppna hanteringskonsolen för AD FS på den primära AD FS-servern.
2. Expandera noden AD FS och klicka på **förtroende för förlitande part**.
3. Högerklicka på ditt Office 365-/ Azure förlitande part och välj **egenskaper**.
4. Välj den **Avancerat** fliken och markera den säkra hash-algoritmen SHA256.
5. Klicka på **OK**.

![SHA256 Signeringsalgoritm--MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell-cmdlets
1. Öppna PowerShell med administratörsbehörighet på alla AD FS-servern.
2. Ange den säkra hash-algoritmen med hjälp av den **Set-AdfsRelyingPartyTrust** cmdlet.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Läs också
* [Reparera Office 365 förtroende med Azure AD Connect](connect/active-directory-aadconnect-federation-management.md#repairthetrust)

