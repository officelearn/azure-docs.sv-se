---
title: Logga in på Azure Notebooks för hands version
description: Konfigurera ditt användar konto för Azure Notebooks att använda ett Microsoft-konto eller ett arbets-eller skol konto.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 2a19803ec0a621b981fa9198766c58376ba50534
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125846"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Ditt användar konto för Azure Notebooks för hands version

Du kan använda Azure Notebooks med eller utan att logga in med ett användar konto:

- Utan att logga in kan du skapa och köra antecknings böcker, men du kan inte spara antecknings böcker eller datafiler som en del av projekten. Användare som får en länk till en Azure-anteckningsbok kan till exempel njuta av den bärbara datorn utan att behöva logga in.
- När du är inloggad bevarar Azure Notebooks alla dina projekt med ditt konto. Inloggade användare har även ett användar-ID som gör att de kan dela sina projekt och antecknings böcker med andra.
- När kontot som används för Azure Notebooks också är kopplat till en Azure-prenumeration får du ytterligare fördelar, till exempel att köra antecknings böcker på mer kraftfulla servrar, skapa privata antecknings böcker och beviljar behörigheter till antecknings böcker till enskilda användare.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Att logga in på Azure Notebooks kräver antingen ett Microsoft-konto eller ett arbets-eller skol konto. Du uppmanas att ange ditt konto när du väljer kommandot **Logga in** på den övre högra sidan av sidan antecknings böcker:

![Inloggnings kommando för Azure Notebooks](media/accounts/sign-in-command.png)

Allt arbete som du gör i Azure Notebooks associeras med det konto som du använder för att logga in. Varje konto måste också ha ett unikt användar-ID i din [användar profil](azure-notebooks-user-profile.md). Därför kan du logga in på Azure Notebooks med olika konton om du behöver underhålla separata uppsättningar med projekt och separata identiteter. Varje medlem i ett data vetenskaps team kan till exempel ha både enskilda konton, tillsammans med ett delat grupp konto som de använder för att presentera sitt arbete för personer utanför företaget. Lärare kan på samma sätt underhålla ett konto för deras undervisnings roll som skiljer sig från ett konto som används i extern rådgivning eller med öppen källkod.

## <a name="microsoft-accounts"></a>Microsoft-konton

Microsoft-konton används för att logga in på valfritt antal Microsoft-produkter och-tjänster, till exempel Windows, Azure, outlook.com, OneDrive och XBox Live. Om du använder någon av dessa tjänster är det troligt att du redan har ett Microsoft-konto som du kan använda med Azure Notebooks.

Om du är osäker väljer du kommandot **skapa ett** kommando i konto tolken. Du kan skapa en ny Microsoft-konto med valfri e-postadress från vilken provider som helst.

![Kommando för att skapa en ny Microsoft-konto](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Om du försöker skapa ett nytt konto med en e-postadress som redan har ett konto som är associerat med det kan du se meddelandet "du kan inte registrera dig här med en e-postadress till arbetet eller skolan. Använd ett personligt e-postmeddelande, till exempel Gmail eller Yahoo!, eller skaffa ett nytt Outlook-e-postmeddelande. " I så fall kan du försöka logga in med e-postadressen för arbetet utan att skapa ett nytt konto.

För underordnade konton blockeras åtkomsten till Azure Notebooks som standard. När du loggar in med ett underordnat konto visas felet som visas nedan:

![Fel vid försök att logga in med ett underordnat konto: något gick fel, din överordnade har blockerat åtkomst](media/accounts/child-account-error.png)

Om du vill aktivera åtkomst måste en överordnad utföra följande steg:

1. Besök `https://account.live.com/mk` och logga in med ett överordnat konto.
1. I avsnittet för underordnad i fråga väljer du **hantera det här barnets åtkomst till appar från tredje part**.
1. På nästa sida väljer du **Aktivera åtkomst**.
1. När det underordnade kontot nästa gång används för att logga in på Azure Notebooks väljer du **Ja** i den behörighets varning som visas.

> [!Warning]
> Att aktivera åtkomst till appar från tredje part för Azure Notebooks även att ge åtkomst till alla andra appar från tredje part. Föräldrar uppmanas att använda sig av försiktighet när de aktiverar åtkomst och kanske vill övervaka deras barns aktivitet mer nära.

## <a name="work-or-school-accounts"></a>Arbets- eller skolkonton

Ett arbets-eller skol konto skapas av en organisations administratör för att ge en medlem i organisationen åtkomst till Microsofts moln tjänster, till exempel Microsoft 365, och också som ett konto för att logga in på Windows på en domänansluten dator. Ett arbets-eller skol konto använder vanligt vis en organisations e-postadress, till exempel any-user@contoso.com .

Att logga in på Azure Notebooks med ett arbets-eller skol konto kan kräva administratörs medgivande eftersom Azure Notebooks samlar in eller använder (men inte avslöjar) information som kontots e-postadress och användarens webb läsar information. (Webb läsar data används för att optimera funktioner i enlighet med populär användning.)

Administratören för ett organisations konto måste ange ett medgivande för användarnas räkning om användarna är begränsade från att de behöver skickas individuellt. I det här fallet ser användarna meddelandet "du har inte åtkomst till det här programmet":

![Meddelandet "du har inte åtkomst till det här programmet" när du använder ett arbets-eller skol konto](media/accounts/consent-permissions-denied.png)

För att ge medgivande som administratör använder du [sidan administratörs medgivande](https://notebooks.azure.com/account/adminConsent), som vägleder dig genom processen.

## <a name="next-steps"></a>Nästa steg  

> [!div class="nextstepaction"]
> [Redigera din profil och ditt användar-ID](azure-notebooks-user-profile.md)
