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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ad7badfa44a006fd7e71d3b0e42ee95ac698d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918997"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Självstudier:  Konfigurera PHS som säkerhetskopia för AD FS i Azure AD Connect

Följande självstudie vägleder dig genom att ställa in synkronisering av lösenordshash som en säkerhetskopiering och redundans för AD FS.  Det här dokumentet visar också hur du aktiverar synkronisering av lösenordshash som primär autentiseringsmetod om AD FS har misslyckats eller är otillgängligt.

>[!NOTE] 
>Även om de här stegen utförs vanligtvis vid nödfall eller avbrott situationer, bör du testa de här stegen och verifiera dina procedurer innan ett eventuellt strömavbrott.

>[!NOTE]
>I händelse av att du har inte åtkomst till Azure AD Connect-servern eller servern har inte åtkomst till internet, kan du kontakta [Microsoft Support](https://support.microsoft.com/en-us/contactus/) som hjälper till med ändringarna till Azure AD-sida.

## <a name="prerequisites"></a>Nödvändiga komponenter
Den här självstudien bygger på [självstudien: Federera en enskild miljö för AD-skogar till molnet](tutorial-federation.md) och är en förutsättning innan du provar att utföra den här självstudien.  Om du inte har slutfört den här kursen kan du göra det innan du provar att utföra stegen i det här dokumentet.

>[!IMPORTANT]
>Innan du växlar till PHS bör du skapa en säkerhetskopia av din AD FS-miljön.  Detta kan göras med hjälp av den [AD FS snabb återställa verktyget](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

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

> [!IMPORTANT]
> Tänk på att det tar lite tid för lösenordet hashvärden ska synkroniseras med Azure AD.  Det innebär att det kan ta upp tre timmar för synkroniseringar slutförts och innan du börjar autentiserar med lösenords-hash.

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

## <a name="switch-back-to-federation"></a>Gå tillbaka till federation
Nu ska vi visar hur du växlar tillbaka till federation.  Om du vill göra detta måste du göra följande:

1.  Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  Välj **Ändra användarinloggning** och klicka på **Nästa**.
4.  Ange användarnamnet och lösenordet för din globala administratör.  Detta är det konto som har skapats [här](tutorial-federation.md#create-a-global-administrator-in-azure-ad) i föregående självstudie.
5.  På den **användarinloggning** väljer **Federation med AD FS** och klicka på **nästa**.  
6. På sidan med inloggningsuppgifterna för domänadministratören anger du användarnamnet contoso\Administrator och lösenordet och klickar på **Nästa.**
7. På skärmen för AD FS-servergruppen klickar du på **nästa**.
8. På den **Azure AD-domän** skärmen, väljer domänen från listrutan och klickar på **nästa**.
9. Klicka på **Konfigurera** på skärmen **Klart att konfigurera**.
10. När konfigurationen är klar klickar du på **nästa**.
![Konfigurera](media/tutorial-phs-backup/backup4.png)</br>
11. På den **verifiera federationsanslutning** klickar du på **Kontrollera**.  Du kan behöva konfigurera DNS-poster (lägga till poster för A och AAAA) för att detta ska slutföras.
![Verifiera](media/tutorial-phs-backup/backup5.png)</br>
12. Klicka på **avsluta**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Återställa AD FS och Azure förtroende
Nu ska vi återställa förtroendet mellan AD FS och Azure.

1.  Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  Välj **hantera Federation** och klicka på **nästa**.
4.  Välj **återställa Azure AD-förtroende** och klicka på **nästa**.
![Återställ](media/tutorial-phs-backup/backup6.png)</br>
5.  På den **Anslut till Azure AD** skärmen Ange användarnamnet och lösenordet för din globala administratör.
6.  På den **Anslut till AD FS** skärmen, ange contoso\Administrator användarnamn och lösenord och klicka på **nästa.**
7.  På den **certifikat** klickar du på **nästa**.

## <a name="test-signing-in-with-one-of-our-users"></a>Testa att logga in med någon av våra användare

1.  Bläddra till [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klientorganisation.  Du behöver logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.
![Verifiera](media/tutorial-password-hash-sync/verify1.png)

Du har nu har installerat en hybrididentitetsmiljö som du kan använda för att testa och bekanta dig med allt som Azure har att erbjuda.

## <a name="next-steps"></a>Nästa steg


- [Maskinvara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md)
