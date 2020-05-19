---
title: Markera en app som utgivare verifierad-Microsoft Identity Platform | Azure
description: Beskriver hur du markerar en app när utgivaren verifieras. När ett program har marker ATS som utgivare verifierat, innebär det att utgivaren har verifierat sin identitet med hjälp av ett Microsoft Partner Network konto som har slutfört verifierings processen och associerat det här MPN-kontot med sin program registrering.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 0c1f279e49b938fb391223bec47d23326e34b2ea
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598138"
---
# <a name="mark-your-app-as-publisher-verified-preview"></a>Markera din app som utgivare verifierad (för hands version)

När ett program har marker ATS som utgivare verifierat, innebär det att utgivaren har verifierat sin identitet med hjälp av sina Microsoft Partner Network-konton (MPN) och har associerat MPN-kontot med sin program registrering. Den här artikeln beskriver hur du slutför processen för [utgivar verifiering (för hands version)](publisher-verification-overview.md) .

## <a name="quickstart"></a>Snabbstart
Om du redan har registrerat dig i Microsoft Partner Network (MPN) och har uppfyllt [kraven](publisher-verification-overview.md#requirements)kan du komma igång direkt: 

1. Gå till [registrerings portalen](https://aka.ms/PublisherVerificationPreview)för Preview-appen.

1. Välj en app och klicka på **varumärkes anpassning**. 

1. Klicka på **Lägg till MPN-ID för att verifiera utgivare** och granska de listade kraven.

1. Ange ditt MPN-ID och klicka på **Verifiera och spara**.

Mer information om särskilda förmåner, krav och vanliga frågor finns i [översikten](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Markera din app som utgivare verifierad
Se till att du har uppfyllt [kraven](publisher-verification-overview.md#requirements)och följ sedan de här stegen för att markera dina appar som utgivare verifierad.  

1. Se till att du är inloggad med ett organisations konto (Azure AD) som har behörighet att göra ändringar i de appar som du vill markera som utgivare verifierad och på MPN-kontot i Partner Center. 

    - I Azure AD måste användaren antingen vara ägare till appen eller ha en av följande roller: program administratör, Cloud program admin, global administratör. 

    - Den här användaren måste ha följande roller i Partner Center: MPN admin, Account admin eller global admin (detta är en delad roll som administreras i Azure AD). 

1. Navigera till för hands versionen av appens registrerings Portal:  

1. Klicka på en app som du vill markera som utgivare verifierad och öppna bladet anpassning. 

1. Se till att appens utgivares domän är korrekt inställd. Den här domänen måste vara: 

    - Läggas till i Azure AD-klienten som en DNS-verifierad domän,  

    - Matcha domänen för den e-postadress som används under verifierings processen för ditt MPN-konto. 

1. Klicka på **Lägg till MPN-ID för att verifiera utgivaren** nära längst ned på sidan. 

1. Ange ditt **MPN-ID**. Detta MPN-ID måste vara för: 

    - Ett giltigt Microsoft Partner Network-konto som har slutfört verifierings processen.  

    - Partnerns globala konto (PGA) för din organisation. 

1. Klicka på **Verifiera och spara**. 

1. Vänta tills begäran har bearbetats, det kan ta några minuter. 

1. Om verifieringen lyckades stängs fönstret utgivar verifiering och du kommer tillbaka till bladet anpassning. Ett blått verifierat märke visas bredvid **visnings namnet**för din verifierade utgivare. 

1. Användare som uppmanas att godkänna din app kommer att börja se aktivitets ikonen snart när du har gått igenom processen, men det kan ta en stund innan detta kan replikeras i systemet. 

1. Testa den här funktionen genom att logga in på ditt program och se till att det verifierade aktivitets fältet visas på medgivande skärmen. Om du är inloggad som en användare som redan har beviljat medgivande till appen, kan du använda Frågeparametern *= medgivande* -Frågeparametern för att tvinga en bekräftelse. 

1. Upprepa den här processen vid behov för eventuella ytterligare appar som du vill att aktivitets ikonen ska visas för. Du kan använda Microsoft Graph för att göra detta snabbare i bulk, och PowerShell-cmdletar kommer snart att vara tillgängliga. Mer information finns i [göra Microsoft API Graph-anrop](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) . 

Klart! Berätta för oss om du har feedback om processen, resultatet eller funktionen i allmänhet. 

## <a name="next-steps"></a>Nästa steg
Läs [fel söknings informationen](troubleshoot-publisher-verification.md)om du stöter på problem.