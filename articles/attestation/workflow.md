---
title: Arbets flöde för Azure-attestering
description: Arbets flödet för Azure-attestering.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 240b27f897d8e7a34026701cf7fdc844eb9d4086
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89237380"
---
# <a name="workflow"></a>Arbetsflöde

Microsoft Azure attesteringen tar emot bevis från enclavess och utvärderar bevis mot Azures säkerhets bas linje och konfigurerbara principer. Vid lyckad flödet genererar Azure-attestering en token för attestering för att bekräfta trovärdigheten för enklaven.

Följande aktörer ingår i ett arbets flöde för Azure-attestering:

- **Förlitande part**: komponenten som förlitar sig på Azure-attestering för att verifiera enklaven-giltighet. 
- **Klient**: komponenten som samlar in information från en enklaven och skickar förfrågningar till Azure-attestering. 
- **Azure-attestering**: komponenten som godkänner enklaven-bevis från klienten, validerar den och returnerar attesterings-token till klienten


## <a name="enclave-validation-work-flow"></a>Arbets flöde för enklaven-validering

Här följer allmänna steg i ett typiskt SGX-enklaven för attestering av arbets flöde (med Azure-attestering):

1. Klienten samlar in bevis från en enklaven. Bevis är information om enklaven-miljön och klient biblioteket som körs i enklaven.
1. Klienten har en URI som refererar till en instans av Azure-attestering. Klienten autentiserar till Azure AD och erhåller en åtkomsttoken.
1. Klienten skickar bevis till Azure-attestering tillsammans med åtkomsttoken. Exakt den information som skickas till providern beror på typen av enklaven.
1. Azure-attesteringen verifierar den inskickade informationen och utvärderar den mot en konfigurerad princip. Om verifieringen lyckas utfärdar Azure-attestering en attesterings-token och returnerar den till klienten. Om det här steget Miss lyckas rapporterar Azure-attestering ett fel till klienten. 
1. Klienten skickar attesterings-token till förlitande part. Den förlitande parten anropar den offentliga nyckelns metadata-slutpunkt för Azure-attestering för att hämta signerings certifikat. Den förlitande parten verifierar sedan signaturen för attesterings-token och säkerställer enklaven-trovärdigheten. 

![Enklaven-validerings flöde](./media/validation-flow.png)


## <a name="next-steps"></a>Nästa steg
- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)
