---
title: 'Självstudier:  Konfigurera PHS som säkerhetskopia för AD FS i Azure AD Connect | Microsoft Docs'
description: Visar hur du aktiverar synkronisering av lösenordshash som en säkerhetskopia och för AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2281bc451a5acf9e4e634a124161a3e8b0734deb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58090516"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Självstudier:  Konfigurera PHS som säkerhetskopia för AD FS i Azure AD Connect

Följande självstudie vägleder dig genom att ställa in synkronisering av lösenordshash som en säkerhetskopiering och redundans för AD FS.  Det här dokumentet visar också hur du aktiverar synkronisering av lösenordshash som primär autentiseringsmetod om AD FS har misslyckats eller är otillgängligt.

## <a name="prerequisites"></a>Förutsättningar
Den här självstudien bygger på [självstudien: Federera en enskild miljö för AD-skogar till molnet](tutorial-federation.md) och är en förutsättning innan du provar att utföra den här självstudien.  Om du inte har slutfört den här kursen kan du göra det innan du provar att utföra stegen i det här dokumentet.

## <a name="enable-phs-in-azure-ad-connect"></a>Aktivera PHS i Azure AD Connect
Det första steget, nu när vi har en Azure AD Connect-miljö som använder federation, är att aktivera lösenordshashsynkronisering och låta Azure AD Connect synkronisera hashvärdena.

Gör följande:

1.  Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  På sidan Ytterligare uppgifter väljer du **Anpassa synkroniseringsalternativ** och klickar sedan på **Nästa**.
4.  Ange användarnamnet och lösenordet för din globala administratör.  Det här kontot skapades [här](tutorial-federation.md#create-a-global-administrator-in-azure-ad) i föregående självstudie.
5.  På skärmen **Anslut dina kataloger** klickar du på **Nästa**.
6.  På skärmen **Domän- och organisationsenhetsfiltrering** klickar du på **Nästa**.
7.  På skärmen **Valfria funktioner** kan du markera **Synkronisering av lösenordshash** och klicka på **Nästa**.
![Välj](media/tutorial-phs-backup/backup1.png)</br>
8.  Klicka på **Konfigurera** på skärmen **Klart att konfigurera**.
9.  När konfigurationen är klar klickar du på **Avsluta**.
10. Klart!  Du är klar.  Synkronisering av lösenordshash utförs nu och kan användas som en säkerhetskopia om AD FS blir otillgänglig.

## <a name="switch-to-password-hash-synchronization"></a>Växla till synkronisering av lösenordshash
Nu ska vi visa hur du byter till synkronisering av lösenordshash. Innan du börjar bör du fundera på under vilka villkor du ska göra växlingen. Växla inte av tillfälliga skäl, som ett nätverksavbrott, ett mindre AD FS-problem eller ett problem som påverkar en delmängd av dina användare. Om du vill växla eftersom det tar för lång tid att lösa problemet kan du göra följande:

1. Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  Välj **Ändra användarinloggning** och klicka på **Nästa**.
![Ändra](media/tutorial-phs-backup/backup2.png)</br>
4.  Ange användarnamnet och lösenordet för din globala administratör.  Det här kontot skapades [här](tutorial-federation.md#create-a-global-administrator-in-azure-ad) i föregående självstudie.
5.  På skärmen **Användarinloggning** väljer du **Synkronisering av lösenordshash** och markerar rutan **Konvertera inte användarkonton**.  
6.  Låt standardinställningen **Aktivera enkel inloggning** vara kvar och klicka på **Nästa**.
7.  På skärmen **Aktivera enkel inloggning** klickar ni på **Nästa**.
8.  Klicka på **Konfigurera** på skärmen **Klart att konfigurera**.
9.  När konfigurationen är klar klickar du på **Avsluta**.
10. Användare kan nu använda sina lösenord för att logga in på Azure och Azure-tjänster.

## <a name="test-signing-in-with-one-of-our-users"></a>Testa att logga in med någon av våra användare

1. Bläddra till [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klient.  Du måste logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.</br>
   ![Verifiera](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="next-steps"></a>Nästa steg


- [Maskinvara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md)|
