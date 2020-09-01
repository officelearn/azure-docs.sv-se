---
title: Konfigurera en instans och autentisering (portalen)
titleSuffix: Azure Digital Twins
description: Se så här konfigurerar du en instans av tjänsten Azure Digitals dubbla tjänster med hjälp av Azure Portal
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 300a11c36b11230d2bd75534f152a0a5955743ed
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181805"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Konfigurera en digital Azure-instans och autentisering (portal)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Den här artikeln beskriver steg för steg hur du **konfigurerar en ny Azure Digital-instansen**, inklusive hur du skapar instansen och konfigurerar autentisering. När du har slutfört den här artikeln har du en Azure Digital-instansen som är redo att starta programmering mot.

Den här versionen av den här artikeln går igenom de här stegen manuellt, en i taget, med hjälp av Azure Portal. Azure-portalen är en webbaserad, enhetlig konsol som ger ett alternativ till kommandoradsverktyg.
* Om du vill gå igenom dessa steg manuellt med hjälp av CLI, se CLI-versionen av den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (CLI)*](how-to-set-up-instance-cli.md).
* Om du vill köra en automatiserad installation med hjälp av ett skript exempel för distribution, se den skript version som beskrivs i den här artikeln: [*anvisningar: Konfigurera en instans och autentisering (skript)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Skapa Azure Digitals-instansen

I det här avsnittet ska du **skapa en ny instans av Azure Digitals dubbla** med [Azure Portal](https://ms.portal.azure.com/). Navigera till portalen och logga in med dina autentiseringsuppgifter.

Börja med att välja _skapa en resurs_ på Start sidan för Azure-tjänster en gång i portalen.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Välj Skapa en resurs från start sidan för Azure Portal":::

Sök efter *Azure Digitals dubbla* i sökrutan och välj tjänsten **Azure Digitals (för hands version)** från resultaten. Välj knappen _skapa_ för att skapa en ny instans av tjänsten.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Välj "skapa" från sidan Azure Digitals dubblare tjänst":::

Fyll i värdena som visas nedan på sidan *skapa resurs* :
* **Prenumeration**: den Azure-prenumeration du använder
  - **Resurs grupp**: en resurs grupp där instansen ska distribueras. Om du inte redan har en befintlig resurs grupp i åtanke kan du skapa en här genom att välja länken *Skapa ny* och ange ett namn för en ny resurs grupp
* **Plats**: en Azure Digital-aktiverad region för distributionen. Mer information om regional support finns i [*Azure-produkter som är tillgängliga per region (Azure Digitals)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Resurs namn**: ett namn för din Azure Digital-instansen. Namnet på den nya instansen måste vara unikt inom regionen för din prenumeration (vilket innebär att om din prenumeration har en annan Azure Digital-instans i den region som redan använder det namn du väljer, blir du ombedd att välja ett annat namn).

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Fylla i de beskrivna värdena för att skapa en Azure Digital-resurs med dubbla resurser":::

När du är färdig väljer du _Granska + skapa_. Då kommer du till en sammanfattnings sida där du kan granska instans informationen som du har angett och trycka på _skapa_. 

### <a name="verify-success-and-collect-important-values"></a>Verifiera lyckade och samla in viktiga värden

När du har överfört *skapa*kan du visa statusen för din instanss distribution i dina Azure-meddelanden i portalens ikon fält. Meddelandet anger när distributionen har slutförts och du kommer att kunna välja knappen _gå till resurs_ för att visa din skapade instans.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Vy över Azure-meddelanden som visar en lyckad distribution och markerar knappen gå till resurs":::

Alternativt, om distributionen Miss lyckas, indikerar meddelandet varför. Observera råd från fel meddelandet och försök att skapa instansen igen.

>[!TIP]
>När du har skapat din instans kan du när som helst gå tillbaka till sidan genom att söka efter namnet på din instans i Azure Portal Sök fältet.

På *översikts* sidan för instansen noterar du *namnet*, *resurs gruppen*och *värd namnet*. Detta är alla viktiga värden som du kan behöva när du fortsätter att arbeta med din Azure Digital-instansen. Om andra användare kommer att program mera mot instansen bör du dela dessa värden med dem.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Markera viktiga värden från instansens översikts sida":::

Nu har du en Azure Digital-instansen som är redo att sätta igång. Sedan ger du rätt Azure-användare behörighet att hantera den.

## <a name="set-up-user-access-permissions"></a>Konfigurera användar åtkomst behörigheter

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Öppna först sidan för din Azure Digital-instansen i Azure Portal. Från instansens meny väljer du *åtkomst kontroll (IAM)*. Välj knappen  *Lägg till* under *Lägg till en roll tilldelning*.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Välja att lägga till en roll tilldelning från sidan åtkomst kontroll (IAM)":::

På följande sida för *Lägg till roll tilldelning* fyller du i värdena (måste slutföras av en användare med [tillräckliga behörigheter](#prerequisites-permission-requirements) i Azure-prenumerationen):
* **Roll**: Välj *Azure Digitals flätade ägare (för hands version)* från List menyn
* **Tilldela åtkomst till**: Välj *Azure AD-användare, grupp eller tjänstens huvud namn* i list menyn
* **Välj**: Sök efter namnet eller e-postadressen för den användare som ska tilldelas. När du väljer resultatet visas användaren i avsnittet *markerade medlemmar* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Fylla i listade fält i dialog rutan Lägg till roll tilldelning":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Tryck på knappen *Spara* när du är klar med att ange informationen.

### <a name="verify-success"></a>Verifieringen lyckades

Du kan visa roll tilldelningen som du har angett under *åtkomst kontroll (IAM) > roll tilldelningar*. Användaren ska visas i listan med en roll av *Azure Digitals flätade ägare (för hands version)*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vy över roll tilldelningarna för en digital Azure-instans i Azure Portal":::

Nu har du en Azure Digital-instansen som är redo att gå och har tilldelats behörighet att hantera den. Sedan konfigurerar du behörigheter för en klient-app för att komma åt den.

## <a name="set-up-access-permissions-for-client-applications"></a>Konfigurera åtkomst behörigheter för klient program

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Börja med att navigera till [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) i Azure Portal (du kan använda den här länken eller hitta den med Portal Sök fältet). Välj *Appregistreringar* på menyn tjänst och sedan *+ ny registrering*.

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Vy av sidan Azure AD-tjänst i Azure Portal, markera meny alternativet "Appregistreringar" och knappen "+ ny registrering"":::

På sidan *Registrera en program* som följer fyller du i de begärda värdena:
* **Namn**: ett visnings namn för Azure AD-program som ska associeras med registreringen
* **Konto typer som stöds**: Välj *konton endast i den här organisations katalogen (endast standard katalog-en klient)*
* **Omdirigerings-URI**: en *Azure AD Application svars-URL* för Azure AD-programmet. Lägg till en *offentlig klient/ursprunglig (mobile & Desktop)* URI för `http://localhost` .

Tryck på knappen *Registrera* när du är färdig.

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="Vy av sidan Registrera ett program med de beskrivna värdena ifyllda i":::

När registreringen är färdig kommer portalen att omdirigera dig till sidan med information.

### <a name="provide-azure-digital-twins-api-permission"></a>Ge Azure Digitals dubbla API-behörighet

Konfigurera sedan den app-registrering som du har skapat med baseline-behörigheter till Azures digitala dubbla API: er.

Välj *API-behörigheter* på menyn på Portal sidan för din app-registrering. På sidan följande behörigheter trycker du på knappen *+ Lägg till en behörighet* .

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Vy av appens registrering i Azure Portal, markera meny alternativet API-behörigheter och knappen + Lägg till en behörighet":::

På sidan *begär API-behörigheter* som följer växlar du till fliken *API: er som min organisation använder* och söker efter *Azure Digital-dubbla*. Välj *Azure Digitals flätar* från Sök resultaten om du vill fortsätta med att tilldela behörigheter för Azures digitala dubbla API: er.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Vy av Sök resultatet för sidan begär API-behörigheter visar Azure Digital-sidor":::

>[!NOTE]
> Om du har en befintlig Azure Digital-instansen från den tidigare offentliga för hands versionen av tjänsten (före 2020) som fortfarande finns i din prenumeration måste du leta efter *Azure Smart Spaces-tjänst* i stället. Det här är ett äldre namn för samma uppsättning API: er och kommer inte att ändra din erfarenhet utanför det här steget.

Sedan väljer du vilka behörigheter som ska beviljas för dessa API: er. Expandera **Läs-(1)-** behörigheten och markera kryss rutan med texten *Read. Write* för att ge appen registrerings läsare och skrivar behörigheter.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="Vy av sidan begär API-behörigheter väljer Läs. Skriv-behörigheter för Azure Digitals dubbla API: er":::

Tryck på *Lägg till behörigheter* när du är färdig.

### <a name="verify-success"></a>Verifieringen lyckades

På sidan *API-behörigheter* kontrollerar du att det nu finns en post för digitaler i Azure som återspeglar Läs-/skriv behörigheter:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Portal visning av API-behörigheterna för Azure AD-appens registrering, med Läs-/skriv åtkomst för Azure digitala dubbla":::

Du kan också kontrol lera anslutningen till Azure Digitals dubbla i program registreringens *manifest.jspå*, som automatiskt uppdaterades med den digitala Azure-informations informationen när du lade till API-behörigheterna.

Om du vill göra det väljer du *manifest* på menyn för att visa appens registrerings manifest kod. Bläddra till slutet av kod fönstret och leta efter dessa fält under `requiredResourceAccess` . Värdena ska matcha dem i skärm bilden nedan:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Portal visning av manifestet för Azure AD-appens registrering. Kapslad under ' requiredResourceAccess ', ett ' resourceAppId '-värde för 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 och ett ' resourceAccess >-ID '-värde för 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>Samla in viktiga värden

Välj sedan *Översikt* på Meny raden för att se information om appens registrering:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Portal visning av viktiga värden för appens registrering":::

Anteckna *program* -ID och *katalog (klient)-ID: t* som **visas på sidan** . De här värdena kommer att behövas senare för att [autentisera en klient app mot de Azure Digitals dubbla API: er](how-to-authenticate-client.md). Om du inte är den person som ska skriva kod för sådana program bör du dela dessa värden med den person som ska vara.

### <a name="other-possible-steps-for-your-organization"></a>Andra möjliga steg för din organisation

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Nästa steg

Se hur du ansluter klient programmet till din instans genom att skriva klient appens autentiseringsnyckel:
* [*Instruktion: skriva kod för app-autentisering*](how-to-authenticate-client.md)