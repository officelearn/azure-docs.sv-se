---
title: Logga in på förhandsversionen av Azure-anteckningsböcker
description: Konfigurera ditt användarkonto för Azure Notebooks med ett Microsoft-konto eller ett arbets-/skolkonto.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 9401a9e483f3f8db5af827f53937cb0f01b27a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646304"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Ditt användarkonto för förhandsversionen av Azure Notebooks

Du kan använda Azure Notebooks med eller utan att logga in med ett användarkonto:

- Utan att logga in kan du skapa och köra anteckningsböcker men inte behålla anteckningsböcker eller datafiler som en del av projekt. Användare som får en länk till en Azure Notebook, till exempel, kan njuta av anteckningsboken utan att behöva logga in.
- När azure-anteckningsböcker är inloggade behåller de alla dina projekt med ditt konto. Inloggade användare har också ett användar-ID som gör att de kan dela sina projekt och anteckningsböcker med andra.
- När kontot som används för Azure-anteckningsböcker också är associerat med en Azure-prenumeration får du ytterligare fördelar som att köra anteckningsböcker på mer kraftfulla servrar, skapa privata anteckningsböcker och bevilja behörigheter till anteckningsböcker till enskilda användare.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Att logga in på Azure Notebooks kräver antingen ett Microsoft-konto eller ett "Arbete eller skola"-konto. Du uppmanas att ange ditt konto när du väljer kommandot **Logga in** längst upp till höger på sidan Anteckningsböcker:

![Kommandot Logga in för Azure-anteckningsböcker](media/accounts/sign-in-command.png)

Allt arbete du gör i Azure Notebooks är kopplat till kontot du använder för att logga in. Varje konto måste också ha ett unikt användar-ID i din [användarprofil](azure-notebooks-user-profile.md). Därför kan du logga in på Azure Notebooks med olika konton om du behöver underhålla separata grupper av projekt och separata identiteter. Varje medlem i ett datascience-team kan till exempel ha båda enskilda konton tillsammans som ett delat gruppkonto som de använder för att presentera sitt arbete för personer utanför företaget. Lärare kan på samma sätt behålla ett konto för sin undervisningsroll som skiljer sig från ett konto som används i extern rådgivning eller arbete med öppen källkod.

## <a name="microsoft-accounts"></a>Microsoft-konton

Microsoft-konton används för att logga in på valfritt antal Microsoft-produkter och -tjänster, till exempel Windows, Azure, outlook.com, OneDrive och Xbox Live. Om du använder någon av dessa tjänster är det troligt att du redan har ett Microsoft-konto som du kan använda med Azure Notebooks.

Om du är osäker väljer du kommandot **Skapa en** i kontoprompten. Du kan skapa ett nytt Microsoft-konto med valfri e-postadress från vilken leverantör som helst.

![Kommando för att skapa ett nytt Microsoft-konto](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Om du försöker skapa ett nytt konto med en e-postadress som redan har ett konto kopplat till det kan du se meddelandet "Du kan inte registrera dig här med en e-postadress för arbete eller skola. Använd ett personligt e-postmeddelande, till exempel Gmail eller Yahoo!, eller skaffa ett nytt Outlook-e-postmeddelande." Försök i så fall att logga in med e-postadressen för arbetet utan att skapa ett nytt konto.

För underordnade konton blockeras åtkomsten till Azure-anteckningsböcker som standard. När du loggar in med ett barnkonto visas felet nedan:

![Fel vid försök att logga in med ett barnkonto: något gick fel, din förälder har blockerat åtkomst](media/accounts/child-account-error.png)

För att aktivera åtkomst måste en överordnad göra följande:

1. Besök `https://account.live.com/mk` och logga in med ett överordnat konto.
1. I avsnittet för barnet i fråga väljer du **Hantera barnets åtkomst till appar**från tredje part .
1. På nästa sida väljer du **Aktivera åtkomst**.
1. När det underordnade kontot används härnäst för att logga in på Azure Notebooks väljer du **Ja** i behörighetsprompten som visas.

> [!Warning]
> Om du aktiverar åtkomst till appar från tredje part för Azure-anteckningsböcker kan du också få åtkomst för alla andra appar från tredje part. Föräldrar uppmanas att använda diskretion när de aktiverar åtkomst och kanske vill övervaka sina barns aktivitet närmare.

## <a name="work-or-school-accounts"></a>Arbets- eller skolkonton

Ett arbets- eller skolkonto skapas av en organisations administratör för att en medlem i organisationen ska kunna komma åt Microsofts molntjänster som Office 365 och även som ett konto för att logga in på Windows på en domänansluten dator. Ett arbets- eller skolkonto använder vanligtvis en any-user@contoso.comorganisations-e-postadress, till exempel .

Att logga in på Azure-anteckningsböcker med ett arbets- eller skolkonto kan kräva administratörsgodkännande eftersom Azure Notebooks samlar in eller använder (men avslöjar inte) information som kontots e-postadress och användarens webbläsarinformation. (Webbläsardata används för att optimera funktioner enligt populär användning.)

Administratören för ett organisationskonto måste ge samtycke på användarnas vägnar om användarna är begränsade från att godkänna individuellt. I det här fallet ser användarna meddelandet "Du kan inte komma åt det här programmet":

![Meddelandet "Du kan inte komma åt det här programmet" när du använder ett arbets- eller skolkonto](media/accounts/consent-permissions-denied.png)

Om du vill ge ditt samtycke som administratör använder du [administratörens samtyckessida](https://notebooks.azure.com/account/adminConsent), som går igenom processen.

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Redigera din profil och ditt användar-ID](azure-notebooks-user-profile.md)
