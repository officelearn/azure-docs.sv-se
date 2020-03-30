---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 41f2d4540f665137d34d262546cdc1a2edfbae3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77608722"
---
## <a name="prerequisites"></a>Krav
Innan du skriver CDN-hanteringskod måste du göra några förberedelser för att koden ska kunna interagera med Azure Resource Manager. För att göra denna förberedelse måste du:

* Skapa en resursgrupp som innehåller CDN-profilen som skapats i den här självstudien
* Konfigurera Azure Active Directory för att tillhandahålla autentisering för programmet
* Tillämpa behörigheter för resursgruppen så att endast behöriga användare från din Azure AD-klient kan interagera med CDN-profilen

### <a name="creating-the-resource-group"></a>Skapa resursgruppen
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Skapa en resurs**.
3. Sök efter **resursgrupp** och klicka på **Skapa**i fönstret Resursgrupp .

    ![Skapa en ny resursgrupp](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Namnge resursgruppen *CdnConsoleTutorial*.  Välj din prenumeration och välj en plats nära dig.  Om du vill kan du klicka på kryssrutan **Fäst på instrumentpanelen för att** fästa resursgruppen på instrumentpanelen i portalen.  Att fästa gör det lättare att hitta senare.  När du har gjort dina val klickar du på **Skapa**.

    ![Namnge resursgruppen](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. När resursgruppen har skapats kan du hitta den genom att klicka på **Bläddra**och sedan **resursgrupper.**  Öppna den genom att klicka på resursgruppen.  Anteckna ditt **prenumerations-ID**. Vi behöver det senare.

    ![Namnge resursgruppen](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Skapa Azure AD-programmet och tillämpa behörigheter
Det finns två metoder för appautentisering med Azure Active Directory: Enskilda användare eller ett tjänsthuvudnamn. Ett tjänsthuvudnamn liknar ett tjänstkonto i Windows.  I stället för att bevilja en viss användarbehörighet för att interagera med CDN-profilerna beviljas i stället behörigheter till tjänstens huvudnamn.  Tjänsthuvudnamn används vanligtvis för automatiserade, icke-interaktiva processer.  Även om den här självstudien skriver en interaktiv konsolapp fokuserar vi på tjänstens huvudmetod.

Skapa ett tjänsthuvudnamn består av flera steg, inklusive att skapa ett Azure Active Directory-program.  För att skapa det, vi kommer att [följa den här guiden](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Var noga med att följa alla steg i den [länkade handledningen](../articles/active-directory/develop/howto-create-service-principal-portal.md).  Det är *viktigt* att du slutför det exakt som beskrivs.  Se till att notera ditt **klient-ID,** **klientdomännamn** (vanligen en *.onmicrosoft.com-domän* om du inte har angett en anpassad domän), **klient-ID**och **klientautentiseringsnyckel**, eftersom vi behöver den här informationen senare.  Var noga med att skydda **klient-ID** och **klientautentiseringsnyckel,** eftersom dessa autentiseringsuppgifter kan användas av vem som helst för att utföra åtgärder som tjänstens huvudnamn.
>
> När du kommer till steget Konfigurera program för flera innehavare väljer du **Nej**.
>
> När du kommer till steget [Tilldela programmet till en roll](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)använder du resursgruppen som skapats tidigare, *CdnConsoleTutorial*, men i stället för **rollen Läsare** tilldelar du rollen **CDN-profildeltagare.**  När du har tilldelat programmet rollen **CDN-profildeltagare** i resursgruppen går du tillbaka till den här självstudien. 
>
>

När du har skapat tjänstens huvudnamn och tilldelat rollen **CDN-profildeltagare** bör bladet **Användare** för resursgruppen se ut ungefär som följande bild.

![Bladet Användare](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktiv användarautentisering
Om du i stället för ett tjänsthuvudnamn hellre vill ha interaktiv individuell användarautentisering liknar processen för ett huvudnamn för en tjänst.  I själva verket måste du följa samma förfarande, men gör några mindre ändringar.

> [!IMPORTANT]
> Följ bara de här nästa stegen om du väljer att använda individuell användarautentisering i stället för ett huvudnamn för tjänsten.
>
>

1. När du skapar programmet väljer du **Native-programmet**i stället för **webbprogram**.

    ![Internt program](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. På nästa sida uppmanas du att omdirigera **URI**.  URI kommer inte att valideras, men kom ihåg vad du angav. Du behöver det senare.
3. Du behöver inte skapa en **klientautentiseringsnyckel**.
4. I stället för att tilldela ett tjänsthuvudnamn till rollen **CDN-profildeltagare** kommer vi att tilldela enskilda användare eller grupper.  I det här exemplet kan du se att jag har tilldelat *CDN Demo-användare* till **rollen CDN-profildeltagare.**  

    ![Individuell användaråtkomst](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
