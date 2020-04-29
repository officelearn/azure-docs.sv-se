---
title: Värdbaserad testen het | Azure Marketplace
description: Så här konfigurerar du en underhålls värd för en Marketplace-test enhet
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278271"
---
# <a name="hosted-test-drive"></a>Värdbaserad Test Drive

En värdbaserad testen het tar bort komplexiteten i installationen av Microsoft som värd och underhåller den tjänst som utför den användar etablering och avetableringen av test enheten. Den här artikeln är för utgivare som har sitt erbjudande på AppSource eller skapar en ny och vill erbjuda en värdbaserad testen het som ansluter med en Dynamics 365 för kund engagemang, Dynamics 365 för finans-och drifts-eller Dynamics 365 Business Central-instansen.

## <a name="how-to-publish-a-test-drive"></a>Publicera en testen het

Navigera till befintligt erbjudande eller skapa ett nytt erbjudande.

Välj alternativet testa enhet på menyn på sidan.

Välj \'ja\' för \'alternativet för att aktivera\' en testen het.

Ange följande fält i avsnittet \'information.\'

- **Beskrivning**: Ange en översikt över test enheten. Den här texten visas för användaren medan test enheten håller på att tillhandahållas. Det här fältet stöder HTML om du vill tillhandahålla formaterat innehåll.
- **Användar handbok**: överför en detaljerad Användar handbok (fil av typen. pdf) som hjälper användarna att förstå hur de använder din app.
- **Prov Drive demo video**: Ladda upp en video som demonstrerar din app.

Bevilja AppSource-behörighet för att etablera och avetablera användare av test enheter i din klient med hjälp av anvisningarna som finns [här](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

I det \'här steget ska du generera Azure AD App-ID\' : \'t och\' Azure AD App nyckel värden som anges nedan.

Ange följande fält i avsnittet \'teknisk konfiguration:\'

- **Typ av test enhet**: Välj \'Microsoft-värd (exempel: Dynamics 365 för kund engagemang). Detta anger att Microsoft ska vara värd för och underhålla den tjänst som utför den användar etablering och avetableringen av test enheten.
- **Maximalt antal samtidiga test enheter**: Ange det här fältet till antalet samtidiga användare som kan ha en aktiv testen het vid en viss tidpunkt. Varje användare kommer att använda en Dynamics-licens medan deras test enhet är aktiv, så du måste se till att du har minst så många Dynamics-licenser som är tillgängliga för användare av test enheter. Rekommenderat värde 3-5.
- **Test enhetens varaktighet (timmar)**: Ange det här fältet till antalet timmar som användarnas test enhet aktive ras för. Efter så här många timmar kommer användaren att avetableras från din klient. Rekommenderat värde på 2-24 timmar beroende på appens komplexitet. Användaren kan alltid begära en annan testen het om de kör tids gränsen och vill komma åt test enheten igen.
- **Instans-URL**: Ange en URL som användaren av test enheten kommer att gå från första gången till när de startar test enheten. Detta är vanligt vis URL: en för din Dynamics 365-instans som har din app och exempel data installerade på. Exempel värde: https:\//testdrive.CRM.Dynamics.com
- **ID för Azure AD-klient organisation**: Ange ID för Azure-klienten för din Dynamics 365-instans. Om du vill hämta det här värdet loggar du in på \'Azure Portal\'  - \> och navigerar till Azure Active Directory Välj\> egenskaper från meny bladet – kopiera katalog-ID: t. Exempel värde: 72f988bf-86f1-41AF-91ab-2d7cd0111234
- **Azure AD App ID**: id för Azure AD app som du skapade i steg 7. \ exempel värde: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD App nyckel**: hemlighet för Azure AD app som skapades i steg 7. \ exempel värde: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Namn på Azure AD-klient organisation**: Ange namnet på Azure-klienten för din Dynamics 365-instans. Använd formatet \<tenantname. \>onmicrosoft.com. Exempel värde: testdrive.onmicrosoft.com
- **Instans webb-API-URL**: Ange URL: en för webb-API för din Dynamics 365-instans. Du kan hämta det här värdet genom att logga in på Microsoft Dynamics 365-instansen och\> navigera för\> att ställa in\> – anpassning-utvecklarresurser-resurser – instans webb-API (kopiera denna URL). Exempel värde: https:\//testdrive.CRM.Dynamics.com/API/data/v9.0
- **Rollnamn**: Ange namnet på den anpassade säkerhets rollen Dynamics 365 som du har skapat för test enheten. Detta är den roll som ska tilldelas användare under deras test enhet. Exempel värde: testdriverole

## <a name="next-steps"></a>Nästa steg

När du är klar med **publiceringen** av erbjudandet har du en **förhands granskning** av ditt erbjudande när din app har klarat certifieringen. Starta en testen het i användar gränssnittet och kontrol lera att test enheterna körs på rätt sätt. När du känner dig bekvämt med ditt för hands erbjudande är det dags att **gå live!**
