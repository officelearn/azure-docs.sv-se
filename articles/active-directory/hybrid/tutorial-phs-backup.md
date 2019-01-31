---
title: 'Självstudier:  Konfigurera PHS som säkerhetskopiering för AD FS i Azure AD Connect | Microsoft Docs'
description: Visar hur du aktiverar synkronisering av lösenords-hash som en säkerhetskopia och för AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 01/30/2019
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 7e7c9ca30659fd1e99989bb77406b653a8ed9e7f
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55303132"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Självstudier:  Konfigurera PHS som säkerhetskopiering för AD FS i Azure AD Connect

Självstudien vägleder dig genom att ställa in lösenordets hash-synkronisering som en säkerhetskopiering och redundans för AD FS.  Det här dokumentet visar också hur du aktiverar synkronisering av lösenords-hash som primär autentiseringsmetod om AD FS har misslyckades eller inte tillgänglig.

## <a name="prerequisites"></a>Förutsättningar
Den här självstudien bygger på den [självstudien: Federera en enda AD-skogsmiljö till molnet](tutorial-federation.md) och är en per förutsättning innan du försöker den här självstudien.  Om du inte har slutfört den här kursen kan du göra det innan du försöker stegen i det här dokumentet.

## <a name="enable-phs-in-azure-ad-connect"></a>Aktivera PHS i Azure AD Connect
Det första steget, nu när vi har en Azure AD Connect-miljö som använder federation, är att aktivera lösenordshashsynkronisering och låta Azure AD Connect att synkronisera hashvärdena.

Gör följande:

1.  Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  På sidan ytterligare uppgifter väljer **anpassa synkroniseringsalternativ** och klicka på **nästa**.
4.  Ange användarnamnet och lösenordet för din globala administratör.  Det här kontot har skapats [här](tutorial-federation.md#create-a-global-administrator-in-azure-ad) i föregående självstudie.
5.  På den **Anslut dina kataloger** klickar du på **nästa**.
6.  På den **domän och Organisationsenhet filtrering** klickar du på **nästa**.
7.  På den **valfria funktioner** kan du kontrollera **lösenordshashsynkronisering** och klicka på **nästa**.
![Välj](media/tutorial-phs-backup/backup1.png)</br>
8.  På den **redo att konfigurera** skärmen klickar du på **konfigurera**.
9.  När konfigurationen är klar klickar du på **avsluta**.
10. Klart!  Du är klar.  Synkronisering av lösenordshash utförs nu och kan användas som en säkerhetskopia om AD FS blir otillgänglig.

## <a name="switch-to-password-hash-synchronization"></a>Växla till synkronisering av lösenordshash
Nu ska vi visar hur du byter till synkronisering av lösenordshash, om du behöver.  Gör följande:

1. Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  Välj **ändra användarinloggning** och klicka på **nästa**.
![Ändra](media/tutorial-phs-backup/backup2.png)</br>
4.  Ange användarnamnet och lösenordet för din globala administratör.  Det här kontot har skapats [här](tutorial-federation.md#create-a-global-administrator-in-azure-ad) i föregående självstudie.
5.  På den **användarinloggning** väljer **Lösenordshashsynkronisering** och markera den **konvertera inte användarkonton** box.  
6.  Låt standardvärdet **aktivera enkel inloggning** markerats och klickar på **nästa**.
7.  På den **aktivera enkel inloggning** skärmen klickar du på **nästa**.
8.  På den **redo att konfigurera** klickar du på **konfigurera**.
9.  När konfigurationen är klar klickar du på **avsluta**.
10. Användare kan nu använda sina lösenord för att logga in på Azure och Azure services.

## <a name="test-signing-in-with-one-of-our-users"></a>Testa logga in med något av våra användare

1.  Bläddra till [http://myapps.microsoft.com](http://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya innehavaren.  Du måste logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.</br>
![Kontrollera](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="next-steps"></a>Nästa steg


- [Maskinvara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md)|
