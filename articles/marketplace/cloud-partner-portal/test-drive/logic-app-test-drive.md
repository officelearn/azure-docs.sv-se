---
title: Logic App provkörning | Microsoft Docs
description: Beskriver hur du skapar sina Test Drive som ansluter med en Dynamics AX/CRM-instans eller någon annan resurs utöver bara Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 4fd946b53956509844ad0a9396575f1ee2450414
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61070463"
---
<a name="logic-app-test-drive"></a>Logic App-provkörning
====================

Den här artikeln är utgivare som har sitt erbjudande på AppSource och vill skapa sina Test Drive som ansluter med en Dynamics AX/CRM-instans eller någon annan resurs utöver bara Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Hur du skapar en Logic App Test Drive
-----------------------------------

Test Drive-dokumentationen för Logic App Test Drives håller fortfarande på GitHub för [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) och [Kundengagemang](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)går att läsa mer.

<a name="how-to-publish-a-test-drive"></a>Så här publicerar du en Test Drive
---------------------------

Nu när du har din provkörning som skapats beskriver i det här avsnittet var och en av de fält som krävs att publicera din provkörning.

![Aktivera funktionen provkörning](./media/azure-resource-manager-test-drive/howtopub1.png)

Fältet första och viktigaste är att ange om du vill testa med alla obligatoriska fält visas för att fylla. När du väljer **Nej,** formuläret blir inaktiverad och om du publicerar med Test Drive inaktiverat din provkörning tas bort från produktionen.

*Obs!* Om det finns några Provkörningar som aktivt används av användare, fortsätter dessa Provkörningar att köras tills sin session upphör att gälla.

### <a name="details"></a>Information

Nästa avsnitt för att fylla i är information om din provkörning erbjuder.

![Testa drivrutinsinformation](./media/azure-resource-manager-test-drive/howtopub2.png)

**Beskrivning –** *[obligatoriskt fält]* det här är där du skriver den huvudsakliga beskrivningen om vad som är på din provkörning. Kunden kommer här för att läsa vilka scenarier som kommer som täcker din provkörning om produkten. 

**Användaren manuellt –** *[obligatoriskt fält]* det här är detaljerad genomgång av din Test Drive-upplevelse. Kunden öppnas det och kan gå igenom exakt vad du vill ha dem att göra i hela deras Test Drive. Det är viktigt att det här innehållet är lätt att förstå och följ! (Måste vara en PDF-fil)

**Testa enheten demonstrationsvideon -** \[rekommenderas\] användarhandboken ett liknande sätt, är det bäst att inkludera en självstudievideo av din Test Drive-upplevelse. Kunden ska titta på det här tidigare eller under deras Test Drive och kan gå igenom exakt vad du vill ha dem att göra i hela deras Test Drive. Det är viktigt att det här innehållet är lätt att förstå och följ!

- **Namn på** -rubriken för videon
- **Länken** -måste vara en inbäddad URL från YouTube eller Vimeo. Exempel på hur du hämtar den inbäddade URL: en är nedan:
- **Miniatyr** -måste vara en avbildning av hög kvalitet (533 x 324) bildpunkter. Vi rekommenderar att ta en skärmbild av någon del av din Test Drive-upplevelse här.

Nedan visas hur fälten visas för din kund under deras Test Drive-upplevelse.

![Test Drive fält utseende](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Teknisk konfiguration

Nästa avsnitt för att fylla i kan du konfigurera din Test Drive Logikapp och definiera hur specifikt Test Drive instanser arbete.

![Test Drive teknisk konfiguration](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Region** - *[krävs Field]* den region som du väljer är där du kan välja där Test Drive Logic App-resurser distribueras.

    *Obs!* Om din Logikapp har alla anpassade resurser som lagras i en region, kontrollerar du den regionen väljs här. Det bästa sättet att göra detta är att **fullständigt distribuera Logikappen lokalt på din Azure-prenumeration i portalen och kontrollera att det fungerar** innan du skriver in här.

- **Maximal samtidiga Provkörningar** - *[krävs Field]* nummer Provköra instanser som redan är distribuerad och väntar på åtkomst per valda regionen. Kunder kan direkt komma åt den här Provkörningar i stället för att behöva vänta på en distribution.

    *Obs!* Om du kör en webbseminarium/klass där du vill att alla dina N antalet studenter att testa, rekommenderar vi att publicera med N antal frekvent instanser och sedan en gång klassen är över att publicera tillbaka till det normala antalet frekvent instanser.

- **Driv varaktighet (timmar) -** *[krävs Field]* varaktigheten för hur länge Test Drive förblir aktiv i \# timmar. Test Drive avslutas automatiskt efter denna tid har löpt ut.

- **Namn på Azure-resursgrupp -** *[obligatoriskt fält]* skrivning i namn på resursgruppen där ditt Logic App Test Drives sparas.

- **Tilldela namn för Logikappen -** *[obligatoriskt fält]* skrivning i Logic App som används för att tilldela en användare i Test Drive innan kunden får den, Skriv namnet på den här Logikappen. Kontrollera att filen sparas i resursgruppen ovan.

- **Ta bort etableringen av namn för Logikappen -** *[obligatoriskt fält]* skriva i Logic App-namn för din borttagning av alla resurser som skapats i Test Drive. Kontrollera att filen sparas i resursgruppen ovan.

- **Komma åt Information -** *[obligatoriskt fält]* när en kund får sin Test Drive kan komma åt information visas för dem. Dessa instruktioner är avsedda att dela användbara Utdataparametrarna från Test Drive Resource Manager-mallen. För att inkludera utdataparametrar, använda dubbla klammerparenteser (till exempel **{{outputname}}**), och de kommer att infogas korrekt på plats. (HTML-strängformatering bör här renderas i klientdelen).

### <a name="test-drive-deployment-subscription-details"></a>Prenumerationsinformation för Test Drive-distribution

Det är den sista delen att fylla i för att kunna distribuera Test Drives automatiskt genom att ansluta dina Azure-prenumeration och Azure Active Directory (AD).

![Test Drive-distribution prenumerationsinformation](./media/azure-resource-manager-test-drive/subdetails1.png)

**Azure prenumerations-ID** *[krävs Field]* detta ger åtkomst till Azure-tjänster och Azure-portalen. Prenumerationen är där Resursanvändning rapporteras och tjänster faktureras. Om du inte redan har en **separat** Azure-prenumeration för Test Drives, gå vidare och gör ett. Du hittar ID: N för Azure-prenumeration genom att logga in på Azure-portalen och gå till prenumerationer på menyn till vänster.
(Exempel: ”a83645ac-1234-5ab6-6789-1h234g764ghty”)

![Azure-prenumerationer](./media/azure-resource-manager-test-drive/subdetails2.png)

**Azure AD-klient-ID** *[krävs Field]* om du har ett klient-ID redan finns du hittar den nedan i egenskaperna -\> katalog-ID

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Annars skapar du en ny klient i Azure Active Directory.

![Skärmen med Azure Active Directory-egenskaper](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Azure Active Directory-klienter](./media/azure-resource-manager-test-drive/subdetails6.png)

**Azure AD App-ID** *[krävs Field]* nästa steg är att skapa och registrera ett nytt program. Vi använder det här programmet för att utföra åtgärder på din Test Drive-instans.

1. Gå till den nyligen skapade katalogen eller redan befintliga directory och välj Azure Active directory i filterrutan.
2. Sök efter ”appregistreringar” och klicka på ”Lägg till”
3. Ange ett programnamn.
4. Välj typ av som ”webbapp / API”
5. Ange ett värde i inloggnings-URL, vi vann\'t använder fältet.
6. Klicka på Skapa.
7. När programmet har skapats går du till egenskaper -\> ange programmet som flera innehavare och tryck på Spara.

Klicka på Spara. Det sista steget är att hämta program-ID för den här registrerad app och klistra in den i fältet Test Drive här.

![ID för Azure Active Directory-programmet](./media/azure-resource-manager-test-drive/subdetails7.png)

Får vi använder programmet för att distribuera till prenumerationen, behöver vi lägga till programmet som deltagare för prenumerationen. Anvisningarna för dessa är som nedan:

1. Gå till bladet prenumerationer och välj lämplig prenumeration som du använder för Test-enheten.
1. Klicka på **åtkomstkontroll (IAM)**.
1. Klicka på den **rolltilldelningar** fliken.  ![Azure Active Directory, att lägga till ett nytt huvudnamn för åtkomstkontroll](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Klicka på **Lägg till rolltilldelning**.
1. Ange rollen som **deltagare**.
1. Skriv namnet på Azure AD-programmet och Välj program att tilldela rollen.
    ![Azure Active Directory-behörigheter](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Klicka på **Spara**.

**Azure AD App Key -** *[obligatoriskt fält]* sista fältet är att generera en autentiseringsnyckel. Under nycklar, Lägg till en beskrivning av nyckeln, ange hur lång tid att aldrig upphöra, sedan väljer Spara. Det är **viktiga** för att förhindra att ett utgånget nyckel, vilket bryter din provkörning i produktion. Kopiera detta värde och klistra in den i din Test Drive-obligatoriskt.

![Azure Active Directory-nycklar-avsnittet](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Du kan inte använda förhandsversionen av Azure App registreringen eftersom den inte för närvarande genererar en base64-kodad nyckel.


<a name="next-steps"></a>Nästa steg
----------

Nu när du har alla Test Drive fälten ifyllda, gå igenom och **publicera** ditt erbjudande. När din provkörning har gått ut certifieringsprocessen, bör du gå en stor utsträckning testa kundupplevelsen i den **förhandsversion** i ditt erbjudande. Starta en Test Drive i Användargränssnittet och kontrollera att din Provkörningar fullständigt distribueras på rätt sätt.

Det är viktigt att Observera att du inte ta bort någon del av Test Drive som de har etablerats för dina kunder så Test Drive-tjänsten kommer automatiskt att rensa de här resursgrupperna upp när en kund har slutförts med den.

När du känner dig bekväm med ditt erbjudande förhandsversion nu är det dags att **Driftsätt**! Det finns en slutlig granskningsprocess från Microsoft när erbjudandet har publicerats till dubbla Kontrollera hela slutpunkt till slutpunkt-upplevelsen. Om erbjudandet hämtar avvisades av någon anledning, skickar vi ett meddelande till den tekniska kontakten för ditt erbjudande som förklarar vad kommer att behöva hämta har åtgärdats.

Om du har fler frågor, söker felsökningstips eller vill göra din provkörning lyckas, går du till [vanliga frågor och svar, felsökning och bästa praxis](./marketing-and-best-practices.md).
