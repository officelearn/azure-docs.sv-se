---
title: "Azure AD: Självbetjäningsregistrering för lösenordsåterställning | Microsoft Docs"
description: "Registrera autentiseringsdata för lösenordsåterställning med självbetjäning"
services: active-directory
keywords: Active directory password management, password management, Azure AD self service password reset, SSPR
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 0f10ad4904b10a9554e949e77f0192edcb8f624f
ms.lasthandoff: 04/12/2017


---
# <a name="register-for-self-service-password-reset"></a>Registrera för återställning av lösenord med självbetjäning

Om din administratör har aktiverat detta kan du återställa ditt lösenord eller låsa upp ditt konto utan att kontakta supporten med lösenordsåterställning med självbetjäning (SSPR). Innan du kan använda den här funktionen, måste du registrera autentiseringsmetoder eller bekräfta de fördefinierade autentiseringsmetoderna som administratören har fyllt i.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrera eller bekräfta autentiseringsdata med SSPR

1. Öppna webbläsaren på din enhet och gå till [registreringssidan för återställning av lösenord](http://aka.ms/ssprsetup)
2. Ange ditt användarnamn och lösenord som du har fått av administratören
3. Beroende på vilka alternativ som din administratör har godkänt visas ett eller flera av följande objekt som du ska konfigurera eller bekräfta att du vill använda för att återställa ditt lösenord
    * Arbetstelefon - det här alternativet kan bara anges av administratören
    * Telefon för autentisering – ange ett annat telefonnummer som sdu har tillgång till, till exempel en mobiltelefon som kan ta emot en SMS eller samtal (administratören kan fylla det åt dig med ditt mobiltelefonnummer om de redan har din behörighet att använda denna information)
    * E-post för autentisering - Ange en alternativ e-postadress som du kan komma åt utan lösenordet som du behöver återställa
    * Säkerhetsfrågor - Det här alternativet visar lista med frågor som din administratör har godkänt. Du kan inte använda samma svar för mer än en fråga.
4. Ange och kontrollera informationen som din administratör begär. Om flera alternativ är tillgängliga rekommenderar vi att du registrerar flera metoder för att maximal flexibilitet när en annan metod är inte tillgänglig (exempel vid resa och du inte har tillgång till din arbetstelefon)

    ![Registrera autentiseringsmetoder och klicka på slutför][Register]

5. När du är klar med steg 4 väljer du **Slutför**. Nu kan du använda självbetjäning för återställning av lösenord när du behöver framöver.

Om du anger data i Telefon för autentisering eller E-post för autentisering visas detta inte i den globala katalogen. Det är endast du och dina administratörer som kan se dessa data. Endast du kan se svaren på dina säkerhetsfrågor.

Administratörer kan kräva att du bekräftar dina autentiseringsmetoder efter en viss tid för att se till att du har registrerat lämpliga metoder.

## <a name="next-steps"></a>Nästa steg

* [Ändra ditt lösenord med självbetjäning](active-directory-passwords-update-your-own-password.md)
* [Registreringssida för lösenordsåterställning](http://aka.ms/ssprsetup)
* [Portal för lösenordsåterställning](https://passwordreset.microsoftonline.com/)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Registreringssidan för lösenordsåterställning visar registrerade metoder och knappen Slutför"

