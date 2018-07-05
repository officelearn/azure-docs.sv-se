---
title: Granska åtkomst med hjälp av Azure AD åtkomstgranskningar | Microsoft Docs
description: Lär dig hur du granskar åtkomst med hjälp av Azure Active Directory-åtkomstgranskningar.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 09/19/2017
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 52d832de10de93c5bd4b91df821f96845b3b21be
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446647"
---
# <a name="review-access-with-azure-ad-access-reviews"></a>Granska åtkomst med Azure AD åtkomstgranskningar

Azure Active Directory (AD Azure) gör det enklare företag hantera åtkomst till program och medlemmar i grupper i Azure AD och andra Microsoft Online Services med en funktion som kallas åtkomst går igenom. Kanske du har fått ett e-postmeddelande från Microsoft som ber dig att granska åtkomst för medlemmar i en grupp eller användare med åtkomst till ett program. 

## <a name="open-an-access-review"></a>Öppna en åtkomstgranskning

Om du vill se de väntande åtkomstgranskningar, klicka på länken i e-postmeddelandet. Om du inte har e-postmeddelandet, kan du hitta åtkomstgranskningar genom att följa dessa steg:

1. Logga in på den [Azure AD-åtkomstpanelen](https://myapps.microsoft.com).

2. Välj symbolen användaren i det övre högra hörnet på sidan som visar organisationens namn och en standard. Om mer än en organisation visas, väljer du den organisation som har begärt en åtkomstgranskning.

3. Om en panel märkt **Åtkomstgranskningar** är till höger på sidan, väljer den. Om panelen inte visas, det finns inga åtkomstgranskningar att utföra för organisationen och ingen åtgärd krävs just nu.

## <a name="fill-out-an-access-review"></a>Fylla i en åtkomstgranskning

När du väljer en åtkomstgranskning i listan visas namnen på användare som behöver granskas. Visas endast en namn – din egen--om begäran var att granska din egen åtkomst.

För varje rad i listan kan du bestämma om du vill godkänna eller neka användaren åtkomst. Välj raden och välj om du vill godkänna eller neka. (Om du inte vet att användaren anger du det för.)

Granskaren kan kräva att du ange en motivering för godkännande av fortsatt åtkomst och gruppmedlemskap.

## <a name="next-steps"></a>Nästa steg

En användare nekas åtkomst tas inte bort direkt. Det kan tas bort när granskningen är klar eller när en administratör slutar granskningen. Om du vill ändra ditt svar och godkänna en tidigare nekade användare eller neka en tidigare godkända användare, Välj raden, återställa svaret och välj ett nytt svar. Du kan göra det här steget tills åtkomstgranskningen är klar.



