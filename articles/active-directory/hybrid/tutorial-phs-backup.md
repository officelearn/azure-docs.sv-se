---
title: 'Självstudiekurs: Konfigurera PHS som backup för AD FS i Azure AD Connect | Microsoft-dokument'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "64918997"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Självstudiekurs: Konfigurera PHS som backup för AD FS i Azure AD Connect

Följande självstudie vägleder dig genom att ställa in synkronisering av lösenordshash som en säkerhetskopiering och redundans för AD FS.  Det här dokumentet visar också hur du aktiverar synkronisering av lösenordshash som primär autentiseringsmetod om AD FS har misslyckats eller är otillgängligt.

>[!NOTE] 
>Även om dessa steg vanligtvis utförs under nödsituationer eller avbrottssituationer, rekommenderar vi att du testar dessa steg och verifierar dina procedurer innan ett avbrott inträffar.

>[!NOTE]
>Om du inte har tillgång till Azure AD Connect-servern eller om servern inte har åtkomst till internet kan du kontakta [Microsoft Support](https://support.microsoft.com/en-us/contactus/) för att hjälpa till med ändringarna på Azure AD-sidan.

## <a name="prerequisites"></a>Krav
Den här självstudien bygger på [självstudien: Federera en enda AD-skogsmiljö till molnet](tutorial-federation.md) och är en nödvändig per-erforderlig innan du försöker den här självstudien.  Om du inte har slutfört den här kursen kan du göra det innan du provar att utföra stegen i det här dokumentet.

>[!IMPORTANT]
>Innan du byter till PHS bör du skapa en säkerhetskopia av din AD FS-miljö.  Detta kan göras med hjälp av [AD FS Rapid Restore Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

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
8.  Klicka på **Konfigurera**på skärmen **Klar att konfigurera** .
9.  När konfigurationen är klar klickar du på **Avsluta**.
10. Klart!  Du är klar.  Synkronisering av lösenordshash utförs nu och kan användas som en säkerhetskopia om AD FS blir otillgänglig.

## <a name="switch-to-password-hash-synchronization"></a>Växla till synkronisering av lösenordshash
Nu ska vi visa hur du byter till synkronisering av lösenordshash. Innan du börjar bör du fundera på under vilka villkor du ska göra växlingen. Växla inte av tillfälliga skäl, som ett nätverksavbrott, ett mindre AD FS-problem eller ett problem som påverkar en delmängd av dina användare. Om du vill växla eftersom det tar för lång tid att lösa problemet kan du göra följande:

> [!IMPORTANT]
> Tänk på att det tar lite tid innan lösenordsharna synkroniseras med Azure AD.  Det innebär att det kan ta upp 3 timmar innan synkroniseringarna slutförs och innan du kan börja autentisera med hjälp av lösenordshashar.

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

1. Bläddra till[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klient.  Du måste logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.</br>
   ![Verifiera](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Växla tillbaka till federationen
Nu ska vi visa dig hur du byter tillbaka till federationen.  Gör så här:

1.  Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  Välj **Ändra användarinloggning** och klicka på **Nästa**.
4.  Ange användarnamnet och lösenordet för din globala administratör.  Detta är det konto som skapades [här](tutorial-federation.md#create-a-global-administrator-in-azure-ad) i föregående handledning.
5.  På skärmen **Användares inloggning** väljer du **Federation med AD FS** och klickar på **Nästa**.  
6. På sidan med inloggningsuppgifterna för domänadministratören anger du användarnamnet contoso\Administrator och lösenordet och klickar på **Nästa.**
7. Klicka på **Nästa**på AD FS-servergruppens skärm .
8. På **domänskärmen för Azure AD** väljer du domänen i listrutan och klickar på **Nästa**.
9. Klicka på **Konfigurera** på skärmen **Klart att konfigurera**.
10. När konfigurationen är klar klickar du på **Nästa**.
![Konfigurera](media/tutorial-phs-backup/backup4.png)</br>
11. Klicka på **Verifiera**på skärmen **Verifiera federationsanslutning.**  Du kan behöva konfigurera DNS-poster (lägg till A- och AAAA-poster) för att detta ska kunna slutföras.
![Verifiera](media/tutorial-phs-backup/backup5.png)</br>
12. Klicka på **Avsluta**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Återställa AD FS- och Azure-förtroendet
Nu måste vi återställa förtroendet mellan AD FS och Azure.

1.  Dubbelklicka på ikonen för Azure AD Connect som skapades på skrivbordet
2.  Klicka på **Konfigurera**.
3.  Välj **Hantera federation** och klicka på **Nästa**.
4.  Välj **Återställ Azure AD-förtroende** och klicka på **Nästa**.
![Återställ](media/tutorial-phs-backup/backup6.png)</br>
5.  På skärmen **Anslut till Azure AD** anger du användarnamn och lösenord för din globala administratör.
6.  På skärmen **Anslut till AD FS** anger du användarnamn och lösenord för contoso\Administratören och klickar på **Nästa.**
7.  Klicka på **Nästa**på skärmen **Certifikat** .

## <a name="test-signing-in-with-one-of-our-users"></a>Testa att logga in med någon av våra användare

1.  Bläddra till[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användarkonto som har skapats i vår nya klientorganisation.  Du behöver logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.
![Verifiera](media/tutorial-password-hash-sync/verify1.png)

Du har nu har installerat en hybrididentitetsmiljö som du kan använda för att testa och bekanta dig med allt som Azure har att erbjuda.

## <a name="next-steps"></a>Nästa steg


- [Hårdvara och förutsättningar](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md)
