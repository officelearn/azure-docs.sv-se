---
title: Hantera hot mot resurser och data i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om identifiering och skyddsteknik tekniker för DOS-attacker och lösenord attacker i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8e4c145409579e2e3a833494008656d4d0e8e49e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703757"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Hantera hot mot resurser och data i Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C har inbyggda funktioner som kan hjälpa dig att skydda mot hot mot resurser och data. Dessa hot är denial of service-attacker och lösenord för attacker. Denial of service-attacker kan göra resurser tillgängliga för avsedda användarna. Lösenord attacker leda till obehörig åtkomst till resurser. 

## <a name="denial-of-service-attacks"></a>Denial of service-attacker

Azure AD B2C skyddar mot SYN översvämning angrepp med hjälp av en SYN-cookie. Azure AD B2C skyddar även mot DOS-attacker genom att använda gränser för frekvens och anslutningar.

## <a name="password-attacks"></a>Lösenord attacker

Lösenord som anges av användare krävs för att vara förhållandevis komplexa. Azure AD B2C använder minskning tekniker för lösenord attacker. Minskning omfattar lösenord brute force-attacker och ordlisteattacker för lösenord. Med hjälp av olika signaler analyserar Azure AD B2C integriteten för begäranden. Azure AD B2C har utformats för att skilja smart avsedda användarna från hackare och botnät. 

Azure AD B2C använder en avancerad strategi för att låsa konton. Konton som är låsta baserat på IP-Adressen för begäran och det lösenord som anges. Låsningens varaktighet ökar också beroende på sannolikheten att den är en attack. När 10 gånger är har misslyckats med ett lösenord, utförs en minut-kontoutelåsning. Nästa gång en inloggning är misslyckas efter att kontot är upplåst, en annan en minut utelåsning inträffar och fortsätter varje kunde inte logga in. Att ange samma lösenord flera gånger, räknas inte som flera misslyckade inloggningar. 

De första 10 kontoutelåsning punkter är en minut långa. Därefter 10 kontoutelåsning punkter är något längre och öka varaktigheten efter varje 10 kontoutelåsning perioder. Räknaren för kontoutelåsning återställs till noll efter en genomförd inloggning att kontot inte är låst. Kontoutelåsning perioder kan vara upp till fem timmar. 

För närvarande kan du:

- Utlösa en utelåsning med färre än 10 misslyckade inloggningar
- Hämta en lista över utelåst konton
- Konfigurera utelåst princip

Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
