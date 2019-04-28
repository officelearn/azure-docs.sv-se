---
author: zhangmanling
ms.service: cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 7f80c8f1773cfeb8ddfb222d068a5c6571c2e5c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564237"
---
## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du skriver kod för CDN-hantering, måste du göra vissa förberedelser för att aktivera koden för att interagera med Azure Resource Manager. Om du vill göra dessa förberedelser, måste du:

* Skapa en resursgrupp som innehåller den CDN-profil som skapats i den här självstudien
* Konfigurera Azure Active Directory för att tillhandahålla autentisering för programmet
* Tillämpa behörigheter för resursgruppen så att endast behöriga användare från din Azure AD-klient kan interagera med CDN-profil

### <a name="creating-the-resource-group"></a>Skapa resursgruppen
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Skapa en resurs**.
3. Sök efter **resursgrupp** och klicka på i fönstret Resource group **skapa**.

    ![Skapa en ny resursgrupp.](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Ge resursgruppen namnet *CdnConsoleTutorial*.  Välj din prenumeration och välj en plats nära dig.  Om du vill kan du klicka på den **fäst på instrumentpanelen** kryssrutan för att fästa resursgruppen på instrumentpanelen i portalen.  Fästa gör det lättare att hitta senare.  När du har gjort dina val klickar du på **skapa**.

    ![Namngivning av resursgruppen.](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. När resursgruppen har skapats kan du inte har Fäst den på instrumentpanelen, kan du hitta den genom att klicka på **Bläddra**, sedan **resursgrupper**.  Klicka på resursgruppen för att öppna den.  Anteckna din **prenumerations-ID**. Vi behöver den senare.

    ![Namngivning av resursgruppen.](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Skapa Azure AD-programmet och tillämpa behörigheter
Det finns två metoder för app-autentisering med Azure Active Directory: Enskilda användare eller ett huvudnamn för tjänsten. Ett huvudnamn för tjänsten liknar ett tjänstkonto i Windows.  I stället för att bevilja behörigheter för att interagera med CDN-profiler för en viss användare, behörigheter i stället till tjänstens huvudnamn.  Tjänstens huvudnamn används vanligtvis för automatiserad, icke-interaktiv processer.  Även om den här självstudien skriver en interaktiv konsol-app, kan vi att fokusera på metoden som tjänstens huvudnamn.

Skapa ett huvudnamn för tjänsten består av flera steg, inklusive att skapa ett Azure Active Directory-program.  Om du vill skapa det vi kommer att [i den här kursen](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Se till att följa alla steg i den [länkade självstudien](../articles/active-directory/develop/howto-create-service-principal-portal.md).  Det är *viktiga* du slutför den exakt som beskrivs.  Se till att notera ditt **klient-ID**, **klientdomännamn** (vanligtvis en *. onmicrosoft.com* domän utan att ange en anpassad domän), **klient-ID** , och **klienten autentiseringsnyckeln**, som vi behöver den här informationen senare.  Var noga med att skydda din **klient-ID** och **klienten autentiseringsnyckeln**, enligt de här autentiseringsuppgifterna kan användas av vem som helst till att köra som tjänstens huvudnamn.
>
> När du kommer till steget med namnet konfigurera program för flera klienter, Välj **nr**.
>
> När du kommer till steget [tilldela programmet till en roll](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-the-application-to-a-role), använda resursgruppen som skapades tidigare, *CdnConsoleTutorial*, men i stället för den **läsare** roll, tilldela den **CDN-Profildeltagare** roll.  När du har tilldelat programmet den **CDN-Profildeltagare** -rollen på resursgruppen, gå tillbaka till den här självstudien. 
>
>

När du har skapat tjänstens huvudnamn och tilldelats den **CDN-Profildeltagare** rollen, de **användare** bladet för resursgruppen bör likna följande bild.

![Bladet användare](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktiv användarautentisering
Om du vill hellre ha interaktiv enskilda användarautentisering i stället för ett huvudnamn för tjänsten, är processen liknande den för ett huvudnamn för tjänsten.  I själva verket kan behöva du följer samma procedur men gör mindre ändringar.

> [!IMPORTANT]
> Följ bara dessa nästa steg om du väljer att använda enskilda användarautentisering i stället för ett huvudnamn för tjänsten.
>
>

1. När du skapar ditt program, i stället för **webbprogram**, Välj **programspecifik**.

    ![Internt program](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. På nästa sida uppmanas du en **omdirigerings-URI**.  URI: N kommer inte att verifiera, men kom ihåg vad du har angett. Du behöver den senare.
3. Behöver du inte skapa en **klienten autentiseringsnyckeln**.
4. I stället för att tilldela ett tjänstobjekt i **CDN-Profildeltagare** roll, vi kommer att tilldela enskilda användare eller grupper.  I det här exemplet kan du se att jag har tilldelat *CDN Demo-användare* till den **CDN-Profildeltagare** roll.  

    ![Enskilda användaråtkomst](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
