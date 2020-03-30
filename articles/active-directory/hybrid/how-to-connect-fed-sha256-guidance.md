---
title: Ändra signaturhh-algoritm för förtroende för Office 365-förlitande part – Azure
description: Den här sidan innehåller riktlinjer för att ändra SHA-algoritm för federationsförtroende med Office 365
keywords: SHA1,SHA256,O365,federation,aadconnect,adfs,ad fs,change sha,federation trust,relying party trust SHA
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2233b434fda628dcf812a62f06541fc4b0296aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897350"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Ändra signaturhh-algoritm för förtroende för Office 365-förlitande part
## <a name="overview"></a>Översikt
AD FS (Active Directory Federation Services) signerar sina token till Microsoft Azure Active Directory för att säkerställa att de inte kan manipuleras. Signaturen kan baseras på SHA1 eller SHA256. Azure Active Directory stöder nu token som signerats med en SHA256-algoritm, och vi rekommenderar att du ställer in tokensigneringsalgoritmen till SHA256 för den högsta säkerhetsnivån. I den här artikeln beskrivs de steg som behövs för att ställa in tokensigneringsalgoritmen på den säkrare SHA256-nivån.

>[!NOTE]
>Microsoft rekommenderar användning av SHA256 som algoritm för signering av token eftersom det är säkrare än SHA1 men SHA1 fortfarande är ett alternativ som stöds.

## <a name="change-the-token-signing-algorithm"></a>Ändra tokensigneringsalgoritmen
När du har angett signaturalgoritmen med en av de två processerna nedan signerar AD FS token för Office 365-förlitande part förtroende med SHA256. Du behöver inte göra några extra konfigurationsändringar, och den här ändringen påverkar inte din möjlighet att komma åt Office 365 eller andra Azure AD-program.

### <a name="ad-fs-management-console"></a>HANTERINGSKONSOL FÖR AD FS
1. Öppna AD FS-hanteringskonsolen på den primära AD FS-servern.
2. Expandera AD FS-noden och klicka på **Förtroenden för förlitande part**.
3. Högerklicka på ditt förtroende för den förlitande parten för Office 365/Azure och välj **Egenskaper**.
4. Välj fliken **Avancerat** och välj den säkra hash-algoritmen SHA256.
5. Klicka på **OK**.

![SHA256 signeringsalgoritm - MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell-cmdlets
1. Öppna PowerShell under administratörsbehörighet på valfri AD FS-server.
2. Ställ in den säkra hash-algoritmen med hjälp av cmdleten **Set-AdfsRelyingPartyTrust.**
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Läs även
* [Reparera Office 365-förtroende med Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

