---
title: Värdbaserade provkörning | Microsoft Docs
description: Hur du ställer in en Underhåll Marketplace finns testa
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5057c0c781cb9ec60ecde7dd3f4bf96089b902df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559797"
---
# <a name="hosted-test-drive"></a>Värdbaserad Test Drive

En värd Test Drive tar bort komplexiteten i installationen av Microsoft hosting och underhålla den tjänst som utför Test Drive användaren etablering och borttagning. Den här artikeln är avsedd för utgivare som har sitt erbjudande på AppSource eller skapar en ny och vill erbjuda en värdbaserad provkörning som ansluter med en Dynamics 365 för kundengagemang, Dynamics 365 för finans- och Operations- eller Dynamics 365 Business Central instans.

## <a name="how-to-publish-a-test-drive"></a>Så här publicerar du en Test Drive

Gå till befintliga erbjudandet eller skapa ett nytt erbjudande.

Välj alternativet Test Drive på menyn på klientsidan.

Välj \'Ja\' för \'aktivera en Test Drive\' alternativet.

Ange följande fält i den \'information\' avsnittet.

- **Beskrivning**: Ger en översikt över din provkörning. Den här texten visas för användaren när Test Drive etableras. Det här fältet stöder HTML om du vill ge formaterat innehåll.
- **Användarhandbok**: Ladda upp en detaljerad användarhandboken (fil av typen PDF) som hjälper dig Test Drive användarna att förstå hur du använder din App.
- **Testa enheten demonstrationsvideon**: Du kan också ladda upp en video som visar din App.

Grant AppSource behörighet att etablera och avetablera Test Drive-användare i din klient med hjälp av anvisningarna finns [här](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

I det här steget ska du generera den \'Azure AD App-Id\' och \'Azure AD App-nyckeln\' värden som anges nedan.

Ange följande fält i den \'teknisk konfiguration\' avsnittet:

- **Typ av Test Drive**: Välj \'Microsoft Hosted (exempel Dynamics 365 för kundengagemang)-alternativet. Detta anger att Microsoft ska vara värd för och underhålla den tjänst som utför Test Drive användaren etablering och borttagning.
- **Maxantal samtidiga Provkörningar**: Ange fältet till antalet samtidiga användare som kan ha en aktiv provkörning vid en given tidpunkt tid. Varje användare kommer att använda en Dynamics-licens när deras Test Drive är aktiv, så du måste se till att du har minst detta antal Dynamics licenser som är tillgängliga för Test Drive-användare. Rekommenderat värde från 3 till 5.
- **Driv varaktighet (timmar)**: Ange fältet till antalet timmar Test Drive ska aktiveras för användarna. Efter detta antal timmar, kommer användaren avetableras från din klient. Rekommenderat värde på 2 – 24 timmar beroende på komplexiteten för din App. Användaren kan begära ett annat Test Drive alltid om de körs utanför tid och vill använda Test Drive igen.
- **Instans-URL: en**: Ange en URL som användaren provkörning först öppnas när de startar Test Drive. Detta är vanligtvis URL: en för din Dynamics 365-instans som har din App och exempeldata som installeras på. Exempelvärde: https:\//testdrive.crm.dynamics.com
- **Azure AD-klient-ID**: Ange ID för Azure-klient för din Dynamics 365-instans. Att hämta det här värdet, logga in på Azure-portalen och gå till \'Azure Active Directory\'  - \> väljer Egenskaper från menyn bladet –\> kopiera Directory-ID. Exempelvärde: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD App ID**: ID för den Azure AD-App som du skapade i steg 7. \ exempelvärde: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD-Appnyckeln**: Hemligheten för den Azure AD-App som skapats i steg 7. \ exempelvärde: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Namn på Azure AD-klient**: Ange namnet på Azure-klient för din Dynamics 365-instans. Använd formatet \<tenantname.\> onmicrosoft.com. Exempelvärde: testdrive.onmicrosoft.com
- **Instans-URL: en för webb-API**: Ange URL: en för webb-API för din Dynamics 365-instans. Du kan hämta det här värdet genom att logga in på din Microsoft Dynamics 365-instans och navigera till inställning -\> anpassning -\> utvecklarresurser -\> instans webb-API (kopiera URL: en). Exempelvärde: https:\//testdrive.crm.dynamics.com/api/data/v9.0 
- **Rollnamnet**: Ange namnet på den anpassade Dynamics 365 säkerhetsrollen du har skapat för Test Drive. Det här är den roll som ska tilldelas användare under deras Test Drive. Exempelvärde: testdriverole

## <a name="next-steps"></a>Nästa steg

Redo när **publicera** ditt erbjudande, när din app har passerat-certifiering, har du en **förhandsversion** i ditt erbjudande. Starta en Test Drive i Användargränssnittet och kontrollera att din Provkörningar körs korrekt. När du känner dig bekväm med ditt erbjudande förhandsversion nu är det dags att **gå live!**
