---
title: Värd provkörning | Azure Marketplace
description: Så här konfigurerar du en Marketplace-värddator
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278271"
---
# <a name="hosted-test-drive"></a>Värdbaserad Test Drive

En värddator testenhet tar bort komplexiteten i installationen av Microsoft-värd och underhåller tjänsten som utför testenhetens användaretablering och avetablering. Den här artikeln är avsedd för utgivare som har sitt erbjudande på AppSource eller bygger en ny och vill erbjuda en värddator, som ansluter till en Dynamics 365 för Customer Engagement, Dynamics 365 for Finance and Operations eller Dynamics 365 Business Central Instans.

## <a name="how-to-publish-a-test-drive"></a>Så här publicerar du en provkörning

Navigera till befintliga erbjudanden eller skapa ett nytt erbjudande.

Välj alternativet Provkörning på sidomenyn.

Välj \'\' Ja \'för att\' aktivera ett provkörningsalternativ.

Ange följande fält \'i\' avsnittet Information.

- **Beskrivning**: Ge en översikt över din provkörning. Den här texten visas för användaren medan provkörningen etableras. Det här fältet stöder HTML om du vill tillhandahålla formaterat innehåll.
- **Användarhandbok:** Ladda upp en detaljerad bruksanvisning (fil av typen .pdf) som hjälper Test Drive-användare att förstå hur du använder din app.
- **Test Drive Demo Video:** Kan du ladda upp en video som visar din app.

Bevilja AppSource-behörighet att etablera och avetablera Test Drive-användare i din klientorganisation med hjälp av instruktionerna [här](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

I det här steget \'genererar du värdena \'Azure AD\' App ID\' och Azure AD App Key som nämns nedan.

Ange följande fält \'i\' avsnittet Teknisk konfiguration:

- **Typ av provkörning:** Välj \'Microsoft Hosted (exempel Dynamics 365 for Customer Engagement)' alternativ. Detta indikerar att Microsoft kommer att vara värd för och underhålla tjänsten som utför testenhetens användaretablering och avetablering.
- **Max samtidiga testenheter:** Ange det här fältet till antalet samtidiga användare som kan ha en aktiv provkörning vid en viss tidpunkt. Varje användare förbrukar en Dynamics-licens medan deras testenhet är aktiv, så du måste se till att du har minst så många Dynamics-licenser tillgängliga för Test Drive-användare. Rekommenderat värde på 3-5.
- **Provkörningslängd (timmar):** Ange det här fältet till antalet timmar som användarnas provkörning ska vara aktiv för. Efter så här många timmar kommer användaren att avetableras från din klient. Rekommenderat värde på 2-24 timmar beroende på komplexiteten i din app. Användaren kan alltid begära en annan provkörning om de får på tid och vill komma åt provkörningen igen.
- **Instans-URL:** Ange en URL som testenhetsanvändaren från början kommer att navigeras till när de startar provkörningen. Det här är vanligtvis url:en för din Dynamics 365-instans som har dina app- och exempeldata installerade på. Exempelvärde: https:\//testdrive.crm.dynamics.com
- **Azure AD-klient-ID:** Ange ID för Azure-klienten för din Dynamics 365-instans. Om du vill hämta det här \'värdet loggar\'  - \> du in på\> Azure Portal och navigerar till Azure Active Directory Select Properties från menybladet - Kopiera katalog-ID.To retrieve this value, login to Azure portal and navigate to Azure Active Directory Select Properties from menu blade - Copy the Directory ID. Exempel värde: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD-app-ID:** ID för Azure AD-appen som du skapade i steg 7.\ Exempelvärde: 53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD-appnyckel:** Hemlighet för Azure AD-appen som skapats i steg 7.\ Exempelvärde: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Azure AD-klientnamn:** Ange namnet på Azure-klienten för din Dynamics 365-instans. Använd formatet \<för klientnamnet. \>onmicrosoft.com. Exempelvärde: testdrive.onmicrosoft.com
- **Instans webb-API URL:** Ange webb-API URL för Din Dynamics 365-instans. Du kan hämta det här värdet genom att logga in på din\> Microsoft\> Dynamics 365-instans och navigera till Inställning - Anpassning - Utvecklarresurser -\> Instanswebb-API (Kopiera den här URL:en). Exempelvärde: https:\//testdrive.crm.dynamics.com/api/data/v9.0
- **Rollnamn**: Ange namnet på den anpassade Dynamics 365-säkerhetsroll som du har skapat för Test Drive. Det här är den roll som ska tilldelas användare under testenheten. Exempelvärde: testdriverole

## <a name="next-steps"></a>Nästa steg

När du är klar **med** ditt erbjudande, när din app har certifierats, får du en **förhandsgranskning** av ditt erbjudande. Starta en provkörning i användargränssnittet och kontrollera att testenheterna fungerar som de ska. När du känner dig bekväm med ditt förhandsgranskningserbjudande, är det nu dags att **gå live!**
