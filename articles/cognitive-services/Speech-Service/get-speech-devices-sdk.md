---
title: Hämta Speech Devices SDK
description: 'Lär dig hur du får åtkomst till SDK: N för tal-enheter.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: f70b41cd7e3a7a6eddf32ae6ad024fa9ac040f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281790"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Hämta Cognitive Services tal enheter SDK

## <a name="requesting-access"></a>Begär åtkomst

Tal Devices SDK är en begränsad förhandsversion och kräver att du registreras i programmet. För närvarande föredrar Microsoft stora företag som kandidater för åtkomst till den här produkten.

Följ dessa steg för att få åtkomst till tal-SDK för enheter:

1. Gå till Microsoft Speech enheter SDK [fyllt i registreringsformuläret](https://aka.ms/sdsdk-signup).
1. Läs den [licensavtalet](speech-devices-sdk-license.md).
1. Om du samtycker till villkoren i licensavtalet, Välj ”Jag godkänner”.
1. Svara på frågorna i formuläret.
1. Skicka formuläret. 
1. Om din e-postadress inte redan är en del av Azure Active Directory kan du få ett e-postinbjudan se nedan vid godkännande. Om din e-postadress finns redan i Azure Active Directory, får du ett e-postmeddelande från Microsoft Speech-teamet vid godkännande och du kan gå vidare till [ladda ner tal enheter SDK](#download-the-speech-devices-sdk).

## <a name="approval-e-mail"></a>E-post för godkännande

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com> 
Subject: You're invited to the Microsoft organization 
```

![e-postmeddelande](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>Godkänna åtkomst
Utför stegen nedan för att ansluta till Azure Active Directory med den e-postadress som du angav under registreringen. Den här processen ger dig åtkomst till tal Devices SDK [hämtningswebbplats](https://shares.datatransfer.microsoft.com/).

1. Klicka på **börjar** i e-postmeddelandet du fick. Om din organisation är redan en Office 365-kund kan du uppmanas att logga in och kan gå vidare till steg 8.

2. Klicka på **nästa** i lanserade webbläsarfönstret.

    ![autentisering-fönstret](media/speech-devices-sdk/get-sdk-2.png)

3. Skapa ett Microsoft-konto om du inte redan har ett. Ange samma e-postadressen som du har fått e-postinbjudan i steg 6 ovan.

    ![Skapa Microsoft-konto](media/speech-devices-sdk/get-sdk-3.png)

4. Klicka på **nästa** att skapa ett lösenord.

5. När du uppmanas att verifiera din e-post, tillbaka till din inkorg för att få verifieringskoden som skickas till dig.
 
7. Klistra in eller ange säkerhetskoden från e-postmeddelandet i dialogrutan. I det här exemplet är det ”8406”. Klicka sedan på **Nästa**.

    ![verifiera e-post](media/speech-devices-sdk/get-sdk-6.png)
 
8. Du har bekräftat att din e-postadress (från steg 6) är nu en del av Azure Active Directory när du ser åtkomst till panelen programmet i webbläsarfönstret. Nu har du åtkomst till hämtningsplatsen tal Devices SDK.

## <a name="download-the-speech-devices-sdk"></a>Ladda ned SDK för tal-enheter

Gå till den [tal Devices SDK hämtningsplats](https://shares.datatransfer.microsoft.com/) och logga in med Account du skapade tidigare. Du kan nu hämta tal enheter SDK, associerade exempelkoden och referensmaterial genom att följa dessa steg.

![Hämtningsplats för SDK](media/speech-devices-sdk/get-sdk-7.png)

1. Hämta och installera Aspera Connect-verktyget när du uppmanas att göra det genom att webbläsaren.

    ![Hämta Aspera ansluta](media/speech-devices-sdk/get-sdk-8.png)
 
1. Klicka på **Ja** att växla till Aspera ansluta.

    ![Växla till Aspera ansluta](media/speech-devices-sdk/get-sdk-9.png)
 
1. Klicka på **Tillåt** att bekräfta hämtning av filer med Aspera Connect.

    ![ladda ned med Aspera Connect](media/speech-devices-sdk/get-sdk-10.png)
 
1. Stäng fönstret för Aspera ansluta överföringar när filerna har laddats ned.

    ![Aspera ansluta överföringar fönster](media/speech-devices-sdk/get-sdk-11.png)
 
Som standard filerna har hämtats till din **hämtar** mapp. Du kan logga ut från den här platsen nu. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med SDK för tal-enheter](speech-devices-sdk-qsg.md)
