---
title: Hämta Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Speech-tjänsten fungerar med en mängd olika enheter och ljud datakällor. Nu kan dra du ditt talprogram till nästa nivå med matchande maskinvara och programvara. I den här artikeln lär du dig att få åtkomst till tal Devices SDK och börja utveckla.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 875240b779f05c1b13f71bf2133b9abfaa209239
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223365"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Hämta Cognitive Services tal enheter SDK

Tal Devices SDK är en begränsad förhandsversion och kräver att du registreras i programmet. För närvarande föredrar Microsoft stora företag som kandidater för åtkomst till den här produkten.

## <a name="request-access"></a>Begär åtkomst

Du får åtkomst till SDK: N för tal enheter:

1. Gå till Microsoft Speech enheter SDK [fyllt i registreringsformuläret](https://aka.ms/sdsdk-signup).
1. Läs den [licensavtalet](speech-devices-sdk-license.md).
1. Om du samtycker till villkoren i licensavtalet, markera **jag godkänner**.
1. Svara på frågorna i formuläret.
1. Skicka formuläret.
1. Om din e-postadress inte redan är en del av Azure Active Directory (AD Azure) får du ett e-postinbjudan som i följande exempel när du har godkänt för åtkomst. Om din e-postadress är redan i Azure AD, du får ett e-postmeddelande från Microsoft Speech-teamet när du har godkänt för åtkomst och du kan gå vidare till [ladda ner tal enheter SDK](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>E-post för godkännande

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com>
Subject: You're invited to the Microsoft organization
```

![e-postmeddelande](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Godkänna åtkomst

Utför följande steg för att ansluta till Azure AD med den e-postadress som du angav under registreringen. Den här processen ger dig åtkomst till tal Devices SDK [hämtningswebbplats](https://shares.datatransfer.microsoft.com/).

1. I e-postmeddelandet du fick, väljer **börjar**. Om din organisation är redan en Office 365-kund kan du uppmanas att logga in och du kan gå vidare till steg 8.

2. I webbläsarfönstret som öppnas väljer du **nästa**.

    ![autentisering-fönstret](media/speech-devices-sdk/get-sdk-2.png)

3. Skapa ett Microsoft-konto om du inte redan har ett. Ange samma e-postadressen som du har fått e-postinbjudan.

    ![Skapa ett Microsoft-konto](media/speech-devices-sdk/get-sdk-3.png)

4. Välj **nästa** att skapa ett lösenord.

5. Få verifieringskoden från e-postinbjudan du fick när du uppmanas att verifiera din e-post.

7. Klistra in eller ange säkerhetskoden från e-postmeddelandet i dialogrutan. I det här exemplet säkerhetskoden är **8406**. Välj **Nästa**.

    ![Verifiera e-post](media/speech-devices-sdk/get-sdk-6.png)

8. När du ser åtkomst till panelen programmet i webbläsaren har du bekräftat att din e-postadress är en del av Azure AD. Nu har du åtkomst till hämtningsplatsen tal Devices SDK.

## <a name="download-the-speech-devices-sdk"></a>Ladda ned SDK för tal-enheter

Gå till den [tal Devices SDK hämtningsplats](https://shares.datatransfer.microsoft.com/). Logga in med Microsoft-konto som du skapade tidigare.

![Hämtningsplats för SDK](media/speech-devices-sdk/get-sdk-7.png)

Ladda ned tal enheter SDK, som är associerade exempelkoden och referensmaterial:

1. Hämta och installera Aspera Connect-verktyget när du uppmanas i webbläsaren.

    ![Hämta Aspera ansluta](media/speech-devices-sdk/get-sdk-8.png)

1. Välj **Ja** att växla appar till Aspera ansluta.

    ![Växla till Aspera ansluta](media/speech-devices-sdk/get-sdk-9.png)

1. Välj **Tillåt** att bekräfta laddar ned filerna med hjälp av Aspera ansluta.

    ![Hämta med hjälp av Aspera ansluta](media/speech-devices-sdk/get-sdk-10.png)

1. Stäng fönstret Aspera ansluta överföringar när filerna har hämtats.

    ![Aspera ansluta överföringar fönster](media/speech-devices-sdk/get-sdk-11.png)

Som standard filerna laddas ned till din **hämtar** mapp. Du kan logga ut från den här platsen nu.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med SDK för tal-enheter](speech-devices-sdk-qsg.md)
