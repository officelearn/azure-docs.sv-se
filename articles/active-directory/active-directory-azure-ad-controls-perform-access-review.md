---
title: "Granska åtkomst med hjälp av Azure AD åtkomst till granskningar | Microsoft Docs"
description: "Lär dig mer om att granska åtkomst med hjälp av Azure Active Directory access granskningar."
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 9cd11d41c68c29bfcba44673ae6dbd154fc463f6
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Granska åtkomst med Azure AD åtkomst till granskningar

Azure Active Directory (AD Azure) förenklar företag hantera åtkomst till program och medlemmar i grupper i Azure AD och andra Microsoft Online Services med en funktion som kallas åtkomst går igenom. Kanske du har fått ett e-postmeddelande från Microsoft som uppmanar dig att granska åtkomst för medlemmar i en grupp eller användare med åtkomst till ett program. 

## <a name="open-an-access-review"></a>Öppna en åtkomst-granskning

Välj länken i e-postmeddelandet om du vill se väntande åtkomst granskningar. Om du inte har e-postmeddelandet, kan du hitta åtkomst granskningar genom att följa dessa steg:

1. Logga in på den [Azure AD-åtkomstpanelen](https://myapps.microsoft.com).

2. Välj symbolen för användaren i övre högra hörnet på sidan som visar organisationens namn och standard. Om mer än en organisation visas väljer du den organisation som har begärt en åtkomst-granskning.

3. Om en panel märkt **åtkomst till granskningar** är till höger på sidan Välj. Om panelen inte visas, det finns ingen åtkomst granskningar ska utföras för en organisation och ingen åtgärd krävs just nu.

## <a name="fill-out-an-access-review"></a>Fyll i en åtkomst-granskning

När du väljer en åtkomst-granskning i listan visas namnen på användare som ska granskas. Du kan se endast en namn - egna--om begäran var att granska din egen åtkomst.

För varje rad i listan kan du bestämma om du vill godkänna eller neka användaren åtkomst. Välj raden och välja om du vill godkänna eller neka. (Om du inte vet att användaren kan du ange att för.)

Granskaren kan kräva att du kan ange en motivering för att godkänna fortsatt åtkomst eller gruppmedlemskap.

## <a name="next-steps"></a>Nästa steg

En användare nekas åtkomst tas inte bort direkt. Det kan tas bort när granskningen har slutförts eller när en administratör stoppar granskningen. Om du vill ändra ditt svar och godkänna en tidigare nekade användare eller neka en tidigare godkänd användare, Välj raden återställa svaret och välj ett nytt svar. Du kan göra det här steget tills åtkomst granskningen har slutförts.



